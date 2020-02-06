---
title: Middleware ASP.NET Core
author: rick-anderson
description: Přečtěte si o ASP.NET Core middlewaru a kanálu požadavků.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2020
uid: fundamentals/middleware/index
ms.openlocfilehash: 47f465c00138acf434c6ec59f757e37361ad97db
ms.sourcegitcommit: 0e21d4f8111743bcb205a2ae0f8e57910c3e8c25
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77034101"
---
# <a name="aspnet-core-middleware"></a>Middleware ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)

Middleware je software, který je včleněn do kanálu aplikace a zpracovává požadavky a odpovědi. Každá komponenta:

* Zvolí, zda předá požadavek další komponentě v kanálu.
* Může provádět práci před voláním a po volání další komponenty v kanálu.

Delegáti požadavku se používají k vytvoření kanálu požadavku. Delegáti požadavků zpracovávají každý HTTP požadavek.

Delegáti žádostí jsou nakonfigurováni pomocí metod rozšíření <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>a <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>. Jednotliví delegáti požadavků mohou být definováni v jednom řádku jako anonymní metoda (tzv. in-line middlewary), nebo mohou být definováni ve znovupoužitelné třídě. Tyto opakovaně použitelné třídy a vložené anonymní metody jsou *middleware*, označované také jako *komponenty middlewaru*. Každá middlewarová komponenta v kanálu požadavku zodpovídá za vyvolání další komponenty v kanálu nebo předčasné ukončení kanálu. Když jsou krátkodobé okruhy middleware, nazývá se *middleware terminálu* , protože zabrání dalšímu middlewaru ve zpracování žádosti.

<xref:migration/http-modules> vysvětluje rozdíl mezi kanály požadavků v ASP.NET Core a ASP.NET 4. x a poskytuje další ukázky middlewaru.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>Vytvoření kanálu middlewaru pomocí IApplicationBuilder

Kanál požadavků ASP.NET Core se skládá z posloupnosti delegátů požadavku a volají se jeden po druhém. Následující diagram znázorňuje tento koncept. Vlákno provádění postupuje po směru černé šipky.

![Vzor zpracování požadavku zobrazujující příchod, zpracování požadavku prostřednictvím tři middlewarů a odpověď opouštějící aplikaci. Každý middleware provádí svou vlastní logiku a předává požadavek dalšímu middlewaru příkazem next(). Po zpracování požadavku třetím middlewarem prochází žádost zpět přes předchozí dva middlewary v opačném pořadí pro další zpracování, které následuje po příkazu next(), předtím, než opustí aplikaci jako odpověď klientovi.](index/_static/request-delegate-pipeline.png)

Každý delegát můžet provádět operace před vyvoláním a po vyvolání dalšího delegáta. Delegáti zpracovávající výjimky by měli být voláni v kanálu co nejdříve, aby mohli zachytit výjimky, ke kterým dochází v pozdějších etapách kanálu.

Nejjednodušší možná aplikace ASP.NET Core nastavuje jediného delegáta požadavků, který zpracovává všechny požadavky. Tento případ ve skutečnosti neobsahuje kanál požadavků. Místo toho se volá jediná anonymní funkce v reakci na každý požadavek HTTP.

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

Řetězení více požadavků delegátů společně s <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>. Parametr `next` představuje dalšího delegáta v kanálu. Kanál můžete pro krátké okruhy vymezit tím *, že nevoláte* *Další* parametr. Obvykle můžete provádět akce před vykonáním i po vykonání dalšího delegáta, jak ukazuje následující příklad:

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

Když delegát neprojde požadavek na dalšího delegáta, nazývá se to *krátkodobý kanál žádosti*. Předčasné ukončení kanálu je často žádoucí, protože eliminuje zbytečně vykonanou práci. Například [middleware statických souborů](xref:fundamentals/static-files) může fungovat jako *middleware terminálu* tím, že zpracovává požadavek na statický soubor a krátký okruh zbývajících částí kanálu. Middleware přidané do kanálu předtím, než middleware, který ukončí další zpracování, stále zpracovává kód po jejich `next.Invoke`ch příkazech. V takovém případě se však zobrazí varování před pokusem o zápis do odpovědi, která se už odeslala.

