---
title: ASP.NET základní middleware
author: rick-anderson
description: Přečtěte si o ASP.NET middleware core a kanálu požadavků.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
uid: fundamentals/middleware/index
ms.openlocfilehash: 6bf8ed823386ca4e1cf78982f7fba41fba429db8
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751072"
---
# <a name="aspnet-core-middleware"></a>ASP.NET základní middleware

::: moniker range=">= aspnetcore-3.0"

[Podle Rick Anderson](https://twitter.com/RickAndMSFT) a Steve [Smith](https://ardalis.com/)

Middleware je software, který je sestaven do kanálu aplikace pro zpracování požadavků a odpovědí. Každá složka:

* Zvolí, zda má být požadavek předáván další součásti v kanálu.
* Může provádět práci před a za další součást v kanálu.

Delegáti požadavku se používají k vytvoření kanálu požadavku. Delegáti požadavku zpracovávají každý požadavek HTTP.

Delegáti požadavků <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>jsou <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>konfigurováni pomocí metod , a <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> rozšíření. Delegát jednotlivých požadavků lze zadat in-line jako anonymní metodu (tzv. in-line middleware), nebo může být definován v opakovaně použitelné třídě. Tyto opakovaně použitelné třídy a in-line anonymní metody jsou *middleware*, také volal *middleware komponenty*. Každá součást middlewaru v kanálu požadavků je zodpovědná za vyvolání další součásti v kanálu nebo zkratování kanálu. Když middleware zkratuje, nazývá se *to terminálmiddleware,* protože zabraňuje dalšímu middlewaru ve zpracování požadavku.

<xref:migration/http-modules>vysvětluje rozdíl mezi kanály požadavků v ASP.NET Core a ASP.NET 4.x a poskytuje další ukázky middlewaru.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>Vytvoření kanálu middlewaru pomocí aplikace IApplicationBuilder

Kanál požadavku ASP.NET Jádra se skládá z posloupnosti delegátů požadavků, nazývaných jeden po druhém. Následující diagram znázorňuje koncept. Vlákno provedení následuje černé šipky.

![Žádost o zpracování vzor zobrazující požadavek příjezdu, zpracování prostřednictvím tří middlewares a odpověď opuštění aplikace. Každý middleware spustí svou logiku a ruce pryč požadavek na další middleware na další () prohlášení. Poté, co třetí middleware zpracuje požadavek, požadavek přejde zpět přes předchozí dva middlewares v opačném pořadí pro další zpracování po jejich další () příkazy před opuštěním aplikace jako odpověď na klienta.](index/_static/request-delegate-pipeline.png)

Každý delegát může provádět operace před a po dalším delegátovi. Delegáti zpracování výjimek by měly být volány v rané fázi kanálu, aby mohly zachytit výjimky, ke kterým dochází v pozdějších fázích kanálu.

Nejjednodušší možné ASP.NET aplikace Core nastaví delegáta jednoho požadavku, který zpracovává všechny požadavky. Tento případ nezahrnuje kanál skutečných požadavků. Místo toho je volána jedna anonymní funkce jako odpověď na každý požadavek HTTP.

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

Zřetězit více <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>delegátů požadavků společně s . Parametr `next` představuje další delegát v kanálu. Můžete zkratovat potrubí tím, že *volání* *další* parametr. Obvykle můžete provádět akce před i po dalším delegátovi, jak ukazuje následující příklad:

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

Pokud delegát nepředá požadavek dalšímu delegátovi, nazývá se *zkratování kanálu požadavku*. Zkratování je často žádoucí, protože zabraňuje zbytečné práci. Například [middleware statického souboru](xref:fundamentals/static-files) může fungovat jako *koncový middleware* zpracováním požadavku na statický soubor a zkratováním zbytku kanálu. Middleware přidán do kanálu před middleware, který ukončí další `next.Invoke` zpracování stále zpracovává kód po jejich příkazy. Viz následující upozornění o pokusu o zápis na odpověď, která již byla odeslána.

> [!WARNING]
> Nevolejte `next.Invoke` po odeslání odpovědi klientovi. Změny <xref:Microsoft.AspNetCore.Http.HttpResponse> po odpověď byla spuštěna vyvolat výjimku. Například změny, jako je například nastavení záhlaví a stavový kód vyvolat výjimku. Zápis do těla `next`odpovědi po volání :
>
> * Může způsobit porušení protokolu. Například psaní více, `Content-Length`než je uvedeno .
> * Může dojít k poškození formátu těla. Například zápis zápatí HTML do souboru CSS.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*>je užitečná nápověda k označení, zda byla odeslána záhlaví nebo do ní bylo zapsáno tělo.

<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>delegáti neobdrží `next` parametr. První `Run` delegát je vždy terminál a ukončí kanálu. `Run`je konvence. Některé součásti middleware `Run[Middleware]` mohou vystavit metody, které běží na konci kanálu:

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

V předchozím příkladu `Run` delegát `"Hello from 2nd delegate."` zapíše do odpovědi a potom ukončí kanál. Pokud `Use` je `Run` za delegáta `Run` přidán jiný nebo delegát, není volána.

<a name="order"></a>

## <a name="middleware-order"></a>Middleware objednávka

Následující diagram znázorňuje úplný kanál zpracování požadavků pro aplikace ASP.NET Core MVC a Razor Pages. Můžete vidět, jak jsou v typické aplikaci uspořádány stávající middlewares a kde jsou přidány vlastní middlewares. Máte plnou kontrolu nad tím, jak uchovat stávající middlewares nebo aplikovat nové vlastní middlewares podle potřeby pro vaše scénáře.

![ASP.NET middleware potrubí Core](index/_static/middleware-pipeline.svg)

**Middleware koncového bodu** v předchozím diagramu spustí kanál filtru&mdash;pro odpovídající typ aplikace MVC nebo Razor Pages.

![ASP.NET potrubí filtru Core](index/_static/mvc-endpoint.svg)

Pořadí, ve kterém jsou komponenty `Startup.Configure` middleware přidány do metody, definuje pořadí, ve kterém jsou komponenty middleware vyvolány na požadavky a obrácené pořadí odpovědi. Pořadí je **důležité** pro zabezpečení, výkon a funkčnost.

Následující `Startup.Configure` metoda přidá součásti middlewaru související se zabezpečením v doporučeném pořadí:

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

V předchozím kódu:

* Middleware, který není přidán při vytváření nové webové aplikace s [účty jednotlivých uživatelů,](xref:security/authentication/identity) je komentovaný.
* Ne každý middleware musí jít v tomto přesném pořadí, ale mnozí dělají. Například `UseCors`, `UseAuthentication`, `UseAuthorization` a musí jít v uvedeném pořadí.

Následující `Startup.Configure` metoda přidává middleware komponenty pro běžné scénáře aplikací:

1. Zpracování výjimky/chyby
   * Když se aplikace spustí ve vývojovém prostředí:
     * Vývojář exception Page<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>Middleware ( ) hlásí chyby běhu aplikace.
     * Chyba databáze Stránka Middleware hlásí chyby za běhu databáze.
   * Když se aplikace spustí v produkčním prostředí:
     * Výjimka Handler<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>Middleware ( ) zachytí výjimky vysílané v následujících middlewares.
     * Http Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) přidá `Strict-Transport-Security` záhlaví.
