---
title: Middleware ASP.NET Core
author: rick-anderson
description: Další informace o ASP.NET Core middleware a kanál žádosti.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2019
uid: fundamentals/middleware/index
ms.openlocfilehash: 74ecc1fa099d2d33ceb066decc912978da53bfb3
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724460"
---
# <a name="aspnet-core-middleware"></a>Middleware ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)

Middleware je software, který je včleněn do kanálu aplikace a zpracovává požadavky a odpovědi. Každá komponenta:

* Zvolí, zda předá požadavek další komponentě v kanálu.
* Může provádět práci před voláním a po volání další komponenty v kanálu.

Delegáti požadavku se používají k vytvoření kanálu požadavku. Delegáti požadavků zpracovávají každý HTTP požadavek.

Delegáti požadavků jsou nakonfigurováni pomocí rozšiřujících metod <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> a <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>. Jednotliví delegáti požadavků mohou být definováni v jednom řádku jako anonymní metoda (tzv. in-line middlewary), nebo mohou být definováni ve znovupoužitelné třídě. Tyto znovupoužitelné třídy a jednořádkové metody jsou *middlewary*, také označované jako *middlewarové komponenty*. Každá middlewarová komponenta v kanálu požadavku zodpovídá za vyvolání další komponenty v kanálu nebo předčasné ukončení kanálu. Middleware, který předčasně ukončí kanál, je nazýván *terminální middleware*, protože zabraňuje dalšímu middlewaru ve zpracování požadavku.

<xref:migration/http-modules> vysvětluje rozdíl mezi kanály požadavků v ASP.NET Core a ASP.NET 4.x a poskytuje další ukázky middlewaru.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>Vytvoření kanálu middlewaru s IApplicationBuilder

Kanál požadavků ASP.NET Core se skládá z posloupnosti delegátů požadavku a volají se jeden po druhém. Následující diagram znázorňuje tento koncept. Vlákno provádění postupuje po směru černé šipky.

![Vzor zpracování požadavku zobrazujující příchod, zpracování požadavku prostřednictvím tři middlewarů a odpověď opouštějící aplikaci. Každý middleware provádí svou vlastní logiku a předává požadavek dalšímu middlewaru příkazem next(). Po zpracování požadavku třetím middlewarem prochází žádost zpět přes předchozí dva middlewary v opačném pořadí pro další zpracování, které následuje po příkazu next(), předtím, než opustí aplikaci jako odpověď klientovi.](index/_static/request-delegate-pipeline.png)

Každý delegát můžet provádět operace před vyvoláním a po vyvolání dalšího delegáta. Delegáti zpracovávající výjimky by měli být voláni v kanálu co nejdříve, aby mohli zachytit výjimky, ke kterým dochází v pozdějších etapách kanálu.

Nejjednodušší možný aplikace ASP.NET Core nastaví delegáta jedné žádosti, která zpracovává všechny požadavky. Tento případ neobsahuje kanál aktuálního požadavku. Místo toho jednoho anonymní funkce je volána v reakci na každý požadavek HTTP.

[!code-csharp[](index/snapshot/Middleware/Startup.cs?name=snippet1)]

První <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegáta ukončí kanálu.

Zřetězit více požadavek delegátů spolu s <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>. `next` Parametr představuje další delegáta v kanálu. Můžete zkrácenou kanál pomocí *není* volání *Další* parametru. Můžete obvykle provádět akce před i po další delegáta, jak ukazuje následující příklad:

[!code-csharp[](index/snapshot/Chain/Startup.cs?name=snippet1)]

Je-li delegáta není úspěšný žádost o další delegátu, nazývá *zkrácenou kanál žádosti*. Krátký cyklus je často žádoucí, protože tím eliminujete zbytečné práce. Například [Middleware statické soubory](xref:fundamentals/static-files) může fungovat jako *terminálu middleware* tak, že zpracování žádosti o statický soubor a krátký cyklus rest z kanálu. Middleware přidaný do kanálu middleware, který ukončí dalšímu zpracování stále zpracovává kódu po jejich `next.Invoke` příkazy. Nicméně zobrazí následující upozornění o pokusu o zápis, která již byla odeslána odpověď.

> [!WARNING]
> Nevolejte `next.Invoke` po odeslání odpovědi klientovi. Změny <xref:Microsoft.AspNetCore.Http.HttpResponse> po spuštění odpovědi vrátit výjimku. Například změny, jako je nastavení hlaviček a stavovým kódem vyvolat výjimku. Zápis do datové části odpovědi po volání `next`:
>
> * Může způsobit narušení protokolu. Například zápis informace než uvedené `Content-Length`.
> * Může dojít k poškození formát těla zprávy. HTML zápatí například zápis do souboru šablony stylů CSS.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> je užitečné nápovědu k označení, pokud byly odeslány hlavičky nebo text byl zapsán do.

