---
title: Middleware ASP.NET Core
author: rick-anderson
description: Přečtěte si o ASP.NET Core middlewaru a kanálu požadavků.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2019
uid: fundamentals/middleware/index
ms.openlocfilehash: 89cd505810eefeeeb8f708ab82244bbd2e341f38
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308178"
---
# <a name="aspnet-core-middleware"></a>Middleware ASP.NET Core

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)


Middleware je software, který je včleněn do roury aplikace a zpracovává požadavky a odpovědi. Každá komponenta:

* Zvolí, zda předá požadavek další komponentě v rouře.
* Může provádět práci před voláním a po volání další komponenty v rouře.

Delegáti požadavku se používají k vytvoření roury požadavku. Delegáti požadavků zpracovávají každý HTTP požadavek.

Delegáti požadavků jsou nakonfigurováni pomocí rozšiřujících metod <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, a <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>. Jednotliví delegáti požadavků mohou být definováni v jednom řádku jako anonymní metoda (tzv. in-line middlewary), nebo mohou být definováni ve znovupoužitelné třídě. Tyto znovupoužitelné třídy a jednořádkové metody jsou *middlewary*, také označované jako *middlewarové komponenty*. Každá middlewarová komponenta v rouře požadavku zodpovídá za vyvolání další komponenty v rouře nebo předčasné ukončení roury. Middleware, který předčasně ukončí rouru, je nazýván *terminální middleware*, protože zabraňuje dalšímu middlewaru ve zpracování požadavku.

<xref:migration/http-modules> vysvětluje rozdíl mezi rourami požadavků v ASP.NET Core a ASP.NET 4.x a poskytuje další ukázky middlewaru.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>Vytvoření roury middlewaru s IApplicationBuilder

Roura požadavků ASP.NET Core se skládá z posloupnost delegátů požadavku a volají se jedna po druhé. Následující diagram znázorňuje tento koncept. Vlákno provádění postupuje po směru černé šipky.

![Vzor zpracování požadavku zobrazuje příchod, zpracování požadavku skrze tři middlewary a odpověď opuštějící aplikaci. Každý middleware provádí svou vlastní logiku a předává požadavek dalšímu middlewaru příkazem next(). Po zpracování požadavku třetím middlewarem prochází žádost zpět přes předchozí dva middlewary v opačném pořadí pro další zpracování, které následuje po příkazu next(), předtím, než opustí aplikaci jako odpověď klientovi.](index/_static/request-delegate-pipeline.png)

Každý delegát můžet provádět operace před vyvoláním a po vyvolání dalšího delegáta. Delegáti zpracovávající výjimky by měli být voláni v rouře co nejdříve, aby mohli zachytit výjimky, ke kterým dochází v pozdějších etapách roury.

Nejjednodušší možná ASP.NET Core aplikace nastavuje jediného delegáta požadavků, který zpracovává všechny požadavky. Tento případ ve skutečnosti neobsahuje rouru požadavků. Místo toho je volána jediná anonymní funkce v reakci na každý HTTP požadavek.

[!code-csharp[](index/snapshot/Middleware/Startup.cs?name=snippet1)]

První delegát <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> ukončuje rouru.

Zřetězit dohromady více delegátů požadavku je možné pomocí <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>. Parametr `next` představuje dalšího delegáta v rouře. Předčasného ukončení roury lze dosáhnout *nevyvoláním* parametru *next*. Obvykle můžete provádět akce před vykonáním i po vykonání dalšího delegáta, jak ukazuje následující příklad:

[!code-csharp[](index/snapshot/Chain/Startup.cs?name=snippet1)]

Nepředání požadavku delegátem dalšímu delegátu se nazývá *předčasné ukončení (short-circuiting) roury požadavků*. Předčasné ukončení roury je často žádoucí, protože eliminuje zbytečně vykonanou práci. Například [Middleware pro statické soubory](xref:fundamentals/static-files) může fungovat jako *terminální middleware* tak, že zpracuje žádost o statický soubor a předčasně ukončuje další zpracování v rouře. Middlewary přidané do roury před terminálním middlewarem stále zpracovávají kód následující za příkazem `next.Invoke`. Nicméně, v takovém případě dojde k zobrazení varování před pokusem o zápis do odpovědi, která již byla odeslána.

