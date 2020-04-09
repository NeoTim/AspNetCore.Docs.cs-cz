---
title: Zpracování chyb v ASP.NET jádru
author: rick-anderson
description: Zjistěte, jak zpracovat chyby v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: fundamentals/error-handling
ms.openlocfilehash: 28b463bccfb8aff4d10b95aa9a984455b4f4b976
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658814"
---
# <a name="handle-errors-in-aspnet-core"></a>Zpracování chyb v ASP.NET jádru

Tom [Dykstra](https://github.com/tdykstra/) a [Steve Smith](https://ardalis.com/)

Tento článek popisuje běžné přístupy ke zpracování chyb v ASP.NET webových aplikací core. Viz <xref:web-api/handle-errors> webová api.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples). ([Jak stáhnout](xref:index#how-to-download-a-sample).) Článek obsahuje pokyny, jak nastavit direktivy preprocesoru (`#if`, `#endif`, `#define`) v ukázkové aplikaci, aby bylo možné povolit různé scénáře.

## <a name="developer-exception-page"></a>Stránka výjimky pro vývojáře

*Stránka výjimky vývojáře* zobrazuje podrobné informace o výjimkách požadavku. Stránka je zpřístupněna balíčkem [Microsoft.AspNetCore.Diagnostics,](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) který je v [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Přidejte kód `Startup.Configure` k metodě, která povoluje stránku, když je aplikace spuštěná ve vývojovém [prostředí](xref:fundamentals/environments):

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

Umístěte volání <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> před všechny middleware, které chcete zachytit výjimky.

> [!WARNING]
> Stránku výjimky pro vývojáře povolte **pouze v případě, že je aplikace spuštěná ve vývojovém prostředí**. Nechcete sdílet podrobné informace o výjimce veřejně, když se aplikace spustí v produkčním prostředí. Další informace o konfiguraci prostředí <xref:fundamentals/environments>naleznete v tématu .

Stránka obsahuje následující informace o výjimce a požadavku:

* Trasování zásobníku
* Parametry řetězce dotazu (pokud existuje)
* Cookies (pokud nějaké)
* Hlavičky

Chcete-li zobrazit stránku výjimky pro `DevEnvironment` vývojáře v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte direktivu preprocesoru a na domovské stránce vyberte **Aktivovat výjimku.**

## <a name="exception-handler-page"></a>Stránka obslužné rutiny výjimky

Chcete-li nakonfigurovat vlastní stránku zpracování chyb pro produkční prostředí, použijte middleware pro zpracování výjimek. Middleware:

* Úlovky a protokoly výjimky.
* Znovu provede požadavek v alternativním kanálu pro uvedenou stránku nebo řadič. Požadavek není znovu proveden, pokud byla odpověď spuštěna.

V následujícím příkladu <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> přidá middleware pro zpracování výjimek v prostředích, která nejsou ve vývoji:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

Šablona aplikace Razor Pages obsahuje chybovou stránku`ErrorModel`(*.cshtml*) a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> třídu ( ) ve složce *Stránky.* Pro aplikaci MVC obsahuje šablona projektu metodu akce Chyba a zobrazení chyby. Zde je akční metoda:

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

Neoznačte metodu akce obslužné rutiny chyby atributy metody HTTP, například `HttpGet`. Explicitní slovesa zabránit některé požadavky z dosažení metody. Povolit anonymní přístup k metodě tak, aby neověřené uživatelé mohli získat zobrazení chyby.

### <a name="access-the-exception"></a>Přístup k výjimce

Slouží <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> k přístupu k výjimce a původní cestě požadavku v řadiči obslužné rutiny chyb nebo na stránce:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> **Neobsluhovat** citlivé informace o chybách klientům. Chyby obsluhy jsou bezpečnostní riziko.

Chcete-li zobrazit stránku zpracování výjimek `ProdEnvironment` `ErrorHandlerPage` v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte direktivy a preprocesora a vyberte **aktivovat výjimku** na domovské stránce.

## <a name="exception-handler-lambda"></a>Obslužná rutina výjimky lambda

Alternativou k [vlastní stránce obslužné rutiny výjimky](#exception-handler-page) je poskytnout lambda na <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>. Použití lambda umožňuje přístup k chybě před vrácením odpovědi.

Tady je příklad použití lambdy pro zpracování výjimek:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

V předchozím kódu `await context.Response.WriteAsync(new string(' ', 512));` je přidán, takže prohlížeč Internet Explorer zobrazí chybovou zprávu, nikoli iE chybová zpráva. Další informace naleznete v [tomto problému GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16144).

> [!WARNING]
> **Neobsluhovat** citlivé informace <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> o chybách od klientů nebo klientům. Chyby obsluhy jsou bezpečnostní riziko.

Chcete-li zobrazit výsledek zpracování výjimky lambda v `ProdEnvironment` `ErrorHandlerLambda` [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte a preprocesordirektia a vyberte **Aktivovat výjimku** na domovské stránce.

## <a name="usestatuscodepages"></a>UseStatusCodePages

Ve výchozím nastavení aplikace ASP.NET Core neposkytuje stránku se stavovým kódem pro stavové kódy HTTP, například *404 – nebylo nalezeno*. Aplikace vrátí stavový kód a prázdné tělo odpovědi. Chcete-li poskytnout stránky se stavovým kódem, použijte middleware Stránky stavového kódu.

Middleware je k dispozici prostřednictvím balíčku [Microsoft.AspNetCore.Diagnostics,](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) který je v [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Chcete-li povolit výchozí obslužné <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> rutiny `Startup.Configure` pouze text pro běžné kódy stavu chyb, volejte metodu:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

Volání `UseStatusCodePages` před požadavkem zpracování middleware (například statické soubor middleware a MVC Middleware).

Tady je příklad textu zobrazeného výchozími obslužnými rutinami:

```
Status Code: 404; Not Found
```

Chcete-li zobrazit jeden z různých formátů stavového kódu stránky v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte jednu ze směrnic preprocesoru, které začínají na `StatusCodePages`a vyberte **Aktivovat 404** na domovské stránce.

## <a name="usestatuscodepages-with-format-string"></a>UseStatusCodePages s formátovacím řetězcem

Chcete-li přizpůsobit typ obsahu odpovědi a <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> text, použijte přetížení, které přebírá typ obsahu a formátovací řetězec:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a>UseStatusCodePages s lambdou

Chcete-li zadat vlastní kód pro zpracování chyb <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> a psaní odpovědí, použijte přetížení, které přebírá výraz lambda:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a>UseStatusCodePagesWithRedirects

Metoda <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> rozšíření:

* Odešle klientovi stavový kód *302 - Found.*
* Přesměruje klienta do umístění uvedeného v šabloně adresy URL.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

Šablona adresy URL `{0}` může obsahovat zástupný symbol pro stavový kód, jak je znázorněno v příkladu. Pokud šablona adresy URL začíná vlnovkou (~), bude vlnovka nahrazena šablonou aplikace `PathBase`. Pokud ukážete na koncový bod v rámci aplikace, vytvořte zobrazení MVC nebo Razor stránku pro koncový bod. Příklad Stránky razor viz *Stránky/StatusCode.cshtml* v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Tato metoda se běžně používá, když aplikace:

* By měl přesměrovat klienta na jiný koncový bod, obvykle v případech, kdy jiná aplikace zpracovává chybu. U webových aplikací odráží adresní řádek prohlížeče klienta přesměrovaný koncový bod.
* Nemělby zachovat a vrátit původní stavový kód s počáteční redirect odpověď.

## <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

Metoda <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> rozšíření:

* Vrátí klientovi původní stavový kód.
* Generuje tělo odpovědi opětovným spuštěním kanálu požadavku pomocí alternativní cesty.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

Pokud ukážete na koncový bod v rámci aplikace, vytvořte zobrazení MVC nebo Razor stránku pro koncový bod. Příklad Stránky razor viz *Stránky/StatusCode.cshtml* v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Tato metoda se běžně používá, když by aplikace měla:

* Zpracujte požadavek bez přesměrování na jiný koncový bod. U webových aplikací odráží adresní řádek prohlížeče klienta původně požadovaný koncový bod.
* Zachovat a vrátit původní stavový kód s odpovědí.

Šablony adres URL a řetězců dotazu`{0}`mohou obsahovat zástupný symbol ( ) pro stavový kód. Šablona adresy URL musí`/`začínat lomítkem ( ). Při použití zástupného symbolu v cestě zkontrolujte, zda koncový bod (stránka nebo řadič) může segment cesty zpracovat. Například razor Page pro chyby by měla přijmout `@page` volitelnou hodnotu segmentu cesty se směrnicí:

```cshtml
@page "{code?}"
```

Koncový bod, který zpracovává chybu může získat původní adresu URL, která generovala chybu, jak je znázorněno v následujícím příkladu:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a>Zakázání stránek stavového kódu

Chcete-li zakázat stránky stavového kódu pro řadič [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) nebo metodu akce MVC, použijte atribut.

Chcete-li zakázat stránky stavového kódu pro konkrétní požadavky v metodě <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>obslužné rutiny Razor Pages nebo v řadiči MVC, použijte :

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a>Kód pro zpracování výjimek

Kód na stránkách zpracování výjimek může vyvolat výjimky. Často je vhodné, aby se stránky výrobních chyb skládaly z čistě statického obsahu.

### <a name="response-headers"></a>Hlavičky odpovědi

Po odeslání záhlaví pro odpověď:

* Aplikace nemůže změnit stavový kód odpovědi.
* Stránky nebo obslužné rutiny výjimek nelze spustit. Odpověď musí být dokončena nebo připojení přerušeno.

## <a name="server-exception-handling"></a>Zpracování výjimek serveru

Kromě logiky zpracování výjimek ve vaší aplikaci může [implementace http serveru](xref:fundamentals/servers/index) zpracovat některé výjimky. Pokud server zachytí výjimku před odesláním hlaviček odpovědí, odešle odpověď *500 - Internal Server Error* bez těla odpovědi. Pokud server zachytí výjimku po odeslání hlavičky odpovědí, server ukončí připojení. Požadavky, které vaše aplikace nezpracovává, zpracovává server. Jakákoli výjimka, ke které dojde, když server zpracovává požadavek, je zpracována zpracováním výjimek serveru. Vlastní chybové stránky aplikace, výjimky zpracování middleware a filtry nemají vliv na toto chování.

## <a name="startup-exception-handling"></a>Zpracování výjimek při spuštění

Pouze vrstva hostování může zpracovávat výjimky, ke kterým dochází při spuštění aplikace. Hostitele lze nakonfigurovat tak, aby [zaznamenával chyby při spuštění](xref:fundamentals/host/web-host#capture-startup-errors) a [podrobné chyby](xref:fundamentals/host/web-host#detailed-errors).

Hostitelská vrstva může zobrazit chybovou stránku pro zachycenou chybu při spuštění pouze v případě, že k chybě dojde po vazbě adresy/portu hostitele. Pokud vazba selže:

* Hostitelská vrstva zaznamenává kritickou výjimku.
* Dotnet proces dojde k chybě.
* Pokud je http server [Kestrel](xref:fundamentals/servers/kestrel), nezobrazí se žádná chybová stránka .

Při spuštění ve službě [IIS](/iis) (nebo služby Azure App Service) nebo [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)je *502.5 – selhání procesu* vrácena [ASP.NET základního modulu,](xref:host-and-deploy/aspnet-core-module) pokud proces nelze spustit. Další informace naleznete v tématu <xref:test/troubleshoot-azure-iis>.

## <a name="database-error-page"></a>Chybová stránka databáze

Middleware s chybou databáze zachycuje výjimky související s databází, které lze vyřešit pomocí migrace entity framework. Dojde-li k těmto výjimkám, je vygenerována odpověď HTML s podrobnostmi o možných akcích k vyřešení problému. Tato stránka by měla být povolena pouze ve vývojovém prostředí. Povolte stránku přidáním kódu do aplikace `Startup.Configure`:

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a>Filtry výjimek

V aplikacích MVC lze filtry výjimek konfigurovat globálně nebo na základě kontroleru nebo akce. V aplikacích Razor Pages je lze konfigurovat globálně nebo na stránce modelu. Tyto filtry zpracovávají všechny neošetřené výjimky, ke kterým dochází při provádění akce kontroleru nebo jiného filtru. Další informace naleznete v tématu <xref:mvc/controllers/filters#exception-filters>.

> [!TIP]
> Filtry výjimek jsou užitečné pro vytváření přesahů výjimky, ke kterým dochází v rámci akcí MVC, ale nejsou tak flexibilní jako middleware pro zpracování výjimek. Doporučujeme používat middleware. Filtry používejte pouze tam, kde je třeba provádět zpracování chyb odlišně podle toho, která akce MVC je vybrána.

## <a name="model-state-errors"></a>Chyby stavu modelu

Informace o tom, jak zpracovat chyby stavu modelu, naleznete v [tématu Model vazby](xref:mvc/models/model-binding) a [model ověření](xref:mvc/models/validation).

## <a name="additional-resources"></a>Další zdroje

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
