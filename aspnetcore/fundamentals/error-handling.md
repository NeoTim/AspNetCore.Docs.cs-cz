---
title: Zpracování chyb v ASP.NET Core
author: rick-anderson
description: Objevte, jak zpracovávat chyby v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/error-handling
ms.openlocfilehash: 7bc21901fe1e9ddf604abf3b5bfecdb8a319f12c
ms.sourcegitcommit: ca6a1f100c1a3f59999189aa962523442dd4ead1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2020
ms.locfileid: "87444103"
---
# <a name="handle-errors-in-aspnet-core"></a>Zpracování chyb v ASP.NET Core

Tím, že [Dykstra](https://github.com/tdykstra/) a [Steve Smith](https://ardalis.com/)

Tento článek se věnuje běžným přístupům ke zpracování chyb v ASP.NET Core Web Apps. Podívejte se <xref:web-api/handle-errors> na webová rozhraní API.

[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples). ([Stažení](xref:index#how-to-download-a-sample).) Tento článek obsahuje pokyny, jak nastavit direktivy preprocesoru ( `#if` , `#endif` , `#define` ) v ukázkové aplikaci pro povolení různých scénářů.

## <a name="developer-exception-page"></a>Stránka s výjimkou pro vývojáře

*Stránka s výjimkou vývojáře* zobrazuje podrobné informace o výjimkách žádostí. Stránku zpřístupňuje balíček [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) , který je ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app). Přidejte kód do `Startup.Configure` metody pro povolení stránky, když aplikace běží ve vývojovém [prostředí](xref:fundamentals/environments):

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

Umístěte volání do <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> před jakýmkoli middlewarem, který chcete zachytit výjimky.

> [!WARNING]
> Stránku s výjimkou vývojářů povolte **jenom v případě, že aplikace běží ve vývojovém prostředí**. Nechcete veřejně sdílet podrobné informace o výjimkách, když aplikace běží v produkčním prostředí. Další informace o konfiguraci prostředí najdete v tématu <xref:fundamentals/environments> .

Stránka obsahuje následující informace o výjimce a žádosti:

* Trasování zásobníku
* Parametry řetězce dotazu (pokud existují)
* Soubory cookie (pokud existují)
* Hlavičky

Chcete-li zobrazit stránku s výjimkou vývojáře v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte `DevEnvironment` direktivu preprocesoru a vyberte možnost **aktivovat výjimku** na domovské stránce.

## <a name="exception-handler-page"></a>Stránka obslužné rutiny výjimek

Chcete-li nakonfigurovat vlastní stránku zpracování chyb pro produkční prostředí, použijte middleware pro zpracování výjimek. Middleware:

* Zachycuje a zaznamenává výjimky.
* Znovu spustí požadavek v alternativním kanálu pro uvedenou stránku nebo kontroler. Požadavek se znovu nespustí, pokud byla odpověď spuštěná.

V následujícím příkladu <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> přidá middleware pro zpracování výjimek v Nevývojovém prostředí:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

RazorŠablona aplikace stránky poskytuje chybovou stránku (*. cshtml*) a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> třídu ( `ErrorModel` ) ve složce *Pages* . V případě aplikace MVC obsahuje šablona projektu metodu chybové akce a zobrazení chyby. Zde je metoda akce:

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

Neoznačovat metodu akce obslužné rutiny chyb pomocí atributů metody HTTP, jako je například `HttpGet` . Explicitní příkazy brání, aby některé žádosti dosáhly metody. Povolí anonymní přístup k metodě, aby neověření uživatelé mohli získat zobrazení chyb.

### <a name="access-the-exception"></a>Přístup k výjimce

Použijte <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> pro přístup k výjimce a původní cestě požadavku v řadiči obslužné rutiny chyb nebo na stránce:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> Neslouží **klientům** informace o citlivých chybách. Obsluha chyb je bezpečnostní riziko.

Chcete-li zobrazit stránku zpracování výjimek v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte `ProdEnvironment` `ErrorHandlerPage` direktivy preprocesoru a a vyberte možnost **aktivovat výjimku** na domovské stránce.

## <a name="exception-handler-lambda"></a>Lambda obslužné rutiny výjimek

Alternativou ke [stránce vlastní obslužné rutiny výjimek](#exception-handler-page) je poskytnout lambda výraz <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> . Použití lambda umožňuje přístup k chybě před vrácením odpovědi.

Zde je příklad použití výrazu lambda pro zpracování výjimek:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

V předchozím kódu `await context.Response.WriteAsync(new string(' ', 512));` je přidán prohlížeč Internet Explorer, aby se zobrazila chybová zpráva, nikoli chybová zpráva IE. Další informace najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/16144).

> [!WARNING]
> **Neobsluhujte** citlivé informace o chybách z <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> klientů ani do nich <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> . Obsluha chyb je bezpečnostní riziko.

Chcete-li zobrazit výsledek lambda zpracování výjimek v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte `ProdEnvironment` `ErrorHandlerLambda` direktivy preprocesoru a a vyberte možnost **aktivovat výjimku** na domovské stránce.

## <a name="usestatuscodepages"></a>UseStatusCodePages

Ve výchozím nastavení aplikace ASP.NET Core neposkytuje stavovou stránku pro stavové kódy HTTP, například *404-Nenalezeno*. Aplikace vrátí stavový kód a prázdné tělo odpovědi. Chcete-li poskytnout stránky se stavovým kódem, použijte middleware stránky stavového kódu.

Middleware zpřístupňuje balíček [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) , který je ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).

Chcete-li povolit výchozí textové obslužné rutiny pro běžné chyby stavových kódů, zavolejte <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> v `Startup.Configure` metodě:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

Volejte `UseStatusCodePages` před middlewarem zpracování požadavků (například middleware pro statický soubor a middleware Mvc).

Tady je příklad textu zobrazovaného výchozími obslužnými rutinami:

```
Status Code: 404; Not Found
```

Pokud chcete v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)zobrazit jeden z různých formátů stavových kódů, použijte jednu z direktiv preprocesoru, která začíná `StatusCodePages` na, a na domovské stránce vyberte **aktivovat na 404** .

