---
title: Relace v ASP.NET Core
author: rick-anderson
description: Objevte přístupy k zachování relace mezi požadavky.
ms.author: riande
ms.custom: mvc
ms.date: 03/06/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/app-state
ms.openlocfilehash: c05129c0f239fb28c83ab1c561dd910305eeb54b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017633"
---
# <a name="session-and-state-management-in-aspnet-core"></a>Správa relací a stavu v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirka Larkin](https://twitter.com/serpent5)a [Diana LaRose](https://github.com/DianaLaRose)

HTTP je bezstavový protokol. Ve výchozím nastavení jsou požadavky HTTP nezávislé zprávy, které neuchovávají hodnoty uživatele. Tento článek popisuje několik přístupů k zachování uživatelských dat mezi požadavky.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Řízení stavu

Stav lze uložit pomocí několika přístupů. Jednotlivé metody jsou popsány dále v tomto tématu.

| Přístup k úložišti | Mechanismus úložiště |
| ---------------- | ----------------- |
| [Cookiepracují](#cookies) | HTTP cookie s. Může zahrnovat data uložená pomocí kódu aplikace na straně serveru. |
| [Stav relace](#session-state) | HTTP cookie s a kód aplikace na straně serveru |
| [TempData](#tempdata) | HTTP cookie s nebo stav relace |
| [Řetězce dotazů](#query-strings) | Řetězce dotazů HTTP |
| [Skrytá pole](#hidden-fields) | Pole formuláře HTTP |
| [HttpContext. Items](#httpcontextitems) | Kód aplikace na straně serveru |
| [Cache](#cache) | Kód aplikace na straně serveru |

## <a name="no-loccookies"></a>Cookiepracují

Cookies ukládají data napříč požadavky. Vzhledem k tomu cookie , že s jsou odesílány s každou žádostí, jejich velikost musí být udržována minimálně. V ideálním případě by měl být pouze identifikátor uložen v cookie s daty uloženými aplikací. Většina prohlížečů omezuje cookie Velikost na 4096 bajtů. cookiePro každou doménu je k dispozici pouze omezený počet s.

Vzhledem cookie k tomu, že s podléhají manipulaci, musí je aplikace ověřit. Cookies můžou je odstranit uživatelé a vypršet na klientech. Nicméně cookie jsou všeobecně odolnější forma trvalosti dat na klientovi.

Cookies se často používají k přizpůsobení, kde je obsah přizpůsoben pro známého uživatele. Uživatel se ve většině případů identifikuje a není ověřený. cookieMůže ukládat jméno uživatele, název účtu nebo jedinečné ID uživatele, jako je například identifikátor GUID. cookieDá se použít pro přístup k individuálnímu nastavení uživatele, jako je například jeho preferovaná barva pozadí webu.

Přečtěte si [Obecné nařízení o ochraně dat (GDPR) Evropské unie](https://ec.europa.eu/info/law/law-topic/data-protection) při vystavování cookie s a zabývat se zájmy ochrany osobních údajů. Další informace najdete v tématu [podpora obecné nařízení o ochraně osobních údajů (GDPR) v ASP.NET Core](xref:security/gdpr).

## <a name="session-state"></a>Stav relace

Stav relace je ASP.NET Core scénář pro ukládání uživatelských dat, když uživatel prochází webovou aplikaci. Stav relace používá úložiště udržované aplikací k zachování dat napříč požadavky klienta. Data relace jsou zálohovaná mezipamětí a považují se za data, která jsou považována za dočasné. Web by měl i nadále fungovat bez dat relace. Kritická data aplikace by měla být uložena v uživatelské databázi a v mezipaměti v rámci relace pouze jako optimalizace výkonu.

Relace není v aplikacích podporovaná, [SignalR](xref:signalr/index) protože [ SignalR rozbočovač](xref:signalr/hubs) se může spustit nezávisle na kontextu http. K tomu může dojít například v případě, že je požadavek dlouhého cyklického dotazování otevřený centrem po dobu životnosti kontextu HTTP požadavku.

ASP.NET Core udržuje stav relace tím cookie , že poskytuje klientovi, který obsahuje ID relace. cookieID relace:

* Se do aplikace pošle s každým požadavkem.
* Používá aplikace k načtení dat relace.

Stav relace vykazuje následující chování:

* Relace cookie je specifická pro prohlížeč. Relace nejsou sdíleny mezi prohlížeči.
* Relace cookie s se odstraní, když se ukončí relace prohlížeče.
* Pokud cookie je přijata pro relaci s vypršenou platností, vytvoří se nová relace, která bude používat stejnou relaci cookie .
* Prázdné relace nejsou zachovány. Relace musí mít alespoň jednu nastavenou hodnotu pro zachování relace napříč požadavky. Pokud se relace nezachová, vygeneruje se pro každý nový požadavek nové ID relace.
* Aplikace po poslední žádosti zachovává relaci po určitou dobu. Aplikace buď nastaví časový limit relace, nebo použije výchozí hodnotu 20 minut. Stav relace je ideální pro ukládání uživatelských dat:
  * To je specifické pro konkrétní relaci.
  * Kde data nevyžadují trvalé úložiště napříč relacemi.
* Data relace se odstraní buď při <xref:Microsoft.AspNetCore.Http.ISession.Clear%2A?displayProperty=nameWithType> volání implementace, nebo po vypršení platnosti relace.
* Neexistuje žádný výchozí mechanismus pro informování kódu aplikace, že se zavřel klientský prohlížeč nebo když je relace cookie na klientovi Odstraněná nebo vypršela její platnost.
* Stav relace cookie s není ve výchozím nastavení označen jako základní. Stav relace není funkční, pokud návštěvník lokality nepovoluje sledování. Další informace naleznete v tématu <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Neukládejte citlivá data do stavu relace. Uživatel pravděpodobně neukončí prohlížeč a vymaže relaci cookie . Některé prohlížeče udržují platnou relaci cookie v oknech prohlížeče. Relace nemusí být omezena na jednoho uživatele. Další uživatel může pokračovat v procházení aplikace se stejnou relací cookie .

Poskytovatel mezipaměti v paměti ukládá data relace do paměti serveru, kde se aplikace nachází. Ve scénáři serverové farmy:

* Pomocí *rychlých relací* spojíte každou relaci s konkrétní instancí aplikace na samostatném serveru. [Azure App Service](https://azure.microsoft.com/services/app-service/) využívá [Směrování žádostí aplikace (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) k vykonání rychlých relací ve výchozím nastavení. Rychlé relace ale můžou ovlivnit škálovatelnost a zkomplikovat aktualizace webových aplikací. Lepším řešením je použití distribuované mezipaměti Redis nebo SQL Server, která nevyžaduje relace v rychlém provozu. Další informace naleznete v tématu <xref:performance/caching/distributed>.
* Relace cookie je šifrována prostřednictvím <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> . Ochrana dat musí být správně nakonfigurovaná tak, aby na každém počítači přečetla relace cookie s. Další informace najdete v tématech <xref:security/data-protection/introduction> a [poskytovatelé úložiště klíčů](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Konfigurovat stav relace

Balíček [Microsoft. AspNetCore. Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) :

* Je implicitně obsaženo v rozhraní.
* Poskytuje middleware pro správu stavu relace.

Chcete-li povolit middleware relace, `Startup` musí obsahovat:

* Libovolná <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> mezipaměť paměti. `IDistributedCache`Implementace se používá jako záložní úložiště pro relaci. Další informace naleznete v tématu <xref:performance/caching/distributed>.
* Volání <xref:Microsoft.Extensions.DependencyInjection.SessionServiceCollectionExtensions.AddSession%2A> v `ConfigureServices` .
* Volání <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A> v `Configure` .

Následující kód ukazuje, jak nastavit zprostředkovatele relace v paměti s výchozí implementací v paměti `IDistributedCache` :

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup4.cs?name=snippet1&highlight=12-19,45)]

Předchozí kód nastaví krátký časový limit pro zjednodušení testování.

Důležité je pořadí middlewaru.  Zavolejte `UseSession` za `UseRouting` a před `UseEndpoints` . Viz [řazení middlewaru](xref:fundamentals/middleware/index#order).

[HttpContext. Session](xref:Microsoft.AspNetCore.Http.HttpContext.Session) je k dispozici po nakonfigurování stavu relace.

`HttpContext.Session`k ní nelze přistupovat před `UseSession` voláním.

Novou relaci s novou relací cookie nelze vytvořit poté, co aplikace začne zapisovat do datového proudu odpovědí. Výjimka se zaznamená do protokolu webového serveru a nezobrazuje se v prohlížeči.

### <a name="load-session-state-asynchronously"></a>Asynchronní načtení stavu relace

Výchozí zprostředkovatel relací v ASP.NET Core načítá záznamy relací z podkladového <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> záložního úložiště asynchronně pouze v případě <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync%2A?displayProperty=nameWithType> , že je metoda explicitně volána <xref:Microsoft.AspNetCore.Http.ISession.TryGetValue%2A> před <xref:Microsoft.AspNetCore.Http.ISession.Set%2A> <xref:Microsoft.AspNetCore.Http.ISession.Remove%2A> metodami, nebo. Pokud se `LoadAsync` nevolá jako první, načte se příslušný záznam relace synchronně, což může způsobit škálování na výkon.

Aby aplikace vynutily tento model, zabalte <xref:Microsoft.AspNetCore.Session.DistributedSessionStore> a <xref:Microsoft.AspNetCore.Session.DistributedSession> implementují verze, které vyvolávají výjimku, pokud `LoadAsync` metoda není volána před `TryGetValue` , `Set` nebo `Remove` . Zabalené verze zaregistrujte do kontejneru služby.

### <a name="session-options"></a>Možnosti relace

K přepsání výchozích hodnot relace použijte <xref:Microsoft.AspNetCore.Builder.SessionOptions> .

| Možnost | Popis |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie> | Určuje nastavení použité k vytvoření cookie . <xref:Microsoft.AspNetCore.Http.CookieBuilder.Name>Výchozí hodnota je <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookieName?displayProperty=nameWithType> ( `.AspNetCore.Session` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.Path>Výchozí hodnota je <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookiePath?displayProperty=nameWithType> ( `/` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite>Výchozí hodnota je <xref:Microsoft.AspNetCore.Http.SameSiteMode.Lax?displayProperty=nameWithType> ( `1` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly>Výchozí hodnota je `true` . <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential>Výchozí hodnota je `false` . |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> | `IdleTimeout`Určuje, jak dlouho může být relace nečinná, než dojde k opuštění jejího obsahu. Při každém přístupu k relaci se obnoví časový limit. Toto nastavení se vztahuje pouze na obsah relace, nikoli na cookie . Výchozí nastavení je 20 minut. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IOTimeout> | Maximální doba, po kterou je možné načíst relaci z úložiště nebo ji zapsat zpět do úložiště. Toto nastavení se může vztahovat jenom na asynchronní operace. Tento časový limit se dá zakázat pomocí <xref:System.Threading.Timeout.InfiniteTimeSpan> . Výchozí hodnota je 1 minuta. |

Relace používá cookie ke sledování a identifikaci požadavků z jednoho prohlížeče. Ve výchozím nastavení se jedná o cookie název `.AspNetCore.Session` a používá cestu k `/` . Vzhledem k tomu cookie , že výchozí hodnota neurčuje doménu, není k dispozici pro skript na straně klienta na stránce (protože se jedná o <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> výchozí nastavení `true` ).

Pokud chcete přepsat cookie výchozí nastavení relace, použijte <xref:Microsoft.AspNetCore.Builder.SessionOptions> :

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup2.cs?name=snippet1&highlight=5-10)]

Tato aplikace používá <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> vlastnost k určení, jak dlouho může být relace nečinná, než dojde k opuštění jejího obsahu v mezipaměti serveru. Tato vlastnost je nezávislá na cookie vypršení platnosti. Každý požadavek, který projde [middlewarem relace](xref:Microsoft.AspNetCore.Session.SessionMiddleware) , obnoví časový limit.

Stav relace není *zamknutý*. Pokud se dvě požadavky současně pokoušejí změnit obsah relace, poslední požadavek přepíše první. `Session`je implementován jako *souvislá relace*, což znamená, že veškerý obsah je uložen společně. Když se dvě žádosti snaží změnit jiné hodnoty relace, může poslední požadavek přepsat změny relace provedené prvním.

### <a name="set-and-get-session-values"></a>Nastavení a získání hodnot relace

Stav relace je k dispozici Razor z <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> třídy Pages nebo <xref:Microsoft.AspNetCore.Mvc.Controller> třídy MVC s <xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType> . Tato vlastnost je <xref:Microsoft.AspNetCore.Http.ISession> implementace.

`ISession`Implementace poskytuje několik metod rozšíření pro nastavení a načtení hodnot typu Integer a String. Rozšiřující metody jsou v <xref:Microsoft.AspNetCore.Http> oboru názvů.

`ISession`metody rozšíření:

* [Get (ISession; String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.Get%2A)
* [GetInt32 (ISession; String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetInt32%2A)
* [GetString (ISession; String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetString%2A)
* [Setint32 – (ISession; String; Int32)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetInt32%2A)
* [SetString (ISession; String; String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetString%2A)

Následující příklad načte hodnotu relace pro `IndexModel.SessionKeyName` klíč ( `_Name` v ukázkové aplikaci) na Razor stránce Pages:

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

Následující příklad ukazuje, jak nastavit a získat celé číslo a řetězec:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Všechna data relace musí být serializována, aby bylo možné povolit scénář distribuované mezipaměti, i když používáte mezipaměť v paměti. Řetězcové a celočíselné serializace jsou poskytovány metodami rozšíření <xref:Microsoft.AspNetCore.Http.ISession> . Komplexní typy musí být serializovány uživatelem pomocí jiného mechanismu, jako je například JSON.

K serializaci objektů použijte následující vzorový kód:

[!code-csharp[](app-state/samples/3.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

Následující příklad ukazuje, jak nastavit a získat serializovatelný objekt s `SessionExtensions` třídou:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core zpřístupňuje Razor stránky [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) nebo Controller <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> . Tato vlastnost ukládá data, dokud je nepřečetla v jiné žádosti. Metody [Keep (String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) a [prohlížet (String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) lze použít k prohlédnutí dat bez odstranění na konci žádosti. [Uchová](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) označení všech položek ve slovníku pro uchování. `TempData`dojde

* Hodí se pro přesměrování, pokud se vyžadují data pro více než jeden požadavek.
* Implementované `TempData` poskytovateli pomocí cookie nebo stav relace.

## <a name="tempdata-samples"></a>Ukázky TempData

Vezměte v úvahu následující stránku, která vytvoří zákazníka:

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

Zobrazí se následující stránka `TempData["Message"]` :

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

V předchozím kódu se na konci požadavku `TempData["Message"]` neodstraní, protože **not** `Peek` se používá. Aktualizace stránky zobrazuje obsah `TempData["Message"]` .

Následující kód je podobný předchozímu kódu, ale používá `Keep` se k zachování dat na konci požadavku:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

Navigace mezi stránkami *IndexPeek* a *IndexKeep* se neodstraní `TempData["Message"]` .

Následující kód se zobrazí `TempData["Message"]` , ale na konci žádosti `TempData["Message"]` se odstraní:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>TempData poskytovatelé

cookieZprostředkovatel TempData založený na bázi se ve výchozím nastavení používá k ukládání TempData do cookie s.

cookieData se šifrují pomocí <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> kódování a pak jsou v <xref:Microsoft.AspNetCore.WebUtilities.Base64UrlTextEncoder> bloku. Maximální cookie velikost je menší než [4096 bajtů](http://www.faqs.org/rfcs/rfc2965.html) z důvodu šifrování a bloků dat. cookieData nejsou komprimována, protože komprimace šifrovaných dat může vést k problémům se zabezpečením, jako jsou například útoky na [trestné činy](https://wikipedia.org/wiki/CRIME_(security_exploit)) a [porušení](https://wikipedia.org/wiki/BREACH_(security_exploit)) . Další informace o cookie poskytovateli TempData založeném na službě najdete v tématu <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider> .

### <a name="choose-a-tempdata-provider"></a>Zvolit poskytovatele TempData

Výběr poskytovatele TempData zahrnuje několik předpokladů, například:

* Používá aplikace již stav relace? V takovém případě použití poskytovatele TempData stavu relace nemá žádné další náklady na aplikaci nad rámec velikosti dat.
* Používá aplikace TempData jenom poměrně malé objemy dat, až 500 bajtů? Pokud ano, cookie poskytovatel TempData přidá malé náklady na každý požadavek, který přenese TempData. V takovém případě může poskytovatel TempData stavu relace vyhýbat se tomu, aby v každém požadavku Trip velké množství dat, dokud se TempData nespotřebovává.
* Běží aplikace v serverové farmě na více serverech? Pokud ano, není nutná žádná další konfigurace, která by používala cookie poskytovatele TempData mimo ochranu dat (viz <xref:security/data-protection/introduction> a [poskytovatelé úložiště klíčů](xref:security/data-protection/implementation/key-storage-providers)).

Většina webových klientů, jako jsou webové prohlížeče, vynutila omezení pro maximální velikost každého cookie a celkový počet cookie s. Při použití cookie poskytovatele TempData ověřte, že aplikace nepřekračuje [Tato omezení](http://www.faqs.org/rfcs/rfc2965.html). Vezměte v úvahu celkovou velikost dat. Účet pro zvýšení cookie velikosti v důsledku šifrování a bloků dat.

### <a name="configure-the-tempdata-provider"></a>Konfigurace poskytovatele TempData

cookieVe výchozím nastavení je povolený zprostředkovatel TempData na bázi.

Chcete-li povolit zprostředkovatele TempData založeného na relaci, použijte <xref:Microsoft.Extensions.DependencyInjection.MvcViewFeaturesMvcBuilderExtensions.AddSessionStateTempDataProvider%2A> metodu rozšíření. Je požadováno pouze jedno volání `AddSessionStateTempDataProvider` :

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup3.cs?name=snippet1&highlight=4,6,8,30)]

## <a name="query-strings"></a>Řetězce dotazů

Omezené množství dat lze předat z jedné žádosti do druhé tím, že ji přidáte do řetězce dotazu nové žádosti. To je užitečné pro zachycení stavu trvalým způsobem, který umožňuje sdílet odkazy s vloženým stavem prostřednictvím e-mailu nebo sociálních sítí. Vzhledem k tomu, že řetězce dotazu URL jsou veřejné, nikdy nepoužívejte řetězce dotazů pro citlivá data.

Kromě nezamýšleného sdílení, včetně dat v řetězcích dotazů, může aplikace vystavit útokům prostřednictvím [CSRF (mezi lokalitami)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) . Všechny zachované stavy relací musí chránit před CSRF útoky. Další informace najdete v tématu [prevence útoků proti falšování (XSRF/CSRF) žádostí mezi weby](xref:security/anti-request-forgery).

## <a name="hidden-fields"></a>Skrytá pole

Data je možné uložit do skrytých polí formuláře a pak je odeslat zpět na další žádost. To je běžné ve vícestránkových formulářích. Vzhledem k tomu, že klient může potenciálně manipulovat s daty, aplikace musí vždy znovu ověřit data uložená ve skrytých polích.

## <a name="httpcontextitems"></a>HttpContext. Items

<xref:Microsoft.AspNetCore.Http.HttpContext.Items?displayProperty=nameWithType>Kolekce se používá k ukládání dat při zpracování jediné žádosti. Obsah kolekce se po zpracování žádosti zahodí. `Items`Kolekce se často používá k tomu, aby komponenty nebo middleware komunikovaly, když pracují v různých časových okamžicích během žádosti a nemají přímý způsob předávání parametrů.

V následujícím příkladu [middleware](xref:fundamentals/middleware/index) přidá `isVerified` do `Items` kolekce:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup.cs?name=snippet1)]

Pro middleware, které se používají jenom v jedné aplikaci, `string` jsou přijatelné i pevné klíče. Middleware sdílené mezi aplikacemi by měly používat jedinečné klíče objektů, aby se předešlo kolizi klíčů. Následující příklad ukazuje, jak použít jedinečný klíč objektu definovaný ve třídě middleware:

[!code-csharp[](app-state/samples/3.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Další kód může přistupovat k hodnotě uložené v `HttpContext.Items` použití klíče vystaveného pomocí třídy middleware:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Tento přístup má také výhodu eliminace použití řetězců klíčů v kódu.

## <a name="cache"></a>Mezipaměť

Ukládání dat do mezipaměti je účinný způsob, jak je ukládat a načítat. Aplikace může řídit dobu života položek v mezipaměti. Další informace naleznete v tématu <xref:performance/caching/response>.

Data uložená v mezipaměti nejsou přidružena ke konkrétnímu požadavku, uživateli nebo relaci. **Neukládat do mezipaměti data specifická pro uživatele, která mohou být načtena jinými požadavky uživatele.**

Chcete-li ukládat do mezipaměti data napříč aplikacemi, přečtěte si téma <xref:performance/caching/memory> .

## <a name="common-errors"></a>Běžné chyby

* Při pokusu o aktivaci Microsoft. AspNetCore. Session. DistributedSessionStore se nepovedlo přeložit službu pro typ Microsoft. Extensions. Caching. Distributed. IDistributedCache.

  To je obvykle způsobeno selháním konfigurace alespoň jedné `IDistributedCache` implementace. Další informace naleznete v tématech <xref:performance/caching/distributed> a <xref:performance/caching/memory>.

Pokud middleware relace nemůže uchovat relaci:

* Middleware zaznamená výjimku a požadavek bude normálně pokračovat.
* To vede k nepředvídatelnému chování.

Middleware relace může selhat při zachování relace, pokud není k dispozici záložní úložiště. Uživatel například ukládá v relaci nákupní košík. Uživatel přidá položku na košík, ale potvrzení se nezdařilo. Aplikace neví o selhání, takže oznamuje uživateli, že se položka přidala do svého košíku, což není pravda.

Doporučený postup pro kontrolu chyb je zavolat, `await feature.Session.CommitAsync` Jakmile se aplikace dokončí zápisem do relace. <xref:Microsoft.AspNetCore.Http.ISession.CommitAsync*>vyvolá výjimku, pokud není záložní úložiště k dispozici. `CommitAsync`V případě chyby může aplikace zpracovat výjimku. <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync*>Vyvolá se za stejných podmínek, když úložiště dat není k dispozici.
  
## <a name="no-locsignalr-and-session-state"></a>SignalRa stav relace

SignalRaplikace by neměly pro ukládání informací používat stav relace. SignalRaplikace se můžou ukládat na stav připojení v `Context.Items` centru. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Další zdroje

<xref:host-and-deploy/web-farm>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), [Diana LaRose](https://github.com/DianaLaRose)a [Luke Latham](https://github.com/guardrex)

HTTP je bezstavový protokol. Bez provedení dalších kroků jsou požadavky HTTP nezávislé zprávy, které neuchovávají uživatelské hodnoty nebo stav aplikace. Tento článek popisuje několik přístupů k zachování uživatelských dat a stavu aplikace mezi požadavky.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Řízení stavu

Stav lze uložit pomocí několika přístupů. Jednotlivé metody jsou popsány dále v tomto tématu.

| Přístup k úložišti | Mechanismus úložiště |
| ---------------- | ----------------- |
| [Cookiepracují](#cookies) | HTTP cookie s (může zahrnovat data uložená pomocí kódu aplikace na straně serveru) |
| [Stav relace](#session-state) | HTTP cookie s a kód aplikace na straně serveru |
| [TempData](#tempdata) | HTTP cookie s nebo stav relace |
| [Řetězce dotazů](#query-strings) | Řetězce dotazů HTTP |
| [Skrytá pole](#hidden-fields) | Pole formuláře HTTP |
| [HttpContext. Items](#httpcontextitems) | Kód aplikace na straně serveru |
| [Cache](#cache) | Kód aplikace na straně serveru |
| [Injektáž závislostí](#dependency-injection) | Kód aplikace na straně serveru |

## <a name="no-loccookies"></a>Cookiepracují

Cookies ukládají data napříč požadavky. Vzhledem k tomu cookie , že s jsou odesílány s každou žádostí, jejich velikost musí být udržována minimálně. V ideálním případě by měl být pouze identifikátor uložen v cookie s daty uloženými aplikací. Většina prohlížečů omezuje cookie Velikost na 4096 bajtů. cookiePro každou doménu je k dispozici pouze omezený počet s.

Vzhledem cookie k tomu, že s podléhají manipulaci, musí je aplikace ověřit. Cookies můžou je odstranit uživatelé a vypršet na klientech. Nicméně cookie jsou všeobecně odolnější forma trvalosti dat na klientovi.

Cookies se často používají k přizpůsobení, kde je obsah přizpůsoben pro známého uživatele. Uživatel se ve většině případů identifikuje a není ověřený. cookieMůže ukládat jméno uživatele, název účtu nebo jedinečné ID uživatele (například identifikátor GUID). Pak můžete použít cookie pro přístup k individuálnímu nastavení uživatele, jako je například jeho preferovaná barva pozadí webu.

Nezapomeňte na [obecné předpisy Evropské unie pro ochranu dat (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection) při vystavování cookie s a zabývat se zájmy ochrany osobních údajů. Další informace najdete v tématu [podpora obecné nařízení o ochraně osobních údajů (GDPR) v ASP.NET Core](xref:security/gdpr).

## <a name="session-state"></a>Stav relace

Stav relace je ASP.NET Core scénář pro ukládání uživatelských dat, když uživatel prochází webovou aplikaci. Stav relace používá úložiště udržované aplikací k zachování dat napříč požadavky klienta. Data relace jsou zálohovaná mezipamětí a považují se za nepřesná data &mdash; , která by měla lokalita dál fungovat bez dat relace. Kritická data aplikace by měla být uložena v uživatelské databázi a v mezipaměti v rámci relace pouze jako optimalizace výkonu.

> [!NOTE]
> Relace není v aplikacích podporovaná, [SignalR](xref:signalr/index) protože [ SignalR rozbočovač](xref:signalr/hubs) se může spustit nezávisle na kontextu http. K tomu může dojít například v případě, že je požadavek dlouhého cyklického dotazování otevřený centrem po dobu životnosti kontextu HTTP požadavku.

ASP.NET Core udržuje stav relace tím cookie , že poskytuje klientovi, který obsahuje ID relace, která se pošle do aplikace s každým požadavkem. Aplikace používá ID relace k načtení dat relace.

Stav relace vykazuje následující chování:

* Vzhledem k tomu cookie , že relace je specifická pro prohlížeč, relace se v prohlížečích nesdílí.
* Relace cookie s se odstraní, když se ukončí relace prohlížeče.
* Pokud cookie je přijata pro relaci s vypršenou platností, vytvoří se nová relace, která bude používat stejnou relaci cookie .
* Prázdné relace nejsou zachované. &mdash; relace musí mít nastavenou aspoň jednu hodnotu, aby se relace mezi požadavky zachovala. Pokud se relace nezachová, vygeneruje se pro každý nový požadavek nové ID relace.
* Aplikace po poslední žádosti zachovává relaci po určitou dobu. Aplikace buď nastaví časový limit relace, nebo použije výchozí hodnotu 20 minut. Stav relace je ideální pro ukládání uživatelských dat, která jsou specifická pro konkrétní relaci, ale v případě, že data nevyžadují trvalé úložiště napříč relacemi.
* Data relace se odstraní buď při <xref:Microsoft.AspNetCore.Http.ISession.Clear%2A?displayProperty=nameWithType> volání implementace, nebo po vypršení platnosti relace.
* Neexistuje žádný výchozí mechanismus pro informování kódu aplikace, že se zavřel klientský prohlížeč nebo když je relace cookie na klientovi Odstraněná nebo vypršela její platnost.
* Šablony ASP.NET Core MVC a Razor stránky zahrnují podporu pro obecné nařízení o ochraně osobních údajů (GDPR). Stav relace cookie není ve výchozím nastavení označen jako základní, takže stav relace není funkční, pokud návštěvník lokality nepovoluje sledování. Další informace naleznete v tématu <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Neukládejte citlivá data do stavu relace. Uživatel pravděpodobně neukončí prohlížeč a vymaže relaci cookie . Některé prohlížeče udržují platnou relaci cookie v oknech prohlížeče. Relace nemusí být omezená na jednoho uživatele &mdash; , takže další uživatel může pokračovat v procházení aplikace pomocí stejné relace cookie .

Poskytovatel mezipaměti v paměti ukládá data relace do paměti serveru, kde se aplikace nachází. Ve scénáři serverové farmy:

* Pomocí *rychlých relací* spojíte každou relaci s konkrétní instancí aplikace na samostatném serveru. [Azure App Service](https://azure.microsoft.com/services/app-service/) využívá [Směrování žádostí aplikace (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) k vykonání rychlých relací ve výchozím nastavení. Rychlé relace ale můžou ovlivnit škálovatelnost a zkomplikovat aktualizace webových aplikací. Lepším řešením je použití distribuované mezipaměti Redis nebo SQL Server, která nevyžaduje relace v rychlém provozu. Další informace naleznete v tématu <xref:performance/caching/distributed>.
* Relace cookie je šifrována prostřednictvím <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> . Ochrana dat musí být správně nakonfigurovaná tak, aby na každém počítači přečetla relace cookie s. Další informace najdete v tématech <xref:security/data-protection/introduction> a [poskytovatelé úložiště klíčů](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Konfigurovat stav relace

Balíček [Microsoft. AspNetCore. Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) , který je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), poskytuje middleware pro správu stavu relace. Chcete-li povolit middleware relace, `Startup` musí obsahovat:

* Libovolná <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> mezipaměť paměti. `IDistributedCache`Implementace se používá jako záložní úložiště pro relaci. Další informace naleznete v tématu <xref:performance/caching/distributed>.
* Volání <xref:Microsoft.Extensions.DependencyInjection.SessionServiceCollectionExtensions.AddSession%2A> v `ConfigureServices` .
* Volání <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A> v `Configure` .

Následující kód ukazuje, jak nastavit zprostředkovatele relace v paměti s výchozí implementací v paměti `IDistributedCache` :

[!code-csharp[](app-state/samples/2.x/SessionSample/Startup.cs?name=snippet1&highlight=5-14,34)]

Důležité je pořadí middlewaru. V předchozím příkladu `InvalidOperationException` dojde k výjimce při `UseSession` vyvolání po `UseMvc` . Další informace najdete v tématu [řazení middlewaru](xref:fundamentals/middleware/index#order).

<xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType>je k dispozici po konfiguraci stavu relace.

`HttpContext.Session`k ní nelze přistupovat před `UseSession` voláním.

Novou relaci s novou relací cookie nelze vytvořit poté, co aplikace začne zapisovat do datového proudu odpovědí. Výjimka se zaznamená do protokolu webového serveru a nezobrazuje se v prohlížeči.

### <a name="load-session-state-asynchronously"></a>Asynchronní načtení stavu relace

Výchozí zprostředkovatel relací v ASP.NET Core načítá záznamy relací z podkladového <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> záložního úložiště asynchronně pouze v případě <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync%2A?displayProperty=nameWithType> , že je metoda explicitně volána <xref:Microsoft.AspNetCore.Http.ISession.TryGetValue%2A> před <xref:Microsoft.AspNetCore.Http.ISession.Set%2A> <xref:Microsoft.AspNetCore.Http.ISession.Remove%2A> metodami, nebo. Pokud se `LoadAsync` nevolá jako první, načte se příslušný záznam relace synchronně, což může způsobit škálování na výkon.

Aby aplikace vynutily tento model, zabalte <xref:Microsoft.AspNetCore.Session.DistributedSessionStore> a <xref:Microsoft.AspNetCore.Session.DistributedSession> implementují verze, které vyvolávají výjimku, pokud `LoadAsync` metoda není volána před `TryGetValue` , `Set` nebo `Remove` . Zabalené verze zaregistrujte do kontejneru služby.

### <a name="session-options"></a>Možnosti relace

K přepsání výchozích hodnot relace použijte <xref:Microsoft.AspNetCore.Builder.SessionOptions> .

| Možnost | Popis |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie> | Určuje nastavení použité k vytvoření cookie . <xref:Microsoft.AspNetCore.Http.CookieBuilder.Name>Výchozí hodnota je <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookieName?displayProperty=nameWithType> ( `.AspNetCore.Session` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.Path>Výchozí hodnota je <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookiePath?displayProperty=nameWithType> ( `/` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite>Výchozí hodnota je <xref:Microsoft.AspNetCore.Http.SameSiteMode.Lax?displayProperty=nameWithType> ( `1` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly>Výchozí hodnota je `true` . <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential>Výchozí hodnota je `false` . |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> | `IdleTimeout`Určuje, jak dlouho může být relace nečinná, než dojde k opuštění jejího obsahu. Při každém přístupu k relaci se obnoví časový limit. Toto nastavení se vztahuje pouze na obsah relace, nikoli na cookie . Výchozí nastavení je 20 minut. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IOTimeout> | Maximální doba, po kterou je možné načíst relaci z úložiště nebo ji zapsat zpět do úložiště. Toto nastavení se může vztahovat jenom na asynchronní operace. Tento časový limit se dá zakázat pomocí <xref:System.Threading.Timeout.InfiniteTimeSpan> . Výchozí hodnota je 1 minuta. |

Relace používá cookie ke sledování a identifikaci požadavků z jednoho prohlížeče. Ve výchozím nastavení se jedná o cookie název `.AspNetCore.Session` a používá cestu k `/` . Vzhledem k tomu cookie , že výchozí hodnota neurčuje doménu, není k dispozici pro skript na straně klienta na stránce (protože se jedná o <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> výchozí nastavení `true` ).

Pokud chcete přepsat cookie výchozí nastavení relace, použijte `SessionOptions` :

[!code-csharp[](app-state/samples_snapshot/2.x/SessionSample/Startup.cs?name=snippet1&highlight=14-19)]

Tato aplikace používá <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> vlastnost k určení, jak dlouho může být relace nečinná, než dojde k opuštění jejího obsahu v mezipaměti serveru. Tato vlastnost je nezávislá na cookie vypršení platnosti. Každý požadavek, který projde [middlewarem relace](xref:Microsoft.AspNetCore.Session.SessionMiddleware) , obnoví časový limit.

Stav relace není *zamknutý*. Pokud se dvě požadavky současně pokoušejí změnit obsah relace, poslední požadavek přepíše první. `Session`je implementován jako *souvislá relace*, což znamená, že veškerý obsah je uložen společně. Když se dvě žádosti snaží změnit jiné hodnoty relace, může poslední požadavek přepsat změny relace provedené prvním.

### <a name="set-and-get-session-values"></a>Nastavení a získání hodnot relace

Stav relace je k dispozici Razor z <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> třídy Pages nebo <xref:Microsoft.AspNetCore.Mvc.Controller> třídy MVC s <xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType> . Tato vlastnost je <xref:Microsoft.AspNetCore.Http.ISession> implementace.

`ISession`Implementace poskytuje několik metod rozšíření pro nastavení a načtení hodnot typu Integer a String. Metody rozšíření jsou v <xref:Microsoft.AspNetCore.Http> oboru názvů (přidání `using Microsoft.AspNetCore.Http;` příkazu pro získání přístupu k metodám rozšíření) při odkazování na balíček [Microsoft. AspNetCore. http. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) v projektu. Oba balíčky jsou součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).

`ISession`metody rozšíření:

* [Get (ISession; String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.Get%2A)
* [GetInt32 (ISession; String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetInt32%2A)
* [GetString (ISession; String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetString%2A)
* [Setint32 – (ISession; String; Int32)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetInt32%2A)
* [SetString (ISession; String; String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetString%2A)

Následující příklad načte hodnotu relace pro `IndexModel.SessionKeyName` klíč ( `_Name` v ukázkové aplikaci) na Razor stránce Pages:

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

Následující příklad ukazuje, jak nastavit a získat celé číslo a řetězec:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Všechna data relace musí být serializována, aby bylo možné povolit scénář distribuované mezipaměti, i když používáte mezipaměť v paměti. Řetězcové a celočíselné serializace jsou poskytovány metodami rozšíření <xref:Microsoft.AspNetCore.Http.ISession> ). Komplexní typy musí být serializovány uživatelem pomocí jiného mechanismu, jako je například JSON.

Přidejte následující metody rozšíření pro nastavení a získání serializovatelných objektů:

[!code-csharp[](app-state/samples/2.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

Následující příklad ukazuje, jak nastavit a získat serializovatelný objekt s metodami rozšíření:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core zpřístupňuje Razor stránky [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) nebo Controller <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> . Tato vlastnost ukládá data, dokud je nepřečetla v jiné žádosti. Metody [Keep (String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) a [prohlížet (String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) lze použít k prohlédnutí dat bez odstranění na konci požadavku. [Keep ()](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) označí všechny položky ve slovníku pro uchování. `TempData`je zvláště užitečné pro přesměrování, pokud se vyžadují data pro více než jeden požadavek. `TempData`je implementována `TempData` poskytovateli pomocí cookie nebo stav relace.

## <a name="tempdata-samples"></a>Ukázky TempData

Vezměte v úvahu následující stránku, která vytvoří zákazníka:

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

Zobrazí se následující stránka `TempData["Message"]` :

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

V předchozím kódu se na konci požadavku `TempData["Message"]` neodstraní, protože **not** `Peek` se používá. Aktualizace stránky se zobrazí `TempData["Message"]` .

Následující kód je podobný předchozímu kódu, ale používá `Keep` se k zachování dat na konci požadavku:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

Navigace mezi stránkami *IndexPeek* a *IndexKeep* se neodstraní `TempData["Message"]` .

Následující kód se zobrazí `TempData["Message"]` , ale na konci žádosti `TempData["Message"]` se odstraní:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>TempData poskytovatelé

cookieZprostředkovatel TempData založený na bázi se ve výchozím nastavení používá k ukládání TempData do cookie s.

cookieData se šifrují pomocí <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> kódování a pak jsou v <xref:Microsoft.AspNetCore.WebUtilities.Base64UrlTextEncoder> bloku. Vzhledem k tomu cookie , že je v bloku, cookie omezení jedné velikosti nalezené v ASP.NET Core 1. x se nepoužije. cookieData nejsou komprimována, protože komprimace šifrovaných dat může vést k problémům se zabezpečením, jako jsou například útoky na [trestné činy](https://wikipedia.org/wiki/CRIME_(security_exploit)) a [porušení](https://wikipedia.org/wiki/BREACH_(security_exploit)) . Další informace o cookie poskytovateli TempData založeném na službě najdete v tématu <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider> .

### <a name="choose-a-tempdata-provider"></a>Zvolit poskytovatele TempData

Výběr poskytovatele TempData zahrnuje několik předpokladů, například:

1. Používá aplikace již stav relace? V takovém případě použití poskytovatele TempData stavu relace nemá žádné další náklady na aplikaci (kromě velikosti dat).
2. Používá aplikace TempData jenom poměrně malé objemy dat (až 500 bajtů)? Pokud ano, cookie poskytovatel TempData přidá malé náklady na každý požadavek, který přenese TempData. V takovém případě může poskytovatel TempData stavu relace vyhýbat se tomu, aby v každém požadavku Trip velké množství dat, dokud se TempData nespotřebovává.
3. Běží aplikace v serverové farmě na více serverech? Pokud ano, není nutná žádná další konfigurace, která by používala cookie poskytovatele TempData mimo ochranu dat (viz <xref:security/data-protection/introduction> a [poskytovatelé úložiště klíčů](xref:security/data-protection/implementation/key-storage-providers)).

> [!NOTE]
> Většina webových klientů (například webových prohlížečů) vynutila omezení maximální velikosti každého cookie , celkového počtu cookie s nebo obojího. Při použití cookie poskytovatele TempData ověřte, že aplikace nepřekračuje tato omezení. Vezměte v úvahu celkovou velikost dat. Účet pro zvýšení cookie velikosti v důsledku šifrování a bloků dat.

### <a name="configure-the-tempdata-provider"></a>Konfigurace poskytovatele TempData

cookieVe výchozím nastavení je povolený zprostředkovatel TempData na bázi.

Chcete-li povolit zprostředkovatele TempData založeného na relaci, použijte <xref:Microsoft.Extensions.DependencyInjection.MvcViewFeaturesMvcBuilderExtensions.AddSessionStateTempDataProvider%2A> metodu rozšíření:

[!code-csharp[](app-state/samples_snapshot_2/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13,32)]

Důležité je pořadí middlewaru. V předchozím příkladu `InvalidOperationException` dojde k výjimce při `UseSession` vyvolání po `UseMvc` . Další informace najdete v tématu [řazení middlewaru](xref:fundamentals/middleware/index#order).

> [!IMPORTANT]
> Pokud cílíte .NET Framework a používáte zprostředkovatele TempData založeného na relacích, přidejte do projektu balíček [Microsoft. AspNetCore. Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) .

## <a name="query-strings"></a>Řetězce dotazů

Omezené množství dat lze předat z jedné žádosti do druhé tím, že ji přidáte do řetězce dotazu nové žádosti. To je užitečné pro zachycení stavu trvalým způsobem, který umožňuje sdílet odkazy s vloženým stavem prostřednictvím e-mailu nebo sociálních sítí. Vzhledem k tomu, že řetězce dotazu URL jsou veřejné, nikdy nepoužívejte řetězce dotazů pro citlivá data.

Kromě nezamýšleného sdílení, včetně dat v dotazovacích řetězcích, můžou vytvářet příležitosti pro útoky [CSRF (mezi lokalitami)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) , které můžou uživatelům při ověřování navštěvovat škodlivé weby. Útočníci pak můžou z aplikace ukrást uživatelská data nebo můžou jménem uživatele převzít škodlivé akce. Všechny zachované aplikace nebo stav relace musí chránit před CSRF útoky. Další informace najdete v tématu [prevence útoků proti falšování (XSRF/CSRF) žádostí mezi weby](xref:security/anti-request-forgery).

## <a name="hidden-fields"></a>Skrytá pole

Data je možné uložit do skrytých polí formuláře a pak je odeslat zpět na další žádost. To je běžné ve vícestránkových formulářích. Vzhledem k tomu, že klient může potenciálně manipulovat s daty, aplikace musí vždy znovu ověřit data uložená ve skrytých polích.

## <a name="httpcontextitems"></a>HttpContext. Items

<xref:Microsoft.AspNetCore.Http.HttpContext.Items?displayProperty=nameWithType>Kolekce se používá k ukládání dat při zpracování jediné žádosti. Obsah kolekce se po zpracování žádosti zahodí. `Items`Kolekce se často používá k tomu, aby komponenty nebo middleware komunikovaly, když pracují v různých časových okamžicích během žádosti a nemají přímý způsob předávání parametrů.

V následujícím příkladu [middleware](xref:fundamentals/middleware/index) přidá `isVerified` do `Items` kolekce.

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

Později v kanálu může další middleware získat přístup k hodnotě `isVerified` :

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync($"Verified: {context.Items["isVerified"]}");
});
```

Pro middleware, které používá jediná aplikace, `string` jsou klíče přijatelné. Middleware sdílené mezi instancemi aplikace by měly používat jedinečné klíče objektů, aby se předešlo kolizi klíčů. Následující příklad ukazuje, jak použít jedinečný klíč objektu definovaný ve třídě middleware:

[!code-csharp[](app-state/samples/2.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Další kód může přistupovat k hodnotě uložené v `HttpContext.Items` použití klíče vystaveného pomocí třídy middleware:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Tento přístup má také výhodu eliminace použití řetězců klíčů v kódu.

## <a name="cache"></a>Mezipaměť

Ukládání dat do mezipaměti je účinný způsob, jak je ukládat a načítat. Aplikace může řídit dobu života položek v mezipaměti.

Data uložená v mezipaměti nejsou přidružena ke konkrétnímu požadavku, uživateli nebo relaci. **Dejte pozor, abyste nemuseli ukládat data specifická pro uživatele, která mohou načíst žádosti jiných uživatelů.**

Další informace naleznete v tématu <xref:performance/caching/response>.

## <a name="dependency-injection"></a>Injektáž závislostí

Použití [Injektáže závislosti](xref:fundamentals/dependency-injection) k zpřístupnění dat všem uživatelům:

1. Definujte službu obsahující data. Například třída s názvem `MyAppData` je definována:

    ```csharp
    public class MyAppData
    {
        // Declare properties and methods
    }
    ```

2. Přidejte třídu služby do `Startup.ConfigureServices` :

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

  To je obvykle způsobeno selháním konfigurace alespoň jedné `IDistributedCache` implementace. Další informace naleznete v tématech <xref:performance/caching/distributed> a <xref:performance/caching/memory>.

* V případě, že middleware relace nemůže uchovat relaci (například pokud není k dispozici záložní úložiště), middleware zaznamená výjimku a požadavek bude normálně pokračovat. To vede k nepředvídatelnému chování.

  Uživatel například ukládá v relaci nákupní košík. Uživatel přidá položku na košík, ale potvrzení se nezdařilo. Aplikace neví o selhání, takže oznamuje uživateli, že se položka přidala do svého košíku, což není pravda.

  Doporučený postup pro kontrolu chyb je zavolat `await feature.Session.CommitAsync();` z kódu aplikace, když se aplikace dokončí zápisem do relace. `CommitAsync`vyvolá výjimku, pokud není záložní úložiště k dispozici. `CommitAsync`V případě chyby může aplikace zpracovat výjimku. `LoadAsync`Vyvolá se za stejných podmínek, kdy úložiště dat není k dispozici.
  
## <a name="no-locsignalr-and-session-state"></a>SignalRa stav relace

SignalRaplikace by neměly pro ukládání informací používat stav relace. SignalRaplikace se můžou ukládat na stav připojení v `Context.Items` centru. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Další materiály

<xref:host-and-deploy/web-farm>
::: moniker-end