1. Https Přesměrování Middleware<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>( ) přesměruje http požadavky na HTTPS.
1. Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) vrací statické soubory a zkratuje další zpracování požadavků.
1. Middleware zásad<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>používání souborů cookie ( ) je v souladu s obecnými předpisy EU o ochraně osobních údajů (GDPR).
1. Směrování middlewaru (`UseRouting`) pro směrování požadavků.
1. Autentizační<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>middleware ( ) se pokusí ověřit uživatele dříve, než mu bude povolen přístup k zabezpečeným prostředkům.
1. Autorizace Middleware (`UseAuthorization`) opravňuje uživatele k přístupu k zabezpečeným prostředkům.
1. Middleware relace<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>( ) vytváří a udržuje stav relace. Pokud aplikace používá stav relace, zavolejte Middleware relace po Middleware zásad cookie a před MVC Middleware.
1. Endpoint Routing Middleware (`UseEndpoints` s `MapRazorPages`) pro přidání koncových bodů Razor Pages do kanálu požadavků.

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

V předchozím příkladu kódu je každá metoda <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> rozšíření <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> middleware vystavena prostřednictvím oboru názvů.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>je první middleware komponenta přidána do kanálu. Proto middleware obslužné rutiny výjimek zachytí všechny výjimky, ke kterým dochází v pozdějších voláních.

