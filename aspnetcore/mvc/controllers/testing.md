---
title: Logicer Test Controller v ASP.NET Core
author: ardalis
description: Naučte se testovat logiku kontroleru v ASP.NET Core s MOQ a xUnit.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: mvc/controllers/testing
ms.openlocfilehash: 7f4fcb1a5d6e9959c751ebe24e41b39ee05a5819
ms.sourcegitcommit: 67116718dc33a7a01696d41af38590fdbb58e014
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799503"
---
# <a name="test-controller-logic-in-aspnet-core"></a>Logicer Test Controller v ASP.NET Core

[Steve Smith](https://ardalis.com/)

::: moniker range=">= aspnetcore-3.0"

[Řadiče](xref:mvc/controllers/actions) hrají v libovolné ASP.NET Core aplikaci MVC ústřední roli. V takovém případě byste měli mít jistotu, že se řadiče chovají tak, jak mají. Automatizované testy můžou detekovat chyby, než se aplikace nasadí do produkčního prostředí.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/testing/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="unit-tests-of-controller-logic"></a>Testování částí logiky kontroleru

[Testy jednotek](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) zahrnují testování součásti aplikace v izolaci od jejich infrastruktury a závislostí. V případě logiky kontroleru testování jednotek je testován pouze obsah jedné akce, nikoli chování jeho závislostí nebo samotného rozhraní.

Nastavte testy jednotek pro akce kontroleru, abyste se mohli soustředit na chování řadiče. Test jednotek kontroleru zabraňuje scénářům, jako jsou [filtry](xref:mvc/controllers/filters), [Směrování](xref:fundamentals/routing)a [vazby modelů](xref:mvc/models/model-binding). Testy, které pokrývají interakce mezi součástmi, které společně reagují na žádost, jsou zpracovávány *integračními testy*. Další informace o integračních testech naleznete v tématu <xref:test/integration-tests>.

Pokud vytváříte vlastní filtry a trasy, jednotkové testy je třeba izolovat, nikoli jako součást testů na konkrétní akci kontroleru.

Chcete-li předvést testy jednotek kontrol, zkontrolujte následující kontroler v ukázkové aplikaci. V domovském řadiči se zobrazuje seznam relací debaty a umožňuje vytváření nových relací debaty s požadavkem POST:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

Předchozí kontroler:

* Následuje po [principu explicitních závislostí](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).
* Očekává [vložení závislosti (di)](xref:fundamentals/dependency-injection) k poskytnutí instance `IBrainstormSessionRepository`.
* Dá se testovat s napodobnou `IBrainstormSessionRepository`ovou službou pomocí objektové architektury, jako je [MOQ](https://www.nuget.org/packages/Moq/). Napodobný *objekt* je prefabrikované objekt s předem stanovenou sadou vlastností a způsobu použití pro testování. Další informace najdete v tématu [Úvod do integračních testů](xref:test/integration-tests#introduction-to-integration-tests).

Metoda `HTTP GET Index` nemá žádné smyček ani větvení a volá jenom jednu metodu. Testování částí pro tuto akci:

* Napodobuje `IBrainstormSessionRepository` službu pomocí metody `GetTestSessions`. `GetTestSessions` vytvoří dvě návrhy pracovních debat s daty a názvy relací.
* Spustí metodu `Index`.
* Provede kontrolní výrazy u výsledku vráceného metodou:
  * Vrátí se <xref:Microsoft.AspNetCore.Mvc.ViewResult>.
  * [ViewDataDictionary. model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) je `StormSessionViewModel`.
  * V `ViewDataDictionary.Model`jsou uloženy dvě relace debaty.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

Test metody `HTTP POST Index` v rámci řadiče domény ověřuje, že:

* Pokud je [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) `false`, metoda Action 400 vrátí <xref:Microsoft.AspNetCore.Mvc.ViewResult> *nesprávného požadavku* s odpovídajícími daty.
* Když je `ModelState.IsValid` `true`:
  * Je volána metoda `Add` v úložišti.
  * <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> se vrátí se správnými argumenty.

Neplatný stav modelu je testován přidáním chyb pomocí <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>, jak je znázorněno v prvním testu níže:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

Pokud [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) není platný, vrátí se stejný `ViewResult` jako požadavek GET. Test se nepokusí předat neplatný model. Předání neplatného modelu není platný přístup, protože vazba modelu není spuštěná (i když [test integrace](xref:test/integration-tests) používá vazbu modelu). V takovém případě není testována vazba modelu. Tyto testy jednotek testuje pouze kód v metodě Action.

Druhý test ověří, zda je `ModelState` platný:

* Přidá se nový `BrainstormSession` (přes úložiště).
* Metoda vrací `RedirectToActionResult` s očekávanými vlastnostmi.

Přezkoušená volání, která nejsou volána, jsou obvykle ignorována, ale volání `Verifiable` na konci volání instalace umožňuje v testu vzhledové ověřování. To se provádí s voláním `mockRepo.Verify`, které nefunguje test, pokud se očekávaná metoda nevolala.

> [!NOTE]
> Knihovna MOQ použitá v této ukázce umožňuje kombinovat ověřitelná, nebo "striktní", napodobná neověřitelnými písmeny (označované také jako "volné" nebo zástupné procedury). Přečtěte si další informace o přizpůsobení napodobení [chování pomocí MOQ](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).

[SessionController](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) v ukázkové aplikaci zobrazuje informace týkající se konkrétní relace debaty. Kontroler obsahuje logiku pro práci s neplatnými `id` hodnotami (pro pokrytí těchto scénářů existují dva scénáře `return` v následujícím příkladu). Poslední příkaz `return` vrátí nový `StormSessionViewModel` zobrazení (*Controllers/SessionController. cs*):

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

Testy jednotek zahrnují jeden test pro každý `return` scénář v řadiči relace `Index` akci:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

Když se přesunete na kontroler nápadů, aplikace zpřístupňuje funkce jako webové rozhraní API na trase `api/ideas`:

* Metoda `ForSession` vrací seznam nápadů (`IdeaDTO`) přidružených k relaci debaty.
* Metoda `Create` přidává do relace nové nápady.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

Vyhněte se vracení entit obchodních domén přímo prostřednictvím volání rozhraní API. Entity domény:

* Často zahrnuje více dat, než vyžaduje klient nástroje.
* Nenutně navýšení interního doménového modelu aplikace s veřejně vystaveným rozhraním API.

Mapování mezi doménovými entitami a typy vrácenými do klienta lze provést:

* Ručně s `Select`LINQ, jak ukázková aplikace používá. Další informace naleznete v tématu [LINQ (Language Integrated Query)](/dotnet/standard/using-linq).
* Automaticky pomocí knihovny, například automatického [mapovače](https://github.com/AutoMapper/AutoMapper).

V dalším kroku Tato ukázková aplikace znázorňuje jednotkové testy pro `Create` a `ForSession` metody rozhraní API pro kontroler nápadů.

Ukázková aplikace obsahuje dva `ForSession` testy. První test určuje, zda `ForSession` vrátí <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (HTTP se nenašlo) pro neplatnou relaci:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

Druhý `ForSession` test určuje, zda `ForSession` pro platnou relaci vrátí seznam nápadů relace (`<List<IdeaDTO>>`). Kontroly také prověří první nápad a potvrdí jeho vlastnost `Name` je správná:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

Chcete-li otestovat chování metody `Create`, pokud je `ModelState` neplatný, ukázková aplikace přidá do kontroleru chybu modelu v rámci testu. Nepokoušejte se testovat ověřování modelu nebo vazbu modelu v testování částí&mdash;stačí otestovat chování metody akce, pokud je začínat neplatným `ModelState`:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

Druhý test `Create` závisí na úložišti, které vrací `null`, takže je úložiště nakonfigurované tak, aby vracelo `null`. Není nutné vytvářet testovací databázi (v paměti nebo jinak) a vytvořit dotaz, který tento výsledek vrátí. Test lze provést v jediném příkazu, protože ukázka kódu ilustruje:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

Třetí `Create` test `Create_ReturnsNewlyCreatedIdeaForSession`ověří, že je volána metoda `UpdateAsync` úložiště. Tento model je volán pomocí `Verifiable`a je volána metoda `Verify` označeného úložiště k potvrzení, že je provedena ověřitelná metoda. Nejedná se o zodpovědnost za testování jednotek, aby bylo zajištěno, že metoda `UpdateAsync` uložila&mdash;dat, která může být provedena pomocí testu integrace.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a>Test ActionResult\<T >

V ASP.NET Core 2,1 nebo novějším [ActionResult\<t >](xref:web-api/action-return-types#actionresultt-type) (<xref:Microsoft.AspNetCore.Mvc.ActionResult%601>) můžete vracet typ odvozený od `ActionResult` nebo vracet konkrétní typ.

Ukázková aplikace obsahuje metodu, která vrací `List<IdeaDTO>` pro danou relaci `id`. Pokud `id` relace neexistuje, kontroler vrátí <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

Do `ApiIdeasControllerTests`jsou zahrnuté dva testy `ForSessionActionResult` kontroleru.

První test potvrdí, že kontroler vrátí `ActionResult`, ale ne neexistující seznam nápadů pro neexistující relaci `id`:

* Typ `ActionResult` je `ActionResult<List<IdeaDTO>>`.
* <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> je <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

Pro platnou relaci `id`druhý test potvrdí, že metoda vrátí:

* `ActionResult` s typem `List<IdeaDTO>`
* [> ActionResult\<t. Hodnota](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) je typ `List<IdeaDTO>`.
* První položka v seznamu je platný nápad, který odpovídá nápadu uloženému v relaci s přípravou (získaná voláním `GetTestSession`).

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

Ukázková aplikace také obsahuje metodu pro vytvoření nového `Idea` pro danou relaci. Kontroler vrátí:

* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> pro neplatný model.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>, pokud relace neexistuje.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>, když se relace aktualizuje novým nápadem.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

`ApiIdeasControllerTests`obsahuje tři testy `CreateActionResult`.

První text potvrdí, že se vrátí <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> pro neplatný model.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

Druhý test ověří, zda je vrácena <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>, pokud relace neexistuje.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

Pro platnou relaci `id`konečný test potvrzuje, že:

* Metoda vrací `ActionResult` s typem `BrainstormSession`.
* [> ActionResult\<t. Výsledkem](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) je <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult>. `CreatedAtActionResult` je analogická k *201 vytvořené* odpovědi s hlavičkou `Location`.
* [> ActionResult\<t. Hodnota](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) je typ `BrainstormSession`.
* Bylo vyvoláno nepoužité volání aktualizace relace, `UpdateAsync(testSession)`. Volání metody `Verifiable` je kontrolováno spuštěním `mockRepo.Verify()` ve kontrolním výrazu.
* Pro relaci jsou vraceny dva objekty `Idea`.
* Poslední položka (`Idea` přidaná v rámci volání metody `UpdateAsync`) odpovídá `newIdea` přidanému do relace v testu.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Řadiče](xref:mvc/controllers/actions) hrají v libovolné ASP.NET Core aplikaci MVC ústřední roli. V takovém případě byste měli mít jistotu, že se řadiče chovají tak, jak mají. Automatizované testy můžou detekovat chyby, než se aplikace nasadí do produkčního prostředí.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/testing/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="unit-tests-of-controller-logic"></a>Testování částí logiky kontroleru

[Testy jednotek](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) zahrnují testování součásti aplikace v izolaci od jejich infrastruktury a závislostí. V případě logiky kontroleru testování jednotek je testován pouze obsah jedné akce, nikoli chování jeho závislostí nebo samotného rozhraní.

Nastavte testy jednotek pro akce kontroleru, abyste se mohli soustředit na chování řadiče. Test jednotek kontroleru zabraňuje scénářům, jako jsou [filtry](xref:mvc/controllers/filters), [Směrování](xref:fundamentals/routing)a [vazby modelů](xref:mvc/models/model-binding). Testy, které pokrývají interakce mezi součástmi, které společně reagují na žádost, jsou zpracovávány *integračními testy*. Další informace o integračních testech naleznete v tématu <xref:test/integration-tests>.

Pokud vytváříte vlastní filtry a trasy, jednotkové testy je třeba izolovat, nikoli jako součást testů na konkrétní akci kontroleru.

Chcete-li předvést testy jednotek kontrol, zkontrolujte následující kontroler v ukázkové aplikaci. V domovském řadiči se zobrazuje seznam relací debaty a umožňuje vytváření nových relací debaty s požadavkem POST:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

Předchozí kontroler:

* Následuje po [principu explicitních závislostí](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).
* Očekává [vložení závislosti (di)](xref:fundamentals/dependency-injection) k poskytnutí instance `IBrainstormSessionRepository`.
* Dá se testovat s napodobnou `IBrainstormSessionRepository`ovou službou pomocí objektové architektury, jako je [MOQ](https://www.nuget.org/packages/Moq/). Napodobný *objekt* je prefabrikované objekt s předem stanovenou sadou vlastností a způsobu použití pro testování. Další informace najdete v tématu [Úvod do integračních testů](xref:test/integration-tests#introduction-to-integration-tests).

Metoda `HTTP GET Index` nemá žádné smyček ani větvení a volá jenom jednu metodu. Testování částí pro tuto akci:

* Napodobuje `IBrainstormSessionRepository` službu pomocí metody `GetTestSessions`. `GetTestSessions` vytvoří dvě návrhy pracovních debat s daty a názvy relací.
* Spustí metodu `Index`.
* Provede kontrolní výrazy u výsledku vráceného metodou:
  * Vrátí se <xref:Microsoft.AspNetCore.Mvc.ViewResult>.
  * [ViewDataDictionary. model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) je `StormSessionViewModel`.
  * V `ViewDataDictionary.Model`jsou uloženy dvě relace debaty.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

Test metody `HTTP POST Index` v rámci řadiče domény ověřuje, že:

* Pokud je [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) `false`, metoda Action 400 vrátí <xref:Microsoft.AspNetCore.Mvc.ViewResult> *nesprávného požadavku* s odpovídajícími daty.
* Když je `ModelState.IsValid` `true`:
  * Je volána metoda `Add` v úložišti.
  * <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> se vrátí se správnými argumenty.

Neplatný stav modelu je testován přidáním chyb pomocí <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>, jak je znázorněno v prvním testu níže:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

Pokud [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) není platný, vrátí se stejný `ViewResult` jako požadavek GET. Test se nepokusí předat neplatný model. Předání neplatného modelu není platný přístup, protože vazba modelu není spuštěná (i když [test integrace](xref:test/integration-tests) používá vazbu modelu). V takovém případě není testována vazba modelu. Tyto testy jednotek testuje pouze kód v metodě Action.

Druhý test ověří, zda je `ModelState` platný:

* Přidá se nový `BrainstormSession` (přes úložiště).
* Metoda vrací `RedirectToActionResult` s očekávanými vlastnostmi.

Přezkoušená volání, která nejsou volána, jsou obvykle ignorována, ale volání `Verifiable` na konci volání instalace umožňuje v testu vzhledové ověřování. To se provádí s voláním `mockRepo.Verify`, které nefunguje test, pokud se očekávaná metoda nevolala.

> [!NOTE]
> Knihovna MOQ použitá v této ukázce umožňuje kombinovat ověřitelná, nebo "striktní", napodobná neověřitelnými písmeny (označované také jako "volné" nebo zástupné procedury). Přečtěte si další informace o přizpůsobení napodobení [chování pomocí MOQ](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).

[SessionController](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) v ukázkové aplikaci zobrazuje informace týkající se konkrétní relace debaty. Kontroler obsahuje logiku pro práci s neplatnými `id` hodnotami (pro pokrytí těchto scénářů existují dva scénáře `return` v následujícím příkladu). Poslední příkaz `return` vrátí nový `StormSessionViewModel` zobrazení (*Controllers/SessionController. cs*):

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

Testy jednotek zahrnují jeden test pro každý `return` scénář v řadiči relace `Index` akci:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

Když se přesunete na kontroler nápadů, aplikace zpřístupňuje funkce jako webové rozhraní API na trase `api/ideas`:

* Metoda `ForSession` vrací seznam nápadů (`IdeaDTO`) přidružených k relaci debaty.
* Metoda `Create` přidává do relace nové nápady.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

Vyhněte se vracení entit obchodních domén přímo prostřednictvím volání rozhraní API. Entity domény:

* Často zahrnuje více dat, než vyžaduje klient nástroje.
* Nenutně navýšení interního doménového modelu aplikace s veřejně vystaveným rozhraním API.

Mapování mezi doménovými entitami a typy vrácenými do klienta lze provést:

* Ručně s `Select`LINQ, jak ukázková aplikace používá. Další informace naleznete v tématu [LINQ (Language Integrated Query)](/dotnet/standard/using-linq).
* Automaticky pomocí knihovny, například automatického [mapovače](https://github.com/AutoMapper/AutoMapper).

V dalším kroku Tato ukázková aplikace znázorňuje jednotkové testy pro `Create` a `ForSession` metody rozhraní API pro kontroler nápadů.

Ukázková aplikace obsahuje dva `ForSession` testy. První test určuje, zda `ForSession` vrátí <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (HTTP se nenašlo) pro neplatnou relaci:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

Druhý `ForSession` test určuje, zda `ForSession` pro platnou relaci vrátí seznam nápadů relace (`<List<IdeaDTO>>`). Kontroly také prověří první nápad a potvrdí jeho vlastnost `Name` je správná:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

Chcete-li otestovat chování metody `Create`, pokud je `ModelState` neplatný, ukázková aplikace přidá do kontroleru chybu modelu v rámci testu. Nepokoušejte se testovat ověřování modelu nebo vazbu modelu v testování částí&mdash;stačí otestovat chování metody akce, pokud je začínat neplatným `ModelState`:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

Druhý test `Create` závisí na úložišti, které vrací `null`, takže je úložiště nakonfigurované tak, aby vracelo `null`. Není nutné vytvářet testovací databázi (v paměti nebo jinak) a vytvořit dotaz, který tento výsledek vrátí. Test lze provést v jediném příkazu, protože ukázka kódu ilustruje:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

Třetí `Create` test `Create_ReturnsNewlyCreatedIdeaForSession`ověří, že je volána metoda `UpdateAsync` úložiště. Tento model je volán pomocí `Verifiable`a je volána metoda `Verify` označeného úložiště k potvrzení, že je provedena ověřitelná metoda. Nejedná se o zodpovědnost za testování jednotek, aby bylo zajištěno, že metoda `UpdateAsync` uložila&mdash;dat, která může být provedena pomocí testu integrace.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a>Test ActionResult\<T >

V ASP.NET Core 2,1 nebo novějším [ActionResult\<t >](xref:web-api/action-return-types#actionresultt-type) (<xref:Microsoft.AspNetCore.Mvc.ActionResult%601>) můžete vracet typ odvozený od `ActionResult` nebo vracet konkrétní typ.

Ukázková aplikace obsahuje metodu, která vrací `List<IdeaDTO>` pro danou relaci `id`. Pokud `id` relace neexistuje, kontroler vrátí <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

Do `ApiIdeasControllerTests`jsou zahrnuté dva testy `ForSessionActionResult` kontroleru.

První test potvrdí, že kontroler vrátí `ActionResult`, ale ne neexistující seznam nápadů pro neexistující relaci `id`:

* Typ `ActionResult` je `ActionResult<List<IdeaDTO>>`.
* <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> je <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

Pro platnou relaci `id`druhý test potvrdí, že metoda vrátí:

* `ActionResult` s typem `List<IdeaDTO>`
* [> ActionResult\<t. Hodnota](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) je typ `List<IdeaDTO>`.
* První položka v seznamu je platný nápad, který odpovídá nápadu uloženému v relaci s přípravou (získaná voláním `GetTestSession`).

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

Ukázková aplikace také obsahuje metodu pro vytvoření nového `Idea` pro danou relaci. Kontroler vrátí:

* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> pro neplatný model.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>, pokud relace neexistuje.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>, když se relace aktualizuje novým nápadem.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

`ApiIdeasControllerTests`obsahuje tři testy `CreateActionResult`.

První text potvrdí, že se vrátí <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> pro neplatný model.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

Druhý test ověří, zda je vrácena <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>, pokud relace neexistuje.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

Pro platnou relaci `id`konečný test potvrzuje, že:

* Metoda vrací `ActionResult` s typem `BrainstormSession`.
* [> ActionResult\<t. Výsledkem](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) je <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult>. `CreatedAtActionResult` je analogická k *201 vytvořené* odpovědi s hlavičkou `Location`.
* [> ActionResult\<t. Hodnota](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) je typ `BrainstormSession`.
* Bylo vyvoláno nepoužité volání aktualizace relace, `UpdateAsync(testSession)`. Volání metody `Verifiable` je kontrolováno spuštěním `mockRepo.Verify()` ve kontrolním výrazu.
* Pro relaci jsou vraceny dva objekty `Idea`.
* Poslední položka (`Idea` přidaná v rámci volání metody `UpdateAsync`) odpovídá `newIdea` přidanému do relace v testu.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:test/integration-tests>
* [Vytvoření a spuštění testů jednotek pomocí sady Visual Studio](/visualstudio/test/unit-test-your-code)
* [MyTested. AspNetCore. Mvc-Fluent test Library pro ASP.NET Core Mvc](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc) &ndash; knihovnu testování jednotek se silnými typy a poskytuje rozhraní Fluent pro testování MVC a webových aplikací API. (*Společnost Microsoft nespravuje ani nepodporuje.* )

