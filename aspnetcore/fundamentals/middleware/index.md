---
title: Middleware ASP.NET Core
author: rick-anderson
description: Přečtěte si o ASP.NET Core middlewaru a kanálu požadavků.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/index
ms.openlocfilehash: 32a4e54a46f062f3ff45d0b840237be53406dbdb
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630884"
---
# <a name="aspnet-core-middleware"></a>Middleware ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)

Middleware je software, který se sestaví do kanálu aplikace za účelem zpracování požadavků a odpovědí. Jednotlivé komponenty:

* Zvolí, jestli se má žádost předat další komponentě v kanálu.
* Může provádět práci před a za další komponentou v kanálu.

Delegáti žádostí se používají k sestavení kanálu požadavků. Delegát žádosti zpracuje jednotlivé požadavky HTTP.

Delegáti žádostí jsou nakonfigurováni pomocí <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> metod rozšíření, a. Jednotlivé delegáty žádostí je možné zadat jako anonymní metodu (označovanou v rámci middlewarového middlewaru) nebo ji lze definovat v opakovaně použitelné třídě. Tyto opakovaně použitelné třídy a vložené anonymní metody jsou *middleware*, označované také jako *komponenty middlewaru*. Každá součást middlewaru v kanálu požadavků zodpovídá za vyvolání další komponenty v kanálu nebo při krátkém okruhu kanálu. Když jsou krátkodobé okruhy middleware, nazývá se *middleware terminálu* , protože zabrání dalšímu middlewaru ve zpracování žádosti.

<xref:migration/http-modules> Vysvětluje rozdíl mezi kanály požadavků v ASP.NET Core a ASP.NET 4. x a poskytuje další ukázky middlewaru.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>Vytvoření kanálu middlewaru pomocí IApplicationBuilder

Kanál žádostí o ASP.NET Core se skládá z posloupnosti delegátů požadavků, který se nazývá jedna po druhé. Následující diagram znázorňuje koncept. Vlákno provádění následuje za černými šipkami.

![Požadavek zpracování, který ukazuje, že se dorazí na požadavek, zpracovává se prostřednictvím tří middlewarů a odpověď opouští aplikaci Každý middleware spustí svou logiku a předá požadavek dalšímu middlewaru v příkazu Next (). Poté, co třetí middleware zpracuje požadavek, požadavek předá předchozí dva middleware v obráceném pořadí pro další zpracování po jejich dalších () příkazech před ukončením aplikace jako reakci na klienta.](index/_static/request-delegate-pipeline.png)

Každý delegát může provádět operace před a po dalším delegátu. Delegáty zpracování výjimek by se měly volat v rámci kanálu na začátku, takže můžou zachytit výjimky, ke kterým dochází v pozdějších fázích kanálu.

Nejjednodušší možná ASP.NET Core aplikace nastaví jediného delegáta žádosti, který zpracovává všechny požadavky. Tento případ nezahrnuje skutečný kanál žádostí. Místo toho je volána jedna anonymní funkce v reakci na každý požadavek HTTP.

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

Řetězení více požadavků delegátů společně s <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> . `next`Parametr představuje dalšího delegáta v kanálu. Kanál můžete pro krátké okruhy vymezit tím *, že nevoláte* *Další* parametr. Obvykle můžete provádět akce před i po dalším delegátu, jak ukazuje následující příklad:

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

Když delegát neprojde požadavek na dalšího delegáta, nazývá se to *krátkodobý kanál žádosti*. Krátkodobé okruhy jsou často žádoucí, protože brání zbytečné práci. Například [middleware statických souborů](xref:fundamentals/static-files) může fungovat jako *middleware terminálu* tím, že zpracovává požadavek na statický soubor a krátký okruh zbývajících částí kanálu. Middleware přidané do kanálu předtím, než middleware, který ukončí další zpracování, stále zpracovává kód po svých `next.Invoke` příkazech. Přečtěte si ale následující upozornění týkající se pokusu o zápis do odpovědi, která již byla odeslána.

