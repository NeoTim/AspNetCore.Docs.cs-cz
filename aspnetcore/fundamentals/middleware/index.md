---
title: Middleware ASP.NET Core
author: rick-anderson
description: Přečtěte si o ASP.NET Core middlewaru a kanálu požadavků.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/08/2019
uid: fundamentals/middleware/index
ms.openlocfilehash: 5d02e1eb37693881d5b1855e1ed163590d8a44d3
ms.sourcegitcommit: fcdf9aaa6c45c1a926bd870ed8f893bdb4935152
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72165311"
---
# <a name="aspnet-core-middleware"></a>Middleware ASP.NET Core

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)

Middleware je software, který je včleněn do kanálu aplikace a zpracovává požadavky a odpovědi. Každá komponenta:

* Zvolí, zda předá požadavek další komponentě v kanálu.
* Může provádět práci před voláním a po volání další komponenty v kanálu.

Delegáti požadavku se používají k vytvoření kanálu požadavku. Delegáti požadavků zpracovávají každý HTTP požadavek.

Delegáti požadavků jsou nakonfigurováni pomocí rozšiřujících metod <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> a <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>. Jednotliví delegáti požadavků mohou být definováni v jednom řádku jako anonymní metoda (tzv. in-line middlewary), nebo mohou být definováni ve znovupoužitelné třídě. Tyto znovupoužitelné třídy a jednořádkové metody jsou *middlewary*, také označované jako *middlewarové komponenty*. Každá middlewarová komponenta v kanálu požadavku zodpovídá za vyvolání další komponenty v kanálu nebo předčasné ukončení kanálu. Middleware, který předčasně ukončí kanál, je nazýván *terminální middleware*, protože zabraňuje dalšímu middlewaru ve zpracování požadavku.

<xref:migration/http-modules> vysvětluje rozdíl mezi kanály požadavků v ASP.NET Core a ASP.NET 4.x a poskytuje další ukázky middlewaru.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>Vytvoření kanálu middlewaru pomocí IApplicationBuilder

Kanál požadavků ASP.NET Core se skládá z posloupnosti delegátů požadavku a volají se jeden po druhém. Následující diagram znázorňuje tento koncept. Vlákno provádění postupuje po směru černé šipky.

![Vzor zpracování požadavku zobrazujující příchod, zpracování požadavku prostřednictvím tři middlewarů a odpověď opouštějící aplikaci. Každý middleware provádí svou vlastní logiku a předává požadavek dalšímu middlewaru příkazem next(). Po zpracování požadavku třetím middlewarem prochází žádost zpět přes předchozí dva middlewary v opačném pořadí pro další zpracování, které následuje po příkazu next(), předtím, než opustí aplikaci jako odpověď klientovi.](index/_static/request-delegate-pipeline.png)

Každý delegát můžet provádět operace před vyvoláním a po vyvolání dalšího delegáta. Delegáti zpracovávající výjimky by měli být voláni v kanálu co nejdříve, aby mohli zachytit výjimky, ke kterým dochází v pozdějších etapách kanálu.

Nejjednodušší možná aplikace ASP.NET Core nastavuje jediného delegáta požadavků, který zpracovává všechny požadavky. Tento případ ve skutečnosti neobsahuje kanál požadavků. Místo toho se volá jediná anonymní funkce v reakci na každý požadavek HTTP.

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

První delegát <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> ukončuje kanál.

Zřetězit dohromady více delegátů požadavku je možné pomocí <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>. Parametr `next` představuje dalšího delegáta v kanálu. Předčasného ukončení kanálu lze dosáhnout *nevyvoláním* parametru *next*. Obvykle můžete provádět akce před vykonáním i po vykonání dalšího delegáta, jak ukazuje následující příklad:

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

Nepředání požadavku delegátem dalšímu delegátu se nazývá *předčasné ukončení (short-circuiting) kanálu požadavků*. Předčasné ukončení kanálu je často žádoucí, protože eliminuje zbytečně vykonanou práci. Například [Middleware pro statické soubory](xref:fundamentals/static-files) může fungovat jako *terminální middleware* tak, že zpracuje žádost o statický soubor a předčasně ukončuje další zpracování v kanálu. Middlewary přidané do kanálu před terminálním middlewarem stále zpracovávají kód následující za příkazem `next.Invoke`. V takovém případě se však zobrazí varování před pokusem o zápis do odpovědi, která se už odeslala.

