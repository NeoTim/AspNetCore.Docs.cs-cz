---
title: Zpracování chyb v ASP.NET Core
author: tdykstra
description: Objevte, jak zpracovávat chyby v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/05/2019
uid: fundamentals/error-handling
ms.openlocfilehash: d809c70b3fae6b2d21d5ec0871298d905b873d5d
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665360"
---
# <a name="handle-errors-in-aspnet-core"></a>Zpracování chyb v ASP.NET Core

Podle [Petr Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex), a [Steve Smith](https://ardalis.com/)

Tento článek se věnuje běžné přístupy k zpracování chyb v aplikacích ASP.NET Core.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x) ([stažení](xref:index#how-to-download-a-sample))

## <a name="developer-exception-page"></a>Stránce výjimek pro vývojáře

Chcete-li nakonfigurovat aplikaci, která zobrazí stránka, která jsou uvedeny podrobné informace o žádosti o výjimkách, použijte *stránku výjimek pro vývojáře*. Na stránce je k dispozici ve [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) balíček, který je k dispozici v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app). Přidejte řádek, který `Startup.Configure` metodu, když aplikace běží při vývoji [prostředí](xref:fundamentals/environments):

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_UseDeveloperExceptionPage)]

Umístěte volání <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> před veškerý middleware, ve které chcete zaznamenat tak výjimky.

> [!WARNING]
> Povolit na stránce výjimek pro vývojáře **pouze, když je aplikace spuštěna ve vývojovém prostředí**. Nechcete veřejně sdílet podrobné informace o výjimce při spuštění aplikace v produkčním prostředí. Další informace o konfiguraci prostředí najdete v tématu <xref:fundamentals/environments>.

Stránce výjimek pro vývojáře najdete spuštění ukázkové aplikace s prostředím nastavena na `Development` a přidejte `?throw=true` k základní adrese URL aplikace. Stránka obsahuje následující informace o výjimku a požadavek:

* Trasování zásobníku
* Parametry řetězce dotazu (pokud existuje)
* Soubory cookie (pokud existuje)
* Záhlaví

## <a name="configure-a-custom-exception-handling-page"></a>Konfigurace vlastní výjimky zpracování stránky

Pokud není aplikace spuštěna ve vývojovém prostředí, zavolejte <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> metodu rozšíření k přidání Middleware zpracování výjimek. Middleware:

* Zachytává výjimky.
* Protokoly výjimky.
* Znovu provede požadavek na alternativní kanálu pro stránku nebo řadič uvedené. Daný požadavek není znovu spustit, pokud odpověď byla spuštěna.

V následujícím příkladu z ukázkové aplikace <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> přidá Middleware zpracování výjimek v jiných vývojových prostředích. Určuje metodu rozšíření chybové stránky nebo ovladač na `/Error` koncový bod pro znovu spustit požadavky po výjimky jsou zachyceny a přihlášení:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_UseExceptionHandler1)]

Šablona aplikace Razor Pages poskytuje chybovou stránku (*.cshtml*) a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> třídy (`ErrorModel`) ve složce stránky.

V aplikaci MVC následující metody obslužné rutiny chyb je součástí šablony aplikace MVC a zobrazí se v kontroler Home:

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

Není uspořádání metody akce obslužná rutina chyby s atributy metody HTTP, jako například `HttpGet`. Explicitní příkazy zabránit v dosažení metodu některé požadavky. Povolit anonymní přístup k metodě tak, aby se neověřené uživatele dostávají zobrazení chyb.

## <a name="access-the-exception"></a>Přístup k výjimce

Použití <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> pro přístup k výjimce nebo původní cestu požadavku v kontroleru nebo stránky:

* Cesta je k dispozici <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature.Path> vlastnost.
* Přečtěte si <xref:System.Exception?displayProperty=fullName> z zděděnou [IExceptionHandlerFeature.Error](xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature.Error) vlastnost.

