---
title: Logicer Test Controller v ASP.NET Core
author: ardalis
description: Naučte se testovat logiku kontroleru v ASP.NET Core s MOQ a xUnit.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/testing
ms.openlocfilehash: 4deae7f7511e3ce94450bc06d5fc8dc77a94f212
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767080"
---
# <a name="unit-test-controller-logic-in-aspnet-core"></a>Logika kontroleru testu jednotek v ASP.NET Core

[Steve Smith](https://ardalis.com/)

::: moniker range=">= aspnetcore-3.0"

[Testy jednotek](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) zahrnují testování součásti aplikace v izolaci od jejich infrastruktury a závislostí. V případě logiky kontroleru testování jednotek je testován pouze obsah jedné akce, nikoli chování jeho závislostí nebo samotného rozhraní.

## <a name="unit-testing-controllers"></a>Řadiče testování částí

Nastavte testy jednotek pro akce kontroleru, abyste se mohli soustředit na chování řadiče. Test jednotek kontroleru zabraňuje scénářům, jako jsou [filtry](xref:mvc/controllers/filters), [Směrování](xref:fundamentals/routing)a [vazby modelů](xref:mvc/models/model-binding). Testy, které pokrývají interakce mezi součástmi, které společně reagují na žádost, jsou zpracovávány *integračními testy*. Další informace o integračních testech naleznete v <xref:test/integration-tests>tématu.

Pokud vytváříte vlastní filtry a trasy, jednotkové testy je třeba izolovat, nikoli jako součást testů na konkrétní akci kontroleru.

Chcete-li předvést testy jednotek kontrol, zkontrolujte následující kontroler v ukázkové aplikaci. 

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/testing/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

V domovském řadiči se zobrazuje seznam relací debaty a umožňuje vytváření nových relací debaty s požadavkem POST:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

Předchozí kontroler:

* Následuje po [principu explicitních závislostí](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).
* Očekává [vložení závislosti (di)](xref:fundamentals/dependency-injection) k poskytnutí instance `IBrainstormSessionRepository`.
* Dá se testovat pomocí napodobné `IBrainstormSessionRepository` služby pomocí objektové architektury, jako je [MOQ](https://www.nuget.org/packages/Moq/). Napodobný *objekt* je prefabrikované objekt s předem stanovenou sadou vlastností a způsobu použití pro testování. Další informace najdete v tématu [Úvod do integračních testů](xref:test/integration-tests#introduction-to-integration-tests).

`HTTP GET Index` Metoda nemá žádné smyček ani větvení a volá jenom jednu metodu. Testování částí pro tuto akci:

* Napodobuje `IBrainstormSessionRepository` službu pomocí `GetTestSessions` metody. `GetTestSessions`Vytvoří dvě modely pracovních debat s daty a názvy relací.
* Spustí `Index` metodu.
* Provede kontrolní výrazy u výsledku vráceného metodou:
  * <xref:Microsoft.AspNetCore.Mvc.ViewResult> Je vrácen.
  * [ViewDataDictionary. model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) je `StormSessionViewModel`.
  * V portálu jsou uloženy dvě relace debaty `ViewDataDictionary.Model`.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

Testy `HTTP POST Index` metody v domácím řadiči ověří, že:

* Pokud je `false` [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) , metoda Action vrátí *400 Chybný požadavek* <xref:Microsoft.AspNetCore.Mvc.ViewResult> s příslušnými daty.
* Kdy `ModelState.IsValid` je `true`:
  * Je `Add` volána metoda v úložišti.
  * A <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> se vrátí se správnými argumenty.

Neplatný stav modelu je testován přidáním chyb pomocí <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> , jak je znázorněno v prvním testu níže:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

Když je [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) neplatný, vrátí se `ViewResult` stejná jako pro požadavek GET. Test se nepokusí předat neplatný model. Předání neplatného modelu není platný přístup, protože vazba modelu není spuštěná (i když [test integrace](xref:test/integration-tests) používá vazbu modelu). V takovém případě není testována vazba modelu. Tyto testy jednotek testuje pouze kód v metodě Action.

Druhý test ověří, zda `ModelState` je platný:

* Přidá se `BrainstormSession` nový (přes úložiště).
* Metoda vrátí `RedirectToActionResult` s očekávanými vlastnostmi.

Napodobovaná volání, která nejsou volána, jsou obvykle ignorována, ale volání `Verifiable` na konci volání instalace umožňuje v testu vzhled. To se provádí s voláním `mockRepo.Verify`, které nefunguje test, pokud se očekávaná metoda nevolala.

> [!NOTE]
> Knihovna MOQ použitá v této ukázce umožňuje kombinovat ověřitelná, nebo "striktní", napodobná neověřitelnými písmeny (označované také jako "volné" nebo zástupné procedury). Přečtěte si další informace o přizpůsobení napodobení [chování pomocí MOQ](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).

[SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) v ukázkové aplikaci zobrazuje informace týkající se konkrétní relace debaty. Kontroler obsahuje logiku k zaznamenání `id` neplatných hodnot ( `return` v následujícím příkladu existují dva scénáře, které se týkají těchto scénářů). Poslední `return` příkaz vrátí nové `StormSessionViewModel` zobrazení (*Controllers/SessionController. cs*):

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

Testy jednotek zahrnují jeden test pro každý `return` scénář v akci řadiče `Index` relace:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

Přesunutí na kontroler nápadů aplikace zpřístupňuje funkce jako webové rozhraní API v `api/ideas` trase:

* Pomocí `ForSession` metody je vrácen seznam`IdeaDTO`nápadů () přidružených k relaci debaty.
* `Create` Metoda přidává do relace nové nápady.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

Vyhněte se vracení entit obchodních domén přímo prostřednictvím volání rozhraní API. Entity domény:

* Často zahrnuje více dat, než vyžaduje klient nástroje.
* Nenutně navýšení interního doménového modelu aplikace s veřejně vystaveným rozhraním API.

Mapování mezi doménovými entitami a typy vrácenými do klienta lze provést:

* Ručně pomocí LINQ `Select`, jak ukázková aplikace používá. Další informace naleznete v tématu [LINQ (Language Integrated Query)](/dotnet/standard/using-linq).
* Automaticky pomocí knihovny, například automatického [mapovače](https://github.com/AutoMapper/AutoMapper).

V dalším kroku Tato ukázková aplikace ukazuje testy jednotek `Create` rozhraní `ForSession` API a kontroleru nápadů.

Ukázková aplikace obsahuje dva `ForSession` testy. První test určuje, zda `ForSession` vrátí hodnotu <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (http Nenalezeno) pro neplatnou relaci:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

Druhý `ForSession` test určuje, zda `ForSession` vrátí seznam nápadů relace (`<List<IdeaDTO>>`) pro platnou relaci. Kontroly také prověří první nápad a potvrdí, že `Name` jeho vlastnost je správná:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

Chcete-li otestovat chování `Create` metody, pokud `ModelState` je neplatná, ukázková aplikace přidá do kontroleru chybu modelu v rámci testu. Nepokoušejte se testovat ověřování modelu nebo vazbu modelů v testech&mdash;jednotek stačí testovat chování metody akce, pokud je začínat neplatným: `ModelState`

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

Druhý test nástroje `Create` závisí na vráceném `null`úložišti, takže je nakonfigurované navýšení úložiště. `null` Není nutné vytvářet testovací databázi (v paměti nebo jinak) a vytvořit dotaz, který tento výsledek vrátí. Test lze provést v jediném příkazu, protože ukázka kódu ilustruje:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

Třetí `Create` test `Create_ReturnsNewlyCreatedIdeaForSession`ověří, zda je volána `UpdateAsync` metoda úložiště. Tento maketa je volána `Verifiable`s a je volána `Verify` metoda označeného úložiště k potvrzení, že je provedena ověřitelná metoda. Nejedná se o zodpovědnost za testování jednotek, aby bylo zajištěno, že `UpdateAsync` metoda&mdash;uložila data, která lze provést s testem integrace.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a>Test ActionResult\<T>

V ASP.NET Core 2,1 nebo novější [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type) (<xref:Microsoft.AspNetCore.Mvc.ActionResult%601>) umožňuje vrátit typ odvozený od `ActionResult` nebo vracet konkrétní typ.

Ukázková aplikace obsahuje metodu, která vrací `List<IdeaDTO>` pro danou relaci. `id` Pokud relace `id` neexistuje, kontroler vrátí <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

Do nástroje jsou zahrnuté dva testy `ForSessionActionResult` kontroleru. `ApiIdeasControllerTests`

První test potvrdí, že kontroler vrátí `ActionResult` neexistující seznam nápadů pro neexistující relaci: `id`

* `ActionResult` Typ je `ActionResult<List<IdeaDTO>>`.
* <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> Je a <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

Pro platnou relaci `id`druhý test potvrdí, že metoda vrátí:

* `ActionResult` S `List<IdeaDTO>` typem.
* [ActionResult\<T>. Hodnota](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) je `List<IdeaDTO>` typ.
* První položka v seznamu je platný nápad, který odpovídá nápadu uloženému v relaci vzoru (získaný voláním `GetTestSession`).

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

Ukázková aplikace také obsahuje metodu pro vytvoření nového `Idea` pro danou relaci. Kontroler vrátí:

* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>Neplatný model.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>Pokud relace neexistuje.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Když se relace aktualizuje novým nápadem.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

Tři testy nástroje `CreateActionResult` jsou součástí `ApiIdeasControllerTests`.

První text potvrdí, že <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> je vrácen pro neplatný model.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

Druhý test ověří, zda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> je vrácena, pokud relace neexistuje.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

Pro platnou relaci `id`konečný test potvrzuje, že:

* Metoda vrací `ActionResult` s `BrainstormSession` typem.
* [ActionResult\<T>. Výsledkem](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) je <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult>. `CreatedAtActionResult`je analogický k *201 vytvořené* odpovědi s `Location` hlavičkou.
* [ActionResult\<T>. Hodnota](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) je `BrainstormSession` typ.
* Bylo vyvoláno nepoužité volání `UpdateAsync(testSession)`aktualizace relace. Volání `Verifiable` metody je kontrolováno spuštěním `mockRepo.Verify()` ve kontrolním výrazu.
* Pro `Idea` relaci jsou vraceny dva objekty.
* Poslední položka ( `Idea` přidaná přívoláním metody `UpdateAsync`) se shoduje s `newIdea` přidáním do relace v testu.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Řadiče](xref:mvc/controllers/actions) hrají v libovolné ASP.NET Core aplikaci MVC ústřední roli. V takovém případě byste měli mít jistotu, že se řadiče chovají tak, jak mají. Automatizované testy můžou detekovat chyby, než se aplikace nasadí do produkčního prostředí.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/testing/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="unit-tests-of-controller-logic"></a>Testování částí logiky kontroleru

[Testy jednotek](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) zahrnují testování součásti aplikace v izolaci od jejich infrastruktury a závislostí. V případě logiky kontroleru testování jednotek je testován pouze obsah jedné akce, nikoli chování jeho závislostí nebo samotného rozhraní.

Nastavte testy jednotek pro akce kontroleru, abyste se mohli soustředit na chování řadiče. Test jednotek kontroleru zabraňuje scénářům, jako jsou [filtry](xref:mvc/controllers/filters), [Směrování](xref:fundamentals/routing)a [vazby modelů](xref:mvc/models/model-binding). Testy, které pokrývají interakce mezi součástmi, které společně reagují na žádost, jsou zpracovávány *integračními testy*. Další informace o integračních testech naleznete v <xref:test/integration-tests>tématu.

Pokud vytváříte vlastní filtry a trasy, jednotkové testy je třeba izolovat, nikoli jako součást testů na konkrétní akci kontroleru.

Chcete-li předvést testy jednotek kontrol, zkontrolujte následující kontroler v ukázkové aplikaci. V domovském řadiči se zobrazuje seznam relací debaty a umožňuje vytváření nových relací debaty s požadavkem POST:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

Předchozí kontroler:

* Následuje po [principu explicitních závislostí](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).
* Očekává [vložení závislosti (di)](xref:fundamentals/dependency-injection) k poskytnutí instance `IBrainstormSessionRepository`.
* Dá se testovat pomocí napodobné `IBrainstormSessionRepository` služby pomocí objektové architektury, jako je [MOQ](https://www.nuget.org/packages/Moq/). Napodobný *objekt* je prefabrikované objekt s předem stanovenou sadou vlastností a způsobu použití pro testování. Další informace najdete v tématu [Úvod do integračních testů](xref:test/integration-tests#introduction-to-integration-tests).

`HTTP GET Index` Metoda nemá žádné smyček ani větvení a volá jenom jednu metodu. Testování částí pro tuto akci:

* Napodobuje `IBrainstormSessionRepository` službu pomocí `GetTestSessions` metody. `GetTestSessions`Vytvoří dvě modely pracovních debat s daty a názvy relací.
* Spustí `Index` metodu.
* Provede kontrolní výrazy u výsledku vráceného metodou:
  * <xref:Microsoft.AspNetCore.Mvc.ViewResult> Je vrácen.
  * [ViewDataDictionary. model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) je `StormSessionViewModel`.
  * V portálu jsou uloženy dvě relace debaty `ViewDataDictionary.Model`.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

Testy `HTTP POST Index` metody v domácím řadiči ověří, že:

* Pokud je `false` [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) , metoda Action vrátí *400 Chybný požadavek* <xref:Microsoft.AspNetCore.Mvc.ViewResult> s příslušnými daty.
* Kdy `ModelState.IsValid` je `true`:
  * Je `Add` volána metoda v úložišti.
  * A <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> se vrátí se správnými argumenty.

Neplatný stav modelu je testován přidáním chyb pomocí <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> , jak je znázorněno v prvním testu níže:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

Když je [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) neplatný, vrátí se `ViewResult` stejná jako pro požadavek GET. Test se nepokusí předat neplatný model. Předání neplatného modelu není platný přístup, protože vazba modelu není spuštěná (i když [test integrace](xref:test/integration-tests) používá vazbu modelu). V takovém případě není testována vazba modelu. Tyto testy jednotek testuje pouze kód v metodě Action.

Druhý test ověří, zda `ModelState` je platný:

* Přidá se `BrainstormSession` nový (přes úložiště).
* Metoda vrátí `RedirectToActionResult` s očekávanými vlastnostmi.

Napodobovaná volání, která nejsou volána, jsou obvykle ignorována, ale volání `Verifiable` na konci volání instalace umožňuje v testu vzhled. To se provádí s voláním `mockRepo.Verify`, které nefunguje test, pokud se očekávaná metoda nevolala.

> [!NOTE]
> Knihovna MOQ použitá v této ukázce umožňuje kombinovat ověřitelná, nebo "striktní", napodobná neověřitelnými písmeny (označované také jako "volné" nebo zástupné procedury). Přečtěte si další informace o přizpůsobení napodobení [chování pomocí MOQ](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).

[SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) v ukázkové aplikaci zobrazuje informace týkající se konkrétní relace debaty. Kontroler obsahuje logiku k zaznamenání `id` neplatných hodnot ( `return` v následujícím příkladu existují dva scénáře, které se týkají těchto scénářů). Poslední `return` příkaz vrátí nové `StormSessionViewModel` zobrazení (*Controllers/SessionController. cs*):

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

Testy jednotek zahrnují jeden test pro každý `return` scénář v akci řadiče `Index` relace:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

Přesunutí na kontroler nápadů aplikace zpřístupňuje funkce jako webové rozhraní API v `api/ideas` trase:

* Pomocí `ForSession` metody je vrácen seznam`IdeaDTO`nápadů () přidružených k relaci debaty.
* `Create` Metoda přidává do relace nové nápady.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

Vyhněte se vracení entit obchodních domén přímo prostřednictvím volání rozhraní API. Entity domény:

* Často zahrnuje více dat, než vyžaduje klient nástroje.
* Nenutně navýšení interního doménového modelu aplikace s veřejně vystaveným rozhraním API.

Mapování mezi doménovými entitami a typy vrácenými do klienta lze provést:

* Ručně pomocí LINQ `Select`, jak ukázková aplikace používá. Další informace naleznete v tématu [LINQ (Language Integrated Query)](/dotnet/standard/using-linq).
* Automaticky pomocí knihovny, například automatického [mapovače](https://github.com/AutoMapper/AutoMapper).

V dalším kroku Tato ukázková aplikace ukazuje testy jednotek `Create` rozhraní `ForSession` API a kontroleru nápadů.

Ukázková aplikace obsahuje dva `ForSession` testy. První test určuje, zda `ForSession` vrátí hodnotu <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (http Nenalezeno) pro neplatnou relaci:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

Druhý `ForSession` test určuje, zda `ForSession` vrátí seznam nápadů relace (`<List<IdeaDTO>>`) pro platnou relaci. Kontroly také prověří první nápad a potvrdí, že `Name` jeho vlastnost je správná:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

Chcete-li otestovat chování `Create` metody, pokud `ModelState` je neplatná, ukázková aplikace přidá do kontroleru chybu modelu v rámci testu. Nepokoušejte se testovat ověřování modelu nebo vazbu modelů v testech&mdash;jednotek stačí testovat chování metody akce, pokud je začínat neplatným: `ModelState`

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

Druhý test nástroje `Create` závisí na vráceném `null`úložišti, takže je nakonfigurované navýšení úložiště. `null` Není nutné vytvářet testovací databázi (v paměti nebo jinak) a vytvořit dotaz, který tento výsledek vrátí. Test lze provést v jediném příkazu, protože ukázka kódu ilustruje:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

Třetí `Create` test `Create_ReturnsNewlyCreatedIdeaForSession`ověří, zda je volána `UpdateAsync` metoda úložiště. Tento maketa je volána `Verifiable`s a je volána `Verify` metoda označeného úložiště k potvrzení, že je provedena ověřitelná metoda. Nejedná se o zodpovědnost za testování jednotek, aby bylo zajištěno, že `UpdateAsync` metoda&mdash;uložila data, která lze provést s testem integrace.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a>Test ActionResult\<T>

V ASP.NET Core 2,1 nebo novější [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type) (<xref:Microsoft.AspNetCore.Mvc.ActionResult%601>) umožňuje vrátit typ odvozený od `ActionResult` nebo vracet konkrétní typ.

Ukázková aplikace obsahuje metodu, která vrací `List<IdeaDTO>` pro danou relaci. `id` Pokud relace `id` neexistuje, kontroler vrátí <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

Do nástroje jsou zahrnuté dva testy `ForSessionActionResult` kontroleru. `ApiIdeasControllerTests`

První test potvrdí, že kontroler vrátí `ActionResult` neexistující seznam nápadů pro neexistující relaci: `id`

* `ActionResult` Typ je `ActionResult<List<IdeaDTO>>`.
* <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> Je a <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

Pro platnou relaci `id`druhý test potvrdí, že metoda vrátí:

* `ActionResult` S `List<IdeaDTO>` typem.
* [ActionResult\<T>. Hodnota](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) je `List<IdeaDTO>` typ.
* První položka v seznamu je platný nápad, který odpovídá nápadu uloženému v relaci vzoru (získaný voláním `GetTestSession`).

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

Ukázková aplikace také obsahuje metodu pro vytvoření nového `Idea` pro danou relaci. Kontroler vrátí:

* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>Neplatný model.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>Pokud relace neexistuje.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Když se relace aktualizuje novým nápadem.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

Tři testy nástroje `CreateActionResult` jsou součástí `ApiIdeasControllerTests`.

První text potvrdí, že <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> je vrácen pro neplatný model.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

Druhý test ověří, zda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> je vrácena, pokud relace neexistuje.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

Pro platnou relaci `id`konečný test potvrzuje, že:

* Metoda vrací `ActionResult` s `BrainstormSession` typem.
* [ActionResult\<T>. Výsledkem](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) je <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult>. `CreatedAtActionResult`je analogický k *201 vytvořené* odpovědi s `Location` hlavičkou.
* [ActionResult\<T>. Hodnota](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) je `BrainstormSession` typ.
* Bylo vyvoláno nepoužité volání `UpdateAsync(testSession)`aktualizace relace. Volání `Verifiable` metody je kontrolováno spuštěním `mockRepo.Verify()` ve kontrolním výrazu.
* Pro `Idea` relaci jsou vraceny dva objekty.
* Poslední položka ( `Idea` přidaná přívoláním metody `UpdateAsync`) se shoduje s `newIdea` přidáním do relace v testu.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:test/integration-tests>
* [Vytvoření a spuštění testů jednotek pomocí sady Visual Studio](/visualstudio/test/unit-test-your-code)
* [MyTested. AspNetCore. Mvc-Fluent testuje knihovnu pro ASP.NET Core knihovnu pro](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc) &ndash; testování částí se silnými typy MVC a poskytuje rozhraní Fluent pro testování MVC a webových aplikací API. (*Společnost Microsoft nespravuje ani nepodporuje.*)

