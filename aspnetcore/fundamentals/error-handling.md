---
title: Zpracování chyb v ASP.NET Core
author: tdykstra
description: Objevte, jak zpracovávat chyby v aplikacích ASP.NET Core.
ms.author: tdykstra
ms.custom: mvc
ms.date: 11/01/2018
uid: fundamentals/error-handling
ms.openlocfilehash: fbc86d36f66e71e6ebd84f536148fba2e3c452d8
ms.sourcegitcommit: 408921a932448f66cb46fd53c307a864f5323fe5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51570058"
---
# <a name="handle-errors-in-aspnet-core"></a>Zpracování chyb v ASP.NET Core

Podle [Steve Smith](https://ardalis.com/) a [Petr Dykstra](https://github.com/tdykstra/)

Tento článek se věnuje běžné přístupy k zpracování chyb v aplikacích ASP.NET Core.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x/ErrorHandlingSample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="the-developer-exception-page"></a>Stránce výjimek pro vývojáře

::: moniker range=">= aspnetcore-2.1"

Chcete-li nakonfigurovat aplikaci, která zobrazí stránka, která jsou uvedeny podrobné informace o výjimkách, použijte *stránku výjimek pro vývojáře*. Na stránce je k dispozici ve [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) balíček, který je k dispozici v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app). Přidejte řádek, který `Startup.Configure` metody:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Chcete-li nakonfigurovat aplikaci, která zobrazí stránka, která jsou uvedeny podrobné informace o výjimkách, použijte *stránku výjimek pro vývojáře*. Na stránce je k dispozici ve [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) balíček, který je k dispozici v [metabalíček Microsoft.aspnetcore.all](xref:fundamentals/metapackage). Přidejte řádek, který `Startup.Configure` metody:

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Chcete-li nakonfigurovat aplikaci, která zobrazí stránka, která jsou uvedeny podrobné informace o výjimkách, použijte *stránku výjimek pro vývojáře*. Na stránce je k dispozici tak, že přidáte odkaz na balíček pro [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) balíčku v souboru projektu. Přidejte řádek, který `Startup.Configure` metody:

::: moniker-end

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevExceptionPage&highlight=7)]

Umístěte volání [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) před veškerý middleware, ve které chcete zaznamenat tak výjimky, jako například `app.UseMvc`.

> [!WARNING]
> Povolit na stránce výjimek pro vývojáře **pouze, když je aplikace spuštěna ve vývojovém prostředí**. Nechcete veřejně sdílet podrobné informace o výjimce při spuštění aplikace v produkčním prostředí. [Další informace o konfiguraci prostředí](xref:fundamentals/environments).

Stránce výjimek pro vývojáře najdete spuštění ukázkové aplikace s prostředím nastavena na `Development` a přidejte `?throw=true` k základní adrese URL aplikace. Stránka obsahuje několik karet s informacemi o výjimku a požadavek. První karta obsahuje trasování zásobníku:

![Trasování zásobníku](error-handling/_static/developer-exception-page.png)

Další karta ukazuje dotaz případné parametry řetězce:

![Parametry řetězce dotazu](error-handling/_static/developer-exception-page-query.png)

Pokud požadavek má soubory cookie, zobrazí se na **soubory cookie** kartu. Záhlaví se zobrazují na kartě poslední:

![Záhlaví](error-handling/_static/developer-exception-page-headers.png)

## <a name="configure-a-custom-exception-handling-page"></a>Konfigurace vlastní výjimky zpracování stránky

Na stránce obslužné rutiny výjimky pro použití při není aplikace spuštěna konfiguraci `Development` prostředí:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevExceptionPage&highlight=11)]

V aplikaci s Razor Pages [dotnet nové](/dotnet/core/tools/dotnet-new) šablona Razor Pages nabízí chybovou stránku a chybě `PageModel` třídy v *stránky* složky.