```csharp
// using Microsoft.AspNetCore.Diagnostics;

var exceptionHandlerPathFeature = 
    HttpContext.Features.Get<IExceptionHandlerPathFeature>();
var path = exceptionHandlerPathFeature?.Path;
var error = exceptionHandlerPathFeature?.Error;
```

> [!WARNING]
> Proveďte **není** poskytovat informace o chybě citlivé z <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> nebo <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> klientům. Obsluhuje chyby představuje bezpečnostní riziko.

## <a name="configure-custom-exception-handling-code"></a>Konfigurace vlastního kódu pro zpracování výjimek

Alternativu se, že koncový bod pro chyby pomocí [vlastní výjimky zpracování stránky](#configure-a-custom-exception-handling-page) je poskytnout lambda <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>. Použití výrazu lambda s <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> umožňuje přístup k chybě před vrácením odpovědi.

Ukázková aplikace předvádí vlastní kód ve zpracování výjimek `Startup.Configure`. Aktivuje výjimku **vyvolat výjimky** odkaz na indexovou stránku. Následující výraz lambda spustí:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_UseExceptionHandler2)]

> [!WARNING]
> Proveďte **není** poskytovat informace o chybě citlivé z <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> nebo <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> klientům. Obsluhuje chyby představuje bezpečnostní riziko.

## <a name="configure-status-code-pages"></a>Konfigurace stavu znakové stránky

Ve výchozím nastavení, aplikace ASP.NET Core neposkytuje znakovou stránku Stav pro stavové kódy HTTP, jako například *404 - Nenalezeno*. Aplikace se vrátí stavový kód a prázdné tělo odpovědi. Pokud chcete poskytnout stav znakové stránky, použijte Middleware stránky stavový kód.

Middleware je k dispozici ve [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) balíček, který je k dispozici v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).

Přidejte řádek, který `Startup.Configure` metody:

```csharp
app.UseStatusCodePages();
```

Volání <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> metoda před požadavkem zpracování middleware (například Middleware statické soubory a Middlewarem MVC).

Ve výchozím nastavení, Middleware stránky stavový kód přidá prostého textu obslužné rutiny pro běžné stavové kódy, jako například *404 - Nenalezeno*:

```
Status Code: 404; Not Found
```

Middleware podporuje několik metod rozšíření, které umožňují přizpůsobit chování aplikace.

Přetížení <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> používá výraz lambda, který můžete použít ke zpracování vlastní logiku zpracování chyb a ručně zápisu odpovědi:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

Přetížení <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> obsahu typ a formát řetězce, který můžete použít pro přizpůsobení obsahu textu, typ a odpovědi:

```csharp
app.UseStatusCodePages("text/plain", "Status code page, status code: {0}");
```

### <a name="redirect-and-re-execute-extension-methods"></a>Přesměrování a znovu spusťte rozšiřující metody

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:

* Odešle *302 - nalezen* stavový kód na straně klienta.
* Přesměruje klienta do umístění, které jsou součástí adresy URL šablony.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> se obvykle používá, když aplikace:

* Klient by se měla přesměrovat na jiný koncový bod, obvykle v případech, kde různé aplikace zpracovává chyby. Pro web apps odráží adresního řádku prohlížeče klienta přesměrovaného koncový bod.
* Neměli zachovat a vrátí původní stavový kód odpovědi počáteční přesměrování.

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:

* Vrátí původní stavový kód do klienta.
* Generuje text odpovědi znovu spuštěním kanálu požadavku pomocí alternativní cesty.

```csharp
app.UseStatusCodePagesWithReExecute("/Error/{0}");
```

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> se obvykle používá při aplikace:

* Zpracování žádosti bez přesměrování na jiný koncový bod. Pro web apps odráží adresního řádku prohlížeče klienta na původně požadovanou koncový bod.
* Zachovat a vrátí původní kód stavu odpovědi.