> [!WARNING]
> Nevolejte `next.Invoke` po odeslání odpovědi klientovi. Změny v <xref:Microsoft.AspNetCore.Http.HttpResponse> po započetí odpovědi začlo vracet výjimku. Například změny, jako jsou nastavení hlaviček a stavového kódu, vyvolají výjimku. Zápis do těla odpovědi po volání `next`:
>
> * Může způsobit porušení protokolu. Například zápis, který překračuje uvedenou hodnotu `Content-Length`.
> * Může porušit formát těla zprávy. Například zápis HTML zápatí do CSS souboru.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> je užitečným vodítkem k určení, zda byly hlavičky již odeslány nebo tělo již zapsáno.


## <a name="order"></a>Pořadí


Pořadí, ve kterém jsou middlewarové komponenty přidány do metody `Startup.Configure` definuje pořadí, ve kterém jsou jednotlivé middlewarové komponenty vyvolány při požadavku a v obrácenému pořadí při odpovědi. Pořadí je kritické pro bezpečnost, výkon a funkcionalitu.

Následující metoda `Startup.Configure` přidává middlewarové komponenty pro běžné scénáře aplikací:

::: moniker range=">= aspnetcore-2.0"


1. Zpracování výjimek a chyb
   * Když aplikace běží ve vývojovém prostředí:
     * Aplikace middleware stránky s<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>výjimkou vývojářů () hlásí chyby běhového modulu.
     * Zpráva middleware pro chybovou stránku databáze (<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*>) oznamuje chyby běhového modulu databáze.
   * Když aplikace běží v produkčním prostředí:
     * Middleware obslužné rutiny výjimek (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) zachytává výjimky vyvolané v následujících middlewarech.
     * Middleware HSTS (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>http Strict Transport Security Protocol) () `Strict-Transport-Security` přidá hlavičku.
1. Middleware ()<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>přesměrování protokolu HTTPS () přesměruje požadavky HTTP na https.
1. Soubor middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) statických souborů () vrací statické soubory a další zpracování žádostí o krátkodobé okruhy.
1. Middleware zásad souborů<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>cookie () v aplikaci vyhovuje nařízením v rámci EU obecné nařízení o ochraně osobních údajů (GDPR).
1. Middleware ověřování<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>() se pokusí ověřit uživatele předtím, než budou mít přístup k zabezpečeným prostředkům.
1. Middleware relace<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>() vytváří a udržuje stav relace. Pokud aplikace používá stav relace, volejte middleware relace za middlewarem zásad souborů cookie a před middlewarem MVC.
1. MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) pro přidání MVC do kanálu požadavků.

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

V předchozím ukázkovém kódu je každá middlewarová rozšířující metoda vystavena na rozhraní <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> prostřednictvím jmenného prostoru <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName>.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> je první middlewarová komponenta přidaná do roury. Z toho důvodu zachytává Middleware pro zpracování výjimek všechny výjimky, ke kterým dochází v pozdějších voláních.

Middleware statických souborů je volán brzy v průběhu roury tak, aby mohl zpracovat požadavky a předčasně ukončit rouru bez nutnosti procházet zbylými komponentami. Middleware pro statické soubory neposkytuje **žádnou** kontrolu autorizace. Všechny jím obsluhované souboru, včetně těch uvnitř adresáře *wwwroot*, jsou veřejně dostupné. Způsob zabezpečení statických souborů naleznete v tématu <xref:fundamentals/static-files>.

::: moniker range=">= aspnetcore-2.0"

Pokud není žádost zpracována middlewarem pro statické soubory, je předána autentizačnímu middlewaru (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), který provádí autentizaci. Autentizace neukončuje předčasně neověřené žádosti. Přestože autentizační middleware autentizuje požadavky, autentizace (a odmítnutí) nastane pouze po výběru specifické stránky Razor nebo MVC kontroleru a akce pomocí MVC.

