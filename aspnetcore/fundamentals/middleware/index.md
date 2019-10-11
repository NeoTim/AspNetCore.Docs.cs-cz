---
title: Middleware ASP.NET Core
author: rick-anderson
description: Přečtěte si o ASP.NET Core middlewaru a kanálu požadavků.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/08/2019
uid: fundamentals/middleware/index
ms.openlocfilehash: 8f5c3aabf17e78ae9675048602317c54f08e82a7
ms.sourcegitcommit: 7d3c6565dda6241eb13f9a8e1e1fd89b1cfe4d18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72259810"
---
# <a name="aspnet-core-middleware"></a>Middleware ASP.NET Core

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)

Middleware je software, který se sestaví do kanálu aplikace za účelem zpracování požadavků a odpovědí. Jednotlivé komponenty:

* Zvolí, jestli se má žádost předat další komponentě v kanálu.
* Může provádět práci před a za další komponentou v kanálu.

Delegáti žádostí se používají k sestavení kanálu požadavků. Delegát žádosti zpracuje jednotlivé požadavky HTTP.

Delegáti žádostí jsou nakonfigurováni pomocí metod rozšíření <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> a <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>. Jednotlivé delegáty žádostí je možné zadat jako anonymní metodu (označovanou v rámci middlewarového middlewaru) nebo ji lze definovat v opakovaně použitelné třídě. Tyto opakovaně použitelné třídy a vložené anonymní metody jsou *middleware*, označované také jako *komponenty middlewaru*. Každá součást middlewaru v kanálu požadavků zodpovídá za vyvolání další komponenty v kanálu nebo při krátkém okruhu kanálu. Když jsou krátkodobé okruhy middleware, nazývá se *middleware terminálu* , protože zabrání dalšímu middlewaru ve zpracování žádosti.

<xref:migration/http-modules> vysvětluje rozdíl mezi kanály požadavků v ASP.NET Core a ASP.NET 4. x a poskytuje další ukázky middlewaru.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>Vytvoření kanálu middlewaru pomocí IApplicationBuilder

Kanál žádostí o ASP.NET Core se skládá z posloupnosti delegátů požadavků, který se nazývá jedna po druhé. Následující diagram znázorňuje koncept. Vlákno provádění následuje za černými šipkami.

![Požadavek zpracování, který ukazuje, že se dorazí na požadavek, zpracovává se prostřednictvím tří middlewarů a odpověď opouští aplikaci Každý middleware spustí svou logiku a předá požadavek dalšímu middlewaru v příkazu Next (). Poté, co třetí middleware zpracuje požadavek, požadavek předá předchozí dva middleware v obráceném pořadí pro další zpracování po jejich dalších () příkazech před ukončením aplikace jako reakci na klienta.](index/_static/request-delegate-pipeline.png)

Každý delegát může provádět operace před a po dalším delegátu. Delegáty zpracování výjimek by se měly volat v rámci kanálu na začátku, takže můžou zachytit výjimky, ke kterým dochází v pozdějších fázích kanálu.

Nejjednodušší možná ASP.NET Core aplikace nastaví jediného delegáta žádosti, který zpracovává všechny požadavky. Tento případ nezahrnuje skutečný kanál žádostí. Místo toho je volána jedna anonymní funkce v reakci na každý požadavek HTTP.

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

První delegát <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> ukončí kanál.

Řetězení více požadavků delegátů společně s <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> Parametr `next` představuje dalšího delegáta v kanálu. Kanál můžete pro krátké okruhy vymezit tím *, že nevoláte* *Další* parametr. Obvykle můžete provádět akce před i po dalším delegátu, jak ukazuje následující příklad:

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

Když delegát neprojde požadavek na dalšího delegáta, nazývá se to *krátkodobý kanál žádosti*. Krátkodobé okruhy jsou často žádoucí, protože brání zbytečné práci. Například [middleware statických souborů](xref:fundamentals/static-files) může fungovat jako *middleware terminálu* tím, že zpracovává požadavek na statický soubor a krátký okruh zbývajících částí kanálu. Middleware přidané do kanálu předtím, než middleware, který ukončí další zpracování, stále zpracovává kód po jejich příkazech `next.Invoke`. Přečtěte si ale následující upozornění týkající se pokusu o zápis do odpovědi, která již byla odeslána.

> [!WARNING]
> Po odeslání odpovědi klientovi Nevolejte `next.Invoke`. Změny <xref:Microsoft.AspNetCore.Http.HttpResponse> po zahájení odezvy vyvolávají výjimku. Například změny jako hlavičky nastavení a stavový kód vyvolávají výjimku. Zápis do těla odpovědi po volání `next`:
>
> * Může způsobit narušení protokolu. Například zápis více než uvedené `Content-Length`.
> * Může poškodit formát textu. Například zápis zápatí HTML do souboru CSS.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> je užitečnou nápovědou, která označuje, zda byla odeslána hlavička nebo zda byl text napsán do.

