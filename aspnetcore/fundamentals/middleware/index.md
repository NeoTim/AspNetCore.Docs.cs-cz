---
title: Middleware ASP.NET Core
author: rick-anderson
description: Další informace o ASP.NET Core middleware a kanál žádosti.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: fundamentals/middleware/index
ms.openlocfilehash: 4e5da1036b77e876899ccdea48bdec69454e1657
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52861482"
---
# <a name="aspnet-core-middleware"></a>Middleware ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)

Middleware je software, který je sestaven do kanálu služby aplikace pro zpracování požadavků a odpovědí. Jednotlivé komponenty:

* Zvolí, zda se má předat požadavky na další komponenta v kanálu.
* Práci můžete provádět, před a po zavolání další komponenta v kanálu.

Delegáti požadavku se používají k vytvoření kanálu požadavku. Delegáti žádost o zpracování konkrétního požadavku HTTP.

Požádat o Delegáti jsou nakonfigurováni pomocí <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, a <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> metody rozšíření. Delegát jednotlivých požadavků může být zadaný řádek jako anonymní metody (označované jako middlewaru vložených), nebo může být definován ve třídě opakovaně použitelné. Tyto opakovaně použitelné třídy a v řádku anonymní metody jsou *middleware*, označované také jako *middlewarových komponent*. Jednotlivé komponenty middleware v kanálu požadavku zodpovídá za vyvolání další komponenta v kanálu nebo zkrácenou kanálu.

<xref:migration/http-modules> Vysvětluje rozdíl mezi požadavek kanály v ASP.NET Core a ASP.NET 4.x a poskytuje další ukázky middlewaru.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>Vytvoření kanálu middlewaru s IApplicationBuilder

Kanál žádosti ASP.NET Core se skládá z posloupnost požadavek delegáty, volá se jedna po druhé. Následující diagram ukazuje koncept. Vlákno provádění postupuje černé šipky.

![Vzor zpracování požadavku zobrazující žádosti přicházející, zpracování až tři middlewares a odpovědi opuštění aplikace. Každý middleware běží svou logikou a předá požadavek na další middleware v příkazu metodu next(). Po třetí middleware zpracuje požadavek, žádost prochází zpět předchozí dvě middlewares v obráceném pořadí pro další zpracování po jejich next() příkazy před opuštěním aplikace jako odpověď klientovi.](index/_static/request-delegate-pipeline.png)

Všem delegátům můžete provádět operace, před a po dalším delegáta. Také můžete rozhodnout delegáta úspěšná žádost o další delegátem, která se nazývá *zkrácenou kanál žádosti*. Krátký cyklus je často žádoucí, protože tím eliminujete zbytečné práce. Middleware statické soubory můžete například vrátit žádost o statický soubor a zkrácenou rest z kanálu. Delegáti zpracování výjimek se nazývají již v rané fázi v kanálu, takže se můžete zachytit výjimky, ke kterým dochází v pozdějších fázích kanálu.

Nejjednodušší možný aplikace ASP.NET Core nastaví delegáta jedné žádosti, která zpracovává všechny požadavky. Tento případ neobsahuje kanál aktuálního požadavku. Místo toho jednoho anonymní funkce je volána v reakci na každý požadavek HTTP.

[!code-csharp[](index/snapshot/Middleware/Startup.cs?name=snippet1)]

První <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegáta ukončí kanálu.

Zřetězit více požadavek delegátů spolu s <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>. `next` Parametr představuje další delegáta v kanálu. Můžete zkrácenou kanál pomocí *není* volání *Další* parametru. Můžete obvykle provádět akce před i po další delegáta, jak ukazuje následující příklad:

[!code-csharp[](index/snapshot/Chain/Startup.cs?name=snippet1)]

> [!WARNING]
> Nevolejte `next.Invoke` po odeslání odpovědi klientovi. Změny <xref:Microsoft.AspNetCore.Http.HttpResponse> po spuštění odpovědi vrátit výjimku. Například změny, jako je nastavení hlaviček a stavovým kódem vyvolat výjimku. Zápis do datové části odpovědi po volání `next`:
>
> * Může způsobit narušení protokolu. Například zápis informace než uvedené `Content-Length`.
> * Může dojít k poškození formát těla zprávy. HTML zápatí například zápis do souboru šablony stylů CSS.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> je užitečné nápovědu k označení, pokud byly odeslány hlavičky nebo text byl zapsán do.

## <a name="order"></a>Pořadí

