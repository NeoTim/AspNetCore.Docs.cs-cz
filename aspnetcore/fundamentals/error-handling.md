---
title: Zpracování chyb v ASP.NET Core
author: rick-anderson
description: Objevte, jak zpracovávat chyby v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/error-handling
ms.openlocfilehash: da7f50b27e447b86bd8a06851b767488d51b7050
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90592888"
---
# <a name="handle-errors-in-aspnet-core"></a>Zpracování chyb v ASP.NET Core

::: moniker range=">= aspnetcore-5.0"

[Kirka Larkin](https://twitter.com/serpent5), [Dykstra](https://github.com/tdykstra/)a [Steve Smith](https://ardalis.com/)

Tento článek se věnuje běžným přístupům ke zpracování chyb v ASP.NET Core Web Apps. Podívejte se <xref:web-api/handle-errors> na webová rozhraní API.

[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples). ([Stažení](xref:index#how-to-download-a-sample).) Karta síť v nástrojích pro vývojáře v prohlížeči F12 je užitečná při testování ukázkové aplikace.

## <a name="developer-exception-page"></a>Stránka s výjimkou pro vývojáře

*Stránka s výjimkou vývojáře* zobrazuje podrobné informace o výjimkách žádostí. Šablony ASP.NET Core generují následující kód:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

Předchozí zvýrazněný kód povoluje stránku s výjimkou vývojářů, když aplikace běží ve [vývojovém prostředí](xref:fundamentals/environments).

Šablony jsou na <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> začátku v kanálu middlewaru, aby mohl zachytit výjimky vyvolané v middlewaru, který následuje.

Předchozí kód povoluje stránku výjimky vývojářů ***pouze*** v případě, že aplikace běží ve vývojovém prostředí. Podrobné informace o výjimce by se neměly veřejně zobrazovat, pokud je aplikace spuštěná v produkčním prostředí. Další informace o konfiguraci prostředí najdete v tématu <xref:fundamentals/environments> .

Stránka s výjimkou vývojářů obsahuje následující informace o výjimce a žádosti:

* Trasování zásobníku
* Parametry řetězce dotazu, pokud existují
* Cookies if
* Hlavičky

## <a name="exception-handler-page"></a>Stránka obslužné rutiny výjimek

Chcete-li nakonfigurovat vlastní stránku zpracování chyb pro [produkční prostředí](xref:fundamentals/environments), zavolejte <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> . Tato výjimka zpracovává middleware:

* Zachycuje a zaznamenává výjimky.
* Znovu spustí žádost v alternativním kanálu pomocí uvedené cesty. Požadavek se znovu nespustí, pokud byla odpověď spuštěná. Kód vygenerovaný šablonou znovu spustí požadavek pomocí `/Error` cesty.

V následujícím příkladu <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> přidá middleware pro zpracování výjimek v Nevývojovém prostředí:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

RazorŠablona aplikace stránky poskytuje chybovou stránku (*. cshtml*) a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> třídu ( `ErrorModel` ) ve složce *Pages* . V případě aplikace MVC obsahuje šablona projektu `Error` metodu akce a zobrazení chyb pro domovský kontroler.

Neoznačovat metodu akce obslužné rutiny chyb pomocí atributů metody HTTP, jako je například `HttpGet` . Explicitní příkazy zabraňují, aby některé žádosti dosáhly metody Action. Povolí anonymní přístup k metodě, pokud by se měli neověření uživatelé zobrazit v zobrazení chyby.

### <a name="access-the-exception"></a>Přístup k výjimce

Použijte <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> pro přístup k výjimce a původní cestě požadavku v obslužné rutině chyby. Následující kód přidá `ExceptionMessage` na výchozí *stránky/Error. cshtml. cs* vygenerované šablonami ASP.NET Core:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> Neslouží **klientům** informace o citlivých chybách. Obsluha chyb je bezpečnostní riziko.

Chcete-li otestovat výjimku v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):

* Nastavte prostředí na produkční.
* Odebere komentáře z `webBuilder.UseStartup<Startup>();` *program.cs*.
* Vyberte **aktivovat výjimku** na domovské stránce.

## <a name="exception-handler-lambda"></a>Lambda obslužné rutiny výjimek

Alternativou ke [stránce vlastní obslužné rutiny výjimek](#exception-handler-page) je poskytnout lambda výraz <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> . Použití lambda umožňuje přístup k chybě před vrácením odpovědi.

Následující kód používá lambda pro zpracování výjimek:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> **Neobsluhujte** citlivé informace o chybách z <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> klientů ani do nich <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> . Obsluha chyb je bezpečnostní riziko.

Testování výrazu lambda zpracování výjimek v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):

* Nastavte prostředí na produkční.
* Odebere komentáře z `webBuilder.UseStartup<StartupLambda>();` *program.cs*.
* Vyberte **aktivovat výjimku** na domovské stránce.

## <a name="usestatuscodepages"></a>UseStatusCodePages

Ve výchozím nastavení aplikace ASP.NET Core neposkytuje stavovou stránku pro stavové kódy chyb HTTP, například *404 – Nenalezeno*. Když aplikace nalezne chybový stav HTTP 400-499, který nemá tělo, vrátí stavový kód a prázdné tělo odpovědi. Chcete-li poskytnout stránky se stavovým kódem, použijte middleware stránky stavového kódu. Chcete-li povolit výchozí textové obslužné rutiny pro běžné chyby stavových kódů, zavolejte <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> v `Startup.Configure` metodě:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<!-- Review: 
When you comment out // UseExceptionHandler("/Error");`
you get a browser dependant error, not the codepage as I expected.
call /index/2 -> return StatusCode(500); -> you get the codepage 
-->

Zavolejte `UseStatusCodePages` před middlewarem zpracování žádosti. Například volejte `UseStatusCodePages` před middleware statických souborů a middleware koncových bodů.

Pokud se `UseStatusCodePages` nepoužívá, navigace na adresu URL bez koncového bodu vrátí chybovou zprávu závislou na prohlížeči, která indikuje, že koncový bod nejde najít. Přejděte například na `Home/Privacy2` . Při `UseStatusCodePages` volání funkce prohlížeč vrátí:

```html
Status Code: 404; Not Found
```

`UseStatusCodePages` se obvykle nepoužívá v produkčním prostředí, protože vrací zprávu, která není užitečná pro uživatele.

Testování `UseStatusCodePages` v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):

* Nastavte prostředí na produkční.
* Odebere komentáře z `webBuilder.UseStartup<StartupUseStatusCodePages>();` *program.cs*.
* Vyberte odkazy na domovské stránce na domovské stránce.

### <a name="usestatuscodepages-with-format-string"></a>UseStatusCodePages s formátovacím řetězcem

Chcete-li přizpůsobit typ obsahu odpovědi a text, použijte přetížení <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> , které přijímá typ obsahu a řetězec formátu:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

V předchozím kódu `{0}` je zástupný symbol pro kód chyby.

`UseStatusCodePages` řetězec formátu se obvykle nepoužívá v produkčním prostředí, protože vrací zprávu, která není užitečná pro uživatele.

Chcete-li otestovat `UseStatusCodePages` [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), odstraňte komentáře z `webBuilder.UseStartup<StartupFormat>();` v *program.cs*.

### <a name="usestatuscodepages-with-lambda"></a>UseStatusCodePages s výrazem lambda

Chcete-li určit vlastní kód pro zpracování chyb a psaní odezvy, použijte přetížení <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> , které přijímá výraz lambda:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

`UseStatusCodePages` výraz lambda se obvykle nepoužívá v produkčním prostředí, protože vrací zprávu, která není užitečná pro uživatele.

Chcete-li otestovat `UseStatusCodePages` [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), odstraňte komentáře z `webBuilder.UseStartup<StartupStatusLambda>();` v *program.cs*.

### <a name="usestatuscodepageswithredirects"></a>UseStatusCodePagesWithRedirects

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>Metoda rozšíření:

* Odešle klientovi stavový kód [302](https://developer.mozilla.org/docs/Web/HTTP/Status/302) .
* Přesměruje klienta na koncový bod zpracování chyb uvedený v šabloně URL. Koncový bod pro zpracování chyb obvykle zobrazí informace o chybě a vrátí HTTP 200.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

Šablona adresy URL může obsahovat `{0}` zástupný symbol pro stavový kód, jak je znázorněno v předchozím kódu. Pokud šablona URL začíná na `~` (vlnovku), `~` je nahrazena aplikací `PathBase` . Při zadávání koncového bodu v aplikaci vytvořte zobrazení MVC nebo Razor stránku pro koncový bod. RazorPříklad stránky naleznete v tématu [Pages/MyStatusCode. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).

Tato metoda se běžně používá, když aplikace:

* By měl přesměrování klienta na jiný koncový bod, obvykle v případech, kdy se chyba zpracovává v jiné aplikaci. V případě webových aplikací zobrazuje panel Adresa v prohlížeči klienta přesměrovaný koncový bod.
* Neměl by zachovat a vracet původní stavový kód s počáteční odpovědí přesměrování.

Chcete-li otestovat `UseStatusCodePages` [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), odstraňte komentáře z `webBuilder.UseStartup<StartupSCredirect>();` v *program.cs*.

### <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>Metoda rozšíření:

* Vrátí původní stavový kód klientovi.
* Vygeneruje tělo odpovědi opakovaným spuštěním kanálu požadavků pomocí alternativní cesty.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

Pokud je v aplikaci zadaný koncový bod, vytvořte zobrazení MVC nebo Razor stránku pro koncový bod. Zajistěte, aby bylo `UseStatusCodePagesWithReExecute` `UseRouting` možné žádost přesměrovat na stavovou stránku. RazorPříklad stránky naleznete v tématu [Pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).

Tato metoda se běžně používá v případě, kdy by měla aplikace:

* Zpracuje požadavek bez přesměrování na jiný koncový bod. V případě webových aplikací zobrazuje panel Adresa v prohlížeči klienta původní požadovaný koncový bod.
* Zachovejte a vraťte původní stavový kód s odpovědí.

Šablony URL a řetězce dotazů můžou obsahovat zástupný symbol `{0}` pro stavový kód. Šablona adresy URL musí začínat na `/` .

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

Koncový bod, který zpracovává chybu, může získat původní adresu URL, která generovala chybu, jak je znázorněno v následujícím příkladu:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

RazorPříklad stránky naleznete v tématu [Pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).

Chcete-li otestovat `UseStatusCodePages` [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), odstraňte komentáře z `webBuilder.UseStartup<StartupSCreX>();` v *program.cs*.

## <a name="disable-status-code-pages"></a>Zakázat stavové stránky

Chcete-li zakázat stránky stavového kódu pro řadič MVC nebo metodu akce, použijte atribut [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) .

Chcete-li zakázat stránky stavového kódu pro konkrétní požadavky v Razor metodě obslužné rutiny stránky nebo v KONTROLERU MVC, použijte <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a>Kód pro zpracování výjimek

Kód na stránkách zpracování výjimek může také vyvolat výjimky. Produkční chybové stránky by se měly důkladně testovat a měly by se věnovat zvýšené péči, aby nedocházelo k vygenerování výjimek jejich vlastních.
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a>Hlavičky odpovědi

Po odeslání hlaviček pro odpověď:

* Aplikace nemůže změnit stavový kód odpovědi.
* Jakékoli stránky výjimek nebo obslužné rutiny nelze spustit. Odpověď musí být dokončena nebo bylo připojení přerušeno.

## <a name="server-exception-handling"></a>Zpracování výjimek serveru

Kromě logiky zpracování výjimek v aplikaci může [implementace serveru http](xref:fundamentals/servers/index) zpracovat určité výjimky. Pokud server před odesláním hlaviček odpovědi zachytí výjimku, server odešle `500 - Internal Server Error` odpověď bez těla odpovědi. Pokud server zachytí výjimku po odeslání hlaviček odpovědi, server ukončí připojení. Požadavky, které nejsou zpracovávány aplikací, jsou zpracovávány serverem. Jakákoli výjimka, ke které dojde, když server zpracovává požadavek, je zpracována zpracováním výjimek serveru. Vlastní chybové stránky aplikace, middleware zpracování výjimek a filtry toto chování neovlivňují.

## <a name="startup-exception-handling"></a>Zpracování výjimek při spuštění

Pouze hostující vrstva může zpracovat výjimky, které probíhají při spuštění aplikace. Hostitele je možné nakonfigurovat tak, aby [zachytával chyby při spuštění](xref:fundamentals/host/web-host#capture-startup-errors) a [zachytit podrobné chyby](xref:fundamentals/host/web-host#detailed-errors).

Hostující vrstva může zobrazit chybovou stránku pro zachycenou chybu spuštění pouze v případě, že dojde k chybě po vazbě adresy hostitele/portu. Pokud vazba neproběhne úspěšně:

* Vrstva hostování zaznamená kritickou výjimku.
* Proces dotnet selže.
* Pokud je server HTTP [Kestrel](xref:fundamentals/servers/kestrel), nezobrazí se žádná chybová stránka.

Při spuštění ve [službě IIS](/iis) (nebo Azure App Service) nebo v [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)se v případě, že se proces nespustí 502,5, vrátí [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) *selhání procesu* . Další informace naleznete v tématu <xref:test/troubleshoot-azure-iis>.

## <a name="database-error-page"></a>Chybová stránka databáze

Filtr výjimek stránky pro vývojáře databáze `AddDatabaseDeveloperPageExceptionFilter` zachycuje výjimky týkající se databáze, které lze vyřešit pomocí Entity Framework Core migrace. Pokud dojde k těmto výjimkám, je vygenerována odpověď HTML s podrobnostmi o možných akcích pro vyřešení problému. Tato stránka je povolena pouze ve vývojovém prostředí. Následující kód byl vygenerován Razor šablonami ASP.NET Core stránky, pokud byly zadány jednotlivé uživatelské účty:

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a>Filtry výjimek

V aplikacích MVC se filtry výjimek dají nakonfigurovat globálně nebo na základě jednoho řadiče nebo akce. V Razor aplikacích stránky je lze nakonfigurovat globálně nebo podle modelu stránky. Tyto filtry zpracovávají všechny neošetřené výjimky, ke kterým dojde během provádění akce kontroleru nebo jiného filtru. Další informace naleznete v tématu <xref:mvc/controllers/filters#exception-filters>.

Filtry výjimek jsou užitečné pro vytváření přesahů výjimek, ke kterým dochází v rámci akcí MVC, ale nejsou tak flexibilní jako vestavěné [middleware pro zpracování výjimek](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs) `UseExceptionHandler` . Doporučujeme použít `UseExceptionHandler` , pokud nepotřebujete provádět zpracování chyb odlišně v závislosti na tom, která akce MVC je vybrána.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a>Chyby stavu modelu

Informace o tom, jak zpracovat chyby stavu modelu, najdete v tématu [vázání modelů](xref:mvc/models/model-binding) a [ověřování modelu](xref:mvc/models/validation).

## <a name="additional-resources"></a>Další zdroje

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Tím, že  [Dykstra](https://github.com/tdykstra/)a [Steve Smith](https://ardalis.com/)

Tento článek se věnuje běžným přístupům ke zpracování chyb v ASP.NET Core Web Apps. Podívejte se <xref:web-api/handle-errors> na webová rozhraní API.

[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples). ([Stažení](xref:index#how-to-download-a-sample).)

## <a name="developer-exception-page"></a>Stránka s výjimkou pro vývojáře

*Stránka s výjimkou vývojáře* zobrazuje podrobné informace o výjimkách žádostí. Šablony ASP.NET Core generují následující kód:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

Předchozí kód povoluje stránku s výjimkou vývojářů, když aplikace běží ve [vývojovém prostředí](xref:fundamentals/environments).

Šablony jsou umístěné <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> před jakýmkoli middlewarem, takže výjimky jsou zachyceny v middlewaru, který následuje.

Předchozí kód povoluje stránku výjimky vývojářů **pouze v případě, že aplikace běží ve vývojovém prostředí**. Podrobné informace o výjimce by se neměly veřejně zobrazovat, pokud je aplikace spuštěná v produkčním prostředí. Další informace o konfiguraci prostředí najdete v tématu <xref:fundamentals/environments> .

Stránka s výjimkou vývojářů obsahuje následující informace o výjimce a žádosti:

* Trasování zásobníku
* Parametry řetězce dotazu, pokud existují
* Cookies if
* Hlavičky

## <a name="exception-handler-page"></a>Stránka obslužné rutiny výjimek

Chcete-li nakonfigurovat vlastní stránku zpracování chyb pro produkční prostředí, použijte middleware pro zpracování výjimek. Middleware:

* Zachycuje a zaznamenává výjimky.
* Znovu spustí požadavek v alternativním kanálu pro uvedenou stránku nebo kontroler. Požadavek se znovu nespustí, pokud byla odpověď spuštěná. Kód vygenerovaný šablonou znovu spustí požadavek na `/Error` .

V následujícím příkladu <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> přidá middleware pro zpracování výjimek v Nevývojovém prostředí:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

RazorŠablona aplikace stránky poskytuje chybovou stránku (*. cshtml*) a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> třídu ( `ErrorModel` ) ve složce *Pages* . V případě aplikace MVC obsahuje šablona projektu metodu chybové akce a zobrazení chyby v domovském řadiči.

Neoznačovat metodu akce obslužné rutiny chyb pomocí atributů metody HTTP, jako je například `HttpGet` . Explicitní příkazy brání, aby některé žádosti dosáhly metody. Povolí anonymní přístup k metodě, pokud by se měli neověření uživatelé zobrazit v zobrazení chyby.

### <a name="access-the-exception"></a>Přístup k výjimce

Použijte <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> pro přístup k výjimce a původní cestě požadavku v řadiči obslužné rutiny chyb nebo na stránce:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> Neslouží **klientům** informace o citlivých chybách. Obsluha chyb je bezpečnostní riziko.

Chcete-li aktivovat předchozí stránku zpracování výjimek, nastavte prostředí na produkci a vynuťte výjimku.

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

Middleware zpřístupňuje balíček [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) .

Chcete-li povolit výchozí textové obslužné rutiny pro běžné chyby stavových kódů, zavolejte <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> v `Startup.Configure` metodě:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

Volejte `UseStatusCodePages` před middlewarem zpracování požadavků (například middleware pro statický soubor a middleware Mvc).

Pokud se `UseStatusCodePages` nepoužívá, navigace na adresu URL bez koncového bodu vrátí chybovou zprávu závislou na prohlížeči, která indikuje, že koncový bod nejde najít. Přejděte například na `Home/Privacy2` . Při `UseStatusCodePages` volání funkce prohlížeč vrátí:

```
Status Code: 404; Not Found
```

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

Šablona adresy URL může obsahovat `{0}` zástupný symbol pro stavový kód, jak je znázorněno v příkladu. Pokud šablona URL začíná na `~` (vlnovku), `~` je nahrazena aplikací `PathBase` . Pokud odkazujete na koncový bod v rámci aplikace, vytvořte zobrazení MVC nebo Razor stránku pro koncový bod. RazorPříklad stránky naleznete v tématu *Pages/StatusCode. cshtml* v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

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

<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> vyžaduje balíček NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) .

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

::: moniker-end