> [!WARNING]
> Nevolejte `next.Invoke` po odeslání odpovědi klientovi. Změny <xref:Microsoft.AspNetCore.Http.HttpResponse> poté, co odpověď začala, vyvolávají výjimku. Například [nastavení záhlaví a stavový kód vyvolávají výjimku](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started). Zápis do těla odpovědi po volání `next` :
>
> * Může způsobit narušení protokolu. Například zápis více než uvedené `Content-Length` .
> * Může poškodit formát textu. Například zápis zápatí HTML do souboru CSS.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> je užitečnou nápovědou, která označuje, zda byla odeslána hlavička nebo zda byl text napsán do.

<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> Delegáti neobdrží `next` parametr. První `Run` delegát je vždycky terminál a ukončí kanál. `Run` je konvence. Některé komponenty middlewaru můžou vystavovat `Run[Middleware]` metody, které se spouštějí na konci kanálu:

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

V předchozím příkladu `Run` delegát zapisuje `"Hello from 2nd delegate."` do odpovědi a pak kanál ukončí. Pokud `Use` `Run` je po delegátu přidán jiný nebo delegát `Run` , není volán.

<a name="order"></a>

## <a name="middleware-order"></a>Pořadí middlewaru

Následující diagram znázorňuje kompletní kanál zpracování požadavků pro ASP.NET Core aplikace MVC a Razor stránky. Můžete vidět, jak, v typické aplikaci jsou seřazené existující middleware a kdy se přidávají vlastní middleware. Máte plnou kontrolu nad tím, jak změnit pořadí stávajících middlewarů, nebo vložit nové vlastní middleware podle potřeby pro vaše scénáře.

![ASP.NET Core kanál middlewaru](index/_static/middleware-pipeline.svg)

Middleware **koncového bodu** v předchozím diagramu spouští kanál filtru pro odpovídající typ aplikace &mdash; MVC nebo Razor stránky.

![Kanál filtru ASP.NET Core](index/_static/mvc-endpoint.svg)

Pořadí, v jakém jsou komponenty middleware přidány v `Startup.Configure` metodě, definuje pořadí, ve kterém jsou komponenty middleware vyvolány na žádostech a obráceném pořadí pro odpověď. Pořadí je **důležité** pro zabezpečení, výkon a funkčnost.

Následující `Startup.Configure` metoda přidává do doporučeného pořadí součásti middlewaru související se zabezpečením:

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

V předchozím kódu:

