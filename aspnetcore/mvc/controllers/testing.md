---
title: Testovací kontroler logiku v ASP.NET Core
author: ardalis
description: Zjistěte, jak otestovat logice kontroleru v ASP.NET Core s Moq a xUnit.
ms.author: riande
ms.custom: mvc
ms.date: 08/23/2018
uid: mvc/controllers/testing
ms.openlocfilehash: c8a374f3e3ecfdef1a02e685aecc4e2fcbfcbf48
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410359"
---
# <a name="test-controller-logic-in-aspnet-core"></a>Testovací kontroler logiku v ASP.NET Core

Podle [Steve Smith](https://ardalis.com/)

[Kontrolery](xref:mvc/controllers/actions) přehrát hlavní roli ve všech aplikacích technologie ASP.NET Core MVC. V důsledku toho byste měli mít jistotu, které se chovají kontrolerů tak, jak má. Automatizované testy můžete detekovat chyby, před nasazením aplikace do produkčního prostředí.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/testing/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="unit-tests-of-controller-logic"></a>Testy jednotek logice kontroleru

[Testy jednotek](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) zahrnovat testování částí aplikace v izolaci od jeho infrastrukturu a závislosti. Když logice kontroleru testování částí, jenom obsah jedné akce jsou testovány, není chování z jejich závislých nebo samotného rozhraní.

Nastavte testy jednotek akce kontroleru a zaměřte se na chování kontroleru. Řadič testu jednotek se vyhnete scénáře, jako [filtry](xref:mvc/controllers/filters), [směrování](xref:fundamentals/routing), a [vazby modelu](xref:mvc/models/model-binding). Testy, které se týkají interakce mezi součástmi, které souhrnně odpověď na žádost zpracovává *integrační testy*. Další informace o testy integrace najdete v tématu <xref:test/integration-tests>.

Pokud píšete vlastní filtry a tras, testování částí je v izolaci, nikoli jako součást testů na určitý kontroler akce.

Abychom si předvedli kontroleru testů jednotek, projděte si následující kontroler v ukázkové aplikaci. Kontroler Home zobrazí seznam debaty relací a povolí vytváření nových relací debaty s požadavek POST:

[!code-csharp[](testing/sample/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

Předchozí kontroler:

* Následuje [explicitní závislosti Princip](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).
* Očekává, že [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) poskytnout instance `IBrainstormSessionRepository`.
* Můžete otestovat s imitaci `IBrainstormSessionRepository` služby pomocí mock objektu rozhraní, jako například [Moq](https://www.nuget.org/packages/Moq/). A *imitaci objekt* je objekt kovodělných předem sadu chování vlastnosti a metody pro testování. Další informace najdete v tématu [Úvod do integrace testy](xref:test/integration-tests#introduction-to-integration-tests).

`HTTP GET Index` Metoda nemá žádný nebo větvení a smyček pouze volání metod. Naprogramování testu části pro tuto akci:

* Mocks `IBrainstormSessionRepository` služby pomocí `GetTestSessions` metody. `GetTestSessions` vytvoří dvě Debata mock relace s daty a názvy relací.
* Spustí `Index` metody.
* Vytvoří kontrolní výrazy na výsledek vrácený metodou:
  * A <xref:Microsoft.AspNetCore.Mvc.ViewResult> je vrácena.
  * [ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) je `StormSessionViewModel`.
  * Obsahuje dvě relace debaty ukládají do `ViewDataDictionary.Model`.

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

Kontroler Home `HTTP POST Index` testy metoda ověřuje, že:

* Když [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) je `false`, metoda akce vrací *400 – Chybný požadavek* <xref:Microsoft.AspNetCore.Mvc.ViewResult> s příslušná data.
* Když `ModelState.IsValid` je `true`:
  * `Add` Volání metody v daném úložišti.
  * A <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> je vrácen s správné argumenty.

Do stavu modelu je neplatný je testována přidáním chyb s použitím <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> jak je znázorněno v následujícím prvního testu:

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

Když [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) není platný, stejné `ViewResult` se vrátí jako požadavek GET. Test nebude se pokoušet a zajistěte tak předání modelu je neplatný. Předání neplatný model není platný přístup, protože není spuštěn vazby modelu (Přestože [test integrace](xref:test/integration-tests) používá vazbu modelu). V takovém případě není testovaný vazby modelu. Tyto testy jednotek pouze testovaný kód v metodě akce.

Druhý test ověří, že `ModelState` platí:

* Nový `BrainstormSession` přidá (prostřednictvím úložiště).
* Metoda vrátí `RedirectToActionResult` s očekávané vlastnosti.

Imitaci volání, které nejsou volány jsou obvykle ignoruje, ale volání `Verifiable` volání na konci instalace umožňuje mock ověřování do testu. To se provádí pomocí volání `mockRepo.Verify`, který selže test, pokud nebyla volána metoda očekávané.

> [!NOTE]
> Díky Moq knihovny používané v tomto příkladu je možné kombinovat mocks ověřitelný nebo "přísné", s neověřitelného mocks (také nazývané "dojde ke ztrátě" mocks nebo zástupné procedury). Další informace o [přizpůsobení chování model s využitím Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).

[SessionController](https://github.com/aspnet/Docs/blob/master/aspnetcore/mvc/controllers/testing/sample/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) v ukázce aplikace se zobrazí informace týkající se konkrétní debaty. Kontroler obsahuje logiku se neplatná `id` hodnoty (jsou k dispozici dva `return` scénáře v následujícím příkladu pro tyto scénáře). Finální `return` příkaz vrátí nový `StormSessionViewModel` k zobrazení (*Controllers/SessionController.cs*):

[!code-csharp[](testing/sample/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

Testování částí zahrnují jeden test pro každou `return` scénář v kontroleru relace `Index` akce:

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

Přesun ke kontroleru nápady, aplikace zpřístupňuje funkce jako webové rozhraní API na `api/ideas` trasy:

* Seznam nápadů (`IdeaDTO`) související s debaty je vrácený relace `ForSession` metody.
* `Create` Metoda přidá nové nápady týkající se k relaci.

[!code-csharp[](testing/sample/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

Vyhněte se vracení obchodní domény entity přímo prostřednictvím volání rozhraní API. Entity domény:

* Často zahrnují více dat, než klient vyžaduje.
* Zkombinujte zbytečně model interní domény aplikace s rozhraním API pro veřejně vystavené.

Mapování mezi domény subjekty a typy vrácených do klienta lze provést:

* Ručně pomocí LINQ `Select`, jak ukázková aplikace používá. Další informace najdete v tématu [LINQ (Language Integrated Query)](/dotnet/standard/using-linq).
* Automaticky s knihovnou jako například [AutoMapper](https://github.com/AutoMapper/AutoMapper).

V dalším kroku ukázková aplikace předvádí testů jednotek pro `Create` a `ForSession` metody rozhraní API řadiče nápady.

Ukázková aplikace obsahuje dva `ForSession` testy. První test Určuje, zda `ForSession` vrátí <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (HTTP nebyl nalezen) pro neplatnou relaci:

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

Druhá `ForSession` testů Určuje, zda `ForSession` vrátí seznam nápadů relace (`<List<IdeaDTO>>`) pro relaci platný. Kontroly také prozkoumat první nápad k potvrzení jeho `Name` správnost vlastnost:

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

K otestování chování `Create` metoda při `ModelState` je neplatný, ukázkové aplikace přidá chybu modelu do řadiče testu v rámci. Otestovat ověření modelu nebo vazby při testech jednotek modelu se nepokoušejte&mdash;testování chování metody akce, když setkat s neplatnou `ModelState`:

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

Druhý test `Create` závisí na úložišti vrácení `null`, takže mock úložiště je nakonfigurovaný k vrácení `null`. Není nutné vytvořit testovací databáze (v paměti nebo jinak) a vytvořit dotaz, který vrátí tento výsledek. Test můžete provést v jediném příkazu, jak vzorový kód ukazuje:

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

Třetí `Create` otestovat, `Create_ReturnsNewlyCreatedIdeaForSession`, ověřuje, že v úložišti `UpdateAsync` metoda je volána. Model se nazývá s `Verifiable`a imitaci úložiště `Verify` metoda je volána k potvrzení ověřitelné metody. Není test jednotky odpovědnost zajistit, aby `UpdateAsync` metoda uložit data&mdash;, které můžete provést pomocí o test integrace.

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

::: moniker range=">= aspnetcore-2.1"

## <a name="test-actionresultlttgt"></a>Testování ActionResult&lt;T&gt;

V ASP.NET Core 2.1 nebo novější [ActionResult&lt;T&gt; ](xref:web-api/action-return-types#actionresultt-type) (<xref:Microsoft.AspNetCore.Mvc.ActionResult`1>) umožňuje návratový typ odvozený od `ActionResult` nebo vrácení specifického typu.

Ukázková aplikace obsahuje metodu, která vrací `List<IdeaDTO>` pro dané relace `id`. Pokud relace `id` neexistuje, vrátí řadič <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>:

[!code-csharp[](testing/sample/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

Dvě testů `ForSessionActionResult` řadiče jsou součástí `ApiIdeasControllerTests`.

První test potvrdí, že kontroler vrací `ActionResult` , ale ne neexistující seznamu nápadů pro neexistující relace `id`:

* `ActionResult` Typ je `ActionResult<List<IdeaDTO>>`.
* <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> Je <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

Pro relaci platný `id`, druhý test potvrdí, že metoda vrací:

* `ActionResult` s `List<IdeaDTO>` typu.
* [ActionResult&lt;T&gt;. Hodnota](xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Value*) je `List<IdeaDTO>` typu.
* První položka v seznamu je platný odpovídající nápad uložené v mock relace (získán voláním `GetTestSession`).

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

Ukázková aplikace také zahrnuje metodu pro vytvoření nového `Idea` pro dané relace. Vrátí kontroleru:

* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> Neplatný model.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> Pokud relace neexistuje.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> Při aktualizaci relace s nový nápad.

[!code-csharp[](testing/sample/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

Tři testů `CreateActionResult` jsou součástí `ApiIdeasControllerTests`.

První textový potvrdí, že <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> vrátil neplatný model.

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

Druhý test kontroluje, zda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> je vrácena, pokud relace neexistuje.

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

Pro relaci platný `id`, finální testování potvrdí, že:

* Metoda vrátí `ActionResult` s `BrainstormSession` typu.
* [ActionResult&lt;T&gt;. Výsledek](xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*) je <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult>. `CreatedAtActionResult` je obdobou *201 – vytvořeno* odpověď `Location` záhlaví.
* [ActionResult&lt;T&gt;. Hodnota](xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Value*) je `BrainstormSession` typu.
* Aktualizujte relaci, mock voláním `UpdateAsync(testSession)`, byla vyvolána. `Verifiable` Volání metody je zaškrtnuté políčko spuštěním `mockRepo.Verify()` v kontrolní výrazy.
* Dvě `Idea` objektů pro relaci.
* Poslední položky ( `Idea` přidal mock volání `UpdateAsync`) odpovídá `newIdea` přidat do relace v testu.

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:test/integration-tests>
* [Vytváření a spouštění testování částí pomocí sady Visual Studio](/visualstudio/test/unit-test-your-code).
