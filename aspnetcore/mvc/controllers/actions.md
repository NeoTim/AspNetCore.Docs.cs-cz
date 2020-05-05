---
title: Zpracování požadavků s řadiči ve službě ASP.NET Core MVC
author: ardalis
description: ''
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/actions
ms.openlocfilehash: b7c4d61c4a71939e84bdea180a2f77b6438b15d5
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774194"
---
# <a name="handle-requests-with-controllers-in-aspnet-core-mvc"></a>Zpracování požadavků s řadiči ve službě ASP.NET Core MVC

[Steve Smith](https://ardalis.com/) a [Scott Addie](https://github.com/scottaddie)

Řadiče, akce a výsledky akcí jsou základní součástí způsobu, jakým vývojáři sestavují aplikace pomocí ASP.NET Core MVC.

## <a name="what-is-a-controller"></a>Co je kontroler?

Kontroler se používá k definování a seskupení sady akcí. Akce (nebo *metoda akce*) je metoda na řadiči, který zpracovává požadavky. Řadiče logicky seskupují podobné akce dohromady. Tato agregace akcí umožňuje souhrnně použít společné sady pravidel, například směrování, ukládání do mezipaměti a autorizaci. Požadavky jsou mapovány na akce prostřednictvím [Směrování](xref:mvc/controllers/routing).

Podle konvence třídy kontroleru:

* Nachází se ve složce *řadičů* na úrovni kořenového projektu.
* Zdědit z `Microsoft.AspNetCore.Mvc.Controller`.

Kontroler je třída instantiable, ve které je splněná alespoň jedna z následujících podmínek:

* Název třídy je s `Controller`příponou.
* Třída dědí z třídy, jejíž název je přípona `Controller`.
* `[Controller]` Atribut je použit pro třídu.

Třída kontroleru nesmí mít přidružený `[NonController]` atribut.

Řadiče by měly dodržovat [Princip explicitní závislosti](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies). K implementaci tohoto principu existuje několik přístupů. Pokud více akcí kontroleru vyžaduje stejnou službu, zvažte použití [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) pro vyžádání těchto závislostí. Pokud je služba potřebná jenom pomocí jediné metody akce, můžete pro vyžádání závislosti použít [vkládání akcí](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) .

V rámci vzoru **M**Odel-**V**s**ontroller,** je kontrolor zodpovědný za počáteční zpracování žádosti a vytváření instancí modelu. Obecně platí, že podniková rozhodnutí by se měla provádět v rámci modelu.

Kontroler vezme výsledek zpracování modelu (pokud existuje) a vrátí buď správné zobrazení a jeho přidružená data zobrazení, nebo výsledek volání rozhraní API. Další informace najdete v článku [přehled ASP.NET Core MVC](xref:mvc/overview) a [Začínáme s ASP.NET Core MVC a sadou Visual Studio](xref:tutorials/first-mvc-app/start-mvc).

Kontroler je abstrakce na *úrovni uživatelského rozhraní* . Jejich zodpovědností je zajistit, aby data požadavku byla platná, a zvolili si, které zobrazení (nebo výsledek pro rozhraní API) by mělo být vráceno. Ve dobře se aplikacích nezahrnuje přímo přístup k datům ani obchodní logiku. Místo toho se správce deleguje ke službám, které tyto odpovědnosti zpracovávají.

## <a name="defining-actions"></a>Definování akcí

Veřejné metody na řadiči, s výjimkou `[NonAction]` atributů, jsou akce. Parametry v akcích jsou vázány na data požadavku a jsou ověřovány pomocí [vazby modelu](xref:mvc/models/model-binding). K ověřování modelu dochází pro všechny objekty, které jsou vázány na model. Hodnota `ModelState.IsValid` vlastnosti označuje, zda se vazba a ověření modelu zdařilo.

Metody akcí by měly obsahovat logiku pro mapování požadavků na obchodní obavy. Obchodní aspekty by se obvykle měly vystupovat jako služby, ke kterým řadič přistupuje prostřednictvím [Injektáže závislosti](xref:mvc/controllers/dependency-injection). Akce pak namapuje výsledek obchodní akce do stavu aplikace.

Akce mohou vracet cokoli, ale často vracejí instanci `IActionResult` (nebo `Task<IActionResult>` pro asynchronní metody), které vytvářejí odpověď. Metoda Action zodpovídá za výběr *typu odpovědi*. Výsledek akce *odpovídá*.

### <a name="controller-helper-methods"></a>Pomocné metody kontroleru

Řadiče obvykle dědí z [řadiče](/dotnet/api/microsoft.aspnetcore.mvc.controller), přestože to není vyžadováno. Odvození z `Controller` poskytuje přístup ke třem kategoriím pomocných metod:

#### <a name="1-methods-resulting-in-an-empty-response-body"></a>1. metody, které mají za následek prázdné tělo odpovědi

Není `Content-Type` obsažena hlavička odpovědi HTTP, protože tělo odpovědi nemá obsah k popisu.

V této kategorii existují dva typy výsledků: přesměrování a stavový kód HTTP.

* **Stavový kód HTTP**

    Tento typ vrátí stavový kód HTTP. Několik pomocných metod tohoto typu jsou `BadRequest`, `NotFound`a. `Ok` Například `return BadRequest();` generuje stavový kód 400 při spuštění. Když metody jako `BadRequest`, `NotFound`a `Ok` jsou přetíženy, již nejsou kvalifikovány jako reakce na stavový kód HTTP, protože probíhá vyjednávání obsahu.

* **Přesměrování**

    Tento typ vrátí přesměrování na akci nebo cíl ( `Redirect`pomocí, `LocalRedirect` `RedirectToAction`, nebo `RedirectToRoute`). Například `return RedirectToAction("Complete", new {id = 123});` přesměrování na `Complete`, předání anonymního objektu.

    Typ výsledku přesměrování se liší od typu stavového kódu HTTP primárně v kombinaci s hlavičkou odpovědi `Location` http.

#### <a name="2-methods-resulting-in-a-non-empty-response-body-with-a-predefined-content-type"></a>2. metody, které mají za následek neprázdné tělo odpovědi s předdefinovaným typem obsahu

Většina pomocných metod v této kategorii zahrnuje `ContentType` vlastnost, která umožňuje nastavit hlavičku `Content-Type` odpovědi pro popis těla odpovědi.

V této kategorii existují dva typy výsledků: [zobrazení](xref:mvc/views/overview) a [formátovaná odpověď](xref:web-api/advanced/formatting).

* **Zobrazit**

    Tento typ vrátí zobrazení, které používá model pro vykreslení kódu HTML. Například `return View(customer);` předává model do zobrazení pro datovou vazbu.

* **Naformátovaná odpověď**

    Tento typ vrátí JSON nebo podobný formát výměny dat, který reprezentuje objekt určitým způsobem. Například `return Json(customer);` serializaci poskytnutého objektu do formátu JSON.
    
    Mezi další běžné metody tohoto typu patří `File` a `PhysicalFile`. Například `return PhysicalFile(customerFilePath, "text/xml");` vrátí [PhysicalFileResult](/dotnet/api/microsoft.aspnetcore.mvc.physicalfileresult).

#### <a name="3-methods-resulting-in-a-non-empty-response-body-formatted-in-a-content-type-negotiated-with-the-client"></a>3. metody, které mají za následek neprázdné tělo odpovědi formátované v typu obsahu vyjednané s klientem

Tato kategorie je lépe známá jako **vyjednávání obsahu**. [Vyjednávání obsahu](xref:web-api/advanced/formatting#content-negotiation) se aplikuje vždy, když akce vrátí [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult) typ nebo něco jiného než implementace [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult) . Akce, která vrací`IActionResult` neimplementující (například), `object`vrátí také formátovanou odpověď.

Mezi pomocné metody tohoto typu patří `BadRequest`, `CreatedAtRoute`a. `Ok` Příklady těchto metod jsou `return BadRequest(modelState);`, `return CreatedAtRoute("routename", values, newobject);`, a `return Ok(value);`v uvedeném pořadí. Všimněte si `BadRequest` , `Ok` že a provede vyjednávání obsahu pouze v případě, že byla předána hodnota. bez předávání hodnoty místo toho slouží jako typy výsledku stavového kódu protokolu HTTP. `CreatedAtRoute` Na druhé straně, vždy provádí vyjednávání obsahu, protože jeho přetížení vyžaduje předání hodnoty.

### <a name="cross-cutting-concerns"></a>Otázky pro průřezy

Aplikace obvykle sdílí části svého pracovního postupu. Mezi příklady patří aplikace, která vyžaduje ověření pro přístup k nákupnímu košíku, nebo aplikaci, která na některých stránkách ukládá data do mezipaměti. Chcete-li provést logiku před nebo za metodou akce, použijte *Filtr*. Použití [filtrů](xref:mvc/controllers/filters) při průřezových záležitostech může snížit duplicity.

Většinu atributů filtru, jako `[Authorize]`je například, lze použít na úrovni řadiče nebo akce v závislosti na požadované úrovni členitosti.

Zpracování chyb a ukládání odpovědí do mezipaměti často souvisí mezi různými aspekty:
* [Ošetření chyb](xref:mvc/controllers/filters#exception-filters)
* [Ukládání odpovědí do mezipaměti](xref:performance/caching/response)

Mnoho obav v průřezech se dá zpracovat pomocí filtrů nebo vlastního [middlewaru](xref:fundamentals/middleware/index).