## <a name="order"></a>Pořadí

Pořadí, v jakém jsou komponenty middlewaru přidány v metodě `Startup.Configure`, definuje pořadí, ve kterém jsou komponenty middleware vyvolány na žádostech a obráceném pořadí pro odpověď. Pořadí je důležité pro zabezpečení, výkon a funkčnost.

Následující metoda `Startup.Configure` přidává komponenty middlewaru pro běžné scénáře aplikací:

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

V předchozím příkladu kódu je každá metoda rozšíření middleware vystavena na <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> prostřednictvím oboru názvů <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName>.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> je první součást middleware přidaná do kanálu. Proto middleware obslužné rutiny výjimek zachycuje všechny výjimky, ke kterým dojde v pozdějších voláních.

Middleware statických souborů se zavolá v průběhu kanálu, aby mohl zpracovávat požadavky a krátké okruhy bez toho, aby procházely zbývajícími součástmi. Middleware statických souborů neposkytuje **žádné** autorizační kontroly. Všechny soubory, které poskytuje middleware pro statický soubor, včetně těch v rámci *wwwroot*, jsou veřejně dostupné. Přístup k zabezpečení statických souborů najdete v tématu <xref:fundamentals/static-files>.

Pokud požadavek nezpracovává middleware pro statický soubor, je předán ověřovacímu middlewaru (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), který provádí ověřování. Ověřování neověřuje neověřené požadavky v krátkém okruhu. I když middleware ověřování ověřuje požadavky, autorizaci (a odmítnutí) proběhne pouze poté, co MVC vybere konkrétní stránku Razor nebo kontroler MVC a akci.

Následující příklad ukazuje pořadí middlewaru, kde požadavky na statické soubory jsou zpracovávány pomocí middlewaru statického souboru před použitím middlewaru pro komprimaci odezvy. Statické soubory nejsou s tímto pořadím middlewaru komprimovány. Odezvy Razor Pages lze komprimovat.

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
     * Middleware chybových stránek databáze (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) oznamuje chyby běhového běhu databáze.
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

V předchozím příkladu kódu je každá metoda rozšíření middleware vystavena na <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> prostřednictvím oboru názvů <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName>.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> je první součást middleware přidaná do kanálu. Proto middleware obslužné rutiny výjimek zachycuje všechny výjimky, ke kterým dojde v pozdějších voláních.

Middleware statických souborů se zavolá v průběhu kanálu, aby mohl zpracovávat požadavky a krátké okruhy bez toho, aby procházely zbývajícími součástmi. Middleware statických souborů neposkytuje **žádné** autorizační kontroly. Všechny soubory, které poskytuje middleware pro statický soubor, včetně těch v rámci *wwwroot*, jsou veřejně dostupné. Přístup k zabezpečení statických souborů najdete v tématu <xref:fundamentals/static-files>.

Pokud požadavek nezpracovává middleware pro statický soubor, je předán ověřovacímu middlewaru (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), který provádí ověřování. Ověřování neověřuje neověřené požadavky v krátkém okruhu. I když middleware ověřování ověřuje požadavky, autorizaci (a odmítnutí) proběhne pouze poté, co MVC vybere konkrétní stránku Razor nebo kontroler MVC a akci.

Následující příklad ukazuje pořadí middlewaru, kde požadavky na statické soubory jsou zpracovávány pomocí middlewaru statického souboru před použitím middlewaru pro komprimaci odezvy. Statické soubory nejsou s tímto pořadím middlewaru komprimovány. Odpovědi MVC z <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> lze zkomprimovat.

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

## <a name="use-run-and-map"></a>Použití, spuštění a mapování

Nakonfigurujte kanál HTTP pomocí <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> a <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>. Metoda `Use` může vytvořit krátký okruh kanálu (tj. Pokud nevolá delegáta žádosti `next`). `Run` je konvence a některé komponenty middlewaru můžou vystavovat metody `Run[Middleware]`, které se spouštějí na konci kanálu.

rozšíření <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> se používají jako konvence pro větvení kanálu. `Map` větví kanálu požadavků na základě shody dané cesty požadavku. Pokud cesta k požadavku začíná danou cestou, je větev spuštěná.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

V následující tabulce jsou uvedeny požadavky a odpovědi z `http://localhost:1234` pomocí předchozího kódu.

| Žádost             | Základě                     |
| ------------------- | ---------------------------- |
| localhost: 1234      | Hello z delegáta bez mapy. |
| localhost: 1234/Map1 | Mapování testu 1                   |
| localhost: 1234/MAP2 – | Mapovat test 2                   |
| localhost: 1234/map3 – | Hello z delegáta bez mapy. |

Při použití `Map` se odpovídající segmenty cesty odeberou z `HttpRequest.Path` a připojí se k `HttpRequest.PathBase` pro každý požadavek.

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> větví kanálu požadavků na základě výsledku daného predikátu. K mapování požadavků na novou větev kanálu lze použít jakýkoli predikát typu `Func<HttpContext, bool>`. V následujícím příkladu se k detekci přítomnosti proměnné řetězce dotazu používá predikát `branch`:

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