> [!WARNING]
> Po odeslání odpovědi klientovi nevolejte `next.Invoke`. Změny v <xref:Microsoft.AspNetCore.Http.HttpResponse> po započetí odpovědi začalo vracet výjimku. Výjimku vyvolají například změny, jako jsou nastavení hlaviček a stavového kódu. Zápis do těla odpovědi po volání `next`:
>
> * Může způsobit porušení protokolu. Například zápis, který překračuje uvedenou hodnotu `Content-Length`.
> * Může porušit formát těla zprávy. Například zápis zápatí HTML do souboru CSS.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> je užitečným vodítkem k určení, jestli už proběhlo odeslání hlaviček nebo zápis do těla.

## <a name="order"></a>Pořadí

Pořadí, ve kterém jsou middlewarové komponenty přidány do metody `Startup.Configure`, definuje pořadí, ve kterém se jednotlivé middlewarové komponenty vyvolají při požadavku a v obrácenému pořadí při odpovědi. Pořadí je zásadní pro bezpečnost, výkon a funkcionalitu.

Následující metoda `Startup.Configure` přidává middlewarové komponenty pro běžné scénáře aplikací:

::: moniker range=">= aspnetcore-3.0"

1. Zpracování výjimek a chyb
   * Když aplikace běží ve vývojovém prostředí:
     * Middleware stránky s výjimkou pro vývojáře (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) hlásí chyby modulu runtime aplikace.
     * Zpráva middlewaru chybové stránky databáze oznamuje chyby běhového běhu databáze.
   * Když aplikace běží v produkčním prostředí:
     * Middleware obslužné rutiny výjimek (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) zachytává výjimky vyvolané v následujících middlewarech.
     * Middleware protokolu HTTP Strict Transport Security (HSTS) (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) přidá hlavičku `Strict-Transport-Security`.
1. Middleware přesměrování HTTPS (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) přesměruje požadavky HTTP na HTTPS.
1. Middleware statických souborů (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) vrátí statické soubory a další zpracování žádostí o krátkodobé okruhy.
1. Middleware zásad souborů cookie (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) v aplikaci vyhovuje nařízením EU Obecné nařízení o ochraně osobních údajů (GDPR).
1. Směrování middlewaru (`UseRouting`) pro směrování požadavků.
1. Middleware ověřování (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) se pokusí ověřit uživatele předtím, než budou mít přístup k zabezpečeným prostředkům.
1. Middleware autorizace (`UseAuthorization`) opravňuje uživatele k přístupu k zabezpečeným prostředkům.
1. Middleware relace (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) vytváří a udržuje stav relace. Pokud aplikace používá stav relace, volejte middleware relace za middlewarem zásad souborů cookie a před middlewarem MVC.
1. Middleware směrování koncového bodu (`UseEndpoints` s `MapRazorPages`) pro přidání Razor Pages koncových bodů do kanálu požadavků.

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

V předchozím ukázkovém kódu je každá middlewarová rozšířující metoda vystavena na rozhraní <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> prostřednictvím jmenného prostoru <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName>.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> je první middlewarová komponenta přidaná do kanálu. Z toho důvodu zachytává middleware pro zpracování výjimek všechny výjimky, ke kterým dochází v pozdějších voláních.

Middleware statických souborů je volán brzy v průběhu kanálu tak, aby mohl zpracovat požadavky a předčasně ukončit kanál bez nutnosti procházet zbylými komponentami. Middleware pro statické soubory neposkytuje **žádnou** kontrolu autorizace. Všechny soubory, které poskytuje middleware pro statický soubor, včetně těch v rámci *wwwroot*, jsou veřejně dostupné. Způsob zabezpečení statických souborů naleznete v tématu <xref:fundamentals/static-files>.

Pokud není žádost zpracována middlewarem pro statické soubory, je předána autentizačnímu middlewaru (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), který provádí autentizaci. Autentizace neukončuje předčasně neověřené žádosti. Přestože autentizační middleware autentizuje požadavky, autentizace (a odmítnutí) nastane pouze po výběru specifické stránky Razor nebo MVC kontroleru a akce pomocí MVC.

Následující příklad ukazuje pořadí middlewarů, ve kterém se požadavky na statické soubory zpracovávají middlewarem pro statické soubory před middlewarem pro kompresi odpovědí. Statické soubory se v tomto uspořádání middlewarů nekomprimují. Odezvy Razor Pages lze komprimovat.

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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

