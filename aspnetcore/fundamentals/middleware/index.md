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

Nejjednodušší možná ASP.NET Core aplikace nastaví jediného delegáta žádosti, který zpracovává všechny požadavky. Tento případ nezahrnuje skutečný kanál žádostí. Místo toho je volána jedna anonymní funkce v reakci na každý požadavek HTTP.

[!code-csharp[](index/snapshot/Middleware/Startup.cs?name=snippet1)]

První <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegát ukončí kanál.

Řetězení více požadavků delegátů <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>společně s. `next` Parametr představuje dalšího delegáta v kanálu. Kanál můžete pro krátké okruhy vymezit tím, že *nevoláte* *Další* parametr. Obvykle můžete provádět akce před i po dalším delegátu, jak ukazuje následující příklad:

[!code-csharp[](index/snapshot/Chain/Startup.cs?name=snippet1)]

Když delegát neprojde požadavek na dalšího delegáta, nazývá se to *krátkodobý kanál žádosti*. Krátkodobé okruhy jsou často žádoucí, protože brání zbytečné práci. Například middleware [statických souborů](xref:fundamentals/static-files) může fungovat jako *middleware terminálu* tím, že zpracovává požadavek na statický soubor a krátký okruh zbývajících částí kanálu. Middleware přidané do kanálu předtím, než middleware, který ukončí další zpracování, stále zpracovává `next.Invoke` kód po svých příkazech. Přečtěte si ale následující upozornění týkající se pokusu o zápis do odpovědi, která již byla odeslána.

> [!WARNING]
> Nevolejte `next.Invoke` po odeslání odpovědi klientovi. <xref:Microsoft.AspNetCore.Http.HttpResponse> Změny poté, co odpověď začala, vyvolávají výjimku. Například změny jako hlavičky nastavení a stavový kód vyvolávají výjimku. Zápis do těla odpovědi po volání `next`:
>
> * Může způsobit narušení protokolu. Například zápis více než uvedené `Content-Length`.
> * Může poškodit formát textu. Například zápis zápatí HTML do souboru CSS.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*>je užitečnou nápovědou, která označuje, zda byla odeslána hlavička nebo zda byl text napsán do.

## <a name="order"></a>Objednání

Pořadí, v jakém jsou komponenty middleware `Startup.Configure` přidány v metodě, definuje pořadí, ve kterém jsou komponenty middleware vyvolány na žádostech a obráceném pořadí pro odpověď. Pořadí je důležité pro zabezpečení, výkon a funkčnost.

Následující metoda `Startup.Configure` přidává middlewarové komponenty pro běžné scénáře aplikací:

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

V předchozím ukázkovém kódu je každá middlewarová rozšířující metoda vystavena na rozhraní <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> prostřednictvím jmenného prostoru <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName>.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> je první middlewarová komponenta přidaná do kanálu. Z toho důvodu zachytává middleware pro zpracování výjimek všechny výjimky, ke kterým dochází v pozdějších voláních.

Middleware statických souborů je volán brzy v průběhu kanálu tak, aby mohl zpracovat požadavky a předčasně ukončit kanál bez nutnosti procházet zbylými komponentami. Middleware pro statické soubory neposkytuje **žádnou** kontrolu autorizace. Všechny jím obsluhované souboru, včetně těch uvnitř adresáře *wwwroot*, jsou veřejně dostupné. Způsob zabezpečení statických souborů naleznete v tématu <xref:fundamentals/static-files>.

Pokud není žádost zpracována middlewarem pro statické soubory, je předána autentizačnímu middlewaru (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), který provádí autentizaci. Autentizace neukončuje předčasně neověřené žádosti. Přestože autentizační middleware autentizuje požadavky, autentizace (a odmítnutí) nastane pouze po výběru specifické stránky Razor nebo MVC kontroleru a akce pomocí MVC.

Následující příklad ukazuje pořadí middlewaru, kde požadavky na statické soubory jsou zpracovávány pomocí middlewaru statického souboru před použitím middlewaru pro komprimaci odezvy. Statické soubory nejsou s tímto pořadím middlewaru komprimovány. Odpovědi MVC z <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> lze komprimovat.

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files not compressed by Static File Middleware.
    app.UseStaticFiles();
    app.UseResponseCompression();
    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a>Použití, spuštění a mapování

Nakonfigurujte kanál HTTP pomocí <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>a <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>. Metoda může vytvořit krátký okruh kanálu (tj. Pokud `next` nevolá delegáta žádosti). `Use` `Run`je konvencí a některé komponenty middlewaru můžou vystavovat `Run[Middleware]` metody, které běží na konci kanálu.

<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>rozšíření se používají jako konvence pro větvení kanálu. `Map`rozvětvení kanálu požadavků na základě shody dané cesty požadavku. Pokud cesta k požadavku začíná danou cestou, je větev spuštěná.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs?name=snippet1)]

V následující tabulce jsou uvedeny žádosti a odpovědi z `http://localhost:1234` použití předchozího kódu.

| Request             | Odpověď                     |
| ------------------- | ---------------------------- |
| localhost:1234      | Hello z delegáta bez mapy. |
| localhost: 1234/Map1 | Mapování testu 1                   |
| localhost:1234/map2 | Mapovat test 2                   |
| localhost:1234/map3 | Hello z delegáta bez mapy. |