V aplikaci MVC, není uspořádání metody akce obslužná rutina chyby s atributy metody HTTP, jako například `HttpGet`. Explicitní příkazy zabránit v dosažení metodu některé požadavky. Povolit anonymní přístup k metodě tak, aby se neověřené uživatele dostávají zobrazení chyb.

Například poskytuje následující metody obslužné rutiny chyb [dotnet nové](/dotnet/core/tools/dotnet-new) šablona MVC a zobrazí se v kontroler Home:

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

## <a name="configure-status-code-pages"></a>Konfigurace stavu znakové stránky

Ve výchozím nastavení, aplikace neposkytuje znakovou stránku bohaté stav pro stavové kódy HTTP, jako například *404 Nenalezeno*. Pokud chcete poskytnout stav znakové stránky, použijte Middleware stránky stavový kód.

::: moniker range=">= aspnetcore-2.1"

Middleware je k dispozici ve [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) balíček, který je k dispozici v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Middleware je k dispozici ve [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) balíček, který je k dispozici v [metabalíček Microsoft.aspnetcore.all](xref:fundamentals/metapackage).

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Middleware je k dispozici tak, že přidáte odkaz na balíček pro [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) balíčku v souboru projektu.

::: moniker-end

Přidejte řádek, který `Startup.Configure` metody:

```csharp
app.UseStatusCodePages();
```

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> by měla být volána před požadavkem zpracování middlewares v kanálu (třeba Middleware statické soubory a Middlewarem MVC).

Ve výchozím nastavení přidá Middleware stránky stavový kód obslužné rutiny pro běžné stavové kódy, jako je například 404 prostého textu:

![404 – Stránka](error-handling/_static/default-404-status-code.png)

Middleware podporuje několik metod rozšíření. Jedna metoda má výraz lambda:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

Přetížení `UseStatusCodePages` obsahu typ a formát řetězce:

```csharp
app.UseStatusCodePages("text/plain", "Status code page, status code: {0}");
```
### <a name="redirect-re-execute-extension-methods"></a>Znovu provést rozšiřující metody pro přesměrování

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:

* Odešle *302 - nalezen* stavový kód na straně klienta.
* Přesměruje klienta do umístění, které jsou součástí adresy URL šablony. 

Šablona může obsahovat `{0}` zástupný symbol pro stavový kód. Šablona musí začínat lomítkem (`/`).

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:

* Vrátí původní stavový kód do klienta.
* Určuje, že tělo odpovědi by měl být vygenerován znovu spuštěním kanálu požadavku pomocí alternativní cesty. 

Šablona může obsahovat `{0}` zástupný symbol pro stavový kód. Šablona musí začínat lomítkem (`/`).

```csharp
app.UseStatusCodePagesWithReExecute("/error/{0}");
```

Stav znakové stránky je možné zakázat pro konkrétní požadavky v metodě obslužné rutiny pro stránky Razor nebo kontroler MVC. Zakázat stav znakových stránek, pokus o načtení [IStatusCodePagesFeature](/dotnet/api/microsoft.aspnetcore.diagnostics.istatuscodepagesfeature) z identifikátoru požadavku [HttpContext.Features](/dotnet/api/microsoft.aspnetcore.http.httpcontext.features) kolekce a zakažte funkci, pokud je k dispozici:

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

Použití `UseStatusCodePages*` přetížení body do koncového bodu v rámci aplikace, vytvořte zobrazení MVC nebo stránky Razor pro koncový bod. Například [dotnet nové](/dotnet/core/tools/dotnet-new) šablony pro aplikace Razor Pages vytvoří následující stránky a modelu třídy stránky:

*Error.cshtml*:

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

## <a name="exception-handling-code"></a>Kód zpracování výjimek

Kód výjimky zpracování stránky může vyvolat výjimky. Často je vhodné pro produkční chybové stránky, které se skládají z čistě statický obsah.

Také mějte na paměti, že po odeslání hlaviček pro odpovědi, nelze změnit stavový kód odpovědi ani žádné výjimce stránky nebo obslužné rutiny poběží. Odpovědi musí dokončit, nebo bylo připojení přerušeno.