Pořadí, ve kterém middlewarových komponent přidány `Startup.Configure` metoda definuje pořadí, ve kterém jsou vyvolány middlewarových komponent na požadavky a obrácenému pořadí pro odpověď. Pořadí je důležité pro zabezpečení, výkon a funkčnost.

Následující `Startup.Configure` metoda přidá middlewarových komponent pro běžné scénáře aplikací:

::: moniker range=">= aspnetcore-2.0"

1. Zpracování výjimek a chyb
1. Protokol zabezpečení striktní přenosu HTTP
1. Přesměrování protokolu HTTPS
1. Statický souborový server
1. Vynucení zásad souborů cookie
1. Ověřování
1. Relace
1. MVC

```csharp
public void Configure(IApplicationBuilder app)
{
    if (env.IsDevelopment())
    {
        // When the app runs in the Development environment:
        //   Use the Developer Exception Page to report app runtime errors.
        //   Use the Database Error Page to report database runtime errors.
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        // When the app doesn't run in the Development environment:
        //   Enable the Exception Handler Middleware to catch exceptions
        //     thrown in the following middlewares.
        //   Use the HTTP Strict Transport Security Protocol (HSTS)
        //     Middleware.
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    // Use HTTPS Redirection Middleware to redirect HTTP requests to HTTPS.
    app.UseHttpsRedirection();

    // Return static files and end the pipeline.
    app.UseStaticFiles();

    // Use Cookie Policy Middleware to conform to EU General Data 
    // Protection Regulation (GDPR) regulations.
    app.UseCookiePolicy();

    // Authenticate before the user accesses secure resources.
    app.UseAuthentication();

    // If the app uses session state, call Session Middleware after Cookie 
    // Policy Middleware and before MVC Middleware.
    app.UseSession();

    // Add MVC to the request pipeline.
    app.UseMvc();
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. Zpracování výjimek a chyb
1. Statické soubory
1. Ověřování
1. Relace
1. MVC

```csharp
public void Configure(IApplicationBuilder app)
{
    // Enable the Exception Handler Middleware to catch exceptions
    //   thrown in the following middlewares.
    app.UseExceptionHandler("/Home/Error");

    // Return static files and end the pipeline.
    app.UseStaticFiles();

    // Authenticate before you access secure resources.
    app.UseIdentity();

    // If the app uses session state, call UseSession before 
    // MVC Middleware.
    app.UseSession();

    // Add MVC to the request pipeline.
    app.UseMvcWithDefaultRoute();
}
```

::: moniker-end

V předchozím příkladu kódu, každá metoda rozšíření middlewaru je vystaven na <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> prostřednictvím <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> oboru názvů.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> je první komponenta middlewaru přidané do kanálu. Proto Middleware obslužné rutiny výjimek zachytává všechny výjimky, ke kterým dochází v pozdější volání.

Statický soubor middlewaru je volána v rané fázi kanálu tak, aby mohl zpracovávat požadavky a zkrácenou bez nutnosti kontaktovat zbývající součásti. Poskytuje Middleware statické soubory **žádné** kontroly autorizace. Všechny soubory obsluhuje, včetně těch *wwwroot*, jsou veřejně dostupné. Přístup k zabezpečení statické soubory, naleznete v tématu <xref:fundamentals/static-files>.

::: moniker range=">= aspnetcore-2.0"

Pokud žádost není zpracovávaných middlewarem statický soubor, je předána Middleware ověřování (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), který provádí ověřování. Ověřování není zkrácenou neověřené žádosti. I když ověřovací Middleware ověřuje požadavky, autorizace (a odmítnutí) nastane pouze po MVC vybere konkrétní stránky Razor nebo MVC kontroleru a akce.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Pokud požadavek není zpracováván Middleware statické soubory, je předána Identity Middleware (<xref:Microsoft.AspNetCore.Builder.BuilderExtensions.UseIdentity*>), který provádí ověřování. Identita není zkrácenou neověřené žádosti. I když žádosti o ověření Identity autorizace (a odmítnutí) nastane pouze po MVC vybere konkrétní kontroleru a akce.

::: moniker-end

Následující příklad ukazuje middleware pořadí, ve kterém jsou požadavky pro statické soubory zpracovávány middlewarem statický soubor před Middleware pro kompresi odpovědí. Statické soubory nejsou komprimovaný pomocí tohoto pořadí middlewaru. Odpovědi MVC <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> lze komprimovat.

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files not compressed by Static File Middleware.
    app.UseStaticFiles();
    app.UseResponseCompression();
    app.UseMvcWithDefaultRoute();
}
```