* Middleware, které se nepřidaly při vytváření nové webové aplikace s [jednotlivými účty uživatele](xref:security/authentication/identity) , jsou zakomentovány.
* Ne každý middleware potřebuje přejít v tomto přesném pořadí, ale mnoho do něj. Příklad:
  * `UseCors`, `UseAuthentication` a `UseAuthorization` musí jít v uvedeném pořadí.
  * `UseCors` v současné době `UseResponseCaching` se musí před [touto chybou](https://github.com/dotnet/aspnetcore/issues/23218)přecházet.

Následující `Startup.Configure` metoda přidává komponenty middlewaru pro běžné scénáře aplikací:

1. Zpracování výjimek a chyb
   * Když aplikace běží ve vývojovém prostředí:
     * Aplikace middleware stránky s výjimkou vývojářů ( <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> ) hlásí chyby běhového modulu.
     * Zpráva middlewaru chybové stránky databáze oznamuje chyby běhového běhu databáze.
   * Když aplikace běží v produkčním prostředí:
     * Middleware obslužné rutiny výjimek ( <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> ) zachytává výjimky vyvolané v následujících middlewarech.
     * Middleware HSTS (HTTP Strict Transport Security Protocol) ( <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A> ) přidá `Strict-Transport-Security` hlavičku.
1. Middleware () přesměrování protokolu HTTPS ( <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> ) přesměruje požadavky HTTP na https.
1. Soubor middleware () statických souborů ( <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> ) vrací statické soubory a další zpracování žádostí o krátkodobé okruhy.
1. Cookie Middleware zásad ( <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A> ) aplikace v souladu s pravidly pro EU obecné nařízení o ochraně osobních údajů (GDPR).
1. Směrování middleware ( <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> ) pro směrování požadavků.
1. Middleware ověřování ( <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> ) se pokusí ověřit uživatele předtím, než budou mít přístup k zabezpečeným prostředkům.
1. Middleware autorizace ( <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ) opravňuje uživatele k přístupu k zabezpečeným prostředkům.
1. Middleware relace ( <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A> ) vytváří a udržuje stav relace. Pokud aplikace používá stav relace, volejte middleware relace po Cookie middlewaru zásad a před middlewarem MVC.
1. Middleware směrování koncového bodu ( <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> s <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages%2A> ) pro přidání Razor koncových bodů stránek do kanálu požadavků.

<!--

FUTURE UPDATE

On the next topic overhaul/release update, add API crosslink to "Database Error Page Middleware" in Item 1 of the list ...

Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*

... when available via the API docs.

-->

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseRouting();
    app.UseAuthentication();
    app.UseAuthorization();
    app.UseSession();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

V předchozím příkladu kódu je každá metoda rozšíření middleware vystavena <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> prostřednictvím <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> oboru názvů.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> je první součást middleware přidaná do kanálu. Proto middleware obslužné rutiny výjimek zachycuje všechny výjimky, ke kterým dojde v pozdějších voláních.

Middleware statických souborů se zavolá v průběhu kanálu, aby mohl zpracovávat požadavky a krátké okruhy bez toho, aby procházely zbývajícími součástmi. Middleware statických souborů neposkytuje **žádné** autorizační kontroly. Všechny soubory, které poskytuje middleware pro statický soubor, včetně těch v rámci *wwwroot*, jsou veřejně dostupné. Přístup k zabezpečení statických souborů naleznete v tématu <xref:fundamentals/static-files> .

Pokud požadavek nezpracovává middleware pro statický soubor, je předán do middleware ověřování ( <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> ), který provádí ověřování. Ověřování neověřuje neověřené požadavky v krátkém okruhu. I když middleware ověřování ověřuje požadavky, autorizaci (a odmítnutí) proběhne pouze poté, co MVC vybere konkrétní Razor stránku nebo kontroler MVC a akci.

Následující příklad ukazuje pořadí middlewaru, kde požadavky na statické soubory jsou zpracovávány pomocí middlewaru statického souboru před použitím middlewaru pro komprimaci odezvy. Statické soubory nejsou s tímto pořadím middlewaru komprimovány. RazorOdpovědi na stránky lze komprimovat.

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

V případě aplikací s jednou stránkou (jednostránkové) se middleware standardně <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A> nabývá jako poslední v kanálu middlewaru. Middleware SPA se vyskytuje jako poslední:

* Aby všechny ostatní middleware mohly reagovat na vyhovující požadavky jako první.
* Povolí spuštění jednostránkové se směrováním na straně klienta pro všechny trasy, které serverová aplikace nerozpoznala.

Další informace o jednostránkové naleznete v příručkách pro [reakce](xref:spa/react) [a na](xref:spa/angular) rozch šablon projektů.

### <a name="forwarded-headers-middleware-order"></a>Pořadí middlewaru u předávaných hlaviček

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

## <a name="branch-the-middleware-pipeline"></a>Větvení kanálu middlewaru

<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> rozšíření se používají jako konvence pro větvení kanálu. `Map` rozvětvení kanálu požadavků na základě shody dané cesty požadavku. Pokud cesta k požadavku začíná danou cestou, je větev spuštěná.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

V následující tabulce jsou uvedeny žádosti a odpovědi z `http://localhost:1234` použití předchozího kódu.

| Žádost             | Odpověď                     |
| ------------------- | ---------------------------- |
| localhost: 1234      | Hello z delegáta bez mapy. |
| localhost: 1234/Map1 | Mapování testu 1                   |
| localhost: 1234/MAP2 – | Mapovat test 2                   |
| localhost: 1234/map3 – | Hello z delegáta bez mapy. |

Při `Map` použití se odpovídající segmenty cesty odeberou z `HttpRequest.Path` a připojí se k `HttpRequest.PathBase` pro každý požadavek.

`Map` podporuje vnořování, například:

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

`Map` může také odpovídat více segmentům najednou:

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> rozvětvení kanálu požadavků na základě výsledku daného predikátu. `Func<HttpContext, bool>`K mapování požadavků na novou větev kanálu lze použít jakýkoli predikát typu. V následujícím příkladu se k detekci přítomnosti proměnné řetězce dotazu používá predikát `branch` :

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

V následující tabulce jsou uvedeny žádosti a odpovědi z `http://localhost:1234` použití předchozího kódu:

| Žádost                       | Odpověď                     |
| ----------------------------- | ---------------------------- |
| localhost: 1234                | Hello z delegáta bez mapy. |
| localhost: 1234/? větev = hlavní | Použitá větev = hlavní         |

<xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A> také větví kanál požadavků na základě výsledku daného predikátu. Na rozdíl od `MapWhen` , tato větev se znovu připojí k hlavnímu kanálu, pokud nemá krátký okruh nebo obsahuje middleware terminálu:

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=25-26)]

