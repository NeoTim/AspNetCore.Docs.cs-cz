---
title: Relace v ASP.NET jádru
author: rick-anderson
description: Zjišťujte přístupy k zachování relace mezi požadavky.
ms.author: riande
ms.custom: mvc
ms.date: 03/06/2020
no-loc:
- SignalR
uid: fundamentals/app-state
ms.openlocfilehash: 85d2a418c3aaae40bbcdc040095c2c98d4b7242c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80640035"
---
# <a name="session-and-state-management-in-aspnet-core"></a>Správa relací a stavu v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

[Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), a Diana [LaRose](https://github.com/DianaLaRose)

HTTP je bezstavový protokol. Ve výchozím nastavení jsou požadavky HTTP nezávislé zprávy, které nezachovávají uživatelské hodnoty. Tento článek popisuje několik přístupů k zachování uživatelských dat mezi požadavky.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="state-management"></a>Správa stavu

Stav lze uložit pomocí několika přístupů. Každý přístup je popsán dále v tomto tématu.

| Přístup k úložišti | Mechanismus skladování |
| ---------------- | ----------------- |
| [Soubory cookie](#cookies) | HTTP cookies. Může obsahovat data uložená pomocí kódu aplikace na straně serveru. |
| [Stav relace](#session-state) | HTTP cookies a kód aplikace na straně serveru |
| [TempData](#tempdata) | Soubory cookie HTTP nebo stav relace |
| [Řetězce dotazů](#query-strings) | Řetězce dotazů HTTP |
| [Skrytá pole](#hidden-fields) | Pole formuláře HTTP |
| [HttpContext.Položky](#httpcontextitems) | Kód aplikace na straně serveru |
| [Mezipaměť](#cache) | Kód aplikace na straně serveru |

## <a name="cookies"></a>Soubory cookie

Soubory cookie ukládají data mezi požadavky. Vzhledem k tomu, že soubory cookie jsou odesílány s každou žádostí, jejich velikost by měla být omezena na minimum. V ideálním případě by měl být v souboru cookie uložen pouze identifikátor s daty uloženými aplikací. Většina prohlížečů omezuje velikost souborů cookie na 4096 bajtů. Pro každou doménu je k dispozici pouze omezený počet souborů cookie.

Vzhledem k tomu, že soubory cookie podléhají manipulaci, musí být ověřeny aplikací. Cookies mohou být smazány uživateli a vyprší u klientů. Soubory cookie jsou však obecně nejodolnější formou trvalosti dat na straně klienta.

Soubory cookie se často používají pro přizpůsobení, kde je obsah přizpůsoben známému uživateli. Uživatel je identifikován pouze a není ověřen ve většině případů. Soubor cookie může uložit jméno uživatele, název účtu nebo jedinečné ID uživatele, například identifikátor GUID. Soubor cookie lze použít pro přístup k individuálním nastavením uživatele, například k preferované barvě pozadí webu.

Při vydávání souborů cookie a řešení otázek ochrany osobních údajů se podívejte na [obecné předpisy Evropské unie o ochraně osobních údajů (GDPR).](https://ec.europa.eu/info/law/law-topic/data-protection) Další informace naleznete [v tématu Obecné nařízení o ochraně osobních údajů (GDPR) podpora v ASP.NET Core](xref:security/gdpr).

## <a name="session-state"></a>Stav relace

Stav relace je ASP.NET základní scénář pro ukládání uživatelských dat, zatímco uživatel prochází webovou aplikaci. Stav relace používá úložiště spravované aplikací k uchování dat napříč požadavky od klienta. Data relace jsou zálohována mezipamětí a považována za zasemňovaná data. Web by měl nadále fungovat bez dat relace. Důležitá data aplikace by měla být uložena v databázi uživatelů a uložena v mezipaměti pouze jako optimalizace výkonu.

Relace není podporována v aplikacích [SignalR,](xref:signalr/index) protože [centrum SignalR](xref:signalr/hubs) může být spuštěno nezávisle na kontextu PROTOKOLU HTTP. Například k tomu může dojít, když je dlouhý požadavek dotazování otevřený rozbočovačem po dobu životnosti kontextu HTTP požadavku.

ASP.NET Core udržuje stav relace poskytnutím souboru cookie klientovi, který obsahuje ID relace. ID relace cookie:

* Je odeslándo aplikace s každým požadavkem.
* Aplikace používá k načtení dat relace.

Stav relace vykazuje následující chování:

* Soubor cookie relace je specifický pro prohlížeč. Relace nejsou sdíleny mezi prohlížeči.
* Soubory cookie relace jsou po ukončení relace prohlížeče smazány.
* Pokud je přijat soubor cookie pro relaci s ukončenou platností, je vytvořena nová relace, která používá stejný soubor cookie relace.
* Prázdné relace nejsou zachovány. Relace musí mít alespoň jednu hodnotu nastavenou tak, aby uchovávaly relaci napříč požadavky. Pokud relace není zachována, nové ID relace je generováno pro každý nový požadavek.
* Aplikace uchová v relaci po omezenou dobu po posledním požadavku. Aplikace buď nastaví časový limit relace, nebo použije výchozí hodnotu 20 minut. Stav relace je ideální pro ukládání uživatelských dat:
  * To je specifické pro konkrétní sezení.
  * Kde data nevyžaduje trvalé úložiště napříč relacemi.
* Data relace jsou odstraněna při volání implementace [ISession.Clear](/dotnet/api/microsoft.aspnetcore.http.isession.clear) nebo při vypršení platnosti relace.
* Neexistuje žádný výchozí mechanismus pro informování kódu aplikace, že prohlížeč klienta byl uzavřen nebo když je soubor cookie relace odstraněn nebo vypršela jeho platnost.
* Soubory cookie stavu relace nejsou ve výchozím nastavení označeny jako nezbytné. Stav relace není funkční, pokud není sledování povoleno návštěvníkem webu. Další informace naleznete v tématu <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Neuklápějte citlivá data ve stavu relace. Uživatel nemusí zavřít prohlížeč a vymazat soubor cookie relace. Některé prohlížeče udržují platné soubory cookie relace ve všech oknech prohlížeče. Relace nemusí být omezena na jednoho uživatele. Další uživatel může pokračovat v procházení aplikace se stejným souborem cookie relace.

Zprostředkovatel mezipaměti v paměti ukládá data relace do paměti serveru, kde je aplikace umístěna. Ve scénáři serverové farmy:

* Pomocí *nepřístupových relací* můžete každou relaci spojit s konkrétní instancí aplikace na jednotlivém serveru. [Služba Azure App Service](https://azure.microsoft.com/services/app-service/) používá [směrování žádostí o aplikace (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) k vynucení nenucených relací ve výchozím nastavení. Rychlé relace však mohou ovlivnit škálovatelnost a komplikovat aktualizace webových aplikací. Lepším přístupem je použití distribuované mezipaměti Redis nebo SQL Server, která nevyžaduje rychlé relace. Další informace naleznete v tématu <xref:performance/caching/distributed>.
* Soubor cookie relace je šifrován prostřednictvím [iDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector). Ochrana dat musí být správně nakonfigurována tak, aby četla soubory cookie relace v každém počítači. Další informace naleznete <xref:security/data-protection/introduction> v tématu a [zprostředkovatelé úložiště klíčů](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Konfigurace stavu relace

Balíček [Microsoft.AspNetCore.Session:](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/)

* Je součástí implicitně v rámci.
* Poskytuje middleware pro správu stavu relace.

Chcete-li povolit `Startup` middleware relace, musí obsahovat:

* Všechny mezipaměti [iDistributedCache.](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) Implementace `IDistributedCache` se používá jako záložní úložiště pro relaci. Další informace naleznete v tématu <xref:performance/caching/distributed>.
* Volání [AddSession](/dotnet/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions.addsession) v `ConfigureServices`.
* Volání [UseSession](/dotnet/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions.usesession#Microsoft_AspNetCore_Builder_SessionMiddlewareExtensions_UseSession_Microsoft_AspNetCore_Builder_IApplicationBuilder_) v `Configure`.

Následující kód ukazuje, jak nastavit zprostředkovatele relace v paměti s `IDistributedCache`výchozí implementací v paměti :

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup4.cs?name=snippet1&highlight=12-19,39)]

Předchozí kód nastaví krátký časový čas pro zjednodušení testování.

Pořadí middleware je důležité.  `UseSession` Zavolejte `UseRouting` po `UseEndpoints`a před . Viz [Middleware Objednávání](xref:fundamentals/middleware/index#order).

[HttpContext.Session](xref:Microsoft.AspNetCore.Http.HttpContext.Session) je k dispozici po konfiguraci stavu relace.

`HttpContext.Session`nelze získat přístup dříve, než `UseSession` byla volána.

Novou relaci s novou relací cookie nelze vytvořit poté, co aplikace začala zapisovat do datového proudu odpovědí. Výjimka je zaznamenána v protokolu webového serveru a není zobrazena v prohlížeči.

### <a name="load-session-state-asynchronously"></a>Načíst stav relace asynchronně

Výchozí poskytovatel relace v ASP.NET základní načte záznamy relací z podkladového zálohování [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) asynchronně uložit pouze v případě, že metoda [ISession.LoadAsync](/dotnet/api/microsoft.aspnetcore.http.isession.loadasync) je explicitně volána před metodami [TryGetValue](/dotnet/api/microsoft.aspnetcore.http.isession.trygetvalue), [Set](/dotnet/api/microsoft.aspnetcore.http.isession.set)nebo [Remove.](/dotnet/api/microsoft.aspnetcore.http.isession.remove) Pokud `LoadAsync` není volána jako první, základní záznam relace je načten synchronně, což může vynaložit snížení výkonu ve velkém měřítku.

Chcete-li, aby aplikace vynucovaly tento vzor, zabalte implementace [DistributedSessionStore](/dotnet/api/microsoft.aspnetcore.session.distributedsessionstore) a `TryGetValue` [DistributedSession](/dotnet/api/microsoft.aspnetcore.session.distributedsession) verzemi, které vyvolají výjimku, pokud `LoadAsync` metoda není volána před , `Set`, nebo `Remove`. Zaregistrujte zabalené verze v kontejneru služeb.

### <a name="session-options"></a>Možnosti relace

Chcete-li přepsat výchozí hodnoty relace, použijte [možnosti sessionoptions](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions).

| Možnost | Popis |
| ------ | ----------- |
| [Soubor cookie](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookie) | Určuje nastavení použitá k vytvoření souboru cookie. [Název](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name) je výchozí [na SessionDefaults.CookieName](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`). [Cesta](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path) je výchozí na [SessionDefaults.CookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`). [SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite) výchozí [SameSite.Lax](/dotnet/api/microsoft.aspnetcore.http.samesitemode) (`1`). [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) výchozí `true`. [Standard IsEssential](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.isessential) `false`je výchozí pro . |
| [Idletimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) | Označuje, `IdleTimeout` jak dlouho může být relace nečinná, než bude její obsah opuštěn. Každý přístup k relaci obnoví časový čas. Toto nastavení platí pouze pro obsah relace, nikoli pro soubor cookie. Výchozí nastavení je 20 minut. |
| [IOTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.iotimeout) | Maximální doba povolená k načtení relace z úložiště nebo potvrzení zpět do úložiště. Toto nastavení se může vztahovat pouze na asynchronní operace. Tento časový rozsah lze zakázat pomocí [InfiniteTimeSpan](/dotnet/api/system.threading.timeout.infinitetimespan). Výchozí hodnota je 1 minuta. |

Relace používá soubor cookie ke sledování a identifikaci požadavků z jednoho prohlížeče. Ve výchozím nastavení je `.AspNetCore.Session`tento soubor cookie `/`pojmenován a používá cestu aplikace . Vzhledem k tomu, že výchozí soubor cookie neurčuje doménu, není k dispozici skriptu na `true`straně klienta na stránce (protože [httponly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) výchozí).

Chcete-li přepsat výchozí nastavení <xref:Microsoft.AspNetCore.Builder.SessionOptions>relace souborů cookie, použijte :

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup2.cs?name=snippet1&highlight=5-10)]

Aplikace používá [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) vlastnost určit, jak dlouho relace může být nečinný před jeho obsah v mezipaměti serveru jsou opuštěny. Tato vlastnost je nezávislá na vypršení platnosti souboru cookie. Každý požadavek, který prochází [Middleware relace](/dotnet/api/microsoft.aspnetcore.session.sessionmiddleware) obnoví časový výtok.

Stav relace *je bez zamykání*. Pokud se dva požadavky současně pokusí změnit obsah relace, poslední požadavek přepíše první. `Session`je implementována jako *souvislé zasedání*, což znamená, že veškerý obsah je uložen společně. Pokud se dva požadavky snaží změnit různé hodnoty relace, může poslední požadavek přepsat změny relace provedené prvním.

### <a name="set-and-get-session-values"></a>Nastavení a získání hodnot relace

Stav relace je přístupný z třídy Razor Pages [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) nebo MVC [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller) s [httpcontext.session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session). Tato vlastnost je implementace [ISession.](/dotnet/api/microsoft.aspnetcore.http.isession)

Implementace `ISession` poskytuje několik metod rozšíření pro nastavení a načtení celé číslo a řetězcové hodnoty. Metody rozšíření jsou v oboru názvů [Microsoft.AspNetCore.Http.](/dotnet/api/microsoft.aspnetcore.http)

`ISession`metody rozšíření:

* [Get (ISession, Řetězec)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.get)
* [GetInt32(ISession, Řetězec)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getint32)
* [GetString (iSession, řetězec)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getstring)
* [SetInt32(ISession, Řetězec, Int32)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setint32)
* [SetString(iSession, Řetězec, Řetězec)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setstring)

Následující příklad načte hodnotu `IndexModel.SessionKeyName` relace`_Name` pro klíč (v ukázkové aplikaci) na stránce Razor Pages:

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

Následující příklad ukazuje, jak nastavit a získat celé číslo a řetězec:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Všechna data relace musí být serializována, aby byl povolen scénář distribuované mezipaměti, a to i při použití mezipaměti v paměti. Řetězec a celé číslo serializátory jsou poskytovány metody rozšíření [ISession](/dotnet/api/microsoft.aspnetcore.http.isession). Komplexní typy musí být serializovány uživatelem pomocí jiného mechanismu, jako je například JSON.

K serializaci objektů použijte následující ukázkový kód:

[!code-csharp[](app-state/samples/3.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

Následující příklad ukazuje, jak nastavit a získat serializovatelný objekt s třídou: `SessionExtensions`

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core zpřístupňuje Razor Pages <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) nebo Controller . Tato vlastnost ukládá data, dokud se číst v jiném požadavku. [Keep(String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) a [Peek(string)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) metody lze prozkoumat data bez odstranění na konci požadavku. [Zachovat](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) označí všechny položky ve slovníku pro uchovávání. `TempData`Je:

* Užitečné pro přesměrování, pokud jsou data požadována pro více než jeden požadavek.
* Implementováno zprostředkovateli `TempData` pomocí souborů cookie nebo stavu relace.

## <a name="tempdata-samples"></a>Ukázky TempData

Vezměte v úvahu následující stránku, která vytvoří zákazníka:

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

Zobrazí se `TempData["Message"]`na následující stránce :

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

V předchozí značky na konci požadavku `TempData["Message"]` **není** odstraněn, `Peek` protože se používá. Aktualizace stránky zobrazí obsah `TempData["Message"]`aplikace .

Následující značky jsou podobné předchozímu kódu, `Keep` ale používají se k zachování dat na konci požadavku:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

Přechod mezi stránkami *IndexPeek* a *IndexKeep* neodstraníte `TempData["Message"]`.

Následující kód `TempData["Message"]`se zobrazí , ale na `TempData["Message"]` konci požadavku, je odstraněn:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>Zprostředkovatelé TempData

Poskytovatel TempData založený na souborech cookie se ve výchozím nastavení používá k ukládání TempData v souborech cookie.

Data cookie jsou šifrována pomocí [iDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector), kódované [Base64UrlTextEncoder](/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder), pak chunked. Maximální velikost souboru cookie je menší než [4096 bajtů](http://www.faqs.org/rfcs/rfc2965.html) z důvodu šifrování a bloků. Data souborů cookie nejsou komprimována, protože komprese šifrovaných dat může vést k bezpečnostním problémům, jako jsou útoky [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) a [BREACH.](https://wikipedia.org/wiki/BREACH_(security_exploit)) Další informace o zprostředkovateli TempData založeném na souborech cookie naleznete v tématu [CookieTempDataProvider](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.cookietempdataprovider).

### <a name="choose-a-tempdata-provider"></a>Výběr zprostředkovatele TempData

Výběr zprostředkovatele TempData zahrnuje několik aspektů, například:

* Používá aplikace již stav relace? Pokud ano, pomocí stavu relace Zprostředkovatel TempData nemá žádné další náklady na aplikaci nad rámec velikosti dat.
* Používá aplikace TempData pouze střídmě pro relativně malé množství dat, až 500 bajtů? Pokud ano, poskytovatel souboru cookie TempData přidá malé náklady na každý požadavek, který nese TempData. Pokud tomu tak není, stav relace TempData zprostředkovatele může být prospěšné, aby se zabránilo round-tripping velké množství dat v každém požadavku, dokud TempData je spotřebována.
* Běží aplikace v serverové farmě na více serverech? Pokud ano, není nutná žádná další konfigurace pro použití poskytovatele souboru <xref:security/data-protection/introduction> cookie TempData mimo ochranu dat (viz a [zprostředkovatelé úložiště klíčů).](xref:security/data-protection/implementation/key-storage-providers)

Většina webových klientů, jako jsou webové prohlížeče, vynucuje omezení maximální velikosti každého souboru cookie a celkového počtu souborů cookie. Při používání poskytovatele souborů cookie TempData ověřte, zda aplikace tyto [limity](http://www.faqs.org/rfcs/rfc2965.html)nepřekročí . Zvažte celkovou velikost dat. Zohledňuje zvýšení velikosti souborů cookie v důsledku šifrování a bloků.

### <a name="configure-the-tempdata-provider"></a>Konfigurace zprostředkovatele TempData

Poskytovatel TempData založený na souborech cookie je ve výchozím nastavení povolen.

Chcete-li povolit zprostředkovatele TempData založeného na relaci, použijte metodu rozšíření [AddSessionStateTempDataProvider.](/dotnet/api/microsoft.extensions.dependencyinjection.mvcviewfeaturesmvcbuilderextensions.addsessionstatetempdataprovider) Je vyžadováno `AddSessionStateTempDataProvider` pouze jedno volání:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup3.cs?name=snippet1&highlight=4,6,30)]

## <a name="query-strings"></a>Řetězce dotazů

Omezené množství dat lze předat z jednoho požadavku do druhého přidáním do řetězce dotazu nového požadavku. To je užitečné pro zachycení stavu trvalým způsobem, který umožňuje sdílení odkazů s vloženým stavem prostřednictvím e-mailu nebo sociálních sítí. Vzhledem k tomu, že řetězce dotazů URL jsou veřejné, nikdy nepoužívejte řetězce dotazu pro citlivá data.

Kromě neúmyslného sdílení, včetně dat v řetězecích dotazů můžete vystavit aplikace k [útokům mezi požadavky na web (CSRF).](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) Jakýkoli zachovaný stav relace musí chránit před útoky CSRF. Další informace naleznete [v tématu Zabránění útokům na vyžádání mezi servery (XSRF/CSRF).](xref:security/anti-request-forgery)

## <a name="hidden-fields"></a>Skrytá pole

Data mohou být uložena ve skrytých polích formuláře a odeslána zpět na další požadavek. To je běžné ve vícestránkových formulářích. Vzhledem k tomu, že klient může potenciálně manipulovat s daty, aplikace musí vždy znovu ověřit data uložená ve skrytých polích.

## <a name="httpcontextitems"></a>HttpContext.Položky

Kolekce [HttpContext.Items](/dotnet/api/microsoft.aspnetcore.http.httpcontext.items) se používá k ukládání dat při zpracování jednoho požadavku. Obsah kolekce jsou zahozeny po zpracování požadavku. Kolekce `Items` se často používá k povolení komponenty nebo middleware komunikovat, když pracují v různých bodech v čase během požadavku a nemají žádný přímý způsob, jak předat parametry.

V následujícím příkladu [middleware](xref:fundamentals/middleware/index) `isVerified` `Items` přidá do kolekce:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup.cs?name=snippet1)]

Pro middleware, který se používá pouze `string` v jedné aplikaci, jsou pevné klávesy přijatelné. Middleware sdílený mezi aplikacemi by měl používat jedinečné objektové klíče, aby se zabránilo kolizím klíčů. Následující příklad ukazuje, jak používat jedinečný klíč objektu definovaný ve třídě middleware:

[!code-csharp[](app-state/samples/3.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Jiný kód může přistupovat `HttpContext.Items` k hodnotě uložené v použití klíče vystaveného třídou middleware:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Tento přístup má také tu výhodu, že eliminuje použití řetězce klíčů v kódu.

## <a name="cache"></a>Mezipaměť

Ukládání do mezipaměti je efektivní způsob ukládání a načítání dat. Aplikace může řídit životnost položek uložených v mezipaměti. Další informace naleznete v tématu <xref:performance/caching/response>.

Data uložená v mezipaměti nejsou přidružena k určitému požadavku, uživateli nebo relaci. **Neukládat do mezipaměti data specifická pro uživatele, které mohou být načteny jinými požadavky uživatelů.**

Chcete-li uložit data <xref:performance/caching/memory>z celé aplikace do mezipaměti, přečtěte si další informace.

## <a name="common-errors"></a>Běžné chyby

* "Nelze vyřešit službu pro typ Microsoft.Extensions.Caching.Distributed.IDistributedCache při pokusu o aktivaci souboru Microsoft.AspNetCore.Session.DistributedSessionStore."

  To je obvykle způsobeno tím, že `IDistributedCache` nenakonfigurujete alespoň jednu implementaci. Další informace naleznete v tématech <xref:performance/caching/distributed> a <xref:performance/caching/memory>.

Pokud middleware relace nepřetrvává relaci:

* Middleware protokoluje výjimku a požadavek pokračuje normálně.
* To vede k nepředvídatelnému chování.

Middleware relace může selhat uchovat relaci, pokud záložní úložiště není k dispozici. Uživatel například ukládá nákupní košík v relaci. Uživatel přidá položku do košíku, ale potvrzení se nezdaří. Aplikace neví o selhání, takže uživateli oznámí, že položka byla přidána do košíku, což není pravda.

Doporučený přístup ke kontrole chyb `await feature.Session.CommitAsync` je volání, když se aplikace provádí zápis do relace. <xref:Microsoft.AspNetCore.Http.ISession.CommitAsync*>vyvolá výjimku, pokud záložní úložiště není k dispozici. Pokud `CommitAsync` se nezdaří, aplikace může zpracovat výjimku. <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync*>vyvolá za stejných podmínek, když úložiště dat není k dispozici.
  
## <a name="signalr-and-session-state"></a>SignalR a stav relace

Aplikace SignalR by neměly používat stav relace k ukládání informací. Aplikace SignalR lze uložit `Context.Items` na stav připojení v rozbočovači. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Další zdroje

<xref:host-and-deploy/web-farm>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), Diana [LaRose](https://github.com/DianaLaRose), a [Luke Latham](https://github.com/guardrex)

HTTP je bezstavový protokol. Bez dalších kroků jsou požadavky HTTP nezávislé zprávy, které nezachovávají uživatelské hodnoty nebo stav aplikace. Tento článek popisuje několik přístupů k zachování uživatelských dat a stavu aplikace mezi požadavky.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="state-management"></a>Správa stavu

Stav lze uložit pomocí několika přístupů. Každý přístup je popsán dále v tomto tématu.

| Přístup k úložišti | Mechanismus skladování |
| ---------------- | ----------------- |
| [Soubory cookie](#cookies) | HTTP cookies (mohou obsahovat data uložená pomocí kódu aplikace na straně serveru) |
| [Stav relace](#session-state) | HTTP cookies a kód aplikace na straně serveru |
| [TempData](#tempdata) | Soubory cookie HTTP nebo stav relace |
| [Řetězce dotazů](#query-strings) | Řetězce dotazů HTTP |
| [Skrytá pole](#hidden-fields) | Pole formuláře HTTP |
| [HttpContext.Položky](#httpcontextitems) | Kód aplikace na straně serveru |
| [Mezipaměť](#cache) | Kód aplikace na straně serveru |
| [Vkládání závislostí](#dependency-injection) | Kód aplikace na straně serveru |

## <a name="cookies"></a>Soubory cookie

Soubory cookie ukládají data mezi požadavky. Vzhledem k tomu, že soubory cookie jsou odesílány s každou žádostí, jejich velikost by měla být omezena na minimum. V ideálním případě by měl být v souboru cookie uložen pouze identifikátor s daty uloženými aplikací. Většina prohlížečů omezuje velikost souborů cookie na 4096 bajtů. Pro každou doménu je k dispozici pouze omezený počet souborů cookie.

Vzhledem k tomu, že soubory cookie podléhají manipulaci, musí být ověřeny aplikací. Cookies mohou být smazány uživateli a vyprší u klientů. Soubory cookie jsou však obecně nejodolnější formou trvalosti dat na straně klienta.

Soubory cookie se často používají pro přizpůsobení, kde je obsah přizpůsoben známému uživateli. Uživatel je identifikován pouze a není ověřen ve většině případů. Soubor cookie může uložit jméno uživatele, název účtu nebo jedinečné ID uživatele (například identifikátor GUID). Soubor cookie pak můžete použít pro přístup k individuálním nastavením uživatele, například k jeho preferované barvě pozadí webu.

Při vydávání souborů cookie a řešení otázek ochrany osobních údajů mějte na paměti [obecné předpisy Evropské unie o ochraně osobních údajů (GDPR).](https://ec.europa.eu/info/law/law-topic/data-protection) Další informace naleznete [v tématu Obecné nařízení o ochraně osobních údajů (GDPR) podpora v ASP.NET Core](xref:security/gdpr).

## <a name="session-state"></a>Stav relace

Stav relace je ASP.NET základní scénář pro ukládání uživatelských dat, zatímco uživatel prochází webovou aplikaci. Stav relace používá úložiště spravované aplikací k uchování dat napříč požadavky od klienta. Data relace jsou zálohována mezipamětí a jsou&mdash;považována za pomíjivá data, která by web měl nadále fungovat bez dat relace. Důležitá data aplikace by měla být uložena v databázi uživatelů a uložena v mezipaměti pouze jako optimalizace výkonu.

> [!NOTE]
> Relace není podporována v aplikacích [SignalR,](xref:signalr/index) protože [centrum SignalR](xref:signalr/hubs) může být spuštěno nezávisle na kontextu PROTOKOLU HTTP. Například k tomu může dojít, když je dlouhý požadavek dotazování otevřený rozbočovačem po dobu životnosti kontextu HTTP požadavku.

ASP.NET Core udržuje stav relace poskytnutím souboru cookie klientovi, který obsahuje ID relace, které je odesláno do aplikace s každým požadavkem. Aplikace používá ID relace k načtení dat relace.

Stav relace vykazuje následující chování:

* Vzhledem k tomu, že soubor cookie relace je specifický pro prohlížeč, relace nejsou sdíleny mezi prohlížeči.
* Soubory cookie relace jsou po ukončení relace prohlížeče smazány.
* Pokud je přijat soubor cookie pro relaci s ukončenou platností, je vytvořena nová relace, která používá stejný soubor cookie relace.
* Prázdné relace nejsou zachovány&mdash;relace musí mít alespoň jednu hodnotu nastavenou do něj zachovat relace přes požadavky. Pokud relace není zachována, nové ID relace je generováno pro každý nový požadavek.
* Aplikace uchová v relaci po omezenou dobu po posledním požadavku. Aplikace buď nastaví časový limit relace, nebo použije výchozí hodnotu 20 minut. Stav relace je ideální pro ukládání uživatelských dat, která jsou specifická pro konkrétní relaci, ale kde data nevyžadují trvalé úložiště napříč relacemi.
* Data relace jsou odstraněna při volání implementace [ISession.Clear](/dotnet/api/microsoft.aspnetcore.http.isession.clear) nebo při vypršení platnosti relace.
* Neexistuje žádný výchozí mechanismus pro informování kódu aplikace, že prohlížeč klienta byl uzavřen nebo když je soubor cookie relace odstraněn nebo vypršela jeho platnost.
* Šablony stránek ASP.NET Core MVC a Razor obsahují podporu obecného nařízení o ochraně osobních údajů (GDPR). Soubory cookie stavu relace nejsou ve výchozím nastavení označeny jako nezbytné, takže stav relace není funkční, pokud není sledování povoleno návštěvníkem webu. Další informace naleznete v tématu <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Neuklápějte citlivá data ve stavu relace. Uživatel nemusí zavřít prohlížeč a vymazat soubor cookie relace. Některé prohlížeče udržují platné soubory cookie relace ve všech oknech prohlížeče. Relace nemusí být omezena na&mdash;jednoho uživatele, další uživatel může pokračovat v procházení aplikace se stejným souborem cookie relace.

Zprostředkovatel mezipaměti v paměti ukládá data relace do paměti serveru, kde je aplikace umístěna. Ve scénáři serverové farmy:

* Pomocí *nepřístupových relací* můžete každou relaci spojit s konkrétní instancí aplikace na jednotlivém serveru. [Služba Azure App Service](https://azure.microsoft.com/services/app-service/) používá [směrování žádostí o aplikace (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) k vynucení nenucených relací ve výchozím nastavení. Rychlé relace však mohou ovlivnit škálovatelnost a komplikovat aktualizace webových aplikací. Lepším přístupem je použití distribuované mezipaměti Redis nebo SQL Server, která nevyžaduje rychlé relace. Další informace naleznete v tématu <xref:performance/caching/distributed>.
* Soubor cookie relace je šifrován prostřednictvím [iDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector). Ochrana dat musí být správně nakonfigurována tak, aby četla soubory cookie relace v každém počítači. Další informace naleznete <xref:security/data-protection/introduction> v tématu a [zprostředkovatelé úložiště klíčů](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Konfigurace stavu relace

Balíček [Microsoft.AspNetCore.Session,](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) který je součástí [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), poskytuje middleware pro správu stavu relace. Chcete-li povolit `Startup` middleware relace, musí obsahovat:

* Všechny mezipaměti [iDistributedCache.](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) Implementace `IDistributedCache` se používá jako záložní úložiště pro relaci. Další informace naleznete v tématu <xref:performance/caching/distributed>.
* Volání [AddSession](/dotnet/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions.addsession) v `ConfigureServices`.
* Volání [UseSession](/dotnet/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions.usesession#Microsoft_AspNetCore_Builder_SessionMiddlewareExtensions_UseSession_Microsoft_AspNetCore_Builder_IApplicationBuilder_) v `Configure`.

Následující kód ukazuje, jak nastavit zprostředkovatele relace v paměti s `IDistributedCache`výchozí implementací v paměti :

[!code-csharp[](app-state/samples/2.x/SessionSample/Startup.cs?name=snippet1&highlight=5-14,34)]

Pořadí middleware je důležité. V předchozím příkladu `InvalidOperationException` dojde k `UseSession` výjimce, `UseMvc`když je vyvolána po . Další informace naleznete v tématu [Middleware Ordering](xref:fundamentals/middleware/index#order).

[HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session) je k dispozici po konfiguraci stavu relace.

`HttpContext.Session`nelze získat přístup dříve, než `UseSession` byla volána.

Novou relaci s novou relací cookie nelze vytvořit poté, co aplikace začala zapisovat do datového proudu odpovědí. Výjimka je zaznamenána v protokolu webového serveru a není zobrazena v prohlížeči.

### <a name="load-session-state-asynchronously"></a>Načíst stav relace asynchronně

Výchozí poskytovatel relace v ASP.NET základní načte záznamy relací z podkladového zálohování [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) asynchronně uložit pouze v případě, že metoda [ISession.LoadAsync](/dotnet/api/microsoft.aspnetcore.http.isession.loadasync) je explicitně volána před metodami [TryGetValue](/dotnet/api/microsoft.aspnetcore.http.isession.trygetvalue), [Set](/dotnet/api/microsoft.aspnetcore.http.isession.set)nebo [Remove.](/dotnet/api/microsoft.aspnetcore.http.isession.remove) Pokud `LoadAsync` není volána jako první, základní záznam relace je načten synchronně, což může vynaložit snížení výkonu ve velkém měřítku.

Chcete-li, aby aplikace vynucovaly tento vzor, zabalte implementace [DistributedSessionStore](/dotnet/api/microsoft.aspnetcore.session.distributedsessionstore) a `TryGetValue` [DistributedSession](/dotnet/api/microsoft.aspnetcore.session.distributedsession) verzemi, které vyvolají výjimku, pokud `LoadAsync` metoda není volána před , `Set`, nebo `Remove`. Zaregistrujte zabalené verze v kontejneru služeb.

### <a name="session-options"></a>Možnosti relace

Chcete-li přepsat výchozí hodnoty relace, použijte [možnosti sessionoptions](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions).

| Možnost | Popis |
| ------ | ----------- |
| [Soubor cookie](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookie) | Určuje nastavení použitá k vytvoření souboru cookie. [Název](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name) je výchozí [na SessionDefaults.CookieName](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`). [Cesta](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path) je výchozí na [SessionDefaults.CookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`). [SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite) výchozí [SameSite.Lax](/dotnet/api/microsoft.aspnetcore.http.samesitemode) (`1`). [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) výchozí `true`. [Standard IsEssential](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.isessential) `false`je výchozí pro . |
| [Idletimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) | Označuje, `IdleTimeout` jak dlouho může být relace nečinná, než bude její obsah opuštěn. Každý přístup k relaci obnoví časový čas. Toto nastavení platí pouze pro obsah relace, nikoli pro soubor cookie. Výchozí nastavení je 20 minut. |
| [IOTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.iotimeout) | Maximální doba povolená k načtení relace z úložiště nebo potvrzení zpět do úložiště. Toto nastavení se může vztahovat pouze na asynchronní operace. Tento časový rozsah lze zakázat pomocí [InfiniteTimeSpan](/dotnet/api/system.threading.timeout.infinitetimespan). Výchozí hodnota je 1 minuta. |

Relace používá soubor cookie ke sledování a identifikaci požadavků z jednoho prohlížeče. Ve výchozím nastavení je `.AspNetCore.Session`tento soubor cookie `/`pojmenován a používá cestu aplikace . Vzhledem k tomu, že výchozí soubor cookie neurčuje doménu, není k dispozici skriptu na `true`straně klienta na stránce (protože [httponly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) výchozí).

Chcete-li přepsat výchozí nastavení `SessionOptions`relace souborů cookie, použijte :

[!code-csharp[](app-state/samples_snapshot/2.x/SessionSample/Startup.cs?name=snippet1&highlight=14-19)]

Aplikace používá [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) vlastnost určit, jak dlouho relace může být nečinný před jeho obsah v mezipaměti serveru jsou opuštěny. Tato vlastnost je nezávislá na vypršení platnosti souboru cookie. Každý požadavek, který prochází [Middleware relace](/dotnet/api/microsoft.aspnetcore.session.sessionmiddleware) obnoví časový výtok.

Stav relace *je bez zamykání*. Pokud se dva požadavky současně pokusí změnit obsah relace, poslední požadavek přepíše první. `Session`je implementována jako *souvislé zasedání*, což znamená, že veškerý obsah je uložen společně. Pokud se dva požadavky snaží změnit různé hodnoty relace, může poslední požadavek přepsat změny relace provedené prvním.

### <a name="set-and-get-session-values"></a>Nastavení a získání hodnot relace

Stav relace je přístupný z třídy Razor Pages [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) nebo MVC [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller) s [httpcontext.session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session). Tato vlastnost je implementace [ISession.](/dotnet/api/microsoft.aspnetcore.http.isession)

Implementace `ISession` poskytuje několik metod rozšíření pro nastavení a načtení celé číslo a řetězcové hodnoty. Metody rozšíření jsou v oboru názvů [Microsoft.AspNetCore.Http](/dotnet/api/microsoft.aspnetcore.http) (přidat `using Microsoft.AspNetCore.Http;` příkaz pro získání přístupu k metodám rozšíření) při [microsoft.aspNetCore.Http.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) balíček odkazuje na projekt. Oba balíčky jsou součástí [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

`ISession`metody rozšíření:

* [Get (ISession, Řetězec)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.get)
* [GetInt32(ISession, Řetězec)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getint32)
* [GetString (iSession, řetězec)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getstring)
* [SetInt32(ISession, Řetězec, Int32)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setint32)
* [SetString(iSession, Řetězec, Řetězec)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setstring)

Následující příklad načte hodnotu `IndexModel.SessionKeyName` relace`_Name` pro klíč (v ukázkové aplikaci) na stránce Razor Pages:

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

Následující příklad ukazuje, jak nastavit a získat celé číslo a řetězec:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Všechna data relace musí být serializována, aby byl povolen scénář distribuované mezipaměti, a to i při použití mezipaměti v paměti. Řetězec a celé číslo serializátory jsou poskytovány metody rozšíření [ISession](/dotnet/api/microsoft.aspnetcore.http.isession)). Komplexní typy musí být serializovány uživatelem pomocí jiného mechanismu, jako je například JSON.

Přidejte následující metody rozšíření pro nastavení a získání serializovatelných objektů:

[!code-csharp[](app-state/samples/2.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

Následující příklad ukazuje, jak nastavit a získat serializovatelný objekt s metodami rozšíření:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core zpřístupňuje Razor Pages <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) nebo Controller . Tato vlastnost ukládá data, dokud se číst v jiném požadavku. [Metody Keep(String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) a [Peek(string)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) lze použít ke kontrole dat bez odstranění na konci požadavku. [Keep()](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) označí všechny položky ve slovníku pro uchovávání. `TempData`je zvláště užitečné pro přesměrování, pokud jsou data požadována pro více než jeden požadavek. `TempData`implementována `TempData` poskytovateli, kteří používají soubory cookie nebo stav relace.

## <a name="tempdata-samples"></a>Ukázky TempData

Vezměte v úvahu následující stránku, která vytvoří zákazníka:

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

Zobrazí se `TempData["Message"]`na následující stránce :

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

V předchozí značky na konci požadavku `TempData["Message"]` **není** odstraněn, `Peek` protože se používá. Aktualizace zobrazení `TempData["Message"]`stránky .

Následující značky jsou podobné předchozímu kódu, `Keep` ale používají se k zachování dat na konci požadavku:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

Přechod mezi stránkami *IndexPeek* a *IndexKeep* neodstraníte `TempData["Message"]`.

Následující kód `TempData["Message"]`se zobrazí , ale na `TempData["Message"]` konci požadavku, je odstraněn:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>Zprostředkovatelé TempData

Poskytovatel TempData založený na souborech cookie se ve výchozím nastavení používá k ukládání TempData v souborech cookie.

Data cookie jsou šifrována pomocí [iDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector), kódované [Base64UrlTextEncoder](/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder), pak chunked. Vzhledem k tomu, že soubor cookie je blokový, neplatí limit velikosti jednoho souboru cookie, ASP.NET jádra 1.x. Data souborů cookie nejsou komprimována, protože komprese šifrovaných dat může vést k bezpečnostním problémům, jako jsou útoky [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) a [BREACH.](https://wikipedia.org/wiki/BREACH_(security_exploit)) Další informace o zprostředkovateli TempData založeném na souborech cookie naleznete v tématu [CookieTempDataProvider](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.cookietempdataprovider).

### <a name="choose-a-tempdata-provider"></a>Výběr zprostředkovatele TempData

Výběr zprostředkovatele TempData zahrnuje několik aspektů, například:

1. Používá aplikace již stav relace? Pokud ano, pomocí stavu relace Zprostředkovatel TempData nemá žádné další náklady na aplikaci (kromě velikosti dat).
2. Používá aplikace TempData pouze střídmě pro relativně malé množství dat (až 500 bajtů)? Pokud ano, poskytovatel souboru cookie TempData přidá malé náklady na každý požadavek, který nese TempData. Pokud tomu tak není, stav relace TempData zprostředkovatele může být prospěšné, aby se zabránilo round-tripping velké množství dat v každém požadavku, dokud TempData je spotřebována.
3. Běží aplikace v serverové farmě na více serverech? Pokud ano, není nutná žádná další konfigurace pro použití poskytovatele souboru <xref:security/data-protection/introduction> cookie TempData mimo ochranu dat (viz a [zprostředkovatelé úložiště klíčů).](xref:security/data-protection/implementation/key-storage-providers)

> [!NOTE]
> Většina webových klientů (například webových prohlížečů) vynucuje omezení maximální velikosti každého souboru cookie, celkového počtu souborů cookie nebo obojího. Při použití poskytovatele souboru cookie TempData ověřte, že aplikace tyto limity nepřekročí. Zvažte celkovou velikost dat. Zohledňuje zvýšení velikosti souborů cookie v důsledku šifrování a bloků.

### <a name="configure-the-tempdata-provider"></a>Konfigurace zprostředkovatele TempData

Poskytovatel TempData založený na souborech cookie je ve výchozím nastavení povolen.

Chcete-li povolit zprostředkovatele TempData založeného na relaci, použijte metodu rozšíření [AddSessionStateTempDataProvider:](/dotnet/api/microsoft.extensions.dependencyinjection.mvcviewfeaturesmvcbuilderextensions.addsessionstatetempdataprovider)

[!code-csharp[](app-state/samples_snapshot_2/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13,32)]

Pořadí middleware je důležité. V předchozím příkladu `InvalidOperationException` dojde k `UseSession` výjimce, `UseMvc`když je vyvolána po . Další informace naleznete v tématu [Middleware Ordering](xref:fundamentals/middleware/index#order).

> [!IMPORTANT]
> Pokud cílíte na rozhraní .NET Framework a používáte zprostředkovatele TempData založeného na relacích, přidejte do projektu balíček [Microsoft.AspNetCore.Session.](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/)

## <a name="query-strings"></a>Řetězce dotazů

Omezené množství dat lze předat z jednoho požadavku do druhého přidáním do řetězce dotazu nového požadavku. To je užitečné pro zachycení stavu trvalým způsobem, který umožňuje sdílení odkazů s vloženým stavem prostřednictvím e-mailu nebo sociálních sítí. Vzhledem k tomu, že řetězce dotazů URL jsou veřejné, nikdy nepoužívejte řetězce dotazu pro citlivá data.

Kromě neúmyslného sdílení může zahrnutí dat v řetězcích dotazů vytvářet příležitosti pro útoky [csrf (Cross-Site Request Forgery),](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) které mohou uživatele přimět k návštěvě škodlivých webů při ověřování. Útočníci pak mohou ukrást uživatelská data z aplikace nebo provést škodlivé akce jménem uživatele. Všechny zachované aplikace nebo stav relace musí chránit před útoky CSRF. Další informace naleznete [v tématu Zabránění útokům na vyžádání mezi servery (XSRF/CSRF).](xref:security/anti-request-forgery)

## <a name="hidden-fields"></a>Skrytá pole

Data mohou být uložena ve skrytých polích formuláře a odeslána zpět na další požadavek. To je běžné ve vícestránkových formulářích. Vzhledem k tomu, že klient může potenciálně manipulovat s daty, aplikace musí vždy znovu ověřit data uložená ve skrytých polích.

## <a name="httpcontextitems"></a>HttpContext.Položky

Kolekce [HttpContext.Items](/dotnet/api/microsoft.aspnetcore.http.httpcontext.items) se používá k ukládání dat při zpracování jednoho požadavku. Obsah kolekce jsou zahozeny po zpracování požadavku. Kolekce `Items` se často používá k povolení komponenty nebo middleware komunikovat, když pracují v různých bodech v čase během požadavku a nemají žádný přímý způsob, jak předat parametry.

V následujícím příkladu [middleware](xref:fundamentals/middleware/index) `isVerified` `Items` přidá do kolekce.

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

Později v potrubí, další middleware `isVerified`přístup k hodnotě :

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync($"Verified: {context.Items["isVerified"]}");
});
```

Pro middleware, který používá pouze jedna `string` aplikace, jsou klíče přijatelné. Middleware sdílený mezi instancemi aplikací by měl používat jedinečné klíče objektů, aby se zabránilo kolizím klíčů. Následující příklad ukazuje, jak používat jedinečný klíč objektu definovaný ve třídě middleware:

[!code-csharp[](app-state/samples/2.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Jiný kód může přistupovat `HttpContext.Items` k hodnotě uložené v použití klíče vystaveného třídou middleware:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Tento přístup má také tu výhodu, že eliminuje použití řetězce klíčů v kódu.

## <a name="cache"></a>Mezipaměť

Ukládání do mezipaměti je efektivní způsob ukládání a načítání dat. Aplikace může řídit životnost položek uložených v mezipaměti.

Data uložená v mezipaměti nejsou přidružena k určitému požadavku, uživateli nebo relaci. **Dávejte pozor, abyste do mezipaměti data specifická pro uživatele, které mohou být načteny požadavky jiných uživatelů.**

Další informace naleznete v tématu <xref:performance/caching/response>.

## <a name="dependency-injection"></a>Injektáž závislostí

Použití [vkládání závislostí](xref:fundamentals/dependency-injection) zpřístupnit data všem uživatelům:

1. Definujte službu obsahující data. Například třída s `MyAppData` názvem je definována:

    ```csharp
    public class MyAppData
    {
        // Declare properties and methods
    }
    ```

2. Přidejte třídu `Startup.ConfigureServices`služby do :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<MyAppData>();
    }
    ```

3. Spotřebovávat třídu datové služby:

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

* "Nelze vyřešit službu pro typ Microsoft.Extensions.Caching.Distributed.IDistributedCache při pokusu o aktivaci souboru Microsoft.AspNetCore.Session.DistributedSessionStore."

  To je obvykle způsobeno tím, `IDistributedCache` že nenakonfigurujete alespoň jednu implementaci. Další informace naleznete v tématech <xref:performance/caching/distributed> a <xref:performance/caching/memory>.

* V případě, že middleware relace nepodaří zachovat relaci (například pokud záložní úložiště není k dispozici), middleware protokoluje výjimku a požadavek pokračuje normálně. To vede k nepředvídatelnému chování.

  Uživatel například ukládá nákupní košík v relaci. Uživatel přidá položku do košíku, ale potvrzení se nezdaří. Aplikace neví o selhání, takže uživateli oznámí, že položka byla přidána do košíku, což není pravda.

  Doporučený přístup ke kontrole chyb `await feature.Session.CommitAsync();` je volání z kódu aplikace, když se aplikace provádí zápis do relace. `CommitAsync`vyvolá výjimku, pokud záložní úložiště není k dispozici. Pokud `CommitAsync` se nezdaří, aplikace může zpracovat výjimku. `LoadAsync`vyvolá za stejných podmínek, kde úložiště dat není k dispozici.
  
## <a name="opno-locsignalr-and-session-state"></a>SignalRa stav relace

SignalRaplikace by neměly používat stav relace k ukládání informací. SignalRaplikace mohou ukládat `Context.Items` pro jeden stav připojení v rozbočovači. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Další zdroje

<xref:host-and-deploy/web-farm>
::: moniker-end