## <a name="usestatuscodepages-with-format-string"></a>UseStatusCodePages s formátovacím řetězcem

Chcete-li přizpůsobit typ obsahu odpovědi a text, použijte přetížení <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> , které přijímá typ obsahu a řetězec formátu:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a>UseStatusCodePages s výrazem lambda

Chcete-li určit vlastní kód pro zpracování chyb a psaní odezvy, použijte přetížení <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> , které přijímá výraz lambda:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a>UseStatusCodePagesWithRedirects

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>Metoda rozšíření:

* Odešle klientovi stavový kód *302* .
* Přesměruje klienta na umístění zadané v šabloně adresy URL.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

Šablona adresy URL může obsahovat `{0}` zástupný symbol pro stavový kód, jak je znázorněno v příkladu. Pokud šablona adresy URL začíná vlnovkou (~), bude znak tildy nahrazen symbolem aplikace `PathBase` . Pokud odkazujete na koncový bod v rámci aplikace, vytvořte zobrazení MVC nebo Razor stránku pro koncový bod. RazorPříklad stránky naleznete v tématu *Pages/StatusCode. cshtml* v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Tato metoda se běžně používá, když aplikace:

* By měl přesměrování klienta na jiný koncový bod, obvykle v případech, kdy se chyba zpracovává v jiné aplikaci. V případě webových aplikací zobrazuje panel Adresa v prohlížeči klienta přesměrovaný koncový bod.
* Neměl by zachovat a vracet původní stavový kód s počáteční odpovědí přesměrování.

## <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>Metoda rozšíření:

* Vrátí původní stavový kód klientovi.
* Vygeneruje tělo odpovědi opakovaným spuštěním kanálu požadavků pomocí alternativní cesty.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

Pokud odkazujete na koncový bod v rámci aplikace, vytvořte zobrazení MVC nebo Razor stránku pro koncový bod. Zajistěte, aby bylo `UseStatusCodePagesWithReExecute` `UseRouting` možné žádost přesměrovat na stavovou stránku. RazorPříklad stránky naleznete v tématu *Pages/StatusCode. cshtml* v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Tato metoda se běžně používá v případě, kdy by měla aplikace:

* Zpracuje požadavek bez přesměrování na jiný koncový bod. V případě webových aplikací zobrazuje panel Adresa v prohlížeči klienta původní požadovaný koncový bod.
* Zachovejte a vraťte původní stavový kód s odpovědí.

Šablony URL a řetězce dotazu mohou obsahovat zástupný symbol ( `{0}` ) pro stavový kód. Šablona adresy URL musí začínat lomítkem ( `/` ). Při použití zástupného symbolu v cestě potvrďte, že koncový bod (stránka nebo kontroler) může zpracovat segment cesty. Například Razor Stránka pro chyby by měla přijmout volitelnou hodnotu segmentu cesty s touto `@page` direktivou:

```cshtml
@page "{code?}"
```