Šablony mohou zahrnovat zástupný symbol (`{0}`) pro stavový kód. Šablona musí začínat lomítkem (`/`). Při použití zástupného symbolu, potvrďte, že koncový bod (stránka nebo řadič) může zpracovat segmentu cesty. Například stránky Razor pro chyby by měl přijmout hodnotu volitelná cesta segmentu se `@page` – direktiva:

```cshtml
@page "{code?}"
```

Stav znakové stránky je možné zakázat pro konkrétní požadavky v metodě obslužné rutiny pro stránky Razor nebo kontroler MVC. Zakázat stav znakových stránek, pokus o načtení <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> z identifikátoru požadavku [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features) kolekce a zakažte funkci, pokud je k dispozici:

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

Použití <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> přetížení body do koncového bodu v rámci aplikace, vytvořte zobrazení MVC nebo stránky Razor pro koncový bod. Například šablona aplikace Razor Pages vytváří následující stránky a modelu třídy stránky:

*Error.cshtml*:

::: moniker range=">= aspnetcore-2.2"

```cshtml
@page
@model ErrorModel
@{
    ViewData["Title"] = "Error";
}

<h1 class="text-danger">Error.</h1>
<h2 class="text-danger">An error occurred while processing your request.</h2>

@if (Model.ShowRequestId)
{
    <p>
        <strong>Request ID:</strong> <code>@Model.RequestId</code>
    </p>
}

<h3>Development Mode</h3>
<p>
    Swapping to the <strong>Development</strong> environment displays 
    detailed information about the error that occurred.
</p>
<p>
    <strong>The Development environment shouldn't be enabled for deployed 
    applications.</strong> It can result in displaying sensitive information 
    from exceptions to end users. For local debugging, enable the 
    <strong>Development</strong> environment by setting the 
    <strong>ASPNETCORE_ENVIRONMENT</strong> environment variable to 
    <strong>Development</strong> and restarting the app.
</p>
```

*Error.cshtml.cs*:

```csharp
[ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
public class ErrorModel : PageModel
{
    public string RequestId { get; set; }

    public bool ShowRequestId => !string.IsNullOrEmpty(RequestId);

    public void OnGet()
    {
        RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier;
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```cshtml
@page
@model ErrorModel
@{
    ViewData["Title"] = "Error";
}

<h1 class="text-danger">Error.</h1>
<h2 class="text-danger">An error occurred while processing your request.</h2>

@if (Model.ShowRequestId)
{
    <p>
        <strong>Request ID:</strong> <code>@Model.RequestId</code>
    </p>
}

<h3>Development Mode</h3>
<p>
    Swapping to <strong>Development</strong> environment will display more detailed 
    information about the error that occurred.
</p>
<p>
    <strong>Development environment should not be enabled in deployed applications
    </strong>, as it can result in sensitive information from exceptions being 
    displayed to end users. For local debugging, development environment can be 
    enabled by setting the <strong>ASPNETCORE_ENVIRONMENT</strong> environment 
    variable to <strong>Development</strong>, and restarting the application.
</p>
```

*Error.cshtml.cs*:

```csharp
public class ErrorModel : PageModel
{
    public string RequestId { get; set; }

    public bool ShowRequestId => !string.IsNullOrEmpty(RequestId);