V následující tabulce jsou uvedeny požadavky a odpovědi z `http://localhost:1234` pomocí předchozího kódu.

| Žádost                       | Základě                     |
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

`Map` může současně odpovídat více segmentům:

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a>Vestavěný middleware

ASP.NET Core se dodává s následujícími součástmi middlewaru. Sloupec *Order* poskytuje poznámky k umístění middlewaru v kanálu zpracování požadavků a za jakých podmínek může middleware ukončit zpracování požadavků. Když middleware middleware vytvoří kanál zpracování požadavků a zabrání dalšímu podřízenému middlewaru ve zpracování žádosti, nazývá se *middleware terminálu*. Další informace o krátkodobém okruhu najdete v části [vytvoření kanálu middlewaru pomocí IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) .

| Jiné | Description | Pořadí |
| ---------- | ----------- | ----- |
| [Přihlašovací](xref:security/authentication/identity) | Poskytuje podporu ověřování. | Před `HttpContext.User` je potřeba. Terminál pro zpětná volání OAuth. |
| [Zásady souborů cookie](xref:security/gdpr) | Sleduje souhlas uživatelů při ukládání osobních údajů a vynutila minimální standardy pro pole cookie, například `secure` a `SameSite`. | Před middlewarem, který vydává soubory cookie. Příklady: ověřování, relace, MVC (TempData). |
| [CORS](xref:security/cors) | Konfiguruje sdílení prostředků mezi zdroji. | Před komponenty, které používají CORS. |
| [Diagnostiky](xref:fundamentals/error-handling) | Několik samostatných middlewarů, které poskytují stránku s výjimkou vývojářů, zpracování výjimek, stránky stavového kódu a výchozí webovou stránku pro nové aplikace. | Před komponenty, které generují chyby. Terminál pro výjimky nebo pro výchozí webovou stránku pro nové aplikace |
| [Předávaná záhlaví](xref:host-and-deploy/proxy-load-balancer) | Přepošle hlavičky proxy na aktuální požadavek. | Před komponenty, které používají aktualizované pole. Příklady: schéma, hostitel, IP adresa klienta, metoda. |
| [Kontroly stavu](xref:host-and-deploy/health-checks) | Kontroluje stav aplikace ASP.NET Core a jejích závislostí, jako je například Kontrola dostupnosti databáze. | Terminál, je-li požadavek shodný s koncovým bodem kontroly stavu. |
| [Přepsání metody HTTP](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | Umožňuje příchozí žádosti POST přepsat metodu. | Před komponenty, které používají aktualizovanou metodu. |
| [Přesměrování HTTPS](xref:security/enforcing-ssl#require-https) | Přesměrovat všechny požadavky HTTP na HTTPS. | Před komponenty, které používají adresu URL. |
| [HTTP Strict Transport Security (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Vylepšení zabezpečení – middleware, který přidává speciální hlavičku odpovědi. | Před odesláním odpovědí a po součástech, které upravují požadavky. Příklady: předávané hlavičky, přepis adresy URL. |
| [NÁVRHOVÝ](xref:mvc/overview) | Zpracovává požadavky pomocí MVC/Razor Pages. | Terminál, pokud požadavek odpovídá trase. |
| [OWIN](xref:fundamentals/owin) | Interoperabilita s aplikacemi, servery a middlewarem založeným na OWIN | Terminál, pokud middleware OWIN plně zpracovává požadavek. |
| [Ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) | Poskytuje podporu pro ukládání odpovědí do mezipaměti. | Před součástmi, které vyžadují ukládání do mezipaměti. |
| [Komprese odezvy](xref:performance/response-compression) | Poskytuje podporu pro komprimaci odpovědí. | Před součástmi, které vyžadují kompresi. |
| [Lokalizace žádosti](xref:fundamentals/localization) | Poskytuje podporu lokalizace. | Před lokalizací citlivých komponent. |
| [Směrování koncových bodů](xref:fundamentals/routing) | Definuje a omezuje trasy požadavků. | Terminál pro vyhovující trasy. |
| [Jednání](xref:fundamentals/app-state) | Poskytuje podporu pro správu uživatelských relací. | Před komponenty, které vyžadují relaci. |
| [Statické soubory](xref:fundamentals/static-files) | Poskytuje podporu pro poskytování statických souborů a procházení adresářů. | Terminál, pokud požadavek odpovídá souboru |
| [Přepsání adresy URL](xref:fundamentals/url-rewriting) | Poskytuje podporu pro přepisování adres URL a přesměrování požadavků. | Před komponenty, které používají adresu URL. |
| [WebSockets](xref:fundamentals/websockets) | Povolí protokol WebSockets. | Před komponenty, které jsou vyžadovány pro příjem požadavků protokolu WebSocket. |

## <a name="additional-resources"></a>Další materiály a zdroje informací

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