Middleware statického souboru se nazývá v rané fázi kanálu, takže může zpracovávat požadavky a zkratovat, aniž by procházel zbývajícími součástmi. Middleware statického souboru **neposkytuje žádné** kontroly autorizace. Všechny soubory obsluhované middlewarem static file, včetně souborů pod *webroot*, jsou veřejně dostupné. Přístup k zabezpečeným statickým souborům naleznete v tématu <xref:fundamentals/static-files>.

Pokud požadavek není zpracován middlewarem statického souboru, je předán do<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>ověřovacího middlewaru ( ), který provádí ověřování. Ověřování nezkratuje neověřené požadavky. Přestože ověřovací middleware ověřuje požadavky, autorizace (a odmítnutí) nastane až poté, co MVC vybere konkrétní razor page nebo mvc řadič a akce.

Následující příklad ukazuje pořadí middlewaru, kde jsou požadavky na statické soubory zpracovány middlewarem static file před middlewarem komprese odpovědí. Statické soubory nejsou komprimovány s tímto middleware pořadí. Odpovědi Razor Pages mohou být komprimovány.

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

Pro jednostránkové aplikace (SPA), <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles*> spa middleware obvykle přichází poslední v middleware potrubí. Spa middleware přichází poslední:

* Chcete-li povolit všechny ostatní middlewares reagovat na odpovídající požadavky jako první.
* Povolení spuštění certifikátů pro řešení bez osamocených postupů se směrováním na straně klienta pro všechny trasy, které serverová aplikace nerozpoznala.

Další podrobnosti o projektech SPna naleznete v průvodcích pro šablony projektu [React](xref:spa/react) a [Angular.](xref:spa/angular)

## <a name="branch-the-middleware-pipeline"></a>Větev middlewarového potrubí

<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>rozšíření se používají jako konvence pro větvení kanálu. `Map`větve kanálu požadavku na základě shody dané cesty požadavku. Pokud cesta požadavku začíná danou cestou, je větev spuštěna.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

V následující tabulce jsou uvedeny `http://localhost:1234` požadavky a odpovědi z použití předchozího kódu.

| Žádost             | Odpověď                     |
| ------------------- | ---------------------------- |
| localhost:1234      | Dobrý den, od non-Map delegáta. |
| localhost:1234/map1 | Test mapy 1                   |
| localhost:1234/map2 | Test mapy 2                   |
| localhost:1234/map3 | Dobrý den, od non-Map delegáta. |

Při `Map` použití jsou spárované segmenty `HttpRequest.Path` cesty odebrány `HttpRequest.PathBase` a připojeny ke každému požadavku.

`Map`podporuje vnoření, například:

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

`Map`může také odpovídat více segmentům najednou:

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>větve kanálu požadavků na základě výsledku daného predikátu. Všechny predikáty `Func<HttpContext, bool>` typu lze mapovat požadavky na novou větev kanálu. V následujícím příkladu se predikát používá ke zjištění přítomnosti `branch`proměnné řetězce dotazu :

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

V následující tabulce jsou uvedeny `http://localhost:1234` požadavky a odpovědi z použití předchozího kódu:

| Žádost                       | Odpověď                     |
| ----------------------------- | ---------------------------- |
| localhost:1234                | Dobrý den, od non-Map delegáta. |
| localhost:1234/?branch=master | Použitá větev = hlavní         |

<xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen*>také větví kanálu požadavku na základě výsledku daného predikátu. Na `MapWhen`rozdíl od , je tato větev připojena k hlavnímu kanálu, pokud nezkratuje nebo neobsahuje koncový middleware:

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=25-26)]

