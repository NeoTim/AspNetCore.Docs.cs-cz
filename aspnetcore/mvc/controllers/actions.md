---
title: Zpracování žádosti se řadiče v ASP.NET Core MVC
author: ardalis
description: ''
ms.author: riande
ms.date: 07/03/2017
uid: mvc/controllers/actions
ms.openlocfilehash: 952e4dbb2c4343ca87ace1535e4a5968faf088cf
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64902484"
---
# <a name="handle-requests-with-controllers-in-aspnet-core-mvc"></a>Zpracování žádosti se řadiče v ASP.NET Core MVC

Podle [Steve Smith](https://ardalis.com/) a [Scott Addie](https://github.com/scottaddie)

Kontrolerů, akce a výsledky akcí jsou základní součástí jak vývojářům vytvářet aplikace pomocí ASP.NET Core MVC.

## <a name="what-is-a-controller"></a>Co je řadičem?

Kontroler se používá k definování a seskupit sadu akcí. Akce (nebo *metody akce*) je metoda na řadiči, která zpracovává požadavky. Kontrolery logicky seskupit podobné akce. Tato agregace akce umožňuje společné sady pravidel, jako je směrování, ukládání do mezipaměti a autorizaci, použít společně. Požadavky se mapují na akce prostřednictvím [směrování](xref:mvc/controllers/routing).

Podle konvence třídy kontroleru:
* Jsou umístěny v projektu na úrovni root *řadiče* složky
* Dědí z `Microsoft.AspNetCore.Mvc.Controller`

Kontroler je třídu instantiable, ve kterém je aspoň jeden z následujících podmínek hodnotu true:
* Název třídy je přidán slovem "Controller"
* Třída dědí z třídy, jejíž název je přidán slovem "Controller"
* Třída je doplněn `[Controller]` atribut

Třída kontroleru nesmí mít přiřazený `[NonController]` atribut.

Postupujte podle řadiče [explicitní závislosti Princip](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies). Existuje několik přístupů k implementaci této zásady. Pokud více akce kontroleru vyžadují stejnou službu, zvažte použití [konstruktor vkládání](xref:mvc/controllers/dependency-injection#constructor-injection) k vyžádání těchto závislostí. Pokud služba je nutná pouze jednu akci metodou, zvažte použití [akce vkládání](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) požádat o závislost.

V rámci **M**odelu -**V**obrazit -**C**ontroller vzor kontroleru zodpovídá za počáteční zpracování požadavku a instance modelu. Obecně platí obchodní rozhodnutí je třeba provést v rámci modelu.

Kontroler přebírá výsledek zpracování modelu (pokud existuje) a vrátí správné zobrazení a jeho přidružené zobrazení data nebo výsledek volání rozhraní API. Další informace najdete na [přehled ASP.NET Core MVC](xref:mvc/overview) a [Začínáme s ASP.NET Core MVC a sady Visual Studio](xref:tutorials/first-mvc-app/start-mvc).

Je řadič *uživatelského rozhraní úrovni* abstrakce. Chcete-li data požadavku jsou platná a zvolit, které zobrazení (nebo výsledku pro rozhraní API) má být vrácen jsou jeho zodpovědnosti. V aplikacích pro skvěle neměl by zahrnovat přímo data access nebo obchodní logiku. Místo toho kontroleru deleguje se do služby zpracování tyto povinnosti.

## <a name="defining-actions"></a>Definování akcí

Veřejné metody na řadiči, s výjimkou dekorován `[NonAction]` atributu, jsou akce. Parametry pro akce jsou vázány na data žádosti a se ověřují pomocí [vazby modelu](xref:mvc/models/model-binding). Pro všechno, co je vytvořena vazba modelu dojde k ověření modelu. `ModelState.IsValid` Hodnota vlastnosti označuje, zda vazby modelu a ověření bylo úspěšné.

Metody akce by měla obsahovat logiku pro mapování požadavku na obchodní problém. Možné problémy obchodního charakteru by měly být zastoupeny obvykle jako služby, které kontroleru, ke kterému přistupuje přes [injektáž závislostí](xref:mvc/controllers/dependency-injection). Akce se mapují potom výsledek akce business do stavu aplikace.

Akce nic vrátit, ale často vracet instanci `IActionResult` (nebo `Task<IActionResult>` pro asynchronní metody), která vytváří odpověď. Metoda akce odpovídá pro výběr *druhu odpovědi*. Výsledek akce *nemá zpracování*.

### <a name="controller-helper-methods"></a>Metody Helper kontroleru

Kontrolery obvykle dědí [řadič](/dotnet/api/microsoft.aspnetcore.mvc.controller), i když to není nutné. Odvozování z `Controller` poskytuje přístup k tři kategorie pomocné metody:

#### <a name="1-methods-resulting-in-an-empty-response-body"></a>1. Metody, což vede prázdné tělo odpovědi

Ne `Content-Type` hlavičku HTTP odpovědi je zahrnuta, protože tělo odpovědi chybí obsah pro popis.

Existují dva typy výsledků v rámci této kategorie: Přesměrování a stavový kód HTTP.

* **Kód stavu HTTP**

    Tento typ, vrátí stavový kód HTTP. Několik pomocných metod třídy tohoto typu jsou `BadRequest`, `NotFound`, a `Ok`. Například `return BadRequest();` vytváří stavový kód 400 při spuštění. Pokud metody jako `BadRequest`, `NotFound`, a `Ok` jsou přetíženy, už kvalifikují jako respondéry stavového kódu protokolu HTTP, protože probíhat vyjednávání obsahu.

* **Redirect**

    Tento typ vrátí přesměrování na akci nebo cíl (pomocí `Redirect`, `LocalRedirect`, `RedirectToAction`, nebo `RedirectToRoute`). Například `return RedirectToAction("Complete", new {id = 123});` přesměruje `Complete`, předá anonymní objekt.

    Typ výsledku přesměrování se liší od typu stavový kód HTTP především v přidání `Location` hlavičku HTTP odpovědi.

#### <a name="2-methods-resulting-in-a-non-empty-response-body-with-a-predefined-content-type"></a>2. Výsledkem je tělo odpovědi neprázdný s typem obsahu předdefinované metody

Většina metod helper této kategorie patří `ContentType` vlastnost, což vám umožní nastavit `Content-Type` hlavičku odpovědi k popisu text odpovědi.

Existují dva typy výsledků v rámci této kategorie: [Zobrazení](xref:mvc/views/overview) a [ve formátu odpovědi](xref:web-api/advanced/formatting).

* **Zobrazení**

    Tento typ vrátí zobrazení, ve kterém se používá model k vykreslení HTML. Například `return View(customer);` předává modelu do zobrazení pro datovou vazbu.

* **Formátovaný odpovědi**

    Tento typ vrátí JSON nebo podobně jako formát výměny dat k reprezentaci objektu specifickým způsobem. Například `return Json(customer);` serializuje zadaný objekt do formátu JSON.
    
    Další běžné metody tohoto typu patří `File` a `PhysicalFile`. Například `return PhysicalFile(customerFilePath, "text/xml");` vrátí [PhysicalFileResult](/dotnet/api/microsoft.aspnetcore.mvc.physicalfileresult).

#### <a name="3-methods-resulting-in-a-non-empty-response-body-formatted-in-a-content-type-negotiated-with-the-client"></a>3. V typu obsahu vyjedná s klientem ve formátu metody výsledkem tělo odpovědi není prázdná

Tato kategorie se nazývá lépe **vyjednávání obsahu**. [Vyjednávání obsahu](xref:web-api/advanced/formatting#content-negotiation) pokaždé, když se vrátí akce se vztahuje [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult) typu nebo něco jiného než [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult) implementace. Akce, která vrátí non -`IActionResult` implementace (například `object`) také vrátí hodnotu ve formátu odpovědi.

Zahrnují několik pomocných metod třídy tohoto typu `BadRequest`, `CreatedAtRoute`, a `Ok`. Příklady těchto metod `return BadRequest(modelState);`, `return CreatedAtRoute("routename", values, newobject);`, a `return Ok(value);`v uvedeném pořadí. Všimněte si, že `BadRequest` a `Ok` provedení vyjednávání obsahu pouze v případě, že je předána hodnota; bez předávána hodnota, místo toho slouží jako typy výsledků stavového kódu protokolu HTTP. `CreatedAtRoute` Metodě na druhé straně vždy provede vyjednávání obsahu od jejich přetížení všechny vyžadují předat hodnotu.

### <a name="cross-cutting-concerns"></a>Související aspekty

Aplikace obvykle sdílet části jejich pracovního postupu. Mezi příklady patří aplikace, která ukládá do mezipaměti dat na některé stránky nebo aplikace vyžadující ověření pro přístup k nákupního košíku. Logika před nebo po metody akce, použijte *filtr*. Pomocí [filtry](xref:mvc/controllers/filters) na vyskytující aspekty můžete snížit duplicity.

Nejvíce atributy, jako například filtru `[Authorize]`, je možné použít na úroveň kontroler nebo akce v závislosti na požadované úrovni členitosti.

Zpracování chyb a ukládání odpovědí do mezipaměti jsou často vyskytující aspekty:
* [Ošetření chyb](xref:mvc/controllers/filters#exception-filters)
* [Ukládání odpovědí do mezipaměti](xref:performance/caching/response)

Mnoho vyskytující aspekty možné je zpracovávat pomocí filtrů nebo vlastní [middleware](xref:fundamentals/middleware/index).