## <a name="order"></a>Objednání

Pořadí, ve kterém middlewarových komponent přidány `Startup.Configure` metoda definuje pořadí, ve kterém jsou vyvolány middlewarových komponent na požadavky a obrácenému pořadí pro odpověď. Pořadí je důležité pro zabezpečení, výkon a funkčnost.

Následující metoda `Startup.Configure` přidává middlewarové komponenty pro běžné scénáře aplikací:

1. Zpracování výjimek a chyb
   * Když aplikace běží ve vývojovém prostředí:
     * Middleware stránky výjimky pro vývojáře (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) hlásí chyby za běhu aplikace.
     * Databáze Middleware chybové stránky (<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*>) sestavy databáze chyby za běhu.
   * Při spuštění aplikace v produkčním prostředí:
     * Middleware obslužné rutiny výjimek (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) zachytí výjimky vyvolané v následujících middlewares.
     * Protokol zabezpečení striktní přenosu (HSTS) Middleware HTTP (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) přidá `Strict-Transport-Security` záhlaví.
1. Middleware přesměrování protokolu HTTPS (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) přesměrovává požadavky HTTP na HTTPS.
1. Middleware statické soubory (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) vrátí statické soubory a zkratům další zpracování požadavku.
1. Zásady middlewaru souboru cookie (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) odpovídá aplikace nařízení Evropské unie obecného Regulation (GDPR).
1. Middleware ověřování (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) pokusí o ověření uživatele, aby jim byl povolen přístup k zabezpečeným prostředkům.
1. Middleware relace (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) vytváří a udržuje stav relace. Pokud aplikace používá stav relace, volejte Middleware relace za zásad middlewaru souboru Cookie a před MVC middlewaru.
1. MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) přidat do kanálu požadavku MVC.

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

Middleware statických souborů je volán brzy v průběhu kanálu tak, aby mohl zpracovat požadavky a předčasně ukončit kanál bez nutnosti procházet zbylými komponentami. Middleware pro statické soubory neposkytuje **žádnou** kontrolu autorizace. Všechny jím obsluhované souboru, včetně těch uvnitř adresáře *wwwroot*, jsou veřejně dostupné. Způsob zabezpečení statických souborů naleznete v tématu <xref:fundamentals/static-files>.

Pokud není žádost zpracována middlewarem pro statické soubory, je předána autentizačnímu middlewaru (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), který provádí autentizaci. Autentizace neukončuje předčasně neověřené žádosti. Přestože autentizační middleware autentizuje požadavky, autentizace (a odmítnutí) nastane pouze po výběru specifické stránky Razor nebo MVC kontroleru a akce pomocí MVC.

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

## <a name="use-run-and-map"></a>Použití, spuštění a mapy

Konfigurace kanálu HTTP pomocí <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, a <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>. `Use` Metoda můžete zkrácenou kanálu (tj. Pokud nebude volat `next` delegáta požadavku). `Run` je konvence, a některé middlewarových komponent může vystavit `Run[Middleware]` metody, které běží na konci kanálu.

<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> rozšíření jsou použity jako konvence pro větvení kanálu. `Map*` větve kanál požadavku na základě shody zadanou cestu požadavku. Pokud cesta požadavku začíná dané cesty, větve je proveden.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs?name=snippet1)]

V následující tabulce jsou uvedeny požadavky a odpovědi z `http://localhost:1234` pomocí předchozího kódu.

| Request             | Odpověď                     |
| ------------------- | ---------------------------- |
| localhost:1234      | Dobrý den ze bez mapování delegáta. |
| localhost:1234/map1 | Mapování Test 1                   |
| localhost:1234/map2 | Mapování testů 2                   |
| localhost:1234/map3 | Dobrý den ze bez mapování delegáta. |

Když `Map` se používá, segmenty cesty odpovídající jsou odebrány z `HttpRequest.Path` a připojený k `HttpRequest.PathBase` pro každý požadavek.

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> větve kanál požadavku na základě výsledku daného predikátu. Žádné predikátu typu `Func<HttpContext, bool>` je možné mapovat požadavky na novou větev kanálu. V následujícím příkladu, predikát slouží k detekci přítomnosti proměnnou s řetězcem dotazu `branch`:

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?name=snippet1)]

