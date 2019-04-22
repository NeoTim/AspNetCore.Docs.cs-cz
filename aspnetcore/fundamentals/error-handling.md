---
title: Zpracování chyb v ASP.NET Core
author: tdykstra
description: Objevte, jak zpracovávat chyby v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 04/07/2019
uid: fundamentals/error-handling
ms.openlocfilehash: cbb9462a3c6010e074dc391aa128ac2cbb901456
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59705575"
---
# <a name="handle-errors-in-aspnet-core"></a>Zpracování chyb v ASP.NET Core

Podle [Petr Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex), a [Steve Smith](https://ardalis.com/)

Tento článek se věnuje běžné přístupy k zpracování chyb v aplikacích ASP.NET Core.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples). ([Stažení](xref:index#how-to-download-a-sample).) Tento článek obsahuje pokyny ohledně toho, jak nastavit direktivy preprocesoru (`#if`, `#endif`, `#define`) v ukázkové aplikaci povolit různé scénáře.

## <a name="developer-exception-page"></a>Stránce výjimek pro vývojáře

*Stránku výjimek pro vývojáře* zobrazí podrobné informace o žádosti o výjimkách. Na stránce je k dispozici ve [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app). Přidejte kód, který `Startup.Configure` metoda povolit na stránce, když aplikace běží při vývoji [prostředí](xref:fundamentals/environments):

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

Umístěte volání <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> před veškerý middleware, který chcete zaznamenat tak výjimky.

> [!WARNING]
> Povolit na stránce výjimek pro vývojáře **pouze, když je aplikace spuštěna ve vývojovém prostředí**. Nechcete veřejně sdílet podrobné informace o výjimce při spuštění aplikace v produkčním prostředí. Další informace o konfiguraci prostředí najdete v tématu <xref:fundamentals/environments>.

Stránka obsahuje následující informace o výjimku a požadavek:

* Trasování zásobníku
* Parametry řetězce dotazu (pokud existuje)
* Soubory cookie (pokud existuje)
* Záhlaví

Zobrazíte na stránce výjimek pro vývojáře v [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte `DevEnvironment` preprocesor směrnice a vyberte **spuštění výjimky** na domovské stránce.

## <a name="exception-handler-page"></a>Stránka obslužné rutiny výjimek

Pokud chcete nakonfigurovat vlastní chybové stránky pro produkční prostředí zpracování, použijte Middleware zpracování výjimek. Middleware:

* Zachytí a protokolů výjimek.
* Znovu provede požadavek na alternativní kanálu pro stránku nebo řadič uvedené. Daný požadavek není znovu spustit, pokud odpověď byla spuštěna.

V následujícím příkladu <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> přidá Middleware zpracování výjimek v jiných vývojových prostředích:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

Šablona aplikace Razor Pages poskytuje chybovou stránku (*.cshtml*) a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> třídy (`ErrorModel`) v *stránky* složky. Šablona projektu pro aplikace MVC, obsahuje metodu akce chyba a zobrazení chyb. Tady je metoda akce:

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

Není uspořádání metody akce obslužná rutina chyby s atributy metody HTTP, jako například `HttpGet`. Explicitní příkazy zabránit v dosažení metodu některé požadavky. Povolit anonymní přístup k metodě tak, aby se neověřené uživatele dostávají zobrazení chyb.

### <a name="access-the-exception"></a>Přístup k výjimce

Použití <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> pro přístup k výjimce a původní cestu požadavku v kontroleru obslužná rutina chyby nebo stránky:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> Proveďte **není** poskytovat informace o chybě citlivé klientům. Obsluhuje chyby představuje bezpečnostní riziko.

Chcete zobrazit stránku zpracování výjimek v [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte `ProdEnvironment` a `ErrorHandlerPage` direktivy preprocesoru a vyberte **spuštění výjimky** na domovské stránce.

## <a name="exception-handler-lambda"></a>Lambda obslužné rutiny výjimek

Alternativa k [stránku obslužné rutiny vlastních výjimek](#exception-handler-page) je poskytnout lambda <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>. Použití výrazu lambda umožňuje přístup k chybě před vrácením odpovědi.

Tady je příklad použití výrazu lambda pro zpracování výjimek:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

> [!WARNING]
> Proveďte **není** poskytovat informace o chybě citlivé z <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> nebo <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> klientům. Obsluhuje chyby představuje bezpečnostní riziko.

Chcete-li zobrazit výsledek tohoto výrazu lambda zpracování výjimek v [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte `ProdEnvironment` a `ErrorHandlerLambda` direktivy preprocesoru a vyberte **spuštění výjimky** na domovské stránce.

## <a name="usestatuscodepages"></a>UseStatusCodePages

Ve výchozím nastavení, aplikace ASP.NET Core neposkytuje znakovou stránku Stav pro stavové kódy HTTP, jako například *404 - Nenalezeno*. Aplikace se vrátí stavový kód a prázdné tělo odpovědi. Pokud chcete poskytnout stav znakové stránky, použijte middleware stav znakové stránky.

Middleware je k dispozici ve [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).

Chcete-li povolit výchozích prostého textu obslužných rutin pro běžné kódy chyb stav, zavolejte <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> v `Startup.Configure` metody:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

Volání `UseStatusCodePages` před požadavkem zpracování middleware (například Middleware statické soubory a Middlewarem MVC).

Tady je příklad text, zobrazený výchozích obslužných rutin:

```
Status Code: 404; Not Found
```

Chcete-li zobrazit jeden z různých formátů stránky kód stavu v [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte jednu z direktivy preprocesoru, které začínají `StatusCodePages`a vyberte **aktivační události a 404** na domovské stránce.

## <a name="usestatuscodepages-with-format-string"></a>UseStatusCodePages řetězcem formátu

Chcete-li přizpůsobit typ obsahu odpovědi a text, použijte přetížení <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> , která přebírá typ a formát řetězec obsahu:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a>UseStatusCodePages pomocí výrazu lambda

Zadat vlastní zpracování chyb a odezvy psaní kódu, použijte přetížení <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> , který přijímá výrazy lambda:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirect"></a>UseStatusCodePagesWithRedirect

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> – Metoda rozšíření:

* Odešle *302 - nalezen* stavový kód na straně klienta.
* Přesměruje klienta do umístění, které jsou součástí adresy URL šablony.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

Šablona adresy URL může obsahovat `{0}` zástupný symbol pro kód stavu, jak je znázorněno v příkladu. Pokud šablona adresa URL začíná tildou (~), aplikace nahrazuje tilda `PathBase`. Pokud přejdete na koncový bod v rámci aplikace, vytvořte zobrazení MVC nebo stránky Razor pro koncový bod. Příklad stránky Razor, naleznete v tématu [StatusCode.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x/Pages/StatusCode.cshtml) v [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Tato metoda se obvykle nepoužívá, pokud aplikace:

* Klient by se měla přesměrovat na jiný koncový bod, obvykle v případech, kde různé aplikace zpracovává chyby. Pro web apps odráží adresního řádku prohlížeče klienta přesměrovaného koncový bod.
* Neměli zachovat a vrátí původní stavový kód odpovědi počáteční přesměrování.

## <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> – Metoda rozšíření:

* Vrátí původní stavový kód do klienta.
* Generuje text odpovědi znovu spuštěním kanálu požadavku pomocí alternativní cesty.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

Pokud přejdete na koncový bod v rámci aplikace, vytvořte zobrazení MVC nebo stránky Razor pro koncový bod. Příklad stránky Razor, naleznete v tématu [StatusCode.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x/Pages/StatusCode.cshtml) v [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Tato metoda se obvykle používá při aplikace:

* Zpracování žádosti bez přesměrování na jiný koncový bod. Pro web apps odráží adresního řádku prohlížeče klienta na původně požadovanou koncový bod.
* Zachovat a vrátí původní kód stavu odpovědi.

Adresa URL a šablon řetězců se mohou zahrnovat zástupný text dotazu (`{0}`) pro stavový kód. Šablona adresy URL musí začínat lomítkem (`/`). Pokud používáte zástupný symbol v cestě, potvrďte, že koncový bod (stránka nebo řadič) může zpracovat segmentu cesty. Například stránky Razor pro chyby by měl přijmout hodnotu volitelná cesta segmentu se `@page` – direktiva:

```cshtml
@page "{code?}"
```

Koncový bod, který zpracovává chybu můžete získat původní adresu URL, který vytvořil chybu, jak je znázorněno v následujícím příkladu:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a>Zakázat stav znakové stránky

Stav znakové stránky je možné zakázat pro konkrétní požadavky v metodě obslužné rutiny pro stránky Razor nebo kontroler MVC. Chcete-li zakázat stav znakové stránky, použijte <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a>Kód zpracování výjimek

Kód výjimky zpracování stránky může vyvolat výjimky. Často je vhodné pro produkční chybové stránky, které se skládají z čistě statický obsah.

### <a name="response-headers"></a>Hlavičky odpovědi

Jakmile jsou odeslány hlavičky odpovědi:

* Aplikaci nelze změnit stavový kód odpovědi.
* Žádné výjimce stránky nebo obslužné rutiny nelze spustit. Odpovědi musí dokončit, nebo bylo připojení přerušeno.

## <a name="server-exception-handling"></a>Zpracování výjimek serveru

Kromě ve vaší aplikaci logiky zpracování výjimek [implementaci serveru HTTP](xref:fundamentals/servers/index) dokáže zpracovat některé výjimky. Pokud server zachytí výjimku, před odesláním hlavičky odpovědi, odešle server *500 – Interní chyba serveru* odpovědi bez těla odpovědi. Pokud server zachytí výjimku po odeslání hlaviček odpovědí, server ukončí připojení. Požadavky, které nejsou zpracovány aplikací jsou zpracovány serverem. Jakoukoliv výjimku, která vyvolá se v případě, že server zpracovává žádost je zpracována výjimka serveru zpracování. Aplikace vlastní chybové stránky, zpracování middleware a filtry výjimek nemají vliv na toto chování.

## <a name="startup-exception-handling"></a>Zpracování výjimek při spuštění

Pouze hostování vrstvy dokáže zpracovat výjimky, které se provedou při spuštění aplikace. Hostitele lze nakonfigurovat pro [zachycení chyb při spuštění](xref:fundamentals/host/web-host#capture-startup-errors) a [zachytit podrobné chyby](xref:fundamentals/host/web-host#detailed-errors).

Hostování vrstvy můžete zobrazit chybovou stránku pro chyby zaznamenané při spouštění jenom v případě, že po adresa/port hostitele vazby, dojde k chybě. Pokud se nezdaří vazby:

* Hostování vrstvy zaznamená kritické výjimky.
* Dotnet procesu dojde k chybě.
* Žádná chybová stránka se zobrazí, když je HTTP server [Kestrel](xref:fundamentals/servers/kestrel).

Při spuštění na [IIS](/iis) nebo [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), *502.5 – selhání procesu* vrátí [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) Pokud proces nelze spustit. . Další informace naleznete v tématu <xref:host-and-deploy/iis/troubleshoot>. Informace o řešení problémů se spouštěním pomocí služby Azure App Service najdete v tématu <xref:host-and-deploy/azure-apps/troubleshoot>.

## <a name="database-error-page"></a>Databáze chybovou stránku

[Databáze chybovou stránku](<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*>) middleware zachycuje vztahující se k databázi výjimky, které se dají vyřešit pomocí migrace Entity Framework. Pokud dojde k tyto výjimky, je generována odpověď jazyka HTML s podrobnostmi o možných akcí k problému. Na této stránce musí být povolené pouze ve vývojovém prostředí. Povolit na stránce přidáním kódu k `Startup.Configure`:

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

## <a name="exception-filters"></a>Filtry výjimek

V aplikacích MVC filtry výjimek lze nastavit, globálně nebo na základě na kontroler nebo jednotlivé akce. V aplikacích pro stránky Razor se dají Konfigurovat globálně nebo na stránce modelu. Tyto filtry zpracování neošetřené výjimky, která během provádění akce kontroleru nebo jiný filtr. Další informace naleznete v tématu <xref:mvc/controllers/filters#exception-filters>.

> [!TIP]
> Filtry výjimek jsou užitečné pro soutisku výjimky, ke kterým dochází v rámci akce MVC, ale nejsou tak flexibilní jako Middleware zpracování výjimek. Doporučujeme používat middleware. Použití filtrů jenom tam, kde potřebujete provádět zpracování chyb různě v závislosti na zvolené akci, která MVC.

## <a name="model-state-errors"></a>Chyby stavu modelu

Informace o tom, jak zpracovávat chyby stavu modelu, naleznete v tématu [vazby modelu](xref:mvc/models/model-binding) a [ověření modelu](xref:mvc/models/validation).

## <a name="additional-resources"></a>Další zdroje

* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>
