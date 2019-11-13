---
title: Stav relace a aplikace v ASP.NET Core
author: rick-anderson
description: Zjištění přístupů, které zachovávají stav relace a aplikace mezi požadavky.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: fundamentals/app-state
ms.openlocfilehash: b80b1e72eb2f25e9c9fe07a0c33c14ecf5ae05aa
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963485"
---
# <a name="session-and-app-state-in-aspnet-core"></a>Stav relace a aplikace v ASP.NET Core

Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), [Diana LaRose](https://github.com/DianaLaRose)a [Luke Latham](https://github.com/guardrex)

HTTP je bezstavový protokol. Bez provedení dalších kroků jsou požadavky HTTP nezávislé zprávy, které neuchovávají uživatelské hodnoty nebo stav aplikace. Tento článek popisuje několik přístupů k zachování uživatelských dat a stavu aplikace mezi požadavky.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Správa stavu

Stav lze uložit pomocí několika přístupů. Jednotlivé metody jsou popsány dále v tomto tématu.

| Přístup k úložišti | Mechanismus úložiště |
| ---------------- | ----------------- |
| [Soubory cookie](#cookies) | Soubory cookie protokolu HTTP (můžou zahrnovat data uložená pomocí kódu serverové aplikace) |
| [Stav relace](#session-state) | Soubory cookie protokolu HTTP a kód aplikace na straně serveru |
| [TempData](#tempdata) | Soubory cookie protokolu HTTP nebo stav relace |
| [Řetězce dotazů](#query-strings) | Řetězce dotazů HTTP |
| [Skrytá pole](#hidden-fields) | Pole formuláře HTTP |
| [HttpContext. Items](#httpcontextitems) | Kód aplikace na straně serveru |
| [Mezipaměť](#cache) | Kód aplikace na straně serveru |
| [Injektáž závislostí](#dependency-injection) | Kód aplikace na straně serveru |

## <a name="cookies"></a>Soubory cookie

Soubory cookie ukládají data napříč požadavky. Vzhledem k tomu, že soubory cookie jsou odesílány se všemi požadavky, jejich velikost by měla být nižší. V ideálním případě by se měl v souboru cookie Uložit jenom identifikátor s daty uloženými v aplikaci. Většina prohlížečů omezuje velikost souborů cookie na 4096 bajtů. Pro každou doménu je k dispozici pouze omezený počet souborů cookie.

Vzhledem k tomu, že soubory cookie podléhají manipulaci, musí je aplikace ověřit. Soubory cookie může odstranit uživatel a vypršet na klientech. Soubory cookie jsou však všeobecně odolnější formou trvalosti dat na klientovi.

Soubory cookie jsou často používány pro přizpůsobení, kde je obsah přizpůsoben pro známého uživatele. Uživatel se ve většině případů identifikuje a není ověřený. Soubor cookie může obsahovat jméno uživatele, název účtu nebo jedinečné ID uživatele (například identifikátor GUID). Pak můžete použít soubor cookie pro přístup k individuálnímu nastavení uživatele, jako je například jeho preferovaná barva pozadí webu.

Při vystavování souborů cookie a v otázkách ochrany osobních údajů se zaměříte na [Obecné právní předpisy pro ochranu dat (GDPR) Evropské unie](https://ec.europa.eu/info/law/law-topic/data-protection) . Další informace najdete v tématu [podpora obecné nařízení o ochraně osobních údajů (GDPR) v ASP.NET Core](xref:security/gdpr).

## <a name="session-state"></a>Stav relace

Stav relace je ASP.NET Core scénář pro ukládání uživatelských dat, když uživatel prochází webovou aplikaci. Stav relace používá úložiště udržované aplikací k zachování dat napříč požadavky klienta. Data relace jsou zálohovaná mezipamětí a považují se za data s dočasnými daty&mdash;web by měl dál fungovat bez dat relace. Kritická data aplikace by měla být uložena v uživatelské databázi a v mezipaměti v rámci relace pouze jako optimalizace výkonu.

> [!NOTE]
> Relace není v aplikacích [SignalR](xref:signalr/index) podporovaná, protože [centrumSignalR](xref:signalr/hubs) se může spouštět nezávisle na kontextu http. K tomu může dojít například v případě, že je požadavek dlouhého cyklického dotazování otevřený centrem po dobu životnosti kontextu HTTP požadavku.

ASP.NET Core udržuje stav relace tím, že poskytuje soubor cookie pro klienta, který obsahuje ID relace, která se pošle do aplikace s každým požadavkem. Aplikace používá ID relace k načtení dat relace.

Stav relace vykazuje následující chování:

* Vzhledem k tomu, že soubory cookie relace jsou specifické pro prohlížeč, relace nejsou sdíleny mezi prohlížeči.
* Soubory cookie relace se odstraní po ukončení relace prohlížeče.
* Pokud se soubor cookie přijme pro relaci, která vypršela, vytvoří se nová relace, která bude používat stejný soubor cookie relace.
* Prázdné relace nejsou zachované,&mdash;relace musí mít nastavenou aspoň jednu hodnotu, aby se relace mezi požadavky zachovala. Pokud se relace nezachová, vygeneruje se pro každý nový požadavek nové ID relace.
* Aplikace po poslední žádosti zachovává relaci po určitou dobu. Aplikace buď nastaví časový limit relace, nebo použije výchozí hodnotu 20 minut. Stav relace je ideální pro ukládání uživatelských dat, která jsou specifická pro konkrétní relaci, ale v případě, že data nevyžadují trvalé úložiště napříč relacemi.
* Data relace se odstraní, když se zavolá [ISession. Clear](/dotnet/api/microsoft.aspnetcore.http.isession.clear) implementace nebo když vyprší platnost relace.
* Neexistuje žádný výchozí mechanismus pro informování kódu aplikace, který byl zavřen klientským prohlížečem, nebo při odstranění nebo vypršení platnosti souboru cookie relace na klientovi.
* Šablony stránek ASP.NET Core MVC a Razor zahrnují podporu pro Obecné nařízení o ochraně osobních údajů (GDPR). Soubory cookie stavu relace nejsou ve výchozím nastavení označeny jako důležité, takže stav relace není funkční, pokud návštěvník lokality nepovoluje sledování. Další informace najdete v tématu <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Neukládejte citlivá data do stavu relace. Uživatel pravděpodobně neukončí prohlížeč a nevymaže soubor cookie relace. Některé prohlížeče udržují platné soubory cookie relace napříč okny prohlížeče. Relace nemusí být omezena na jednoho uživatele&mdash;může další uživatel pokračovat v procházení aplikace se stejným souborem cookie relace.

Poskytovatel mezipaměti v paměti ukládá data relace do paměti serveru, kde se aplikace nachází. Ve scénáři serverové farmy:

* Pomocí *rychlých relací* spojíte každou relaci s konkrétní instancí aplikace na samostatném serveru. [Azure App Service](https://azure.microsoft.com/services/app-service/) využívá [Směrování žádostí aplikace (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) k vykonání rychlých relací ve výchozím nastavení. Rychlé relace ale můžou ovlivnit škálovatelnost a zkomplikovat aktualizace webových aplikací. Lepším řešením je použití distribuované mezipaměti Redis nebo SQL Server, která nevyžaduje relace v rychlém provozu. Další informace najdete v tématu <xref:performance/caching/distributed>.
* Soubor cookie relace je zašifrovaný přes [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector). Ochrana dat musí být správně nakonfigurovaná tak, aby na každém počítači četla soubory cookie relace. Další informace najdete v tématu <xref:security/data-protection/introduction> a [poskytovatelé úložiště klíčů](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Konfigurovat stav relace

Balíček [Microsoft. AspNetCore. Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) , který je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), poskytuje middleware pro správu stavu relace. Chcete-li povolit middleware relace, `Startup` musí obsahovat:

* Všechny mezipaměti [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) paměti. Implementace `IDistributedCache` se používá jako záložní úložiště pro relaci. Další informace najdete v tématu <xref:performance/caching/distributed>.
* Volání [AddSession](/dotnet/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions.addsession) v `ConfigureServices`.
* Volání [UseSession](/dotnet/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions.usesession#Microsoft_AspNetCore_Builder_SessionMiddlewareExtensions_UseSession_Microsoft_AspNetCore_Builder_IApplicationBuilder_) v `Configure`.

Následující kód ukazuje, jak nastavit zprostředkovatele relace v paměti s výchozí implementací v paměti `IDistributedCache`:

[!code-csharp[](app-state/samples/2.x/SessionSample/Startup.cs?name=snippet1&highlight=5-14,34)]

Důležité je pořadí middlewaru. V předchozím příkladu dojde k výjimce `InvalidOperationException`, když se `UseSession` vyvolá po `UseMvc`. Další informace najdete v tématu [řazení middlewaru](xref:fundamentals/middleware/index#order).

[HttpContext. Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session) je k dispozici po nakonfigurování stavu relace.

k `HttpContext.Session` nelze přistupovat před voláním `UseSession`.

Novou relaci s novým souborem cookie relace nelze vytvořit poté, co aplikace začne zapisovat do datového proudu odpovědí. Výjimka se zaznamená do protokolu webového serveru a nezobrazuje se v prohlížeči.

### <a name="load-session-state-asynchronously"></a>Asynchronní načtení stavu relace

Výchozí zprostředkovatel relací v ASP.NET Core načítá záznamy relací z podkladového záložního úložiště [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) asynchronně pouze v případě, že metoda [ISession. LoadAsync](/dotnet/api/microsoft.aspnetcore.http.isession.loadasync) je explicitně volána před metodami [TryGetValue](/dotnet/api/microsoft.aspnetcore.http.isession.trygetvalue), [set](/dotnet/api/microsoft.aspnetcore.http.isession.set)nebo [Remove](/dotnet/api/microsoft.aspnetcore.http.isession.remove) . V případě, že se `LoadAsync` nevolá jako první, načte se příslušný záznam relace synchronně, což může způsobit škálování na výkon.

Chcete-li, aby aplikace vynutila tento model, zabalte implementace [DistributedSessionStore](/dotnet/api/microsoft.aspnetcore.session.distributedsessionstore) a [DistributedSession](/dotnet/api/microsoft.aspnetcore.session.distributedsession) s verzemi, které vyvolávají výjimku, pokud `LoadAsync` metoda není volána před `TryGetValue`, `Set`nebo `Remove`. Zabalené verze zaregistrujte do kontejneru služby.

### <a name="session-options"></a>Možnosti relace

Pokud chcete přepsat výchozí nastavení relace, použijte [SessionOptions](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions).

| Možnost | Popis |
| ------ | ----------- |
| [Soubor](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookie) | Určuje nastavení použité k vytvoření souboru cookie. Výchozí [název](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name) je [SessionDefaults. cookie](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`). Výchozí [cesta](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path) je [SessionDefaults. cookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`). [SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite) se standardně [SameSiteMode. LAX](/dotnet/api/microsoft.aspnetcore.http.samesitemode) (`1`). [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) výchozím nastavením je `true`. Ve [výchozím nastavení](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.isessential) je `false`. |
| [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) | `IdleTimeout` určuje, jak dlouho může být relace nečinná, než dojde k opuštění jejího obsahu. Při každém přístupu k relaci se obnoví časový limit. Toto nastavení se vztahuje pouze na obsah relace, nikoli na soubor cookie. Výchozí hodnota je 20 minut. |
| [IOTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.iotimeout) | Maximální doba, po kterou je možné načíst relaci z úložiště nebo ji zapsat zpět do úložiště. Toto nastavení se může vztahovat jenom na asynchronní operace. Tento časový limit se dá zakázat pomocí [InfiniteTimeSpan](/dotnet/api/system.threading.timeout.infinitetimespan). Výchozí hodnota je 1 minuta. |

Relace používá soubor cookie ke sledování a identifikaci požadavků z jednoho prohlížeče. Ve výchozím nastavení je tento soubor cookie pojmenovaný `.AspNetCore.Session`a používá cestu `/`. Vzhledem k tomu, že výchozí soubor cookie neurčuje doménu, není k dispozici pro skript na straně klienta na stránce (protože [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) výchozí nastavení `true`).

Pokud chcete přepsat výchozí hodnoty relace souborů cookie, použijte `SessionOptions`:

[!code-csharp[](app-state/samples_snapshot/2.x/SessionSample/Startup.cs?name=snippet1&highlight=14-19)]

Aplikace používá vlastnost [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) k určení, jak dlouho může být relace nečinná, než dojde k opuštění jejího obsahu v mezipaměti serveru. Tato vlastnost je nezávislá na vypršení platnosti souboru cookie. Každý požadavek, který projde [middlewarem relace](/dotnet/api/microsoft.aspnetcore.session.sessionmiddleware) , obnoví časový limit.

Stav relace není *zamknutý*. Pokud se dvě požadavky současně pokoušejí změnit obsah relace, poslední požadavek přepíše první. `Session` je implementován jako *souvislá relace*, což znamená, že veškerý obsah je uložen společně. Když se dvě žádosti snaží změnit jiné hodnoty relace, může poslední požadavek přepsat změny relace provedené prvním.

### <a name="set-and-get-session-values"></a>Nastavení a získání hodnot relace

Stav relace je k dispozici z Razor Pages třídy [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) nebo třídy [kontroleru](/dotnet/api/microsoft.aspnetcore.mvc.controller) MVC s [HttpContext. Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session). Tato vlastnost je [ISession](/dotnet/api/microsoft.aspnetcore.http.isession) implementace.

Implementace `ISession` poskytuje několik metod rozšíření pro nastavení a načtení hodnot typu Integer a String. Metody rozšíření jsou v oboru názvů [Microsoft. AspNetCore. http](/dotnet/api/microsoft.aspnetcore.http) (přidání příkazu `using Microsoft.AspNetCore.Http;` pro získání přístupu k metodám rozšíření) při odkazování na balíček [Microsoft. AspNetCore. http. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) v projektu. Oba balíčky jsou součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).

`ISession` metody rozšíření:

* [Get (ISession; String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.get)
* [GetInt32 (ISession; String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getint32)
* [GetString (ISession; String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getstring)
* [Setint32 – (ISession; String; Int32)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setint32)
* [SetString (ISession; String; String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setstring)

Následující příklad načte hodnotu relace pro `IndexModel.SessionKeyName` klíč (`_Name` v ukázkové aplikaci) na stránce Razor Pages:

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

Následující příklad ukazuje, jak nastavit a získat celé číslo a řetězec:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Všechna data relace musí být serializována, aby bylo možné povolit scénář distribuované mezipaměti, i když používáte mezipaměť v paměti. Jsou k dispozici minimální řetězcové a číselné serializace (viz metody a metody rozšíření [ISession](/dotnet/api/microsoft.aspnetcore.http.isession)). Komplexní typy musí být serializovány uživatelem pomocí jiného mechanismu, jako je například JSON.

Přidejte následující metody rozšíření pro nastavení a získání serializovatelných objektů:

[!code-csharp[](app-state/samples/2.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

Následující příklad ukazuje, jak nastavit a získat serializovatelný objekt s metodami rozšíření:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>Razor Pages [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) nebo Controller. Tato vlastnost ukládá data, dokud je nepřečetla v jiné žádosti. Metody [Keep (String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) a [prohlížet (String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) lze použít k prohlédnutí dat bez odstranění na konci požadavku. [Keep ()](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) označí všechny položky ve slovníku pro uchování. `TempData` je zvláště užitečné pro přesměrování, pokud se vyžadují data pro více než jeden požadavek. `TempData` implementuje poskytovatelé `TempData` buď pomocí souborů cookie, nebo stavu relace.

## <a name="tempdata-samples"></a>Ukázky TempData

Vezměte v úvahu následující stránku, která vytvoří zákazníka:

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

Následující stránka zobrazuje `TempData["Message"]`:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

V předchozím kódu se na konci žádosti `TempData["Message"]` **neodstraní,** protože se používá `Peek`. Aktualizace stránky zobrazuje `TempData["Message"]`.

Následující kód je podobný předchozímu kódu, ale používá `Keep` k zachování dat na konci požadavku:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

Navigace mezi *IndexPeek* a *IndexKeep* stránkami se neodstraní `TempData["Message"]`.

Následující kód zobrazí `TempData["Message"]`, ale na konci žádosti se `TempData["Message"]` odstraní:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>TempData poskytovatelé

Zprostředkovatel TempData založený na souborech cookie se ve výchozím nastavení používá k ukládání TempData do souborů cookie.

Data souborů cookie se šifrují pomocí [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector)kódovaného pomocí [Base64UrlTextEncoder](/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder)a pak jsou rozdělená do bloků. Vzhledem k tomu, že soubor cookie je zablokované, nepoužije se omezení velikosti jednoho souboru cookie v ASP.NET Core 1. x. Data souborů cookie nejsou komprimována, protože komprimace šifrovaných dat může vést k problémům se zabezpečením, jako jsou [trestné činy](https://wikipedia.org/wiki/CRIME_(security_exploit)) a útoky na [porušení](https://wikipedia.org/wiki/BREACH_(security_exploit)) . Další informace o poskytovateli TempData založených na souborech cookie najdete v tématu [CookieTempDataProvider](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.cookietempdataprovider).

### <a name="choose-a-tempdata-provider"></a>Zvolit poskytovatele TempData

Výběr poskytovatele TempData zahrnuje několik předpokladů, například:

1. Používá aplikace již stav relace? V takovém případě použití poskytovatele TempData stavu relace nemá žádné další náklady na aplikaci (kromě velikosti dat).
2. Používá aplikace TempData jenom poměrně malé objemy dat (až 500 bajtů)? V takovém případě poskytovatel souborů cookie TempData přidá malé náklady na každý požadavek, který přenese TempData. V takovém případě může poskytovatel TempData stavu relace vyhýbat se tomu, aby v každém požadavku Trip velké množství dat, dokud se TempData nespotřebovává.
3. Běží aplikace v serverové farmě na více serverech? V takovém případě není nutná žádná další konfigurace, která by používala poskytovatele souborů cookie TempData mimo ochranu dat (viz <xref:security/data-protection/introduction> a [poskytovatelé úložiště klíčů](xref:security/data-protection/implementation/key-storage-providers)).

> [!NOTE]
> Většina webových klientů (například webových prohlížečů) vynutila omezení pro maximální velikost každého souboru cookie, celkový počet souborů cookie nebo obojí. Při použití poskytovatele souborů cookie TempData ověřte, že aplikace nepřekročí tato omezení. Vezměte v úvahu celkovou velikost dat. Účet pro zvýšení velikosti souboru cookie z důvodu šifrování a bloků dat.

### <a name="configure-the-tempdata-provider"></a>Konfigurace poskytovatele TempData

Ve výchozím nastavení je povolený zprostředkovatel TempData založený na souborech cookie.

Pokud chcete povolit zprostředkovatele TempData založeného na relaci, použijte metodu rozšíření [AddSessionStateTempDataProvider](/dotnet/api/microsoft.extensions.dependencyinjection.mvcviewfeaturesmvcbuilderextensions.addsessionstatetempdataprovider) :

[!code-csharp[](app-state/samples_snapshot_2/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13,32)]

Důležité je pořadí middlewaru. V předchozím příkladu dojde k výjimce `InvalidOperationException`, když se `UseSession` vyvolá po `UseMvc`. Další informace najdete v tématu [řazení middlewaru](xref:fundamentals/middleware/index#order).

> [!IMPORTANT]
> Pokud cílíte .NET Framework a používáte zprostředkovatele TempData založeného na relacích, přidejte do projektu balíček [Microsoft. AspNetCore. Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) .

## <a name="query-strings"></a>Řetězce dotazů

Omezené množství dat lze předat z jedné žádosti do druhé tím, že ji přidáte do řetězce dotazu nové žádosti. To je užitečné pro zachycení stavu trvalým způsobem, který umožňuje sdílet odkazy s vloženým stavem prostřednictvím e-mailu nebo sociálních sítí. Vzhledem k tomu, že řetězce dotazu URL jsou veřejné, nikdy nepoužívejte řetězce dotazů pro citlivá data.

Kromě nezamýšleného sdílení, včetně dat v dotazovacích řetězcích, můžou vytvářet příležitosti pro útoky [CSRF (mezi lokalitami)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) , které můžou uživatelům při ověřování navštěvovat škodlivé weby. Útočníci pak můžou z aplikace ukrást uživatelská data nebo můžou jménem uživatele převzít škodlivé akce. Všechny zachované aplikace nebo stav relace musí chránit před CSRF útoky. Další informace najdete v tématu [prevence útoků proti falšování (XSRF/CSRF) žádostí mezi weby](xref:security/anti-request-forgery).

## <a name="hidden-fields"></a>Skrytá pole

Data je možné uložit do skrytých polí formuláře a pak je odeslat zpět na další žádost. To je běžné ve vícestránkových formulářích. Vzhledem k tomu, že klient může potenciálně manipulovat s daty, aplikace musí vždy znovu ověřit data uložená ve skrytých polích.

## <a name="httpcontextitems"></a>HttpContext. Items

Kolekce [HttpContext. Items](/dotnet/api/microsoft.aspnetcore.http.httpcontext.items) se používá k ukládání dat při zpracování jediné žádosti. Obsah kolekce se po zpracování žádosti zahodí. Kolekce `Items` se často používá k tomu, aby komponenty nebo middleware komunikovaly, když pracují v různých časových okamžicích během žádosti a nemají přímý způsob předávání parametrů.

V následujícím příkladu [middleware](xref:fundamentals/middleware/index) přidá `isVerified` do kolekce `Items`.

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

Později v kanálu může další middleware získat přístup k hodnotě `isVerified`:

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync($"Verified: {context.Items["isVerified"]}");
});
```

Pro middleware, které používá jediná aplikace, jsou `string` klíče přijatelné. Middleware sdílené mezi instancemi aplikace by měly používat jedinečné klíče objektů, aby se předešlo kolizi klíčů. Následující příklad ukazuje, jak použít jedinečný klíč objektu definovaný ve třídě middleware:

[!code-csharp[](app-state/samples/2.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

K hodnotě uložené v `HttpContext.Items` může přistupovat jiný kód pomocí klíče vystaveného pomocí třídy middleware:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Tento přístup má také výhodu eliminace použití řetězců klíčů v kódu.

## <a name="cache"></a>Mezipaměť

Ukládání dat do mezipaměti je účinný způsob, jak je ukládat a načítat. Aplikace může řídit dobu života položek v mezipaměti.

Data uložená v mezipaměti nejsou přidružena ke konkrétnímu požadavku, uživateli nebo relaci. **Dejte pozor, abyste nemuseli ukládat data specifická pro uživatele, která mohou načíst žádosti jiných uživatelů.**

Další informace najdete v tématu <xref:performance/caching/response>.

## <a name="dependency-injection"></a>Injektáž závislostí

Použití [Injektáže závislosti](xref:fundamentals/dependency-injection) k zpřístupnění dat všem uživatelům:

1. Definujte službu obsahující data. Například třída s názvem `MyAppData` je definována:

    ```csharp
    public class MyAppData
    {
        // Declare properties and methods
    }
    ```

2. Přidejte třídu služby pro `Startup.ConfigureServices`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<MyAppData>();
    }
    ```

3. Spotřebovat třídu datové služby:

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

* Při pokusu o aktivaci Microsoft. AspNetCore. Session. DistributedSessionStore se nepovedlo přeložit službu pro typ Microsoft. Extensions. Caching. Distributed. IDistributedCache.

  To je obvykle způsobeno tím, že selhání při konfiguraci alespoň jedné implementace `IDistributedCache`. Další informace naleznete v tématu <xref:performance/caching/distributed> a <xref:performance/caching/memory>.

* V případě, že middleware relace nemůže uchovat relaci (například pokud není k dispozici záložní úložiště), middleware zaznamená výjimku a požadavek bude normálně pokračovat. To vede k nepředvídatelnému chování.

  Uživatel například ukládá v relaci nákupní košík. Uživatel přidá položku na košík, ale potvrzení se nezdařilo. Aplikace neví o selhání, takže oznamuje uživateli, že se položka přidala do svého košíku, což není pravda.

  Doporučený postup pro kontrolu chyb je volat `await feature.Session.CommitAsync();` z kódu aplikace, když se aplikace dokončí zápisem do relace. `CommitAsync` vyvolá výjimku, pokud záložní úložiště není k dispozici. Pokud `CommitAsync` selžou, aplikace může tuto výjimku zpracovat. `LoadAsync` vyvolá stejné podmínky, kdy úložiště dat není k dispozici.
  
## <a name="opno-locsignalr-and-session-state"></a>SignalR a stav relace

SignalR aplikace by neměly k ukládání informací používat stav relace. SignalR aplikace se můžou ukládat do `Context.Items` v centru v rámci připojení. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Další zdroje

<xref:host-and-deploy/web-farm>