Při `Map` použití se odpovídající segmenty cesty odeberou z `HttpRequest.Path` a připojí se k `HttpRequest.PathBase` pro každý požadavek.

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>rozvětvení kanálu požadavků na základě výsledku daného predikátu. K mapování požadavků na `Func<HttpContext, bool>` novou větev kanálu lze použít jakýkoli predikát typu. V následujícím příkladu se k detekci přítomnosti proměnné `branch`řetězce dotazu používá predikát:

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?name=snippet1)]

V následující tabulce jsou uvedeny žádosti a odpovědi z `http://localhost:1234` použití předchozího kódu.

| Request                       | Odpověď                     |
| ----------------------------- | ---------------------------- |
| localhost:1234                | Hello z delegáta bez mapy. |
| localhost:1234/?branch=master | Použitá větev = hlavní         |

`Map`podporuje vnořování, například:

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

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?name=snippet1&highlight=13)]

## <a name="built-in-middleware"></a>Vestavěný middleware

ASP.NET Core se dodává s následujícími součástmi middlewaru. Sloupec *Order* poskytuje poznámky k umístění middlewaru v kanálu zpracování požadavků a za jakých podmínek může middleware ukončit zpracování požadavků. Když middleware middleware vytvoří kanál zpracování požadavků a zabrání dalšímu podřízenému middlewaru ve zpracování žádosti, nazývá se *middleware terminálu*. Další informace o krátkodobém okruhu najdete v části [vytvoření kanálu middlewaru pomocí IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) .

| Middleware | Popis | Objednání |
| ---------- | ----------- | ----- |
| [Ověřování](xref:security/authentication/identity) | Poskytuje podporu ověřování. | Předtím `HttpContext.User` , než je potřeba. Terminál pro zpětná volání OAuth. |
| [Zásady souborů cookie](xref:security/gdpr) | Sleduje souhlas uživatelů při ukládání osobních údajů a vynutila minimální standardy pro pole cookie, jako jsou `secure` a `SameSite`. | Před middlewarem, který vydává soubory cookie. Příklady: Ověřování, relace, MVC (TempData). |
| [CORS](xref:security/cors) | Konfiguruje sdílení prostředků mezi zdroji. | Před komponenty, které používají CORS. |
| [Zpracování výjimek](xref:fundamentals/error-handling) | Zpracovává výjimky. | Před komponenty, které generují chyby. |
| [Předávaná záhlaví](xref:host-and-deploy/proxy-load-balancer) | Přepošle hlavičky proxy na aktuální požadavek. | Před komponenty, které používají aktualizované pole. Příklady: schéma, hostitel, IP adresa klienta, metoda. |
| [Kontroly stavu](xref:host-and-deploy/health-checks) | Kontroluje stav aplikace ASP.NET Core a jejích závislostí, jako je například Kontrola dostupnosti databáze. | Terminál, je-li požadavek shodný s koncovým bodem kontroly stavu. |
| [Přepsání metody HTTP](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | Umožňuje příchozí žádosti POST přepsat metodu. | Před komponenty, které používají aktualizovanou metodu. |
| [Přesměrování HTTPS](xref:security/enforcing-ssl#require-https) | Přesměrovat všechny požadavky HTTP na HTTPS. | Před komponenty, které používají adresu URL. |
| [HTTP Strict Transport Security (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Vylepšení zabezpečení – middleware, který přidává speciální hlavičku odpovědi. | Před odesláním odpovědí a po součástech, které upravují požadavky. Příklady: Předávaná záhlaví, přepisování adresy URL. |
| [MVC](xref:mvc/overview) | Zpracovává požadavky pomocí MVC/Razor Pages. | Terminál, pokud požadavek odpovídá trase. |
| [OWIN](xref:fundamentals/owin) | Interoperabilita s aplikacemi, servery a middlewarem založeným na OWIN | Terminál, pokud middleware OWIN plně zpracovává požadavek. |
| [Ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) | Poskytuje podporu pro ukládání odpovědí do mezipaměti. | Před součástmi, které vyžadují ukládání do mezipaměti. |
| [Komprese odezvy](xref:performance/response-compression) | Poskytuje podporu pro komprimaci odpovědí. | Před součástmi, které vyžadují kompresi. |
| [Lokalizace žádosti](xref:fundamentals/localization) | Poskytuje podporu lokalizace. | Před lokalizací citlivých komponent. |
| [Směrování](xref:fundamentals/routing) | Definuje a omezuje trasy požadavků. | Terminál pro vyhovující trasy. |
| [Relace](xref:fundamentals/app-state) | Poskytuje podporu pro správu uživatelských relací. | Před komponenty, které vyžadují relaci. |
| [Statické soubory](xref:fundamentals/static-files) | Poskytuje podporu pro poskytování statických souborů a procházení adresářů. | Terminál, pokud požadavek odpovídá souboru |
| [Přepsání adresy URL](xref:fundamentals/url-rewriting) | Poskytuje podporu pro přepisování adres URL a přesměrování požadavků. | Před komponenty, které používají adresu URL. |
| [Webové sokety](xref:fundamentals/websockets) | Povolí protokol WebSockets. | Před komponenty, které jsou vyžadovány pro příjem požadavků protokolu WebSocket. |

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