1. Zpracování výjimek a chyb
   * Když aplikace běží ve vývojovém prostředí:
     * Middleware stránky s výjimkou pro vývojáře (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) hlásí chyby modulu runtime aplikace.
     * Middleware chybových stránek databáze (<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*>) oznamuje chyby běhového běhu databáze.
   * Když aplikace běží v produkčním prostředí:
     * Middleware obslužné rutiny výjimek (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) zachytává výjimky vyvolané v následujících middlewarech.
     * Middleware protokolu HTTP Strict Transport Security (HSTS) (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) přidá hlavičku `Strict-Transport-Security`.
1. Middleware přesměrování HTTPS (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) přesměruje požadavky HTTP na HTTPS.
1. Middleware statických souborů (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) vrátí statické soubory a další zpracování žádostí o krátkodobé okruhy.
1. Middleware zásad souborů cookie (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) v aplikaci vyhovuje nařízením EU Obecné nařízení o ochraně osobních údajů (GDPR).
1. Middleware ověřování (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) se pokusí ověřit uživatele předtím, než budou mít přístup k zabezpečeným prostředkům.
1. Middleware relace (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) vytváří a udržuje stav relace. Pokud aplikace používá stav relace, volejte middleware relace za middlewarem zásad souborů cookie a před middlewarem MVC.
1. MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) Chcete-li přidat MVC do kanálu požadavků.

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

V předchozím ukázkovém kódu je každá middlewarová rozšířující metoda vystavena na rozhraní <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> prostřednictvím jmenného prostoru <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName>.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> je první middlewarová komponenta přidaná do kanálu. Z toho důvodu zachytává middleware pro zpracování výjimek všechny výjimky, ke kterým dochází v pozdějších voláních.

Middleware statických souborů je volán brzy v průběhu kanálu tak, aby mohl zpracovat požadavky a předčasně ukončit kanál bez nutnosti procházet zbylými komponentami. Middleware pro statické soubory neposkytuje **žádnou** kontrolu autorizace. Všechny soubory, které poskytuje middleware pro statický soubor, včetně těch v rámci *wwwroot*, jsou veřejně dostupné. Způsob zabezpečení statických souborů naleznete v tématu <xref:fundamentals/static-files>.

Pokud není žádost zpracována middlewarem pro statické soubory, je předána autentizačnímu middlewaru (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), který provádí autentizaci. Autentizace neukončuje předčasně neověřené žádosti. Přestože autentizační middleware autentizuje požadavky, autentizace (a odmítnutí) nastane pouze po výběru specifické stránky Razor nebo MVC kontroleru a akce pomocí MVC.

Následující příklad ukazuje pořadí middlewarů, ve kterém se požadavky na statické soubory zpracovávají middlewarem pro statické soubory před middlewarem pro kompresi odpovědí. Statické soubory se v tomto uspořádání middlewarů nekomprimují. MVC odpovědi z <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> mohou být komprimovány.

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

::: moniker-end

## <a name="use-run-and-map"></a>Metody Use, Run a Map

Konfigurujte HTTP kanál pomocí metod <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> a <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>. Metoda `Use` můžete předčasně ukončit kanál zpracování (tj. v případě, kdy nezavoláte delegáta požadavku `next`). Metoda `Run` je konvence a některé middlewarové komponenty mohou exponovat metodu `Run[Middleware]`, která se spouští na konci kanálu.

Rozšíření <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> jsou používána jako konvence pro větvení kanálu. Metoda `Map` větví kanál požadavků na základě shody se zadanou cestou požadavku. Pokud cesta požadavku začíná danou cestou požadavku, větev se provede.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

V následující tabulce jsou uvedeny požadavky a odpovědi z `http://localhost:1234` pomocí předchozího kódu.

| Žádost             | Odpověď                     |
| ------------------- | ---------------------------- |
| localhost:1234      | Hello from non-Map delegate. |
| localhost: 1234/Map1 | Map Test 1                   |
| localhost:1234/map2 | Map Test 2                   |
| localhost:1234/map3 | Hello from non-Map delegate. |

Při použití `Map` se odpovídající segmenty cesty odeberou z `HttpRequest.Path` a připojí se k `HttpRequest.PathBase` pro každý požadavek.

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> větví kanálu požadavků na základě výsledku daného predikátu. Libovolný predikát typu `Func<HttpContext, bool>` může být použit k mapování požadavků na novou větev kanálu. V následujícím příkladu je použit predikát k detekci přítomnosti proměnné řetězce dotazu (query string) `branch`:

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

V následující tabulce jsou uvedeny požadavky a odpovědi z `http://localhost:1234` pomocí předchozího kódu.

| Žádost                       | Odpověď                     |
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

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a>Integrované middlewary