## <a name="server-exception-handling"></a>Zpracování výjimek serveru

Kromě ve vaší aplikaci logiky zpracování výjimek [server](xref:fundamentals/servers/index) hostování vaší aplikace provádí nějaké zpracování výjimek. Pokud server zachytí výjimku, před odesláním záhlaví, odešle server *500 – Interní chyba serveru* odpověď se žádný text. Pokud server zachytí výjimku po odeslání hlavičky, server ukončí připojení. Požadavky, které nejsou zpracovány aplikací jsou zpracovány serverem. Všechny vyvolané výjimky jsou zpracována výjimka serveru zpracování. Žádné nakonfigurované vlastní chybové stránky nebo zpracování výjimek middleware nebo filtry nemají vliv na toto chování.

## <a name="startup-exception-handling"></a>Zpracování výjimek při spuštění

Pouze hostování vrstvy dokáže zpracovat výjimky, které se provedou při spuštění aplikace. Použití [webového hostitele](xref:fundamentals/host/web-host), můžete [konfigurace hostitele chování v reakci na chyby při spuštění](xref:fundamentals/host/web-host#detailed-errors) s `captureStartupErrors` a `detailedErrors` klíče.

Hostování můžete jenom zobrazit chybovou stránku pro chyby zaznamenané při spouštění, pokud dojde k chybě po adresa/port hostitele vazby. Pokud z nějakého důvodu selže všechny vazby, hostování vrstvy zaznamená kritické výjimky, dojde k chybě dotnet procesu a žádná chybová stránka se zobrazí, když je aplikace spuštěná na [Kestrel](xref:fundamentals/servers/kestrel) serveru.

Při spuštění na [IIS](/iis) nebo [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), *502.5 selhání procesu* je vrácený [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) Pokud proces nemůže být bylo zahájeno. Informace o řešení problémů se spouštěním, při hostování za nástrojem službou IIS najdete v tématu <xref:host-and-deploy/iis/troubleshoot>. Informace o řešení problémů se spouštěním pomocí služby Azure App Service najdete v tématu <xref:host-and-deploy/azure-apps/troubleshoot>.

## <a name="aspnet-core-mvc-error-handling"></a>Zpracování chyb technologie ASP.NET Core MVC

[MVC](xref:mvc/overview) aplikace mají některé další možnosti pro zpracování chyb, jako je například konfigurace filtry výjimek a provedení ověření modelu.

### <a name="exception-filters"></a>Filtry výjimek

Filtry výjimek se dá nakonfigurovat globálně nebo na základě na kontroler nebo akcích v aplikaci MVC. Tyto filtry zpracování neošetřené výjimky, která během provádění akce kontroleru nebo jiný filtr. Tyto filtry nejsou volány jinak. Další informace najdete v tématu [filtry](xref:mvc/controllers/filters).

> [!TIP]
> Filtry výjimek jsou vhodné pro soutisku výjimky, ke kterým dochází v rámci akce MVC, ale nejsou tak flexibilní jako middleware pro zpracování chyb. Obecně přednost použití middlewaru a použití filtrů jenom tam, kde potřebujete provádět zpracování chyb *jinak* závislosti na zvolené akci, která MVC.

### <a name="handling-model-state-errors"></a>Zpracování chyby stavu modelu

[Ověření modelu](xref:mvc/models/validation) vyvolá se před vyvoláním každé akce kontroleru a zodpovídá za metodu akce ke kontrole `ModelState.IsValid` a reagují odpovídajícím způsobem.

Některé aplikace zvolte dodržovat standardní konvence týkající se chyby ověření modelu, v takovém případě [filtr](xref:mvc/controllers/filters) může být vhodné místo pro implementaci tuto zásadu. Měli byste otestovat chování vaše akce se stavy modelu je neplatný. Další informace najdete v [testovací kontroler logiku](xref:mvc/controllers/testing).

## <a name="additional-resources"></a>Další zdroje

* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>