V následující tabulce jsou uvedeny požadavky a odpovědi z `http://localhost:1234` pomocí předchozího kódu.

| Request                       | Odpověď                     |
| ----------------------------- | ---------------------------- |
| localhost:1234                | Dobrý den ze bez mapování delegáta. |
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

ASP.NET Core se dodává s následujícími součástmi middlewaru. *Pořadí* sloupec obsahuje poznámky k umístění middleware v kanálu zpracování žádostí a za jakých podmínek middleware může ukončit zpracování požadavku. Je-li middleware zkratům kanál pro zpracování požadavku a zabrání další podřízené middleware zpracování požadavku, nazývá *terminálu middleware*. Další informace o krátký cyklus, najdete v článku [vytvoření kanálu middlewaru s IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) oddílu.

| Middleware | Popis | Objednání |
| ---------- | ----------- | ----- |
| [Ověřování](xref:security/authentication/identity) | Poskytuje podporu ověřování. | Před `HttpContext.User` je potřeba. Terminál pro zpětná volání OAuth. |
| [Zásady souborů cookie](xref:security/gdpr) | Sleduje svolení od uživatelů pro ukládání osobních údajů a vynucuje minimální standardy pro soubor cookie pole, jako například `secure` a `SameSite`. | Před middlewaru, který vydá soubory cookie. Příklady: Ověřování, relace, MVC (TempData). |
| [CORS](xref:security/cors) | Konfiguruje sdílení prostředků různého původu. | Před provedením komponent, které používají CORS. |
| [Zpracování výjimek](xref:fundamentals/error-handling) | Zpracovává výjimky. | Před provedením komponent, které generují chyby. |
| [Přesměrovaná záhlaví](xref:host-and-deploy/proxy-load-balancer) | Předává záhlaví směrovány přes proxy server na aktuální žádost. | Před provedením komponent, které využívají aktualizovanými poli. Příklady: schéma, hostitele, IP adresu klienta, metoda. |
| [Kontrola stavu](xref:host-and-deploy/health-checks) | Kontroluje stav aplikace ASP.NET Core a jeho závislosti, jako je například kontrola dostupnosti databáze. | Terminál, pokud požadavek odpovídá stav vrácení koncového bodu. |
| [Přepsání metody HTTP](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | Umožňuje příchozí požadavek POST k přepsání metody. | Před provedením komponent, které využívají aktualizované metody. |
| [Přesměrování protokolu HTTPS](xref:security/enforcing-ssl#require-https) | Přesměrujte všechny požadavky HTTP na HTTPS. | Před provedením komponent, které využívají adresu URL. |
| [Zabezpečení striktní přenosu HTTP (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Middleware vylepšení zabezpečení, která přidá hlavičku odpovědi speciální | Předtím, než se posílají žádosti a po součásti, které mění žádosti. Příklady: Předané záhlaví, přepisování adres URL. |
| [MVC](xref:mvc/overview) | Zpracovává požadavky pomocí stránky MVC a syntaxe Razor. | Terminál, pokud požadavek odpovídá trase. |
| [OWIN](xref:fundamentals/owin) | Interoperabilita s aplikací OWIN, servery a middlewaru. | Terminál, pokud middlewaru OWIN, který plně zpracuje požadavek. |
| [Ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) | Poskytuje podporu pro ukládání do mezipaměti odpovědi. | Před provedením komponent, které vyžadují ukládání do mezipaměti. |
| [Kompresi odpovědí](xref:performance/response-compression) | Poskytuje podporu pro kompresi odpovědí. | Před provedením komponent, které vyžadují komprese. |
| [Žádost o lokalizaci](xref:fundamentals/localization) | Poskytuje podporu lokalizace. | Před provedením komponent citlivé lokalizace. |
| [Směrování](xref:fundamentals/routing) | Definuje a omezuje žádosti trasy. | Terminál odpovídající trasy. |
| [Relace](xref:fundamentals/app-state) | Poskytuje podporu pro správu uživatelských relací. | Před provedením komponent, které vyžadují relace. |
| [Statické soubory](xref:fundamentals/static-files) | Poskytuje podporu pro poskytování statických souborů a procházení adresářů. | Terminál, pokud požadavek odpovídá souboru. |
| [Přepisování adres URL](xref:fundamentals/url-rewriting) | Poskytuje podporu pro přepis adres URL a přesměrování požadavků. | Před provedením komponent, které využívají adresu URL. |
| [Webové sokety](xref:fundamentals/websockets) | Povolí protokol Websocket. | Před provedením komponent, které jsou nutné, aby přijímal požadavky protokolu WebSocket. |

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