V předchozím příkladu odpověď "Hello z hlavního kanálu". je napsán pro všechny požadavky. Pokud požadavek obsahuje proměnnou řetězce dotazu `branch` , je jeho hodnota protokolována před opětovným připojením k hlavnímu kanálu.

## <a name="built-in-middleware"></a>Vestavěný middleware

ASP.NET Core se dodává s následujícími součástmi middlewaru. Sloupec *Order* poskytuje poznámky k umístění middlewaru v kanálu zpracování požadavků a za jakých podmínek může middleware ukončit zpracování požadavků. Když middleware middleware vytvoří kanál zpracování požadavků a zabrání dalšímu podřízenému middlewaru ve zpracování žádosti, nazývá se *middleware terminálu*. Další informace o krátkodobém okruhu najdete v části [vytvoření kanálu middlewaru pomocí IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) .

| Middleware | Popis | Objednání |
| ---------- | ----------- | ----- |
| [Authentication](xref:security/authentication/identity) | Poskytuje podporu ověřování. | Předtím `HttpContext.User` , než je potřeba. Terminál pro zpětná volání OAuth. |
| [Autorizace](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) | Poskytuje podporu autorizace. | Hned po ověřovacím middlewaru. |
| [Cookie Politických](xref:security/gdpr) | Sleduje souhlas uživatelů při ukládání osobních údajů a vynutila minimální standardy pro cookie pole, například `secure` a `SameSite` . | Před middlewarem, který vydává problémy cookie s. Příklady: ověřování, relace, MVC (TempData). |
| [CORS](xref:security/cors) | Konfiguruje sdílení prostředků mezi zdroji. | Před komponenty, které používají CORS. `UseCors` v současné době `UseResponseCaching` se musí před [touto chybou](https://github.com/dotnet/aspnetcore/issues/23218)přecházet.|
| [Diagnostika](xref:fundamentals/error-handling) | Několik samostatných middlewarů, které poskytují stránku s výjimkou vývojářů, zpracování výjimek, stránky stavového kódu a výchozí webovou stránku pro nové aplikace. | Před komponenty, které generují chyby. Terminál pro výjimky nebo pro výchozí webovou stránku pro nové aplikace |
| [Předávaná záhlaví](xref:host-and-deploy/proxy-load-balancer) | Přepošle hlavičky proxy na aktuální požadavek. | Před komponenty, které používají aktualizované pole. Příklady: schéma, hostitel, IP adresa klienta, metoda. |
| [Kontroly stavu](xref:host-and-deploy/health-checks) | Kontroluje stav aplikace ASP.NET Core a jejích závislostí, jako je například Kontrola dostupnosti databáze. | Terminál, je-li požadavek shodný s koncovým bodem kontroly stavu. |
| [Šíření hlaviček](xref:fundamentals/http-requests#header-propagation-middleware) | Šíří hlavičky protokolu HTTP z příchozího požadavku do odchozích požadavků klienta HTTP. |
| [Přepsání metody HTTP](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | Umožňuje příchozí žádosti POST přepsat metodu. | Před komponenty, které používají aktualizovanou metodu. |
| [Přesměrování HTTPS](xref:security/enforcing-ssl#require-https) | Přesměrovat všechny požadavky HTTP na HTTPS. | Před komponenty, které používají adresu URL. |
| [HTTP Strict Transport Security (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Vylepšení zabezpečení – middleware, který přidává speciální hlavičku odpovědi. | Před odesláním odpovědí a po součástech, které upravují požadavky. Příklady: předávané hlavičky, přepis adresy URL. |
| [MVC](xref:mvc/overview) | Zpracovává požadavky pomocí MVC nebo Razor stránek. | Terminál, pokud požadavek odpovídá trase. |
| [OWIN](xref:fundamentals/owin) | Interoperabilita s aplikacemi, servery a middlewarem založeným na OWIN | Terminál, pokud middleware OWIN plně zpracovává požadavek. |
| [Ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) | Poskytuje podporu pro ukládání odpovědí do mezipaměti. | Před součástmi, které vyžadují ukládání do mezipaměti. `UseCORS` musí předcházet `UseResponseCaching` .|
| [Komprese odezvy](xref:performance/response-compression) | Poskytuje podporu pro komprimaci odpovědí. | Před součástmi, které vyžadují kompresi. |
| [Lokalizace žádosti](xref:fundamentals/localization) | Poskytuje podporu lokalizace. | Před lokalizací citlivých komponent. |
| [Směrování koncových bodů](xref:fundamentals/routing) | Definuje a omezuje trasy požadavků. | Terminál pro vyhovující trasy. |
| [OVĚŘOVÁNÍ](xref:Microsoft.AspNetCore.Builder.SpaApplicationBuilderExtensions.UseSpa%2A) | Zpracovává všechny požadavky od tohoto bodu v řetězci middleware vrácením výchozí stránky pro aplikaci s jednou stránkou (SPA). | V řetězci pozdě, aby měl přednost další middleware pro obsluhu statických souborů, akcí MVC atd.|
| [Relace](xref:fundamentals/app-state) | Poskytuje podporu pro správu uživatelských relací. | Před komponenty, které vyžadují relaci. | 
| [Statické soubory](xref:fundamentals/static-files) | Poskytuje podporu pro poskytování statických souborů a procházení adresářů. | Terminál, pokud požadavek odpovídá souboru |
| [Přepsání adresy URL](xref:fundamentals/url-rewriting) | Poskytuje podporu pro přepisování adres URL a přesměrování požadavků. | Před komponenty, které používají adresu URL. |
| [WebSockets](xref:fundamentals/websockets) | Povolí protokol WebSockets. | Před komponenty, které jsou vyžadovány pro příjem požadavků protokolu WebSocket. |

## <a name="additional-resources"></a>Další zdroje informací

* [Možnosti životního cyklu a registrace](xref:fundamentals/dependency-injection#lifetime-and-registration-options) obsahují kompletní ukázku middlewaru s *rozsahem*, *přechodnými*a neplatnými službami typu *singleton* .
* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)

Middleware je software, který se sestaví do kanálu aplikace za účelem zpracování požadavků a odpovědí. Jednotlivé komponenty:

* Zvolí, jestli se má žádost předat další komponentě v kanálu.
* Může provádět práci před a za další komponentou v kanálu.

Delegáti žádostí se používají k sestavení kanálu požadavků. Delegát žádosti zpracuje jednotlivé požadavky HTTP.

Delegáti žádostí jsou nakonfigurováni pomocí <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> metod rozšíření, a. Jednotlivé delegáty žádostí je možné zadat jako anonymní metodu (označovanou v rámci middlewarového middlewaru) nebo ji lze definovat v opakovaně použitelné třídě. Tyto opakovaně použitelné třídy a vložené anonymní metody jsou *middleware*, označované také jako *komponenty middlewaru*. Každá součást middlewaru v kanálu požadavků zodpovídá za vyvolání další komponenty v kanálu nebo při krátkém okruhu kanálu. Když jsou krátkodobé okruhy middleware, nazývá se *middleware terminálu* , protože zabrání dalšímu middlewaru ve zpracování žádosti.

<xref:migration/http-modules> Vysvětluje rozdíl mezi kanály požadavků v ASP.NET Core a ASP.NET 4. x a poskytuje další ukázky middlewaru.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>Vytvoření kanálu middlewaru pomocí IApplicationBuilder

Kanál žádostí o ASP.NET Core se skládá z posloupnosti delegátů požadavků, který se nazývá jedna po druhé. Následující diagram znázorňuje koncept. Vlákno provádění následuje za černými šipkami.

![Požadavek zpracování, který ukazuje, že se dorazí na požadavek, zpracovává se prostřednictvím tří middlewarů a odpověď opouští aplikaci Každý middleware spustí svou logiku a předá požadavek dalšímu middlewaru v příkazu Next (). Poté, co třetí middleware zpracuje požadavek, požadavek předá předchozí dva middleware v obráceném pořadí pro další zpracování po jejich dalších () příkazech před ukončením aplikace jako reakci na klienta.](index/_static/request-delegate-pipeline.png)

Každý delegát může provádět operace před a po dalším delegátu. Delegáty zpracování výjimek by se měly volat v rámci kanálu na začátku, takže můžou zachytit výjimky, ke kterým dochází v pozdějších fázích kanálu.

Nejjednodušší možná ASP.NET Core aplikace nastaví jediného delegáta žádosti, který zpracovává všechny požadavky. Tento případ nezahrnuje skutečný kanál žádostí. Místo toho je volána jedna anonymní funkce v reakci na každý požadavek HTTP.

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

První <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> delegát ukončí kanál.

Řetězení více požadavků delegátů společně s <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> . `next`Parametr představuje dalšího delegáta v kanálu. Kanál můžete pro krátké okruhy vymezit tím *, že nevoláte* *Další* parametr. Obvykle můžete provádět akce před i po dalším delegátu, jak ukazuje následující příklad:

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

Když delegát neprojde požadavek na dalšího delegáta, nazývá se to *krátkodobý kanál žádosti*. Krátkodobé okruhy jsou často žádoucí, protože brání zbytečné práci. Například [middleware statických souborů](xref:fundamentals/static-files) může fungovat jako *middleware terminálu* tím, že zpracovává požadavek na statický soubor a krátký okruh zbývajících částí kanálu. Middleware přidané do kanálu předtím, než middleware, který ukončí další zpracování, stále zpracovává kód po svých `next.Invoke` příkazech. Přečtěte si ale následující upozornění týkající se pokusu o zápis do odpovědi, která již byla odeslána.

> [!WARNING]
> Nevolejte `next.Invoke` po odeslání odpovědi klientovi. Změny <xref:Microsoft.AspNetCore.Http.HttpResponse> poté, co odpověď začala, vyvolávají výjimku. Například změny jako hlavičky nastavení a stavový kód vyvolávají výjimku. Zápis do těla odpovědi po volání `next` :
>
> * Může způsobit narušení protokolu. Například zápis více než uvedené `Content-Length` .
> * Může poškodit formát textu. Například zápis zápatí HTML do souboru CSS.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> je užitečnou nápovědou, která označuje, zda byla odeslána hlavička nebo zda byl text napsán do.

<a name="order"></a>

## <a name="middleware-order"></a>Pořadí middlewaru

Pořadí, v jakém jsou komponenty middleware přidány v `Startup.Configure` metodě, definuje pořadí, ve kterém jsou komponenty middleware vyvolány na žádostech a obráceném pořadí pro odpověď. Pořadí je **důležité** pro zabezpečení, výkon a funkčnost.

Následující `Startup.Configure` Metoda přidá do doporučeného pořadí součásti middlewaru související se zabezpečením:

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

V předchozím kódu:

* Middleware, které se nepřidaly při vytváření nové webové aplikace s [jednotlivými účty uživatele](xref:security/authentication/identity) , jsou zakomentovány.
* Ne každý middleware potřebuje přejít v tomto přesném pořadí, ale mnoho do něj. Například `UseCors` a `UseAuthentication` musí jít v uvedeném pořadí.

Následující `Startup.Configure` metoda přidává komponenty middlewaru pro běžné scénáře aplikací:

1. Zpracování výjimek a chyb
   * Když aplikace běží ve vývojovém prostředí:
     * Aplikace middleware stránky s výjimkou vývojářů ( <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> ) hlásí chyby běhového modulu.
     * Zpráva middleware pro chybovou stránku databáze ( `Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage` ) oznamuje chyby běhového modulu databáze.
   * Když aplikace běží v produkčním prostředí:
     * Middleware obslužné rutiny výjimek ( <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> ) zachytává výjimky vyvolané v následujících middlewarech.
     * Middleware HSTS (HTTP Strict Transport Security Protocol) ( <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A> ) přidá `Strict-Transport-Security` hlavičku.
1. Middleware () přesměrování protokolu HTTPS ( <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> ) přesměruje požadavky HTTP na https.
1. Soubor middleware () statických souborů ( <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> ) vrací statické soubory a další zpracování žádostí o krátkodobé okruhy.
1. Cookie Middleware zásad ( <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A> ) aplikace v souladu s pravidly pro EU obecné nařízení o ochraně osobních údajů (GDPR).
1. Middleware ověřování ( <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> ) se pokusí ověřit uživatele předtím, než budou mít přístup k zabezpečeným prostředkům.
1. Middleware relace ( <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A> ) vytváří a udržuje stav relace. Pokud aplikace používá stav relace, volejte middleware relace po Cookie middlewaru zásad a před middlewarem MVC.
1. MVC ( <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> ) pro přidání MVC do kanálu požadavků.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();
    app.UseMvc();
}
```

V předchozím příkladu kódu je každá metoda rozšíření middleware vystavena <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> prostřednictvím <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> oboru názvů.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> je první součást middleware přidaná do kanálu. Proto middleware obslužné rutiny výjimek zachycuje všechny výjimky, ke kterým dojde v pozdějších voláních.

Middleware statických souborů se zavolá v průběhu kanálu, aby mohl zpracovávat požadavky a krátké okruhy bez toho, aby procházely zbývajícími součástmi. Middleware statických souborů neposkytuje **žádné** autorizační kontroly. Všechny soubory, které poskytuje middleware pro statický soubor, včetně těch v rámci *wwwroot*, jsou veřejně dostupné. Přístup k zabezpečení statických souborů naleznete v tématu <xref:fundamentals/static-files> .

Pokud požadavek nezpracovává middleware pro statický soubor, je předán do middleware ověřování ( <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> ), který provádí ověřování. Ověřování neověřuje neověřené požadavky v krátkém okruhu. I když middleware ověřování ověřuje požadavky, autorizaci (a odmítnutí) proběhne pouze poté, co MVC vybere konkrétní Razor stránku nebo kontroler MVC a akci.

Následující příklad ukazuje pořadí middlewaru, kde požadavky na statické soubory jsou zpracovávány pomocí middlewaru statického souboru před použitím middlewaru pro komprimaci odezvy. Statické soubory nejsou s tímto pořadím middlewaru komprimovány. Odpovědi MVC z <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> lze komprimovat.

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a>Použití, spuštění a mapování

Nakonfigurujte kanál HTTP pomocí <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> , <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> a <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> . `Use`Metoda může vytvořit krátký okruh kanálu (tj. Pokud nevolá `next` delegáta žádosti). `Run` je konvencí a některé komponenty middlewaru můžou vystavovat `Run[Middleware]` metody, které běží na konci kanálu.

<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> rozšíření se používají jako konvence pro větvení kanálu. `Map` rozvětvení kanálu požadavků na základě shody dané cesty požadavku. Pokud cesta k požadavku začíná danou cestou, je větev spuštěná.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

V následující tabulce jsou uvedeny žádosti a odpovědi z `http://localhost:1234` použití předchozího kódu.

| Žádost             | Odpověď                     |
| ------------------- | ---------------------------- |
| localhost: 1234      | Hello z delegáta bez mapy. |
| localhost: 1234/Map1 | Mapování testu 1                   |
| localhost: 1234/MAP2 – | Mapovat test 2                   |
| localhost: 1234/map3 – | Hello z delegáta bez mapy. |

Při `Map` použití se odpovídající segmenty cesty odeberou z `HttpRequest.Path` a připojí se k `HttpRequest.PathBase` pro každý požadavek.

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> rozvětvení kanálu požadavků na základě výsledku daného predikátu. `Func<HttpContext, bool>`K mapování požadavků na novou větev kanálu lze použít jakýkoli predikát typu. V následujícím příkladu se k detekci přítomnosti proměnné řetězce dotazu používá predikát `branch` :

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

V následující tabulce jsou uvedeny žádosti a odpovědi z `http://localhost:1234` použití předchozího kódu.

| Žádost                       | Odpověď                     |
| ----------------------------- | ---------------------------- |
| localhost: 1234                | Hello z delegáta bez mapy. |
| localhost: 1234/? větev = hlavní | Použitá větev = hlavní         |

`Map` podporuje vnořování, například:

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

`Map` může také odpovídat více segmentům najednou:

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a>Vestavěný middleware

ASP.NET Core se dodává s následujícími součástmi middlewaru. Sloupec *Order* poskytuje poznámky k umístění middlewaru v kanálu zpracování požadavků a za jakých podmínek může middleware ukončit zpracování požadavků. Když middleware middleware vytvoří kanál zpracování požadavků a zabrání dalšímu podřízenému middlewaru ve zpracování žádosti, nazývá se *middleware terminálu*. Další informace o krátkodobém okruhu najdete v části [vytvoření kanálu middlewaru pomocí IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) .

| Middleware | Popis | Objednání |
| ---------- | ----------- | ----- |
| [Authentication](xref:security/authentication/identity) | Poskytuje podporu ověřování. | Předtím `HttpContext.User` , než je potřeba. Terminál pro zpětná volání OAuth. |
| [Cookie Politických](xref:security/gdpr) | Sleduje souhlas uživatelů při ukládání osobních údajů a vynutila minimální standardy pro cookie pole, například `secure` a `SameSite` . | Před middlewarem, který vydává problémy cookie s. Příklady: ověřování, relace, MVC (TempData). |
| [CORS](xref:security/cors) | Konfiguruje sdílení prostředků mezi zdroji. | Před komponenty, které používají CORS. |
| [Diagnostika](xref:fundamentals/error-handling) | Několik samostatných middlewarů, které poskytují stránku s výjimkou vývojářů, zpracování výjimek, stránky stavového kódu a výchozí webovou stránku pro nové aplikace. | Před komponenty, které generují chyby. Terminál pro výjimky nebo pro výchozí webovou stránku pro nové aplikace |
| [Předávaná záhlaví](xref:host-and-deploy/proxy-load-balancer) | Přepošle hlavičky proxy na aktuální požadavek. | Před komponenty, které používají aktualizované pole. Příklady: schéma, hostitel, IP adresa klienta, metoda. |
| [Kontroly stavu](xref:host-and-deploy/health-checks) | Kontroluje stav aplikace ASP.NET Core a jejích závislostí, jako je například Kontrola dostupnosti databáze. | Terminál, je-li požadavek shodný s koncovým bodem kontroly stavu. |
| [Přepsání metody HTTP](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | Umožňuje příchozí žádosti POST přepsat metodu. | Před komponenty, které používají aktualizovanou metodu. |
| [Přesměrování HTTPS](xref:security/enforcing-ssl#require-https) | Přesměrovat všechny požadavky HTTP na HTTPS. | Před komponenty, které používají adresu URL. |
| [HTTP Strict Transport Security (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Vylepšení zabezpečení – middleware, který přidává speciální hlavičku odpovědi. | Před odesláním odpovědí a po součástech, které upravují požadavky. Příklady: předávané hlavičky, přepis adresy URL. |
| [MVC](xref:mvc/overview) | Zpracovává požadavky pomocí MVC nebo Razor stránek. | Terminál, pokud požadavek odpovídá trase. |
| [OWIN](xref:fundamentals/owin) | Interoperabilita s aplikacemi, servery a middlewarem založeným na OWIN | Terminál, pokud middleware OWIN plně zpracovává požadavek. |
| [Ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) | Poskytuje podporu pro ukládání odpovědí do mezipaměti. | Před součástmi, které vyžadují ukládání do mezipaměti. |
| [Komprese odezvy](xref:performance/response-compression) | Poskytuje podporu pro komprimaci odpovědí. | Před součástmi, které vyžadují kompresi. |
| [Lokalizace žádosti](xref:fundamentals/localization) | Poskytuje podporu lokalizace. | Před lokalizací citlivých komponent. |
| [Směrování koncových bodů](xref:fundamentals/routing) | Definuje a omezuje trasy požadavků. | Terminál pro vyhovující trasy. |
| [Relace](xref:fundamentals/app-state) | Poskytuje podporu pro správu uživatelských relací. | Před komponenty, které vyžadují relaci. |
| [Statické soubory](xref:fundamentals/static-files) | Poskytuje podporu pro poskytování statických souborů a procházení adresářů. | Terminál, pokud požadavek odpovídá souboru |
| [Přepsání adresy URL](xref:fundamentals/url-rewriting) | Poskytuje podporu pro přepisování adres URL a přesměrování požadavků. | Před komponenty, které používají adresu URL. |
| [WebSockets](xref:fundamentals/websockets) | Povolí protokol WebSockets. | Před komponenty, které jsou vyžadovány pro příjem požadavků protokolu WebSocket. |

## <a name="additional-resources"></a>Další materiály

* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