::: moniker-end

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> je první middlewarová komponenta přidaná do kanálu. Z toho důvodu zachytává middleware pro zpracování výjimek všechny výjimky, ke kterým dochází v pozdějších voláních.


Pokud není žádost zpracována middlewarem pro statické soubory, je předána middlewaru identit (<xref:Microsoft.AspNetCore.Builder.BuilderExtensions.UseIdentity*>), který provádí autentizaci. Identita neukončuje předčasně neověřené žádosti. Přestože middleware identit autentizuje požadavky, autentizace (a odmítnutí) nastane pouze po výběru specifického MVC kontroleru a akce pomocí MVC.


Pokud není žádost zpracována middlewarem pro statické soubory, je předána autentizačnímu middlewaru (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), který provádí autentizaci. Autentizace neukončuje předčasně neověřené žádosti. Přestože autentizační middleware autentizuje požadavky, autentizace (a odmítnutí) nastane pouze po výběru specifické stránky Razor nebo MVC kontroleru a akce pomocí MVC.

Následující příklad ukazuje pořadí middlewarů, ve kterém se požadavky na statické soubory zpracovávají middlewarem pro statické soubory před middlewarem pro kompresi odpovědí. Statické soubory se v tomto uspořádání middlewarů nekomprimují. MVC odpovědi z <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> mohou být komprimovány.


```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files not compressed by Static File Middleware.
    app.UseStaticFiles();
    app.UseResponseCompression();
    app.UseMvcWithDefaultRoute();
}
```


### <a name="use-run-and-map"></a>Metody Use, Run a Map

Konfigurujte HTTP kanál pomocí metod Use, Run a Map. Metoda Use můžete předčasně ukončit kanál zpracování (tj. v případě, kdy nezavoláte delegáta požadavku next). Metoda Run je konvence a některé middlewarové komponenty mohou exponovat metodu Run[Middleware], která se spouští na konci kanálu.

Rozšíření <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> jsou používána jako konvence pro větvení kanálu. Metoda Map* větví kanál požadavků na základě shody se zadanou cestou požadavku. Pokud cesta požadavku začíná danou cestou požadavku, větev se provede.


[!code-csharp[](index/snapshot/Chain/StartupMap.cs?name=snippet1)]

V následující tabulce jsou uvedeny žádosti a odpovědi z `http://localhost:1234` použití předchozího kódu.


| Žádost              | Odpověď                            |
| ------------------- | ---------------------------------- |
| localhost:1234      | Hello from non-Map delegate.       |
| localhost:1234/map1 | Map Test 1                         |
| localhost:1234/map2 | Map Test 2                         |
| localhost:1234/map3 | Hello from non-Map delegate.       |

Pokud je použita metoda `Map`, odpovídající segmenty cesty jsou odstraněny z `HttpRequest.Path` a připojeny k `HttpRequest.PathBase` pro každý požadavek.

[MapWhen](/dotnet/api/microsoft.aspnetcore.builder.mapwhenextensions) větví rouru požadavků na základě výsledku daného predikátu. Libovolný predikát typu `Func<HttpContext, bool>` může být použit k mapování požadavků na novou větev roury. V následujícím příkladu je použit predikát k detekci přítomnosti proměnné řetězce dotazu (query string) `branch`:


[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?name=snippet1)]

V následující tabulce jsou uvedeny žádosti a odpovědi z `http://localhost:1234` použití předchozího kódu.


| Žádost                        | Odpověď                      |
| ----------------------------- | ---------------------------- |
| localhost:1234                | Hello from non-Map delegate. |
| localhost:1234/?branch=master | Branch used = master         |

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


`Map` může také porovnávat více segmentů najednou:

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?name=snippet1&highlight=13)]

## <a name="built-in-middleware"></a>Integrované middlewary

ASP.NET Core se dodává s následujícími middlewarovými pomponenty. Sloupec *Pořadí* obsahuje poznámky o umístění middlewaru v rouře zpracování požadavků a o podmínkách, za jakých může middleware ukončit zpracování požadavku. Pokud middleware předčasně ukončí zpracování roury a zabrání tak dalším, podřízeným middlewarům zpracování požadavku, pak je nazýván *terminálním middlewarem*. Další informace o předčasném ukončení roury najdete v sekci [Vytvoření roury middlewaru pomocí IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder).