> [!WARNING]
> Po odeslání odpovědi klientovi Nevolejte `next.Invoke`. Změny <xref:Microsoft.AspNetCore.Http.HttpResponse> po zahájení reakce vyvolávají výjimku. Výjimku vyvolají například změny, jako jsou nastavení hlaviček a stavového kódu. Zápis do těla odpovědi po volání `next`:
>
> * Může způsobit porušení protokolu. Například zápis více než uvedených `Content-Length`.
> * Může porušit formát těla zprávy. Například zápis zápatí HTML do souboru CSS.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> je užitečnou nápovědou, která označuje, zda byla odeslána hlavička nebo byl text napsán do.

Delegáti <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> neobdrží parametr `next`. První delegát `Run` vždy terminálu a ukončí kanál. `Run` je konvence. Některé komponenty middlewaru můžou vystavovat `Run[Middleware]` metody, které běží na konci kanálu:

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]

V předchozím příkladu `Run` delegát zapisuje `"Hello from 2nd delegate."` na odpověď a pak kanál ukončí. Pokud je za delegáta `Run` přidán další `Use` nebo `Run` delegát, není voláno.

<a name="order"></a>

## <a name="middleware-order"></a>Pořadí middlewaru

Pořadí, v jakém jsou komponenty middlewaru přidány v metodě `Startup.Configure` definuje pořadí, ve kterém jsou komponenty middleware vyvolány na žádostech a obráceném pořadí pro odpověď. Pořadí je **důležité** pro zabezpečení, výkon a funkčnost.

Následující metoda `Startup.Configure` přidá do doporučeného pořadí součásti middlewaru související se zabezpečením:

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

V předchozím kódu:

* Middleware, které se nepřidaly při vytváření nové webové aplikace s [jednotlivými účty uživatele](xref:security/authentication/identity) , jsou zakomentovány.
* Ne každý middleware potřebuje přejít v tomto přesném pořadí, ale mnoho do něj. Například `UseCors`, `UseAuthentication`a `UseAuthorization` musí jít v uvedeném pořadí.

Následující metoda `Startup.Configure` přidává komponenty middlewaru pro běžné scénáře aplikací:

1. Zpracování výjimek a chyb
   * Když aplikace běží ve vývojovém prostředí:
     * Middleware stránky s výjimkou pro vývojáře (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) hlásí chyby modulu runtime aplikace.
     * Zpráva middlewaru chybové stránky databáze oznamuje chyby běhového běhu databáze.
   * Když aplikace běží v produkčním prostředí:
     * Middleware obslužné rutiny výjimek (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) zachytává výjimky vyvolané v následujících middlewarech.
     * Middleware HSTS (HTTP Strict Transport Security Protocol) (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) přidává `Strict-Transport-Security` hlavičku.
1. Middleware pro přesměrování HTTPS (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) přesměruje požadavky HTTP na HTTPS.
1. Middleware pro statický soubor (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) vrátí statické soubory a další zpracování žádostí o krátkodobé okruhy.
1. Middleware zásad souborů cookie (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) vyhovuje předpisům platným v EU Obecné nařízení o ochraně osobních údajů (GDPR).
1. Směrování middlewaru (`UseRouting`) na požadavky směrování.
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

V předchozím příkladu kódu je každá metoda rozšíření middleware vystavena <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> prostřednictvím <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName>ho oboru názvů.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> je první součást middleware přidaná do kanálu. Z toho důvodu zachytává middleware pro zpracování výjimek všechny výjimky, ke kterým dochází v pozdějších voláních.

Middleware statických souborů je volán brzy v průběhu kanálu tak, aby mohl zpracovat požadavky a předčasně ukončit kanál bez nutnosti procházet zbylými komponentami. Middleware statických souborů neposkytuje **žádné** autorizační kontroly. Všechny soubory, které poskytuje middleware pro statický soubor, včetně těch v rámci *wwwroot*, jsou veřejně dostupné. Přístup k zabezpečení statických souborů naleznete v tématu <xref:fundamentals/static-files>.