### <a name="use-run-and-map"></a>Použití, spuštění a mapy

Konfigurace kanálu HTTP pomocí `Use`, `Run`, a `Map`. `Use` Metoda můžete zkrácenou kanálu (tj. Pokud nebude volat `next` delegáta požadavku). `Run` je konvence, a některé middlewarových komponent může vystavit `Run[Middleware]` metody, které běží na konci kanálu.

<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> rozšíření jsou použity jako konvence pro větvení kanálu. `Map*` větve kanál požadavku na základě shody zadanou cestu požadavku. Pokud cesta požadavku začíná dané cesty, větve je proveden.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs?name=snippet1)]

V následující tabulce jsou uvedeny požadavky a odpovědi z `http://localhost:1234` pomocí předchozího kódu.

| Požadavek             | Odpověď                     |
| ------------------- | ---------------------------- |
| 1234      | Dobrý den ze bez mapování delegáta. |
| 1234 / map1 | Mapování Test 1                   |
| localhost:1234/map2 | Mapování testů 2                   |
| localhost:1234/map3 | Dobrý den ze bez mapování delegáta. |

Když `Map` se používá, segmenty cesty odpovídající jsou odebrány z `HttpRequest.Path` a připojený k `HttpRequest.PathBase` pro každý požadavek.

[MapWhen](/dotnet/api/microsoft.aspnetcore.builder.mapwhenextensions) větví kanál požadavku na základě výsledku daného predikátu. Žádné predikátu typu `Func<HttpContext, bool>` je možné mapovat požadavky na novou větev kanálu. V následujícím příkladu, predikát slouží k detekci přítomnosti proměnnou s řetězcem dotazu `branch`:

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?name=snippet1)]

V následující tabulce jsou uvedeny požadavky a odpovědi z `http://localhost:1234` pomocí předchozího kódu.

| Požadavek                       | Odpověď                     |
| ----------------------------- | ---------------------------- |
| 1234                | Dobrý den ze bez mapování delegáta. |
| localhost:1234/?branch=master | Větev se použije hlavní =         |

`Map` podporuje vnořené, například:

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

`Map` Můžete také porovnat více segmentů najednou:

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?name=snippet1&highlight=13)]

## <a name="built-in-middleware"></a>Integrované middlewaru

ASP.NET Core se dodává s následujícími součástmi middlewaru. *Pořadí* sloupec obsahuje poznámky k umístění middleware v kanálu požadavku a za jakých podmínek middleware může ukončit žádosti a zabránit dalším middlewarem zpracování požadavku.