Koncový bod, který zpracovává chybu, může získat původní adresu URL, která generovala chybu, jak je znázorněno v následujícím příkladu:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a>Zakázat stavové stránky

Chcete-li zakázat stránky stavového kódu pro řadič MVC nebo metodu akce, použijte [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) atribut.

Chcete-li zakázat stránky stavového kódu pro konkrétní požadavky v Razor metodě obslužné rutiny stránky nebo v KONTROLERU MVC, použijte <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a>Kód pro zpracování výjimek

Kód na stránkách zpracování výjimek může vyvolat výjimky. Pro produkční chybové stránky je často vhodné vytvořit pouze čistě statický obsah.

### <a name="response-headers"></a>Hlavičky odpovědi

Po odeslání hlaviček pro odpověď:

* Aplikace nemůže změnit stavový kód odpovědi.
* Jakékoli stránky výjimek nebo obslužné rutiny nelze spustit. Odpověď musí být dokončena nebo bylo připojení přerušeno.

## <a name="server-exception-handling"></a>Zpracování výjimek serveru

Kromě logiky zpracování výjimek ve vaší aplikaci může [implementace serveru http](xref:fundamentals/servers/index) zpracovat některé výjimky. Pokud server zachytí výjimku před odesláním hlaviček odpovědi, server pošle *500 interní odpověď na chybu serveru* bez těla odpovědi. Pokud server zachytí výjimku po odeslání hlaviček odpovědi, server ukončí připojení. Požadavky, které nejsou zpracovávány vaší aplikací, jsou zpracovávány serverem aplikace. Jakákoli výjimka, ke které dojde, když server zpracovává požadavek, je zpracována zpracováním výjimek serveru. Vlastní chybové stránky aplikace, middleware zpracování výjimek a filtry toto chování neovlivňují.

## <a name="startup-exception-handling"></a>Zpracování výjimek při spuštění

Pouze hostující vrstva může zpracovat výjimky, které probíhají při spuštění aplikace. Hostitele je možné nakonfigurovat tak, aby [zachytával chyby při spuštění](xref:fundamentals/host/web-host#capture-startup-errors) a [zachytit podrobné chyby](xref:fundamentals/host/web-host#detailed-errors).

Hostující vrstva může zobrazit chybovou stránku pro zachycenou chybu spuštění pouze v případě, že dojde k chybě po vazbě adresy hostitele/portu. Pokud vazba neproběhne úspěšně:

* Vrstva hostování zaznamená kritickou výjimku.
* Proces dotnet selže.
* Pokud je server HTTP [Kestrel](xref:fundamentals/servers/kestrel), nezobrazí se žádná chybová stránka.

Při spuštění ve [službě IIS](/iis) (nebo Azure App Service) nebo v [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)se v případě, že se proces nespustí 502,5, vrátí [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) *selhání procesu* . Další informace naleznete v tématu <xref:test/troubleshoot-azure-iis>.

## <a name="database-error-page"></a>Chybová stránka databáze

Middleware chybové stránky databáze zaznamenává výjimky týkající se databáze, které je možné vyřešit pomocí Entity Framework migrace. Pokud dojde k těmto výjimkám, je vygenerována odpověď HTML s podrobnostmi o možných akcích k vyřešení problému. Tato stránka by měla být povolena pouze ve vývojovém prostředí. Povolit stránku přidáním kódu do `Startup.Configure` :

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A>vyžaduje balíček NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) .

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a>Filtry výjimek

V aplikacích MVC se filtry výjimek dají nakonfigurovat globálně nebo na základě jednoho řadiče nebo akce. V Razor aplikacích stránky je lze nakonfigurovat globálně nebo podle modelu stránky. Tyto filtry zpracovávají všechny neošetřené výjimky, ke kterým dojde během provádění akce kontroleru nebo jiného filtru. Další informace naleznete v tématu <xref:mvc/controllers/filters#exception-filters>.

> [!TIP]
> Filtry výjimek jsou užitečné pro vytváření přesahů výjimek, ke kterým dochází v rámci akcí MVC, ale nejsou tak flexibilní jako middleware zpracování výjimek. Doporučujeme použít middleware. Filtry používejte pouze v případě, že je třeba provést zpracování chyb odlišně v závislosti na vybrané akci MVC.

## <a name="model-state-errors"></a>Chyby stavu modelu

Informace o tom, jak zpracovat chyby stavu modelu, najdete v tématu [vázání modelů](xref:mvc/models/model-binding) a [ověřování modelu](xref:mvc/models/validation).

## <a name="additional-resources"></a>Další materiály

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
