---
title: Stav relace a aplikace v ASP.NET Core
author: rick-anderson
description: Objevte přístupy k zachování stavu relace a aplikace mezi požadavky.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2019
uid: fundamentals/app-state
ms.openlocfilehash: 3d878a389462aa7f3932f374034fb7cf11fd191c
ms.sourcegitcommit: c716ea9155a6b404c1f3d3d34e2388454cd276d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2019
ms.locfileid: "66716338"
---
# <a name="session-and-app-state-in-aspnet-core"></a>Stav relace a aplikace v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), [Diana LaRose](https://github.com/DianaLaRose), a [Luke Latham](https://github.com/guardrex)

HTTP je Bezstavová protokol. Bez dalších kroků, požadavky HTTP jsou nezávislé zprávy, které nechcete zachovat hodnoty uživatele nebo stav aplikace. Tento článek popisuje několik přístupů, které chcete zachovat data a aplikace stavu mezi žádostí uživatele.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Správa stavu

Stav může být uložen několik přístupů. Každý přístup je popsána dále v tomto tématu.

| Přístup k ukládání | Mechanismus úložiště |
| ---------------- | ----------------- |
| [Soubory cookie](#cookies) | Soubory cookie protokolu HTTP (můžou zahrnovat data uložená pomocí kódu aplikace na straně serveru) |
| [Stav relace](#session-state) | Soubory cookie protokolu HTTP a kódu aplikace na straně serveru |
| [TempData](#tempdata) | Soubory cookie protokolu HTTP nebo stav relace |
| [Řetězce dotazů](#query-strings) | Řetězce dotazu HTTP |
| [Skrytá pole](#hidden-fields) | Pole formuláře HTTP |
| [HttpContext.Items](#httpcontextitems) | Kód aplikace na straně serveru |
| [Mezipaměť](#cache) | Kód aplikace na straně serveru |
| [Injektáž závislostí](#dependency-injection) | Kód aplikace na straně serveru |

## <a name="cookies"></a>Soubory cookie

Soubory cookie ukládat data napříč požadavky. Protože soubory cookie jsou odesílány při každé žádosti, jejich velikosti by měla omezit na minimum. V ideálním případě by měla pouze identifikátor uložena do souboru cookie s daty uloženými v aplikaci. Většina prohlížečů omezit velikost souboru cookie do 4096 bajtů. Pouze omezený počet soubory cookie jsou k dispozici pro každou doménu.

Protože soubory cookie jsou v souladu s úmyslné poškozování, musí být ověřené aplikací. Soubory cookie může odstranit uživatelů a vypršení platnosti na klientských počítačích. Soubory cookie jsou však obvykle nejvíce trvalý formou trvalost dat na straně klienta.

Soubory cookie se často používají pro přizpůsobení, kde je obsah přizpůsobit pro známé uživatele. Uživatel je identifikovat jen a ve většině případů není ověřen. Soubor cookie může ukládat jméno uživatele, název účtu nebo jedinečné ID uživatele (například identifikátor GUID). Pak můžete soubor cookie pro přístup k individuální nastavení uživatele, jako je barva pozadí své upřednostňované webu.

Mějte na paměti o [Evropské unie obecného nařízení (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection) při vydávání soubory cookie a týkající se ochrany osobních údajů se vztahuje na. Další informace najdete v tématu [podpora obecného Regulation (GDPR) v ASP.NET Core](xref:security/gdpr).

## <a name="session-state"></a>Stav relace

Stav relace je scénář ASP.NET Core určené k ukládání uživatelských dat, zatímco uživatel prochází webové aplikace. Stav relace používá úložiště udržovat aplikace pro uchovávání dat napříč požadavky klientů. Data relace je založená na mezipaměti a považují za dočasné data&mdash;webu by měl dál fungovat bez data relace. Nejdůležitější aplikační data by měla uložené v uživatelské databázi a uložili do mezipaměti v relaci pouze jako optimalizaci výkonu.

> [!NOTE]
> Relace není podporována v [SignalR](xref:signalr/index) aplikace protože [rozbočovače SignalR](xref:signalr/hubs) se dá provádět nezávislé kontextu HTTP. Například tato situace může nastat při dlouho dotazování požadavku je otevřena hub za dobu života kontext žádosti HTTP.

ASP.NET Core udržuje stav relace tím, že poskytuje soubor cookie klientovi, který obsahuje Identifikátor relace, která se odesílá do aplikace spolu s každou žádostí. Aplikace použije Identifikátor relace k načtení dat relace.

Stav relace je třeba následujícího chování:

* Protože soubor cookie relace je specifická pro prohlížeče, relace nejsou sdíleny napříč prohlížeči.
* Soubory cookie relace se odstraní při ukončení relace prohlížeče.
* Pokud pro relaci vypršela platnost souboru cookie, je vytvořit novou relaci, která používá stejný soubor cookie relace.
* Prázdná relace nejsou zachovány&mdash;relace musí mít alespoň jednu hodnotu sady k uchování relace napříč požadavky. Pokud relace není zachována, nové ID relace se vygeneruje pro každý nový požadavek.
* Aplikace si zachová relace po omezenou dobu od poslední žádosti. Aplikace nastaví časový limit relace nebo výchozí hodnotu 20 minut. Stav relace je ideální pro ukládání uživatelských dat, která je specifická pro konkrétní relace, ale pokud dat nevyžaduje, aby trvalého úložiště napříč relacemi.
* Odstranit data relace buď pokud [ISession.Clear](/dotnet/api/microsoft.aspnetcore.http.isession.clear) volána implementace nebo vypršení platnosti relace.
* Neexistuje žádný výchozí mechanismus k informování kód aplikace, že bylo ukončeno prohlížeče klienta nebo když souboru cookie relace se odstraní nebo vypršení platnosti na straně klienta.
* ASP.NET Core MVC a šablony Razor pages zahrnují podporu pro obecné nařízení (GDPR). Soubory cookie pro stav relace nejsou označené základní ve výchozím nastavení, proto stav relace není funkční, pokud je povoleno sledování návštěvníkem lokality. Další informace naleznete v tématu <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Citlivá data neukládejte do stavu relace. Uživatel nemusí zavřete prohlížeč a zrušte souboru cookie relace. Některé prohlížeče udržovat platné soubory cookie v prohlížeči windows. Relace nemusí být omezeny na jednoho uživatele&mdash;dalšího uživatele může nadále procházet aplikace pomocí stejného souboru cookie relace.

Poskytovatel mezipaměti v paměti ukládá data relace v paměti na server, ve kterém se aplikace nachází. Ve scénáři farmy serveru:

* Použití *rychlé relace* a jejich zapojení každá relace pro konkrétní aplikaci instance na jednotlivých serverech. [Azure App Service](https://azure.microsoft.com/services/app-service/) používá [požádat o směrování žádostí na aplikace](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) vynutit rychlé relace ve výchozím nastavení. Rychlé relace však může ovlivnit škálovatelnost a zkomplikovat aktualizace webové aplikace. Lepším řešením je použití Redis nebo SQL Server distribuovaná mezipaměť, která nevyžaduje rychlé relace. Další informace naleznete v tématu <xref:performance/caching/distributed>.
* Soubor cookie relace se šifruje pomocí [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector). Ochrana dat musejí být správně nakonfigurovány ke čtení souborů cookie relací na každém počítači. Další informace najdete v tématu <xref:security/data-protection/introduction> a [zprostředkovateli úložiště klíčů](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Nakonfigurovat stav relace

[Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) balíček, který je součástí [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app), poskytuje middleware pro správu stavu relace. Povolit relace middleware `Startup` musí obsahovat:

* Některé z [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) mezipaměti paměti. `IDistributedCache` Implementace se používá jako záložní úložiště pro relaci. Další informace naleznete v tématu <xref:performance/caching/distributed>.
* Volání [AddSession](/dotnet/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions.addsession) v `ConfigureServices`.
* Volání [UseSession](/dotnet/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions#methods_) v `Configure`.

Následující kód ukazuje, jak nastavit Zprostředkovatel relací v paměti s výchozí implementace v paměti `IDistributedCache`:

[!code-csharp[](app-state/samples/2.x/SessionSample/Startup.cs?name=snippet1&highlight=5-14,34)]

Je důležité pořadí middlewaru. V předchozím příkladu `InvalidOperationException` dojde k výjimce při `UseSession` je volána poté, co `UseMvc`. Další informace najdete v tématu [Middleware řazení](xref:fundamentals/middleware/index#order).

[HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session) je k dispozici po dokončení konfigurace stavu relace.

`HttpContext.Session` Nelze získat přístup před `UseSession` byla volána.

Po aplikaci byl zahájen zápis do datového proudu odpovědí nelze vytvořit novou relaci s nového souboru cookie relace. Výjimky je zaznamenána do protokolu webového serveru a nezobrazuje se v prohlížeči.

### <a name="load-session-state-asynchronously"></a>Načíst stav relace asynchronně

Výchozí zprostředkovatel relací v ASP.NET Core načte záznamy relace ze základního [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) asynchronně pouze tehdy, pokud záložní úložiště [ISession.LoadAsync](/dotnet/api/microsoft.aspnetcore.http.isession.loadasync) explicitně volána metoda před [TryGetValue](/dotnet/api/microsoft.aspnetcore.http.isession.trygetvalue), [nastavit](/dotnet/api/microsoft.aspnetcore.http.isession.set), nebo [odebrat](/dotnet/api/microsoft.aspnetcore.http.isession.remove) metody. Pokud `LoadAsync` není volána nejprve základní záznam relace načtení synchronně, což může způsobit snížení výkonu ve velkém měřítku.

Pokud chcete, aby aplikace vynucení tohoto modelu, zabalit [DistributedSessionStore](/dotnet/api/microsoft.aspnetcore.session.distributedsessionstore) a [DistributedSession](/dotnet/api/microsoft.aspnetcore.session.distributedsession) implementace s verzemi, které vyvolá výjimku, pokud se `LoadAsync` metoda není volána před provedením `TryGetValue`, `Set`, nebo `Remove`. Zaregistrujte zabalené verze v kontejneru služby.

### <a name="session-options"></a>Možnosti relace

Chcete-li přepsat výchozí relace, použijte [SessionOptions](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions).

| Možnost | Popis |
| ------ | ----------- |
| [Soubor cookie](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookie) | Určuje nastavení použité k vytvoření souboru cookie. [Název](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name) výchozí hodnota je [SessionDefaults.CookieName](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`). [Cesta](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path) výchozí hodnota je [SessionDefaults.CookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`). [SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite) výchozí hodnota je [SameSiteMode.Lax](/dotnet/api/microsoft.aspnetcore.http.samesitemode) (`1`). [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) výchozí hodnota je `true`. [IsEssential](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.isessential) výchozí hodnota je `false`. |
| [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) | `IdleTimeout` Určuje, jak dlouho může být relace nečinná předtím, než jsou opuštěných jeho obsah. Každá relace přístup obnoví časový limit. Toto nastavení platí jenom pro obsah relace, není soubor cookie. Výchozí hodnota je 20 minut. |
| [IOTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.iotimeout) | Maximální množství času povoleny relace načtení z úložiště nebo k potvrzení zpět do úložiště. Toto nastavení může platí jenom pro asynchronní operace. Tento časový limit se dají zakázat pomocí [InfiniteTimeSpan](/dotnet/api/system.threading.timeout.infinitetimespan). Výchozí hodnota je 1 minuta. |

Relace do souboru cookie používá ke sledování a identifikace požadavků z jediného prohlížeče. Ve výchozím nastavení, je tento soubor cookie s názvem `.AspNetCore.Session`, a používá cestu k `/`. Protože výchozí soubor cookie nemá určenou doménu, to nebude zpřístupněn pro skript na straně klienta na stránce (protože [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) výchozí hodnota je `true`).

Chcete-li přepsat výchozí hodnoty souboru cookie relace, použijte `SessionOptions`:

[!code-csharp[](app-state/samples_snapshot/2.x/SessionSample/Startup.cs?name=snippet1&highlight=14-19)]

Tato aplikace používá [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) a určí, jak dlouho může být relace nečinná předtím, než jsou opuštěných jeho obsah v mezipaměti serveru. Tato vlastnost je nezávislá na vypršení platnosti souboru cookie. Každý požadavek, který prochází [relace Middleware](/dotnet/api/microsoft.aspnetcore.session.sessionmiddleware) obnoví časový limit.

Stav relace je *nezamykací*. Když dva požadavky současně pokusí změnit obsah relace, poslední žádosti přepíše první. `Session` je implementován jako *přeměnit relace*, což znamená, že veškerý obsah se ukládají společně. Když dva požadavky se snaží změnit hodnoty jinou relací, poslední žádosti může přepsat relace změny provedené v prvním.

### <a name="set-and-get-session-values"></a>Nastavení a získání hodnoty relace

Stav relace se přistupuje ze stránek Razor [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) třídy nebo MVC [řadič](/dotnet/api/microsoft.aspnetcore.mvc.controller) třídy s [HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session). Tato vlastnost je [ISession](/dotnet/api/microsoft.aspnetcore.http.isession) implementace.

`ISession` Implementace poskytuje několik metod rozšíření pro nastavení a načtení celé číslo a řetězec hodnoty. Rozšiřující metody jsou v [Microsoft.AspNetCore.Http](/dotnet/api/microsoft.aspnetcore.http) obor názvů (přidání `using Microsoft.AspNetCore.Http;` prohlášení k získání přístupu k rozšiřující metody) při [Microsoft.AspNetCore.Http.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) balíček se odkazuje v projektu. Oba balíčky jsou součástí [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).

`ISession` rozšiřující metody:

* [Get (ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.get)
* [GetInt32(ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getint32)
* [GetString (ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getstring)
* [Setint32 – (ISession, řetězec, Int32)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setint32)
* [SetString – (ISession, String, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setstring)

Následující příklad načte hodnota relace `IndexModel.SessionKeyName` klíč (`_Name` v ukázkové aplikaci) na stránce pro stránky Razor:

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

Následující příklad ukazuje, jak nastavit a získat celé číslo a řetězec:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Všechna data relace se musí serializovat povolit scénáře distribuované mezipaměti, i když se používá mezipaměť v paměti. Jsou k dispozici minimální řetězcové a číselné serializátory (podívejte se, metody a metody rozšíření [ISession](/dotnet/api/microsoft.aspnetcore.http.isession)). Komplexní typy se musí serializovat uživatelem pomocí jiného mechanismu, jako je JSON.

Přidejte následující metody rozšíření pro nastavení a získání serializovatelné objekty:

[!code-csharp[](app-state/samples/2.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

Následující příklad ukazuje, jak nastavit a získat serializovatelný objekt s metodami rozšíření:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

Zpřístupňuje ASP.NET Core [TempData vlastnost modelu stránky Razor Pages](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.tempdata) nebo [TempData kontroler MVC](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata). Tato vlastnost ukládá data, dokud je pro čtení. [Zachovat](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.itempdatadictionary.keep) a [Náhled](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.itempdatadictionary.peek) metody můžete použít k prozkoumání dat bez potřeby jejich odstranění. TempData je zvláště užitečná pro přesměrování, pokud jsou data požadovaná pro více než jeden požadavek. TempData implementují TempData zprostředkovatelů pomocí souborů cookie nebo stav relace.

### <a name="tempdata-providers"></a>Poskytovatelé TempData

TempData zprostředkovatele na základě souboru cookie se používá ve výchozím nastavení pro ukládání TempData v souborech cookie.

Soubor cookie data se šifrují pomocí [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector)kódovanou s [Base64UrlTextEncoder](/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder), pak rozdělený do bloků dat. Protože souboru cookie, který je rozdělený do bloků dat, velikost jednoho souboru cookie v ASP.NET Core 1.x neplatí omezení. Data souborů cookie není komprimována, protože komprese šifrovaná data může vést k problémům zabezpečení, jako [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) a [porušení](https://wikipedia.org/wiki/BREACH_(security_exploit)) útoky. Další informace o poskytovateli TempData založené na souborech cookie najdete v tématu [CookieTempDataProvider](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.cookietempdataprovider).

### <a name="choose-a-tempdata-provider"></a>Zvolte poskytovatele TempData

Výběru poskytovatele TempData zahrnuje třeba mít na paměti, jako například:

1. Aplikace již používá stav relace? Pokud ano, použití TempData poskytovatele stavu relace má bez dalších nákladů na aplikaci (kromě množství dat).
2. Aplikace používá TempData pouze zřídka u relativně malých objemů dat (až 500 bajtů)? Pokud tedy TempData zprostředkovatele souborů cookie přidá s malými náklady pro každý požadavek, který představuje TempData. V opačném případě může být výhodné vyhnout verzemi velké množství dat v každém požadavku, dokud spotřebované TempData TempData zprostředkovatel stavu relací.
3. Aplikace běží v serverové farmě na více serverů? Pokud tedy není žádná další konfigurace povinná používat poskytovatele TempData souboru cookie mimo ochrany dat (viz <xref:security/data-protection/introduction> a [zprostředkovateli úložiště klíčů](xref:security/data-protection/implementation/key-storage-providers)).

> [!NOTE]
> Většina webových klientů (například webové prohlížeče) vynutit omezení maximální velikosti jednotlivých souborů cookie a celkový počet souborů cookie. Při použití zprostředkovatele TempData souboru cookie, ověřte, že aplikace nebude tato omezení překročí. Celková velikost dat vezměte v úvahu. Účet pro zvýšení velikost souboru cookie kvůli šifrování a dělením dat do bloků.

### <a name="configure-the-tempdata-provider"></a>Konfigurace poskytovatele TempData

Ve výchozím nastavení je povolen zprostředkovatel TempData na základě souboru cookie.

Povolit poskytovatele TempData založeného na relacích, použijte [AddSessionStateTempDataProvider](/dotnet/api/microsoft.extensions.dependencyinjection.mvcviewfeaturesmvcbuilderextensions.addsessionstatetempdataprovider) – metoda rozšíření:

[!code-csharp[](app-state/samples_snapshot_2/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13,32)]

Je důležité pořadí middlewaru. V předchozím příkladu `InvalidOperationException` dojde k výjimce při `UseSession` je volána poté, co `UseMvc`. Další informace najdete v tématu [Middleware řazení](xref:fundamentals/middleware/index#order).

> [!IMPORTANT]
> V případě cílení na rozhraní .NET Framework a pomocí zprostředkovatele TempData založeného na relacích přidáte [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) balíčku do projektu.

## <a name="query-strings"></a>Řetězce dotazů

Omezené množství dat, je možné předat z jednoho požadavku na jiný tak, že přidáte novou žádost o řetězec dotazu. To je užitečné pro zaznamenání stavu trvalé způsobem, který umožňuje propojení s vložený stavu sdílet prostřednictvím e-mailu nebo sociální sítě. Protože řetězce dotazu adresy URL jsou veřejné, nikdy nepoužívejte řetězce dotazu pro citlivá data.

Kromě neúmyslnému sdílení, včetně dat v řetězcích dotazů můžete vytvořit příležitosti pro [padělání žádosti mezi weby (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) útoků, které můžou přimět navštívit weby se zlými úmysly při ověření uživatele. Útočníci můžou potom ukrást uživatelská data z aplikace nebo škodlivé akce jménem uživatele. Stav relace ani zachovaných aplikace musí chránit proti útokům CSRF. Další informace najdete v tématu [útokům zabránilo webů žádosti padělání (XSRF/CSRF)](xref:security/anti-request-forgery).

## <a name="hidden-fields"></a>Skrytá pole

Data můžete uložit ve skrytých polí ve formuláři a pošle zpátky na další požadavek. To je běžné v vícestránkového formulářů. Vzhledem k tomu, že klient může potenciálně manipulovat s daty, aplikace musí vždy znovu ověřit data uložená v skrytá pole.

## <a name="httpcontextitems"></a>HttpContext.Items

[HttpContext.Items](/dotnet/api/microsoft.aspnetcore.http.httpcontext.items) kolekce se používá k ukládání dat při zpracování jedné žádosti. Po zpracování požadavku se zahodí obsah do kolekce. `Items` Kolekci často používá k povolení komponenty nebo middlewaru, který má komunikovat, když fungovat v různých okamžicích v době požadavek a mít přímý způsob, jak předávat parametry.

V následujícím příkladu [middleware](xref:fundamentals/middleware/index) přidá `isVerified` k `Items` kolekce.

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

Později v kanálu, můžete další middleware přistupovat k hodnotě `isVerified`:

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync($"Verified: {context.Items["isVerified"]}");
});
```

Pro middleware, který se používá jenom jednu aplikaci `string` klíče jsou přijatelné. Middleware, které jsou sdílené mezi instancemi aplikace by měl použít jedinečný objekt klíče pro zabránění kolizím klíče. Následující příklad ukazuje, jak používat klíč jedinečný objekt definovaný ve třídě middleware:

[!code-csharp[](app-state/samples/2.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Další kód může přistupovat k hodnotu uloženou v `HttpContext.Items` pomocí klíče vystavené middleware třídy:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Tento přístup také nabízí výhodu v podobě odstranění použití klíčů řetězců v kódu.

## <a name="cache"></a>Mezipaměť

Ukládání do mezipaměti je účinný způsob, jak ukládat a načítat data. Aplikace může kontrolovat životnost položek v mezipaměti.

Data uložená v mezipaměti není spojen s konkrétní požadavek, uživatelem nebo relací. **Buďte opatrní není konkrétního uživatele ukládat data do mezipaměti, který může načíst žádostmi o jiných uživatelů.**

Další informace naleznete v tématu <xref:performance/caching/response>.

## <a name="dependency-injection"></a>Injektáž závislostí

Použití [injektáž závislostí](xref:fundamentals/dependency-injection) zpřístupnění dat pro všechny uživatele:

1. Definujte službu obsahující data. Například třída s názvem `MyAppData` je definována:

    ```csharp
    public class MyAppData
    {
        // Declare properties and methods
    }
    ```

2. Přidat třídu služby `Startup.ConfigureServices`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<MyAppData>();
    }
    ```

3. Využívat třídě datové služby:

    ```csharp
    public class IndexModel : PageModel
    {
        public IndexModel(MyAppData myService)
        {
            // Do something with the service
            //    Examples: Read data, store in a field or property
        }
    }
    ```

## <a name="common-errors"></a>Běžné chyby

* "Nepovedlo se přeložit služby pro typ"Microsoft.Extensions.Caching.Distributed.IDistributedCache"při pokusu o aktivaci"Microsoft.AspNetCore.Session.DistributedSessionStore"."

  To je obvykle způsobeno selhání nakonfigurujte alespoň jednu `IDistributedCache` implementace. Další informace naleznete v tématu <xref:performance/caching/distributed> a <xref:performance/caching/memory>.

* V události, která relace, které middleware nepodaří zachována relaci (například, pokud záložní úložiště není k dispozici), middleware protokoluje výjimku a požadavek pokračuje normálním způsobem. To vede k nepředvídatelné chování.

  Uživatel například ukládá do nákupního košíku v relaci. Uživatel přidá položky do košíku, ale potvrzení nezdaří. Aplikace neví o selhání, aby hlásila uživateli, že byla položka přidána do jejich košíku, což neplatí.

  Doporučený postup ke kontrole chyb je volání `await feature.Session.CommitAsync();` od kódu aplikace, pokud aplikace provádí zápis do relace. `CommitAsync` vyvolá výjimku, pokud záložní úložiště není k dispozici. Pokud `CommitAsync` nezdaří, aplikace může zpracovat výjimku. `LoadAsync` vyvolá za stejných podmínek, kde je úložiště dat není k dispozici.

## <a name="additional-resources"></a>Další zdroje

<xref:host-and-deploy/web-farm>