    [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, 
        NoStore = true)]
    public void OnGet()
    {
        RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier;
    }
}
```

::: moniker-end

## <a name="exception-handling-code"></a>Kód zpracování výjimek

Kód výjimky zpracování stránky může vyvolat výjimky. Často je vhodné pro produkční chybové stránky, které se skládají z čistě statický obsah.

Také, mějte na paměti, jakmile jsou odeslány hlavičky odpovědi:

* Aplikaci nelze změnit stavový kód odpovědi.
* Žádné výjimce stránky nebo obslužné rutiny nelze spustit. Odpovědi musí dokončit, nebo bylo připojení přerušeno.

## <a name="server-exception-handling"></a>Zpracování výjimek serveru

Kromě ve vaší aplikaci logiky zpracování výjimek [implementaci serveru](xref:fundamentals/servers/index) dokáže zpracovat některé výjimky. Pokud server zachytí výjimku, před odesláním hlavičky odpovědi, odešle server *500 – Interní chyba serveru* odpovědi bez těla odpovědi. Pokud server zachytí výjimku po odeslání hlaviček odpovědí, server ukončí připojení. Požadavky, které nejsou zpracovány aplikací jsou zpracovány serverem. Jakoukoliv výjimku, která vyvolá se v případě, že server zpracovává žádost je zpracována výjimka serveru zpracování. Aplikace vlastní chybové stránky, zpracování middleware a filtry výjimek nemají vliv na toto chování.

## <a name="startup-exception-handling"></a>Zpracování výjimek při spuštění

Pouze hostování vrstvy dokáže zpracovat výjimky, které se provedou při spuštění aplikace. Pomocí [webového hostitele](xref:fundamentals/host/web-host), můžete [konfigurace hostitele chování v reakci na chyby při spuštění](xref:fundamentals/host/web-host#detailed-errors) s `captureStartupErrors` a `detailedErrors` klíče.

Hostování můžete jenom zobrazit chybovou stránku pro chyby zaznamenané při spouštění, pokud dojde k chybě po adresa/port hostitele vazby. Pokud z nějakého důvodu selže všechny vazby:

* Hostování vrstvy zaznamená kritické výjimky.
* Dotnet procesu dojde k chybě.
* Žádná chybová stránka se zobrazí, když je aplikace spuštěná na [Kestrel](xref:fundamentals/servers/kestrel) serveru.

Při spuštění na [IIS](/iis) nebo [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), *502.5 – selhání procesu* vrátí [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) Pokud proces nelze spustit. . Další informace naleznete v tématu <xref:host-and-deploy/iis/troubleshoot>. Informace o řešení problémů se spouštěním pomocí služby Azure App Service najdete v tématu <xref:host-and-deploy/azure-apps/troubleshoot>.

## <a name="aspnet-core-mvc-error-handling"></a>Zpracování chyb technologie ASP.NET Core MVC

[MVC](xref:mvc/overview) aplikace mají některé další možnosti pro zpracování chyb, jako je například konfigurace filtry výjimek a provedení ověření modelu.

### <a name="exception-filters"></a>Filtry výjimek

Filtry výjimek se dá nakonfigurovat globálně nebo na základě na kontroler nebo akcích v aplikaci MVC. Tyto filtry zpracování neošetřené výjimky, která během provádění akce kontroleru nebo jiný filtr. Tyto filtry nejsou volány jinak. Další informace naleznete v tématu <xref:mvc/controllers/filters#exception-filters>.

> [!TIP]
> Filtry výjimek jsou užitečné pro soutisku výjimky, ke kterým dochází v rámci akce MVC, ale nejsou tak flexibilní jako Middleware zpracování výjimek. Doporučujeme používat middleware. Použití filtrů jenom tam, kde potřebujete provádět zpracování chyb *jinak* závislosti na zvolené akci, která MVC.

### <a name="handle-model-state-errors"></a>Zpracování chyby stavu modelu

[Ověření modelu](xref:mvc/models/validation) vyvolá se před vyvoláním každé akce kontroleru a zodpovídá za metodu akce ke kontrole [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) a reagují odpovídajícím způsobem.

Některé aplikace zvolte dodržovat standardní zásady pro nakládání s [ověření modelu](xref:mvc/models/validation) chyby, v takovém případě [filtr](xref:mvc/controllers/filters) může být vhodné místo pro implementaci tuto zásadu. Měli byste otestovat chování vaše akce se stavy modelu je neplatný. Další informace naleznete v tématu <xref:mvc/controllers/testing>.

## <a name="additional-resources"></a>Další zdroje

* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>