Pokud požadavek nezpracovává middleware pro statický soubor, je předán do middleware ověřování (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), který provádí ověřování. Autentizace neukončuje předčasně neověřené žádosti. Přestože autentizační middleware autentizuje požadavky, autentizace (a odmítnutí) nastane pouze po výběru specifické stránky Razor nebo MVC kontroleru a akce pomocí MVC.

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

V případě aplikací s jednou stránkou se middleware SPA <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles*> obvykle nachází v kanálu middleware jako poslední. Middleware SPA se vyskytuje jako poslední:

* Aby všechny ostatní middleware mohly reagovat na vyhovující požadavky jako první.
* Povolí spuštění jednostránkové se směrováním na straně klienta pro všechny trasy, které serverová aplikace nerozpoznala.

Další podrobnosti o aplikacích s jednou stránkou naleznete v příručkách pro [reakce](xref:spa/react) a [na](xref: client-side/spa/angular) obrazové šablony.

## <a name="branch-the-middleware-pipeline"></a>Větvení kanálu middlewaru

rozšíření <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> se používají jako konvence pro větvení kanálu. `Map` větví kanálu požadavků na základě shody dané cesty požadavku. Pokud cesta požadavku začíná danou cestou požadavku, větev se provede.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

V následující tabulce jsou uvedeny požadavky a odpovědi z `http://localhost:1234` pomocí předchozího kódu.

| Žádost             | Odpověď                     |
| ------------------- | ---------------------------- |
| localhost:1234      | Hello from non-Map delegate. |
| localhost: 1234/Map1 | Map Test 1                   |
| localhost:1234/map2 | Map Test 2                   |
| localhost:1234/map3 | Hello from non-Map delegate. |

Při použití `Map` se odpovídající segmenty cesty odeberou z `HttpRequest.Path` a připojí se k `HttpRequest.PathBase` pro každý požadavek.

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

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> větví kanálu požadavků na základě výsledku daného predikátu. K mapování požadavků na novou větev kanálu lze použít jakýkoli predikát typu `Func<HttpContext, bool>`. V následujícím příkladu se k detekci přítomnosti proměnné řetězce dotazu používá predikát `branch`:

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

V následující tabulce jsou uvedeny požadavky a odpovědi z `http://localhost:1234` pomocí předchozího kódu:

| Žádost                       | Odpověď                     |
| ----------------------------- | ---------------------------- |
| localhost:1234                | Hello from non-Map delegate. |
| localhost:1234/?branch=master | Branch used = master         |

<xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen*> také větví kanálu požadavků na základě výsledku daného predikátu. Na rozdíl od `MapWhen`tato větev se znovu připojí k hlavnímu kanálu, pokud má krátký okruh nebo obsahuje middleware terminálu:

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=23-24)]

V předchozím příkladu odpověď "Hello z hlavního kanálu". je napsán pro všechny požadavky. Pokud požadavek obsahuje proměnnou řetězce dotazu `branch`, je jeho hodnota protokolována před opětovným připojením k hlavnímu kanálu.

## <a name="built-in-middleware"></a>Integrované middlewary

ASP.NET Core se dodává s následujícími middlewarovými komponenty. Sloupec *Order* poskytuje poznámky k umístění middlewaru v kanálu zpracování požadavků a za jakých podmínek může middleware ukončit zpracování požadavků. Když middleware middleware vytvoří kanál zpracování požadavků a zabrání dalšímu podřízenému middlewaru ve zpracování žádosti, nazývá se *middleware terminálu*. Další informace o krátkodobém okruhu najdete v části [vytvoření kanálu middlewaru pomocí IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) .