V předchozím příkladu odpověď "Hello z hlavního kanálu." je napsán pro všechny požadavky. Pokud požadavek obsahuje proměnnou `branch`řetězce dotazu , jeho hodnota je zaznamenána před opětovnému připojení hlavního kanálu.

## <a name="built-in-middleware"></a>Vestavěný middleware

ASP.NET Core je dodáván s následujícími komponentami middlewaru. Sloupec *Objednávka* obsahuje poznámky o umístění middlewaru v kanálu zpracování požadavků a za jakých podmínek může middleware ukončit zpracování požadavků. Když middleware zkratuje kanál pro zpracování požadavků a zabraňuje dalšímu následnému middlewaru ve zpracování požadavku, nazývá se *to terminálový middleware*. Další informace o zkratování naleznete v [části Vytvoření kanálu middlewaru s IApplicationBuilder.](#create-a-middleware-pipeline-with-iapplicationbuilder)

| Middleware | Popis | Objednání |
| ---------- | ----------- | ----- |
| [Authentication](xref:security/authentication/identity) | Poskytuje podporu ověřování. | Předtím `HttpContext.User` je potřeba. Terminál pro zpětná volání OAuth. |
| [Autorizace](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*) | Poskytuje podporu autorizace. | Ihned po ověřování Middleware. |
| [Zásady používání souborů cookie](xref:security/gdpr) | Sleduje souhlas uživatelů s ukládáním osobních údajů a vynucuje minimální standardy pro pole souborů cookie, například `secure` a `SameSite`. | Před middleware, který vydává cookies. Příklady: Ověřování, Relace, MVC (TempData). |
| [CORS](xref:security/cors) | Konfiguruje sdílení prostředků mezi zdroji. | Před součástmi, které používají CORS. |
| [Diagnostika](xref:fundamentals/error-handling) | Několik samostatných middlewares, které poskytují stránku výjimky pro vývojáře, zpracování výjimek, stránky s kódem stavu a výchozí webovou stránku pro nové aplikace. | Před součástmi, které generují chyby. Terminál pro výjimky nebo zobrazování výchozí webové stránky pro nové aplikace. |
| [Předávaná záhlaví](xref:host-and-deploy/proxy-load-balancer) | Předává proxied záhlaví na aktuální požadavek. | Před součástmi, které spotřebovávají aktualizovaná pole. Příklady: schéma, hostitel, IP klienta, metoda. |
| [Kontrola stavu](xref:host-and-deploy/health-checks) | Zkontroluje stav aplikace ASP.NET Core a její závislosti, jako je například kontrola dostupnosti databáze. | Terminál, pokud požadavek odpovídá koncovému bodu kontroly stavu. |
| [Šíření záhlaví](xref:fundamentals/http-requests#header-propagation-middleware) | Rozšíří hlavičky PROTOKOLU HTTP z příchozího požadavku na odchozí požadavky klienta HTTP. |
| [Přepsání metody HTTP](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | Umožňuje příchozí požadavek POST přepsat metodu. | Před součásti, které spotřebovávají aktualizovanou metodu. |
| [Přesměrování HTTPS](xref:security/enforcing-ssl#require-https) | Přesměrujte všechny požadavky HTTP na protokol HTTPS. | Před součástmi, které spotřebovávají adresu URL. |
| [HTTP Strict Transport Security (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Middleware pro vylepšení zabezpečení, který přidává hlavičku zvláštní odpovědi. | Před odesláním odpovědí a po součástech, které upravují požadavky. Příklady: Předaná záhlaví, přepisování adres URL. |
| [MVC](xref:mvc/overview) | Zpracovává požadavky pomocí stránek MVC/Razor. | Terminál, pokud požadavek odpovídá trase. |
| [OWIN](xref:fundamentals/owin) | Interop s aplikacemi, servery a middlewarem založeným na OWIN. | terminálu, pokud OWIN Middleware plně zpracuje požadavek. |
| [Ukládání odpovědi do mezipaměti](xref:performance/caching/middleware) | Poskytuje podporu pro ukládání odpovědí do mezipaměti. | Před součástmi, které vyžadují ukládání do mezipaměti. |
| [Komprese odezvy](xref:performance/response-compression) | Poskytuje podporu pro kompresi odpovědí. | Před součástmi, které vyžadují kompresi. |
| [Lokalizace požadavku](xref:fundamentals/localization) | Poskytuje podporu lokalizace. | Před lokalizací citlivých součástí. |
| [Směrování koncového bodu](xref:fundamentals/routing) | Definuje a omezuje trasy požadavků. | Terminál pro odpovídající trasy. |
| [Spa](xref:Microsoft.AspNetCore.Builder.SpaApplicationBuilderExtensions.UseSpa*) | Zpracovává všechny požadavky z tohoto bodu v řetězci middleware vrácením výchozí stránky pro jednostránkovou aplikaci (SPA) | Pozdě v řetězci, takže ostatní middleware pro obsluhu statických souborů, Akce MVC atd., má přednost.|
| [Relace](xref:fundamentals/app-state) | Poskytuje podporu pro správu uživatelských relací. | Před součástmi, které vyžadují session. | 
| [Statické soubory](xref:fundamentals/static-files) | Poskytuje podporu pro zobrazování statických souborů a procházení adresářů. | Terminál, pokud požadavek odpovídá souboru. |
| [Přepsání adresy URL](xref:fundamentals/url-rewriting) | Poskytuje podporu pro přepisování adres URL a přesměrování požadavků. | Před součástmi, které spotřebovávají adresu URL. |
| [WebSockets](xref:fundamentals/websockets) | Povolí protokol WebSockets. | Před součásti, které jsou nutné přijímat požadavky WebSocket. |

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Podle Rick Anderson](https://twitter.com/RickAndMSFT) a Steve [Smith](https://ardalis.com/)

Middleware je software, který je sestaven do kanálu aplikace pro zpracování požadavků a odpovědí. Každá složka:

* Zvolí, zda má být požadavek předáván další součásti v kanálu.
* Může provádět práci před a za další součást v kanálu.

Delegáti požadavku se používají k vytvoření kanálu požadavku. Delegáti požadavku zpracovávají každý požadavek HTTP.

Delegáti požadavků <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>jsou <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>konfigurováni pomocí metod , a <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> rozšíření. Delegát jednotlivých požadavků lze zadat in-line jako anonymní metodu (tzv. in-line middleware), nebo může být definován v opakovaně použitelné třídě. Tyto opakovaně použitelné třídy a in-line anonymní metody jsou *middleware*, také volal *middleware komponenty*. Každá součást middlewaru v kanálu požadavků je zodpovědná za vyvolání další součásti v kanálu nebo zkratování kanálu. Když middleware zkratuje, nazývá se *to terminálmiddleware,* protože zabraňuje dalšímu middlewaru ve zpracování požadavku.

<xref:migration/http-modules>vysvětluje rozdíl mezi kanály požadavků v ASP.NET Core a ASP.NET 4.x a poskytuje další ukázky middlewaru.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>Vytvoření kanálu middlewaru pomocí aplikace IApplicationBuilder

Kanál požadavku ASP.NET Jádra se skládá z posloupnosti delegátů požadavků, nazývaných jeden po druhém. Následující diagram znázorňuje koncept. Vlákno provedení následuje černé šipky.

![Žádost o zpracování vzor zobrazující požadavek příjezdu, zpracování prostřednictvím tří middlewares a odpověď opuštění aplikace. Každý middleware spustí svou logiku a ruce pryč požadavek na další middleware na další () prohlášení. Poté, co třetí middleware zpracuje požadavek, požadavek přejde zpět přes předchozí dva middlewares v opačném pořadí pro další zpracování po jejich další () příkazy před opuštěním aplikace jako odpověď na klienta.](index/_static/request-delegate-pipeline.png)

Každý delegát může provádět operace před a po dalším delegátovi. Delegáti zpracování výjimek by měly být volány v rané fázi kanálu, aby mohly zachytit výjimky, ke kterým dochází v pozdějších fázích kanálu.

Nejjednodušší možné ASP.NET aplikace Core nastaví delegáta jednoho požadavku, který zpracovává všechny požadavky. Tento případ nezahrnuje kanál skutečných požadavků. Místo toho je volána jedna anonymní funkce jako odpověď na každý požadavek HTTP.

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

První <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegát ukončí kanál.

Zřetězit více <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>delegátů požadavků společně s . Parametr `next` představuje další delegát v kanálu. Můžete zkratovat potrubí tím, že *volání* *další* parametr. Obvykle můžete provádět akce před i po dalším delegátovi, jak ukazuje následující příklad:

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

Pokud delegát nepředá požadavek dalšímu delegátovi, nazývá se *zkratování kanálu požadavku*. Zkratování je často žádoucí, protože zabraňuje zbytečné práci. Například [middleware statického souboru](xref:fundamentals/static-files) může fungovat jako *koncový middleware* zpracováním požadavku na statický soubor a zkratováním zbytku kanálu. Middleware přidán do kanálu před middleware, který ukončí další `next.Invoke` zpracování stále zpracovává kód po jejich příkazy. Viz následující upozornění o pokusu o zápis na odpověď, která již byla odeslána.

> [!WARNING]
> Nevolejte `next.Invoke` po odeslání odpovědi klientovi. Změny <xref:Microsoft.AspNetCore.Http.HttpResponse> po odpověď byla spuštěna vyvolat výjimku. Například změny, jako je například nastavení záhlaví a stavový kód vyvolat výjimku. Zápis do těla `next`odpovědi po volání :
>
> * Může způsobit porušení protokolu. Například psaní více, `Content-Length`než je uvedeno .
> * Může dojít k poškození formátu těla. Například zápis zápatí HTML do souboru CSS.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*>je užitečná nápověda k označení, zda byla odeslána záhlaví nebo do ní bylo zapsáno tělo.

<a name="order"></a>

## <a name="middleware-order"></a>Middleware objednávka

Pořadí, ve kterém jsou komponenty `Startup.Configure` middleware přidány do metody, definuje pořadí, ve kterém jsou komponenty middleware vyvolány na požadavky a obrácené pořadí odpovědi. Pořadí je **důležité** pro zabezpečení, výkon a funkčnost.

Následující `Startup.Configure` metoda přidá součásti middlewaru související se zabezpečením v doporučeném pořadí:

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

V předchozím kódu:

* Middleware, který není přidán při vytváření nové webové aplikace s [účty jednotlivých uživatelů,](xref:security/authentication/identity) je komentovaný.
* Ne každý middleware musí jít v tomto přesném pořadí, ale mnozí dělají. Například `UseCors` a `UseAuthentication` musí jít v uvedeném pořadí.

Následující `Startup.Configure` metoda přidává middleware komponenty pro běžné scénáře aplikací:

1. Zpracování výjimky/chyby
   * Když se aplikace spustí ve vývojovém prostředí:
     * Vývojář exception Page<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>Middleware ( ) hlásí chyby běhu aplikace.
     * Databáze Error Page`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`Middleware ( ) hlásí chyby za běhu databáze.
   * Když se aplikace spustí v produkčním prostředí:
     * Výjimka Handler<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>Middleware ( ) zachytí výjimky vysílané v následujících middlewares.
     * Http Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) přidá `Strict-Transport-Security` záhlaví.
1. Https Přesměrování Middleware<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>( ) přesměruje http požadavky na HTTPS.
1. Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) vrací statické soubory a zkratuje další zpracování požadavků.
1. Middleware zásad<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>používání souborů cookie ( ) je v souladu s obecnými předpisy EU o ochraně osobních údajů (GDPR).
1. Autentizační<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>middleware ( ) se pokusí ověřit uživatele dříve, než mu bude povolen přístup k zabezpečeným prostředkům.
1. Middleware relace<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>( ) vytváří a udržuje stav relace. Pokud aplikace používá stav relace, zavolejte Middleware relace po Middleware zásad cookie a před MVC Middleware.
1. MVC<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>( ) pro přidání MVC do kanálu požadavku.

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

V předchozím příkladu kódu je každá metoda <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> rozšíření <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> middleware vystavena prostřednictvím oboru názvů.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>je první middleware komponenta přidána do kanálu. Proto middleware obslužné rutiny výjimek zachytí všechny výjimky, ke kterým dochází v pozdějších voláních.

Middleware statického souboru se nazývá v rané fázi kanálu, takže může zpracovávat požadavky a zkratovat, aniž by procházel zbývajícími součástmi. Middleware statického souboru **neposkytuje žádné** kontroly autorizace. Všechny soubory obsluhované middlewarem static file, včetně souborů pod *webroot*, jsou veřejně dostupné. Přístup k zabezpečeným statickým souborům naleznete v tématu <xref:fundamentals/static-files>.

Pokud požadavek není zpracován middlewarem statického souboru, je předán do<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>ověřovacího middlewaru ( ), který provádí ověřování. Ověřování nezkratuje neověřené požadavky. Přestože ověřovací middleware ověřuje požadavky, autorizace (a odmítnutí) nastane až poté, co MVC vybere konkrétní razor page nebo mvc řadič a akce.

Následující příklad ukazuje pořadí middlewaru, kde jsou požadavky na statické soubory zpracovány middlewarem static file před middlewarem komprese odpovědí. Statické soubory nejsou komprimovány s tímto middleware pořadí. Odpovědi MVC z <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> lze komprimovat.

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a>Použití, spuštění a mapa

Nakonfigurujte <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>kanál <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>HTTP pomocí aplikace , a . Metoda `Use` může zkratovat kanálu (to znamená, pokud nevolá `next` delegáta požadavku). `Run`je konvence a některé součásti middleware může vystavit `Run[Middleware]` metody, které běží na konci kanálu.

<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>rozšíření se používají jako konvence pro větvení kanálu. `Map`větve kanálu požadavku na základě shody dané cesty požadavku. Pokud cesta požadavku začíná danou cestou, je větev spuštěna.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

V následující tabulce jsou uvedeny `http://localhost:1234` požadavky a odpovědi z použití předchozího kódu.

| Žádost             | Odpověď                     |
| ------------------- | ---------------------------- |
| localhost:1234      | Dobrý den, od non-Map delegáta. |
| localhost:1234/map1 | Test mapy 1                   |
| localhost:1234/map2 | Test mapy 2                   |
| localhost:1234/map3 | Dobrý den, od non-Map delegáta. |

Při `Map` použití jsou spárované segmenty `HttpRequest.Path` cesty odebrány `HttpRequest.PathBase` a připojeny ke každému požadavku.

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>větve kanálu požadavků na základě výsledku daného predikátu. Všechny predikáty `Func<HttpContext, bool>` typu lze mapovat požadavky na novou větev kanálu. V následujícím příkladu se predikát používá ke zjištění přítomnosti `branch`proměnné řetězce dotazu :

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

V následující tabulce jsou uvedeny `http://localhost:1234` požadavky a odpovědi z použití předchozího kódu.

| Žádost                       | Odpověď                     |
| ----------------------------- | ---------------------------- |
| localhost:1234                | Dobrý den, od non-Map delegáta. |
| localhost:1234/?branch=master | Použitá větev = hlavní         |

`Map`podporuje vnoření, například:

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

`Map`může také odpovídat více segmentům najednou:

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a>Vestavěný middleware

ASP.NET Core je dodáván s následujícími komponentami middlewaru. Sloupec *Objednávka* obsahuje poznámky o umístění middlewaru v kanálu zpracování požadavků a za jakých podmínek může middleware ukončit zpracování požadavků. Když middleware zkratuje kanál pro zpracování požadavků a zabraňuje dalšímu následnému middlewaru ve zpracování požadavku, nazývá se *to terminálový middleware*. Další informace o zkratování naleznete v [části Vytvoření kanálu middlewaru s IApplicationBuilder.](#create-a-middleware-pipeline-with-iapplicationbuilder)

| Middleware | Popis | Objednání |
| ---------- | ----------- | ----- |
| [Authentication](xref:security/authentication/identity) | Poskytuje podporu ověřování. | Předtím `HttpContext.User` je potřeba. Terminál pro zpětná volání OAuth. |
| [Zásady používání souborů cookie](xref:security/gdpr) | Sleduje souhlas uživatelů s ukládáním osobních údajů a vynucuje minimální standardy pro pole souborů cookie, například `secure` a `SameSite`. | Před middleware, který vydává cookies. Příklady: Ověřování, Relace, MVC (TempData). |
| [CORS](xref:security/cors) | Konfiguruje sdílení prostředků mezi zdroji. | Před součástmi, které používají CORS. |
| [Diagnostika](xref:fundamentals/error-handling) | Několik samostatných middlewares, které poskytují stránku výjimky pro vývojáře, zpracování výjimek, stránky s kódem stavu a výchozí webovou stránku pro nové aplikace. | Před součástmi, které generují chyby. Terminál pro výjimky nebo zobrazování výchozí webové stránky pro nové aplikace. |
| [Předávaná záhlaví](xref:host-and-deploy/proxy-load-balancer) | Předává proxied záhlaví na aktuální požadavek. | Před součástmi, které spotřebovávají aktualizovaná pole. Příklady: schéma, hostitel, IP klienta, metoda. |
| [Kontrola stavu](xref:host-and-deploy/health-checks) | Zkontroluje stav aplikace ASP.NET Core a její závislosti, jako je například kontrola dostupnosti databáze. | Terminál, pokud požadavek odpovídá koncovému bodu kontroly stavu. |
| [Přepsání metody HTTP](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | Umožňuje příchozí požadavek POST přepsat metodu. | Před součásti, které spotřebovávají aktualizovanou metodu. |
| [Přesměrování HTTPS](xref:security/enforcing-ssl#require-https) | Přesměrujte všechny požadavky HTTP na protokol HTTPS. | Před součástmi, které spotřebovávají adresu URL. |
| [HTTP Strict Transport Security (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Middleware pro vylepšení zabezpečení, který přidává hlavičku zvláštní odpovědi. | Před odesláním odpovědí a po součástech, které upravují požadavky. Příklady: Předaná záhlaví, přepisování adres URL. |
| [MVC](xref:mvc/overview) | Zpracovává požadavky pomocí stránek MVC/Razor. | Terminál, pokud požadavek odpovídá trase. |
| [OWIN](xref:fundamentals/owin) | Interop s aplikacemi, servery a middlewarem založeným na OWIN. | terminálu, pokud OWIN Middleware plně zpracuje požadavek. |
| [Ukládání odpovědi do mezipaměti](xref:performance/caching/middleware) | Poskytuje podporu pro ukládání odpovědí do mezipaměti. | Před součástmi, které vyžadují ukládání do mezipaměti. |
| [Komprese odezvy](xref:performance/response-compression) | Poskytuje podporu pro kompresi odpovědí. | Před součástmi, které vyžadují kompresi. |
| [Lokalizace požadavku](xref:fundamentals/localization) | Poskytuje podporu lokalizace. | Před lokalizací citlivých součástí. |
| [Směrování koncového bodu](xref:fundamentals/routing) | Definuje a omezuje trasy požadavků. | Terminál pro odpovídající trasy. |
| [Relace](xref:fundamentals/app-state) | Poskytuje podporu pro správu uživatelských relací. | Před součástmi, které vyžadují session. |
| [Statické soubory](xref:fundamentals/static-files) | Poskytuje podporu pro zobrazování statických souborů a procházení adresářů. | Terminál, pokud požadavek odpovídá souboru. |
| [Přepsání adresy URL](xref:fundamentals/url-rewriting) | Poskytuje podporu pro přepisování adres URL a přesměrování požadavků. | Před součástmi, které spotřebovávají adresu URL. |
| [WebSockets](xref:fundamentals/websockets) | Povolí protokol WebSockets. | Před součásti, které jsou nutné přijímat požadavky WebSocket. |

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