| Middleware | Popis | Pořadí |
| ---------- | ----------- | ----- |
| [Ověřování](xref:security/authentication/identity) | Poskytuje podporu ověřování. | Před `HttpContext.User` je potřeba. Terminál pro zpětná volání OAuth. |
| [Zásady souborů cookie](xref:security/gdpr) | Sleduje svolení od uživatelů pro ukládání osobních údajů a vynucuje minimální standardy pro soubor cookie pole, jako například `secure` a `SameSite`. | Před middlewaru, který vydá soubory cookie. Příklady: Ověřování, relace, MVC (TempData). |
| [CORS](xref:security/cors) | Konfiguruje sdílení prostředků různého původu. | Před provedením komponent, které používají CORS. |
| [Diagnostika](xref:fundamentals/error-handling) | Konfiguruje se Diagnostika. | Před provedením komponent, které generují chyby. |
| [Přesměrovaná záhlaví](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions) | Předává záhlaví směrovány přes proxy server na aktuální žádost. | Před provedením komponent, které využívají aktualizovanými poli. Příklady: schéma, hostitele, IP adresu klienta, metoda. |
| [Kontrola stavu](xref:host-and-deploy/health-checks) | Kontroluje stav aplikace ASP.NET Core a jeho závislosti, jako je například kontrola dostupnosti databáze. | Terminál, pokud požadavek odpovídá stav vrácení koncového bodu. |
| [Přepsání metody HTTP](/dotnet/api/microsoft.aspnetcore.builder.httpmethodoverrideextensions) | Umožňuje příchozí požadavek POST k přepsání metody. | Před provedením komponent, které využívají aktualizované metody. |
| [Přesměrování protokolu HTTPS](xref:security/enforcing-ssl#require-https) | Přesměrujte všechny požadavky HTTP na HTTPS (ASP.NET Core 2.1 nebo novější). | Před provedením komponent, které využívají adresu URL. |
| [Zabezpečení striktní přenosu HTTP (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Zabezpečení vylepšení middleware, který přidá hlavičku odpovědi speciální (ASP.NET Core 2.1 nebo novější). | Předtím, než se posílají žádosti a po součásti, které mění žádosti. Příklady: Předané záhlaví, přepisování adres URL. |
| [MVC](xref:mvc/overview) | Zpracuje žádosti s MVC/Razor Pages (ASP.NET Core 2.0 nebo novější). | Terminál, pokud požadavek odpovídá trase. |
| [OWIN](xref:fundamentals/owin) | Interoperabilita s aplikací OWIN, servery a middlewaru. | Terminál, pokud middlewaru OWIN, který plně zpracuje požadavek. |
| [Ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) | Poskytuje podporu pro ukládání do mezipaměti odpovědi. | Před provedením komponent, které vyžadují ukládání do mezipaměti. |
| [Kompresi odpovědí](xref:performance/response-compression) | Poskytuje podporu pro kompresi odpovědí. | Před provedením komponent, které vyžadují komprese. |
| [Žádost o lokalizaci](xref:fundamentals/localization) | Poskytuje podporu lokalizace. | Před provedením komponent citlivé lokalizace. |
| [Směrování](xref:fundamentals/routing) | Definuje a omezuje žádosti trasy. | Terminál odpovídající trasy. |
| [Relace](xref:fundamentals/app-state) | Poskytuje podporu pro správu uživatelských relací. | Před provedením komponent, které vyžadují relace. |
| [Statické soubory](xref:fundamentals/static-files) | Poskytuje podporu pro poskytování statických souborů a procházení adresářů. | Terminál, pokud požadavek odpovídá souboru. |
| [Přepisování adres URL](xref:fundamentals/url-rewriting) | Poskytuje podporu pro přepis adres URL a přesměrování požadavků. | Před provedením komponent, které využívají adresu URL. |
| [Webové sokety](xref:fundamentals/websockets) | Povolí protokol Websocket. | Před provedením komponent, které jsou nutné, aby přijímal požadavky protokolu WebSocket. |

## <a name="write-middleware"></a>Zápis middlewaru

Middleware je obecně zapouzdřené v třídě a vystavený s metodou rozšíření. Vezměte v úvahu následující middlewaru, který nastaví jazykovou verzi pro aktuální požadavek z řetězce dotazu:

[!code-csharp[](index/snapshot/Culture/StartupCulture.cs?name=snippet1)]

Předchozí kód ukázkové slouží k předvedení vytváření komponenta middlewaru. ASP.NET Core lokalizace integrovanou podporu najdete na webu <xref:fundamentals/localization>.

Middleware můžete otestovat tím, že předáte v jazykové verzi, například `http://localhost:7997/?culture=no`.

Následující kód přesune delegáta middleware pro třídu:

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddleware.cs)]

::: moniker range="< aspnetcore-2.0"

Middleware `Task` název metody musí být `Invoke`. V technologii ASP.NET Core 2.0 nebo novější, název může být buď `Invoke` nebo `InvokeAsync`.

::: moniker-end

Poskytuje následující metody rozšíření middleware prostřednictvím <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddlewareExtensions.cs)]

Následující kód volá middleware z `Startup.Configure`:

[!code-csharp[](index/snapshot/Culture/Startup.cs?name=snippet1&highlight=5)]

Postupujte podle middleware [explicitní závislosti Princip](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) zveřejněním závislých 've svém konstruktoru. Middleware je vytvořen jednou za *dobu životnosti aplikace*. Najdete v článku [závislosti na požadavku](#per-request-dependencies) části, pokud je potřeba sdílet s middlewaru v rámci žádost o služby.

Middlewarových komponent lze vyřešit jejich závislosti z [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) prostřednictvím parametry konstruktoru. [UseMiddleware&lt;T&gt; ](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) můžete také přijmout přímo další parametry.

### <a name="per-request-dependencies"></a>Závislosti na základě žádosti

Protože middlewaru je vytvořen při spuštění aplikace, nikoli jednotlivých žádostí, *obor* služby životního cyklu používat middleware konstruktory nejsou sdíleny s jinými typy vložený závislostí při každém požadavku. Pokud musíte sdílet *obor* služby mezi middlewaru a ostatními typy, přidejte tyto služby `Invoke` podpis metody. `Invoke` Metoda může přijímat další parametry, které se vyplní podle DI:

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="additional-resources"></a>Další zdroje

* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