| Middleware | Popis | Pořadí |
| ---------- | ----------- | ----- |

| [Autentizace](xref:security/authentication/identity) | Poskytuje podporu ověřování. | Předtím, než je potřeba `HttpContext.User`. Terminál pro zpětná volání OAuth. |
| [Zásady souborů cookie](xref:security/gdpr) | Sleduje souhlas uživatelů pro ukládání osobních údajů a vynucuje minimální standardy pro cookie pole, jako například `secure` a `SameSite`. | Před middlewarem, který vydává cookies. Příklady: Autentizace, relace, MVC (TempData). |
| [CORS](xref:security/cors) | Konfiguruje sdílení prostředků různého původu. | Před komponentami, které používají CORS. |
| [Zpracování výjimek](xref:fundamentals/error-handling) | Zpracovává výjimky. | Před komponentami, které generují chyby. |
| [Přesměrované hlavičky](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions) | Přesměrovává hlavičky skryté proxy serverem do aktuální žádosti. | Před komponentami, které využívají aktualizovaná pole. Příklady: schéma, hostitel, IP adresa klienta, metoda. |
| [Kontrola stavu](xref:host-and-deploy/health-checks) | Kontroluje stav aplikace ASP.NET Core a jeho závislostí, jako je například kontrola dostupnosti databáze. | Terminál, pokud požadavek odpovídá koncovému bodu kontroly stavu. |
| [Přepsání metody HTTP](/dotnet/api/microsoft.aspnetcore.builder.httpmethodoverrideextensions) | Umožňuje příchozím POST požadavkům k přepsání metody. | Před komponentami, které využívají aktualizovanou metodu. |
| [Přesměrování protokolu HTTPS](xref:security/enforcing-ssl#require-https) | Přesměrujte všechny požadavky HTTP na HTTPS (ASP.NET Core 2.1 nebo novější). | Před komponentami, které využívají adresu URL. |
| [Striktní zabezpečení přenosu HTTP (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Middleware zvyšující bezpečnost, který přidává speciální hlavičku odpovědi (ASP.NET Core 2.1 nebo novější). | Předtím, než se pošlou odpovědi, a potom, co komponenta modifikuje žádost. Příklady: Přesměrované hlavičky, přepisování adres URL. |
| [MVC](xref:mvc/overview) | Zpracovává žádosti s MVC/Razor Pages (ASP.NET Core 2.0 nebo novější). | Terminál, pokud požadavek odpovídá trase. |
| [OWIN](xref:fundamentals/owin) | Interoperabilita s aplikacemi, serverem a middlewarem OWIN. | Terminál, pokud OWIN middleware plně zpracuje požadavek. |
| [Ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) | Poskytuje podporu pro ukládání odpovědí do mezipaměti. | Před komponentami, které vyžadují ukládání do mezipaměti. |
| [Komprese odpovědí](xref:performance/response-compression) | Poskytuje podporu pro kompresi odpovědí. | Před komponentami, které vyžadují komprese. |
| [Lokalizace požadavků](xref:fundamentals/localization) | Poskytuje podporu lokalizace. | Před komponentami citlivými na lokalizaci. |
| [Směrování](xref:fundamentals/routing) | Definuje a omezuje tras požadavků. | Terminál pro odpovídající trasy. |
| [Relace](xref:fundamentals/app-state) | Poskytuje podporu pro správu uživatelských relací. | Před komponentami, které vyžadují relace. |
| [Statické soubory](xref:fundamentals/static-files) | Poskytuje podporu pro poskytování statických souborů a výpis adresářů. | Terminál, pokud požadavek odpovídá souboru. |
| [Přepisování adres URL](xref:fundamentals/url-rewriting) | Poskytuje podporu pro přepis adres URL a přesměrování požadavků. | Před komponentami, které využívají adresu URL. |
| [Webové sokety](xref:fundamentals/websockets) | Povolí protokol WebSocket. | Před komponentami, které jsou nutné pro příjem WebSocket požadavků. |

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