ASP.NET Core se dodává s následujícími middlewarovými komponenty. Sloupec *Pořadí* obsahuje poznámky o umístění middlewaru v kanálu zpracování požadavků a o podmínkách, za jakých může middleware ukončit zpracování požadavku. Pokud middleware předčasně ukončí zpracování kanálu a zabrání tak dalším, podřízeným middlewarům ve zpracování požadavku, pak je nazýván *terminálním middlewarem*. Další informace o předčasném ukončení kanálu najdete v sekci [Vytvoření kanálu middlewaru pomocí IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder).

| Middleware | Popis | Pořadí |
| ---------- | ----------- | ----- |
| [Autentizace](xref:security/authentication/identity) | Poskytuje podporu ověřování. | Předtím, než je potřeba `HttpContext.User`. Terminál pro zpětná volání OAuth. |
| [Zásady souborů cookie](xref:security/gdpr) | Sleduje souhlas uživatelů pro ukládání osobních údajů a vynucuje minimální standardy pro cookie pole, jako například `secure` a `SameSite`. | Před middlewarem, který vydává cookies. Příklady: Autentizace, relace, MVC (TempData). |
| [CORS](xref:security/cors) | Konfiguruje sdílení prostředků mezi zdroji. | Před komponentami, které používají CORS. |
| [Diagnostika](xref:fundamentals/error-handling) | Několik samostatných middlewarů, které poskytují stránku s výjimkou vývojářů, zpracování výjimek, stránky stavového kódu a výchozí webovou stránku pro nové aplikace. | Před komponentami, které generují chyby. Terminál pro výjimky nebo pro výchozí webovou stránku pro nové aplikace |
| [Přesměrované hlavičky](xref:host-and-deploy/proxy-load-balancer) | Přesměrovává hlavičky skryté proxy serverem do aktuální žádosti. | Před komponentami, které využívají aktualizovaná pole. Příklady: schéma, hostitel, IP adresa klienta, metoda. |
| [Kontroly stavu](xref:host-and-deploy/health-checks) | Kontroluje stav aplikace ASP.NET Core a jejích závislostí, jako je například kontrola dostupnosti databáze. | Terminál, pokud požadavek odpovídá koncovému bodu kontroly stavu. |
| [Přepsání metody HTTP](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | Umožňuje příchozím POST požadavkům přepsat metody. | Před komponentami, které využívají aktualizovanou metodu. |
| [Přesměrování HTTPS](xref:security/enforcing-ssl#require-https) | Přesměrovat všechny požadavky HTTP na HTTPS. | Před komponentami, které využívají adresu URL. |
| [Striktní zabezpečení přenosu HTTP (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Vylepšení zabezpečení – middleware, který přidává speciální hlavičku odpovědi. | Předtím, než se pošlou odpovědi, a potom, co komponenta modifikuje žádost. Příklady: Přesměrované hlavičky, přepisování adres URL. |
| [MVC](xref:mvc/overview) | Zpracovává požadavky pomocí MVC/Razor Pages. | Terminál, pokud požadavek odpovídá trase. |
| [OWIN](xref:fundamentals/owin) | Interoperabilita s aplikacemi, serverem a middlewarem OWIN. | Terminál, pokud OWIN middleware plně zpracuje požadavek. |
| [Ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) | Poskytuje podporu pro ukládání odpovědí do mezipaměti. | Před komponentami, které vyžadují ukládání do mezipaměti. |
| [Komprese odpovědí](xref:performance/response-compression) | Poskytuje podporu pro komprimaci odpovědí. | Před komponentami, které vyžadují komprese. |
| [Lokalizace požadavků](xref:fundamentals/localization) | Poskytuje podporu lokalizace. | Před komponentami citlivými na lokalizaci. |
| [Směrování koncových bodů](xref:fundamentals/routing) | Definuje a omezuje trasy požadavků. | Terminál pro odpovídající trasy. |
| [Relace](xref:fundamentals/app-state) | Poskytuje podporu pro správu uživatelských relací. | Před komponentami, které vyžadují relace. |
| [Statické soubory](xref:fundamentals/static-files) | Poskytuje podporu pro poskytování statických souborů a výpis adresářů. | Terminál, pokud požadavek odpovídá souboru |
| [Přepsání adresy URL](xref:fundamentals/url-rewriting) | Poskytuje podporu pro přepisování adres URL a přesměrování požadavků. | Před komponentami, které využívají adresu URL. |
| [Webové sokety](xref:fundamentals/websockets) | Povolí protokol WebSocket. | Před komponentami, které jsou nutné pro příjem WebSocket požadavků. |

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