| Middleware | Popis | Objednání |
| ---------- | ----------- | ----- |
| [Ověřování](xref:security/authentication/identity) | Poskytuje podporu ověřování. | Před `HttpContext.User` je potřeba. Terminál pro zpětná volání OAuth. |
| [Autorizace](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*) | Poskytuje podporu autorizace. | Hned po ověřovacím middlewaru. |
| [Zásady souborů cookie](xref:security/gdpr) | Sleduje souhlas uživatelů při ukládání osobních údajů a vynutila minimální standardy pro pole cookie, jako je například `secure` a `SameSite`. | Před middlewarem, který vydává cookies. Příklady: ověřování, relace, MVC (TempData). |
| [CORS](xref:security/cors) | Konfiguruje sdílení prostředků mezi zdroji. | Před komponentami, které používají CORS. |
| [Diagnostika](xref:fundamentals/error-handling) | Několik samostatných middlewarů, které poskytují stránku s výjimkou vývojářů, zpracování výjimek, stránky stavového kódu a výchozí webovou stránku pro nové aplikace. | Před komponentami, které generují chyby. Terminál pro výjimky nebo pro výchozí webovou stránku pro nové aplikace |
| [Předávaná záhlaví](xref:host-and-deploy/proxy-load-balancer) | Přesměrovává hlavičky skryté proxy serverem do aktuální žádosti. | Před komponentami, které využívají aktualizovaná pole. Příklady: schéma, hostitel, IP adresa klienta, metoda. |
| [Kontroly stavu](xref:host-and-deploy/health-checks) | Kontroluje stav aplikace ASP.NET Core a jejích závislostí, jako je například kontrola dostupnosti databáze. | Terminál, pokud požadavek odpovídá koncovému bodu kontroly stavu. |
| [Šíření hlaviček](xref:fundamentals/http-requests#header-propagation-middleware) | Šíří hlavičky protokolu HTTP z příchozího požadavku do odchozích požadavků klienta HTTP. |
| [Přepsání metody HTTP](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | Umožňuje příchozím POST požadavkům přepsat metody. | Před komponentami, které využívají aktualizovanou metodu. |
| [Přesměrování HTTPS](xref:security/enforcing-ssl#require-https) | Přesměrovat všechny požadavky HTTP na HTTPS. | Před komponentami, které využívají adresu URL. |
| [HTTP Strict Transport Security (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Vylepšení zabezpečení – middleware, který přidává speciální hlavičku odpovědi. | Předtím, než se pošlou odpovědi, a potom, co komponenta modifikuje žádost. Příklady: předávané hlavičky, přepis adresy URL. |
| [MVC](xref:mvc/overview) | Zpracovává požadavky pomocí MVC/Razor Pages. | Terminál, pokud požadavek odpovídá trase. |
| [OWIN](xref:fundamentals/owin) | Interoperabilita s aplikacemi, serverem a middlewarem OWIN. | Terminál, pokud OWIN middleware plně zpracuje požadavek. |
| [Ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) | Poskytuje podporu pro ukládání odpovědí do mezipaměti. | Před komponentami, které vyžadují ukládání do mezipaměti. |
| [Komprese odezvy](xref:performance/response-compression) | Poskytuje podporu pro komprimaci odpovědí. | Před komponentami, které vyžadují komprese. |
| [Lokalizace žádosti](xref:fundamentals/localization) | Poskytuje podporu lokalizace. | Před komponentami citlivými na lokalizaci. |
| [Směrování koncových bodů](xref:fundamentals/routing) | Definuje a omezuje trasy požadavků. | Terminál pro odpovídající trasy. |
| [OVĚŘOVÁNÍ](xref:Microsoft.AspNetCore.Builder.SpaApplicationBuilderExtensions.UseSpa*) | Zpracovává všechny požadavky od tohoto bodu v řetězci middleware vrácením výchozí stránky pro aplikaci s jednou stránkou (SPA). | V řetězci pozdě, aby měl přednost další middleware pro obsluhu statických souborů, akcí MVC atd.|
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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)

Middleware je software, který je včleněn do kanálu aplikace a zpracovává požadavky a odpovědi. Každá komponenta:

* Zvolí, zda předá požadavek další komponentě v kanálu.
* Může provádět práci před voláním a po volání další komponenty v kanálu.

Delegáti požadavku se používají k vytvoření kanálu požadavku. Delegáti požadavků zpracovávají každý HTTP požadavek.

Delegáti žádostí jsou nakonfigurováni pomocí metod rozšíření <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>a <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>. Jednotliví delegáti požadavků mohou být definováni v jednom řádku jako anonymní metoda (tzv. in-line middlewary), nebo mohou být definováni ve znovupoužitelné třídě. Tyto opakovaně použitelné třídy a vložené anonymní metody jsou *middleware*, označované také jako *komponenty middlewaru*. Každá middlewarová komponenta v kanálu požadavku zodpovídá za vyvolání další komponenty v kanálu nebo předčasné ukončení kanálu. Když jsou krátkodobé okruhy middleware, nazývá se *middleware terminálu* , protože zabrání dalšímu middlewaru ve zpracování žádosti.

<xref:migration/http-modules> vysvětluje rozdíl mezi kanály požadavků v ASP.NET Core a ASP.NET 4. x a poskytuje další ukázky middlewaru.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>Vytvoření kanálu middlewaru pomocí IApplicationBuilder

Kanál požadavků ASP.NET Core se skládá z posloupnosti delegátů požadavku a volají se jeden po druhém. Následující diagram znázorňuje tento koncept. Vlákno provádění postupuje po směru černé šipky.

![Vzor zpracování požadavku zobrazujující příchod, zpracování požadavku prostřednictvím tři middlewarů a odpověď opouštějící aplikaci. Každý middleware provádí svou vlastní logiku a předává požadavek dalšímu middlewaru příkazem next(). Po zpracování požadavku třetím middlewarem prochází žádost zpět přes předchozí dva middlewary v opačném pořadí pro další zpracování, které následuje po příkazu next(), předtím, než opustí aplikaci jako odpověď klientovi.](index/_static/request-delegate-pipeline.png)

Každý delegát můžet provádět operace před vyvoláním a po vyvolání dalšího delegáta. Delegáti zpracovávající výjimky by měli být voláni v kanálu co nejdříve, aby mohli zachytit výjimky, ke kterým dochází v pozdějších etapách kanálu.

Nejjednodušší možná aplikace ASP.NET Core nastavuje jediného delegáta požadavků, který zpracovává všechny požadavky. Tento případ ve skutečnosti neobsahuje kanál požadavků. Místo toho se volá jediná anonymní funkce v reakci na každý požadavek HTTP.

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

První <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegát ukončí kanál.

Řetězení více požadavků delegátů společně s <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>. Parametr `next` představuje dalšího delegáta v kanálu. Kanál můžete pro krátké okruhy vymezit tím *, že nevoláte* *Další* parametr. Obvykle můžete provádět akce před vykonáním i po vykonání dalšího delegáta, jak ukazuje následující příklad:

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

Když delegát neprojde požadavek na dalšího delegáta, nazývá se to *krátkodobý kanál žádosti*. Předčasné ukončení kanálu je často žádoucí, protože eliminuje zbytečně vykonanou práci. Například [middleware statických souborů](xref:fundamentals/static-files) může fungovat jako *middleware terminálu* tím, že zpracovává požadavek na statický soubor a krátký okruh zbývajících částí kanálu. Middleware přidané do kanálu předtím, než middleware, který ukončí další zpracování, stále zpracovává kód po jejich `next.Invoke`ch příkazech. V takovém případě se však zobrazí varování před pokusem o zápis do odpovědi, která se už odeslala.

> [!WARNING]
> Po odeslání odpovědi klientovi Nevolejte `next.Invoke`. Změny <xref:Microsoft.AspNetCore.Http.HttpResponse> po zahájení reakce vyvolávají výjimku. Výjimku vyvolají například změny, jako jsou nastavení hlaviček a stavového kódu. Zápis do těla odpovědi po volání `next`:
>
> * Může způsobit porušení protokolu. Například zápis více než uvedených `Content-Length`.
> * Může porušit formát těla zprávy. Například zápis zápatí HTML do souboru CSS.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> je užitečnou nápovědou, která označuje, zda byla odeslána hlavička nebo byl text napsán do.

<a name="order"></a>

## <a name="middleware-order"></a>Pořadí middlewaru

Pořadí, v jakém jsou komponenty middlewaru přidány v metodě `Startup.Configure` definuje pořadí, ve kterém jsou komponenty middleware vyvolány na žádostech a obráceném pořadí pro odpověď. Pořadí je **důležité** pro zabezpečení, výkon a funkčnost.

Následující metoda `Startup.Configure` přidá do doporučeného pořadí součásti middlewaru související se zabezpečením:

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

V předchozím kódu:

* Middleware, které se nepřidaly při vytváření nové webové aplikace s [jednotlivými účty uživatele](xref:security/authentication/identity) , jsou zakomentovány.
* Ne každý middleware potřebuje přejít v tomto přesném pořadí, ale mnoho do něj. Například `UseCors` a `UseAuthentication` musí jít v uvedeném pořadí.

Následující metoda `Startup.Configure` přidává komponenty middlewaru pro běžné scénáře aplikací:

1. Zpracování výjimek a chyb
   * Když aplikace běží ve vývojovém prostředí:
     * Middleware stránky s výjimkou pro vývojáře (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) hlásí chyby modulu runtime aplikace.
     * Middleware chybových stránek databáze (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) oznamuje chyby běhového běhu databáze.
   * Když aplikace běží v produkčním prostředí:
     * Middleware obslužné rutiny výjimek (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) zachytává výjimky vyvolané v následujících middlewarech.
     * Middleware HSTS (HTTP Strict Transport Security Protocol) (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) přidává `Strict-Transport-Security` hlavičku.
1. Middleware pro přesměrování HTTPS (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) přesměruje požadavky HTTP na HTTPS.
1. Middleware pro statický soubor (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) vrátí statické soubory a další zpracování žádostí o krátkodobé okruhy.
1. Middleware zásad souborů cookie (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) vyhovuje předpisům platným v EU Obecné nařízení o ochraně osobních údajů (GDPR).
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

V předchozím příkladu kódu je každá metoda rozšíření middleware vystavena <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> prostřednictvím <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName>ho oboru názvů.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> je první součást middleware přidaná do kanálu. Z toho důvodu zachytává middleware pro zpracování výjimek všechny výjimky, ke kterým dochází v pozdějších voláních.

Middleware statických souborů je volán brzy v průběhu kanálu tak, aby mohl zpracovat požadavky a předčasně ukončit kanál bez nutnosti procházet zbylými komponentami. Middleware statických souborů neposkytuje **žádné** autorizační kontroly. Všechny soubory, které poskytuje middleware pro statický soubor, včetně těch v rámci *wwwroot*, jsou veřejně dostupné. Přístup k zabezpečení statických souborů naleznete v tématu <xref:fundamentals/static-files>.

Pokud požadavek nezpracovává middleware pro statický soubor, je předán do middleware ověřování (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), který provádí ověřování. Autentizace neukončuje předčasně neověřené žádosti. Přestože autentizační middleware autentizuje požadavky, autentizace (a odmítnutí) nastane pouze po výběru specifické stránky Razor nebo MVC kontroleru a akce pomocí MVC.

Následující příklad ukazuje pořadí middlewarů, ve kterém se požadavky na statické soubory zpracovávají middlewarem pro statické soubory před middlewarem pro kompresi odpovědí. Statické soubory se v tomto uspořádání middlewarů nekomprimují. Odpovědi MVC z <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> lze zkomprimovat.

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a>Metody Use, Run a Map

Nakonfigurujte kanál HTTP pomocí <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>a <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>. Metoda `Use` může vytvořit krátký okruh kanálu (tj. Pokud nevolá delegáta žádosti `next`). `Run` je konvence a některé komponenty middlewaru můžou vystavovat `Run[Middleware]` metody, které běží na konci kanálu.

rozšíření <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> se používají jako konvence pro větvení kanálu. `Map` větví kanálu požadavků na základě shody dané cesty požadavku. Pokud cesta požadavku začíná danou cestou požadavku, větev se provede.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

V následující tabulce jsou uvedeny požadavky a odpovědi z `http://localhost:1234` pomocí předchozího kódu.

| Žádost             | Odpověď                     |
| ------------------- | ---------------------------- |
| localhost:1234      | Hello from non-Map delegate. |
| localhost: 1234/Map1 | Map Test 1                   |
| localhost:1234/map2 | Map Test 2                   |
| localhost:1234/map3 | Hello from non-Map delegate. |

Při použití `Map` se odpovídající segmenty cesty odeberou z `HttpRequest.Path` a připojí se k `HttpRequest.PathBase` pro každý požadavek.

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> větví kanálu požadavků na základě výsledku daného predikátu. K mapování požadavků na novou větev kanálu lze použít jakýkoli predikát typu `Func<HttpContext, bool>`. V následujícím příkladu se k detekci přítomnosti proměnné řetězce dotazu používá predikát `branch`:

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

`Map` může také odpovídat více segmentům najednou:

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a>Integrované middlewary

ASP.NET Core se dodává s následujícími middlewarovými komponenty. Sloupec *Order* poskytuje poznámky k umístění middlewaru v kanálu zpracování požadavků a za jakých podmínek může middleware ukončit zpracování požadavků. Když middleware middleware vytvoří kanál zpracování požadavků a zabrání dalšímu podřízenému middlewaru ve zpracování žádosti, nazývá se *middleware terminálu*. Další informace o krátkodobém okruhu najdete v části [vytvoření kanálu middlewaru pomocí IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) .

| Middleware | Popis | Objednání |
| ---------- | ----------- | ----- |
| [Ověřování](xref:security/authentication/identity) | Poskytuje podporu ověřování. | Před `HttpContext.User` je potřeba. Terminál pro zpětná volání OAuth. |
| [Zásady souborů cookie](xref:security/gdpr) | Sleduje souhlas uživatelů při ukládání osobních údajů a vynutila minimální standardy pro pole cookie, jako je například `secure` a `SameSite`. | Před middlewarem, který vydává cookies. Příklady: ověřování, relace, MVC (TempData). |
| [CORS](xref:security/cors) | Konfiguruje sdílení prostředků mezi zdroji. | Před komponentami, které používají CORS. |
| [Diagnostika](xref:fundamentals/error-handling) | Několik samostatných middlewarů, které poskytují stránku s výjimkou vývojářů, zpracování výjimek, stránky stavového kódu a výchozí webovou stránku pro nové aplikace. | Před komponentami, které generují chyby. Terminál pro výjimky nebo pro výchozí webovou stránku pro nové aplikace |
| [Předávaná záhlaví](xref:host-and-deploy/proxy-load-balancer) | Přesměrovává hlavičky skryté proxy serverem do aktuální žádosti. | Před komponentami, které využívají aktualizovaná pole. Příklady: schéma, hostitel, IP adresa klienta, metoda. |
| [Kontroly stavu](xref:host-and-deploy/health-checks) | Kontroluje stav aplikace ASP.NET Core a jejích závislostí, jako je například kontrola dostupnosti databáze. | Terminál, pokud požadavek odpovídá koncovému bodu kontroly stavu. |
| [Přepsání metody HTTP](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | Umožňuje příchozím POST požadavkům přepsat metody. | Před komponentami, které využívají aktualizovanou metodu. |
| [Přesměrování HTTPS](xref:security/enforcing-ssl#require-https) | Přesměrovat všechny požadavky HTTP na HTTPS. | Před komponentami, které využívají adresu URL. |
| [HTTP Strict Transport Security (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Vylepšení zabezpečení – middleware, který přidává speciální hlavičku odpovědi. | Předtím, než se pošlou odpovědi, a potom, co komponenta modifikuje žádost. Příklady: předávané hlavičky, přepis adresy URL. |
| [MVC](xref:mvc/overview) | Zpracovává požadavky pomocí MVC/Razor Pages. | Terminál, pokud požadavek odpovídá trase. |
| [OWIN](xref:fundamentals/owin) | Interoperabilita s aplikacemi, serverem a middlewarem OWIN. | Terminál, pokud OWIN middleware plně zpracuje požadavek. |
| [Ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) | Poskytuje podporu pro ukládání odpovědí do mezipaměti. | Před komponentami, které vyžadují ukládání do mezipaměti. |
| [Komprese odezvy](xref:performance/response-compression) | Poskytuje podporu pro komprimaci odpovědí. | Před komponentami, které vyžadují komprese. |
| [Lokalizace žádosti](xref:fundamentals/localization) | Poskytuje podporu lokalizace. | Před komponentami citlivými na lokalizaci. |
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

::: moniker-end
