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
# <a name="test-controller-logic-in-aspnet-core"></a><span data-ttu-id="8376b-103">Testovací kontroler logiku v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8376b-103">Test controller logic in ASP.NET Core</span></span>

<span data-ttu-id="8376b-104">Podle [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8376b-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8376b-105">[Kontrolery](xref:mvc/controllers/actions) přehrát hlavní roli ve všech aplikacích technologie ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="8376b-105">[Controllers](xref:mvc/controllers/actions) play a central role in any ASP.NET Core MVC app.</span></span> <span data-ttu-id="8376b-106">V důsledku toho byste měli mít jistotu, které se chovají kontrolerů tak, jak má.</span><span class="sxs-lookup"><span data-stu-id="8376b-106">As such, you should have confidence that controllers behave as intended.</span></span> <span data-ttu-id="8376b-107">Automatizované testy můžete detekovat chyby, před nasazením aplikace do produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="8376b-107">Automated tests can detect errors before the app is deployed to a production environment.</span></span>

<span data-ttu-id="8376b-108">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/testing/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8376b-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/testing/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="unit-tests-of-controller-logic"></a><span data-ttu-id="8376b-109">Testy jednotek logice kontroleru</span><span class="sxs-lookup"><span data-stu-id="8376b-109">Unit tests of controller logic</span></span>

<span data-ttu-id="8376b-110">[Testy jednotek](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) zahrnovat testování částí aplikace v izolaci od jeho infrastrukturu a závislosti.</span><span class="sxs-lookup"><span data-stu-id="8376b-110">[Unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) involve testing a part of an app in isolation from its infrastructure and dependencies.</span></span> <span data-ttu-id="8376b-111">Když logice kontroleru testování částí, jenom obsah jedné akce jsou testovány, není chování z jejich závislých nebo samotného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="8376b-111">When unit testing controller logic, only the contents of a single action are tested, not the behavior of its dependencies or of the framework itself.</span></span>

<span data-ttu-id="8376b-112">Nastavte testy jednotek akce kontroleru a zaměřte se na chování kontroleru.</span><span class="sxs-lookup"><span data-stu-id="8376b-112">Set up unit tests of controller actions to focus on the controller's behavior.</span></span> <span data-ttu-id="8376b-113">Řadič testu jednotek se vyhnete scénáře, jako [filtry](xref:mvc/controllers/filters), [směrování](xref:fundamentals/routing), a [vazby modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="8376b-113">A controller unit test avoids scenarios such as [filters](xref:mvc/controllers/filters), [routing](xref:fundamentals/routing), and [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="8376b-114">Testy, které se týkají interakce mezi součástmi, které souhrnně odpověď na žádost zpracovává *integrační testy*.</span><span class="sxs-lookup"><span data-stu-id="8376b-114">Tests that cover the interactions among components that collectively respond to a request are handled by *integration tests*.</span></span> <span data-ttu-id="8376b-115">Další informace o testy integrace najdete v tématu <xref:test/integration-tests>.</span><span class="sxs-lookup"><span data-stu-id="8376b-115">For more information on integration tests, see <xref:test/integration-tests>.</span></span>

<span data-ttu-id="8376b-116">Pokud píšete vlastní filtry a tras, testování částí je v izolaci, nikoli jako součást testů na určitý kontroler akce.</span><span class="sxs-lookup"><span data-stu-id="8376b-116">If you're writing custom filters and routes, unit test them in isolation, not as part of tests on a particular controller action.</span></span>

<span data-ttu-id="8376b-117">Abychom si předvedli kontroleru testů jednotek, projděte si následující kontroler v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8376b-117">To demonstrate controller unit tests, review the following controller in the sample app.</span></span> <span data-ttu-id="8376b-118">Kontroler Home zobrazí seznam debaty relací a povolí vytváření nových relací debaty s požadavek POST:</span><span class="sxs-lookup"><span data-stu-id="8376b-118">The Home controller displays a list of brainstorming sessions and allows the creation of new brainstorming sessions with a POST request:</span></span>

[!code-csharp[](testing/sample/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

<span data-ttu-id="8376b-119">Předchozí kontroler:</span><span class="sxs-lookup"><span data-stu-id="8376b-119">The preceding controller:</span></span>

* <span data-ttu-id="8376b-120">Následuje [explicitní závislosti Princip](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="8376b-120">Follows the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>
* <span data-ttu-id="8376b-121">Očekává, že [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) poskytnout instance `IBrainstormSessionRepository`.</span><span class="sxs-lookup"><span data-stu-id="8376b-121">Expects [dependency injection (DI)](xref:fundamentals/dependency-injection) to provide an instance of `IBrainstormSessionRepository`.</span></span>
* <span data-ttu-id="8376b-122">Můžete otestovat s imitaci `IBrainstormSessionRepository` služby pomocí mock objektu rozhraní, jako například [Moq](https://www.nuget.org/packages/Moq/).</span><span class="sxs-lookup"><span data-stu-id="8376b-122">Can be tested with a mocked `IBrainstormSessionRepository` service using a mock object framework, such as [Moq](https://www.nuget.org/packages/Moq/).</span></span> <span data-ttu-id="8376b-123">A *imitaci objekt* je objekt kovodělných předem sadu chování vlastnosti a metody pro testování.</span><span class="sxs-lookup"><span data-stu-id="8376b-123">A *mocked object* is a fabricated object with a predetermined set of property and method behaviors used for testing.</span></span> <span data-ttu-id="8376b-124">Další informace najdete v tématu [Úvod do integrace testy](xref:test/integration-tests#introduction-to-integration-tests).</span><span class="sxs-lookup"><span data-stu-id="8376b-124">For more information, see [Introduction to integration tests](xref:test/integration-tests#introduction-to-integration-tests).</span></span>

<span data-ttu-id="8376b-125">`HTTP GET Index` Metoda nemá žádný nebo větvení a smyček pouze volání metod.</span><span class="sxs-lookup"><span data-stu-id="8376b-125">The `HTTP GET Index` method has no looping or branching and only calls one method.</span></span> <span data-ttu-id="8376b-126">Naprogramování testu části pro tuto akci:</span><span class="sxs-lookup"><span data-stu-id="8376b-126">The unit test for this action:</span></span>

* <span data-ttu-id="8376b-127">Mocks `IBrainstormSessionRepository` služby pomocí `GetTestSessions` metody.</span><span class="sxs-lookup"><span data-stu-id="8376b-127">Mocks the `IBrainstormSessionRepository` service using the `GetTestSessions` method.</span></span> <span data-ttu-id="8376b-128">`GetTestSessions` vytvoří dvě Debata mock relace s daty a názvy relací.</span><span class="sxs-lookup"><span data-stu-id="8376b-128">`GetTestSessions` creates two mock brainstorm sessions with dates and session names.</span></span>
* <span data-ttu-id="8376b-129">Spustí `Index` metody.</span><span class="sxs-lookup"><span data-stu-id="8376b-129">Executes the `Index` method.</span></span>
* <span data-ttu-id="8376b-130">Vytvoří kontrolní výrazy na výsledek vrácený metodou:</span><span class="sxs-lookup"><span data-stu-id="8376b-130">Makes assertions on the result returned by the method:</span></span>
  * <span data-ttu-id="8376b-131">A <xref:Microsoft.AspNetCore.Mvc.ViewResult> je vrácena.</span><span class="sxs-lookup"><span data-stu-id="8376b-131">A <xref:Microsoft.AspNetCore.Mvc.ViewResult> is returned.</span></span>
  * <span data-ttu-id="8376b-132">[ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) je `StormSessionViewModel`.</span><span class="sxs-lookup"><span data-stu-id="8376b-132">The [ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) is a `StormSessionViewModel`.</span></span>
  * <span data-ttu-id="8376b-133">Obsahuje dvě relace debaty ukládají do `ViewDataDictionary.Model`.</span><span class="sxs-lookup"><span data-stu-id="8376b-133">There are two brainstorming sessions stored in the `ViewDataDictionary.Model`.</span></span>

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

<span data-ttu-id="8376b-134">Kontroler Home `HTTP POST Index` testy metoda ověřuje, že:</span><span class="sxs-lookup"><span data-stu-id="8376b-134">The Home controller's `HTTP POST Index` method tests verifies that:</span></span>

* <span data-ttu-id="8376b-135">Když [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) je `false`, metoda akce vrací *400 – Chybný požadavek* <xref:Microsoft.AspNetCore.Mvc.ViewResult> s příslušná data.</span><span class="sxs-lookup"><span data-stu-id="8376b-135">When [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) is `false`, the action method returns a *400 Bad Request* <xref:Microsoft.AspNetCore.Mvc.ViewResult> with the appropriate data.</span></span>
* <span data-ttu-id="8376b-136">Když `ModelState.IsValid` je `true`:</span><span class="sxs-lookup"><span data-stu-id="8376b-136">When `ModelState.IsValid` is `true`:</span></span>
  * <span data-ttu-id="8376b-137">`Add` Volání metody v daném úložišti.</span><span class="sxs-lookup"><span data-stu-id="8376b-137">The `Add` method on the repository is called.</span></span>
  * <span data-ttu-id="8376b-138">A <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> je vrácen s správné argumenty.</span><span class="sxs-lookup"><span data-stu-id="8376b-138">A <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> is returned with the correct arguments.</span></span>

<span data-ttu-id="8376b-139">Do stavu modelu je neplatný je testována přidáním chyb s použitím <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> jak je znázorněno v následujícím prvního testu:</span><span class="sxs-lookup"><span data-stu-id="8376b-139">An invalid model state is tested by adding errors using <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> as shown in the first test below:</span></span>

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

<span data-ttu-id="8376b-140">Když [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) není platný, stejné `ViewResult` se vrátí jako požadavek GET.</span><span class="sxs-lookup"><span data-stu-id="8376b-140">When [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) isn't valid, the same `ViewResult` is returned as for a GET request.</span></span> <span data-ttu-id="8376b-141">Test nebude se pokoušet a zajistěte tak předání modelu je neplatný.</span><span class="sxs-lookup"><span data-stu-id="8376b-141">The test doesn't attempt to pass in an invalid model.</span></span> <span data-ttu-id="8376b-142">Předání neplatný model není platný přístup, protože není spuštěn vazby modelu (Přestože [test integrace](xref:test/integration-tests) používá vazbu modelu).</span><span class="sxs-lookup"><span data-stu-id="8376b-142">Passing an invalid model isn't a valid approach, since model binding isn't running (although an [integration test](xref:test/integration-tests) does use model binding).</span></span> <span data-ttu-id="8376b-143">V takovém případě není testovaný vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="8376b-143">In this case, model binding isn't tested.</span></span> <span data-ttu-id="8376b-144">Tyto testy jednotek pouze testovaný kód v metodě akce.</span><span class="sxs-lookup"><span data-stu-id="8376b-144">These unit tests are only testing the code in the action method.</span></span>

<span data-ttu-id="8376b-145">Druhý test ověří, že `ModelState` platí:</span><span class="sxs-lookup"><span data-stu-id="8376b-145">The second test verifies that when the `ModelState` is valid:</span></span>

* <span data-ttu-id="8376b-146">Nový `BrainstormSession` přidá (prostřednictvím úložiště).</span><span class="sxs-lookup"><span data-stu-id="8376b-146">A new `BrainstormSession` is added (via the repository).</span></span>
* <span data-ttu-id="8376b-147">Metoda vrátí `RedirectToActionResult` s očekávané vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="8376b-147">The method returns a `RedirectToActionResult` with the expected properties.</span></span>

<span data-ttu-id="8376b-148">Imitaci volání, které nejsou volány jsou obvykle ignoruje, ale volání `Verifiable` volání na konci instalace umožňuje mock ověřování do testu.</span><span class="sxs-lookup"><span data-stu-id="8376b-148">Mocked calls that aren't called are normally ignored, but calling `Verifiable` at the end of the setup call allows mock validation in the test.</span></span> <span data-ttu-id="8376b-149">To se provádí pomocí volání `mockRepo.Verify`, který selže test, pokud nebyla volána metoda očekávané.</span><span class="sxs-lookup"><span data-stu-id="8376b-149">This is performed with the call to `mockRepo.Verify`, which fails the test if the expected method wasn't called.</span></span>

> [!NOTE]
> <span data-ttu-id="8376b-150">Díky Moq knihovny používané v tomto příkladu je možné kombinovat mocks ověřitelný nebo "přísné", s neověřitelného mocks (také nazývané "dojde ke ztrátě" mocks nebo zástupné procedury).</span><span class="sxs-lookup"><span data-stu-id="8376b-150">The Moq library used in this sample makes it possible to mix verifiable, or "strict", mocks with non-verifiable mocks (also called "loose" mocks or stubs).</span></span> <span data-ttu-id="8376b-151">Další informace o [přizpůsobení chování model s využitím Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).</span><span class="sxs-lookup"><span data-stu-id="8376b-151">Learn more about [customizing Mock behavior with Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).</span></span>

<span data-ttu-id="8376b-152">[SessionController](https://github.com/aspnet/Docs/blob/master/aspnetcore/mvc/controllers/testing/sample/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) v ukázce aplikace se zobrazí informace týkající se konkrétní debaty.</span><span class="sxs-lookup"><span data-stu-id="8376b-152">[SessionController](https://github.com/aspnet/Docs/blob/master/aspnetcore/mvc/controllers/testing/sample/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) in the sample app displays information related to a particular brainstorming session.</span></span> <span data-ttu-id="8376b-153">Kontroler obsahuje logiku se neplatná `id` hodnoty (jsou k dispozici dva `return` scénáře v následujícím příkladu pro tyto scénáře).</span><span class="sxs-lookup"><span data-stu-id="8376b-153">The controller includes logic to deal with invalid `id` values (there are two `return` scenarios in the following example to cover these scenarios).</span></span> <span data-ttu-id="8376b-154">Finální `return` příkaz vrátí nový `StormSessionViewModel` k zobrazení (*Controllers/SessionController.cs*):</span><span class="sxs-lookup"><span data-stu-id="8376b-154">The final `return` statement returns a new `StormSessionViewModel` to the view (*Controllers/SessionController.cs*):</span></span>

[!code-csharp[](testing/sample/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

<span data-ttu-id="8376b-155">Testování částí zahrnují jeden test pro každou `return` scénář v kontroleru relace `Index` akce:</span><span class="sxs-lookup"><span data-stu-id="8376b-155">The unit tests include one test for each `return` scenario in the Session controller `Index` action:</span></span>

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

<span data-ttu-id="8376b-156">Přesun ke kontroleru nápady, aplikace zpřístupňuje funkce jako webové rozhraní API na `api/ideas` trasy:</span><span class="sxs-lookup"><span data-stu-id="8376b-156">Moving to the Ideas controller, the app exposes functionality as a web API on the `api/ideas` route:</span></span>

* <span data-ttu-id="8376b-157">Seznam nápadů (`IdeaDTO`) související s debaty je vrácený relace `ForSession` metody.</span><span class="sxs-lookup"><span data-stu-id="8376b-157">A list of ideas (`IdeaDTO`) associated with a brainstorming session is returned by the `ForSession` method.</span></span>
* <span data-ttu-id="8376b-158">`Create` Metoda přidá nové nápady týkající se k relaci.</span><span class="sxs-lookup"><span data-stu-id="8376b-158">The `Create` method adds new ideas to a session.</span></span>

[!code-csharp[](testing/sample/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

<span data-ttu-id="8376b-159">Vyhněte se vracení obchodní domény entity přímo prostřednictvím volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="8376b-159">Avoid returning business domain entities directly via API calls.</span></span> <span data-ttu-id="8376b-160">Entity domény:</span><span class="sxs-lookup"><span data-stu-id="8376b-160">Domain entities:</span></span>

* <span data-ttu-id="8376b-161">Často zahrnují více dat, než klient vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="8376b-161">Often include more data than the client requires.</span></span>
* <span data-ttu-id="8376b-162">Zkombinujte zbytečně model interní domény aplikace s rozhraním API pro veřejně vystavené.</span><span class="sxs-lookup"><span data-stu-id="8376b-162">Unnecessarily couple the app's internal domain model with the publicly exposed API.</span></span>

<span data-ttu-id="8376b-163">Mapování mezi domény subjekty a typy vrácených do klienta lze provést:</span><span class="sxs-lookup"><span data-stu-id="8376b-163">Mapping between domain entities and the types returned to the client can be performed:</span></span>

* <span data-ttu-id="8376b-164">Ručně pomocí LINQ `Select`, jak ukázková aplikace používá.</span><span class="sxs-lookup"><span data-stu-id="8376b-164">Manually with a LINQ `Select`, as the sample app uses.</span></span> <span data-ttu-id="8376b-165">Další informace najdete v tématu [LINQ (Language Integrated Query)](/dotnet/standard/using-linq).</span><span class="sxs-lookup"><span data-stu-id="8376b-165">For more information, see [LINQ (Language Integrated Query)](/dotnet/standard/using-linq).</span></span>
* <span data-ttu-id="8376b-166">Automaticky s knihovnou jako například [AutoMapper](https://github.com/AutoMapper/AutoMapper).</span><span class="sxs-lookup"><span data-stu-id="8376b-166">Automatically with a library, such as [AutoMapper](https://github.com/AutoMapper/AutoMapper).</span></span>

<span data-ttu-id="8376b-167">V dalším kroku ukázková aplikace předvádí testů jednotek pro `Create` a `ForSession` metody rozhraní API řadiče nápady.</span><span class="sxs-lookup"><span data-stu-id="8376b-167">Next, the sample app demonstrates unit tests for the `Create` and `ForSession` API methods of the Ideas controller.</span></span>

<span data-ttu-id="8376b-168">Ukázková aplikace obsahuje dva `ForSession` testy.</span><span class="sxs-lookup"><span data-stu-id="8376b-168">The sample app contains two `ForSession` tests.</span></span> <span data-ttu-id="8376b-169">První test Určuje, zda `ForSession` vrátí <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (HTTP nebyl nalezen) pro neplatnou relaci:</span><span class="sxs-lookup"><span data-stu-id="8376b-169">The first test determines if `ForSession` returns a <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (HTTP Not Found) for an invalid session:</span></span>

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

<span data-ttu-id="8376b-170">Druhá `ForSession` testů Určuje, zda `ForSession` vrátí seznam nápadů relace (`<List<IdeaDTO>>`) pro relaci platný.</span><span class="sxs-lookup"><span data-stu-id="8376b-170">The second `ForSession` test determines if `ForSession` returns a list of session ideas (`<List<IdeaDTO>>`) for a valid session.</span></span> <span data-ttu-id="8376b-171">Kontroly také prozkoumat první nápad k potvrzení jeho `Name` správnost vlastnost:</span><span class="sxs-lookup"><span data-stu-id="8376b-171">The checks also examine the first idea to confirm its `Name` property is correct:</span></span>

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

<span data-ttu-id="8376b-172">K otestování chování `Create` metoda při `ModelState` je neplatný, ukázkové aplikace přidá chybu modelu do řadiče testu v rámci.</span><span class="sxs-lookup"><span data-stu-id="8376b-172">To test the behavior of the `Create` method when the `ModelState` is invalid, the sample app adds a model error to the controller as part of the test.</span></span> <span data-ttu-id="8376b-173">Otestovat ověření modelu nebo vazby při testech jednotek modelu se nepokoušejte&mdash;testování chování metody akce, když setkat s neplatnou `ModelState`:</span><span class="sxs-lookup"><span data-stu-id="8376b-173">Don't try to test model validation or model binding in unit tests&mdash;just test the action method's behavior when confronted with an invalid `ModelState`:</span></span>

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

<span data-ttu-id="8376b-174">Druhý test `Create` závisí na úložišti vrácení `null`, takže mock úložiště je nakonfigurovaný k vrácení `null`.</span><span class="sxs-lookup"><span data-stu-id="8376b-174">The second test of `Create` depends on the repository returning `null`, so the mock repository is configured to return `null`.</span></span> <span data-ttu-id="8376b-175">Není nutné vytvořit testovací databáze (v paměti nebo jinak) a vytvořit dotaz, který vrátí tento výsledek.</span><span class="sxs-lookup"><span data-stu-id="8376b-175">There's no need to create a test database (in memory or otherwise) and construct a query that returns this result.</span></span> <span data-ttu-id="8376b-176">Test můžete provést v jediném příkazu, jak vzorový kód ukazuje:</span><span class="sxs-lookup"><span data-stu-id="8376b-176">The test can be accomplished in a single statement, as the sample code illustrates:</span></span>

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

<span data-ttu-id="8376b-177">Třetí `Create` otestovat, `Create_ReturnsNewlyCreatedIdeaForSession`, ověřuje, že v úložišti `UpdateAsync` metoda je volána.</span><span class="sxs-lookup"><span data-stu-id="8376b-177">The third `Create` test, `Create_ReturnsNewlyCreatedIdeaForSession`, verifies that the repository's `UpdateAsync` method is called.</span></span> <span data-ttu-id="8376b-178">Model se nazývá s `Verifiable`a imitaci úložiště `Verify` metoda je volána k potvrzení ověřitelné metody.</span><span class="sxs-lookup"><span data-stu-id="8376b-178">The mock is called with `Verifiable`, and the mocked repository's `Verify` method is called to confirm the verifiable method is executed.</span></span> <span data-ttu-id="8376b-179">Není test jednotky odpovědnost zajistit, aby `UpdateAsync` metoda uložit data&mdash;, které můžete provést pomocí o test integrace.</span><span class="sxs-lookup"><span data-stu-id="8376b-179">It's not the unit test's responsibility to ensure that the `UpdateAsync` method saved the data&mdash;that can be performed with an integration test.</span></span>

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

::: moniker range=">= aspnetcore-2.1"

## <a name="test-actionresultlttgt"></a><span data-ttu-id="8376b-180">Testování ActionResult&lt;T&gt;</span><span class="sxs-lookup"><span data-stu-id="8376b-180">Test ActionResult&lt;T&gt;</span></span>

<span data-ttu-id="8376b-181">V ASP.NET Core 2.1 nebo novější [ActionResult&lt;T&gt; ](xref:web-api/action-return-types#actionresultt-type) (<xref:Microsoft.AspNetCore.Mvc.ActionResult`1>) umožňuje návratový typ odvozený od `ActionResult` nebo vrácení specifického typu.</span><span class="sxs-lookup"><span data-stu-id="8376b-181">In ASP.NET Core 2.1 or later, [ActionResult&lt;T&gt;](xref:web-api/action-return-types#actionresultt-type) (<xref:Microsoft.AspNetCore.Mvc.ActionResult`1>) enables you to return a type deriving from `ActionResult` or return a specific type.</span></span>

<span data-ttu-id="8376b-182">Ukázková aplikace obsahuje metodu, která vrací `List<IdeaDTO>` pro dané relace `id`.</span><span class="sxs-lookup"><span data-stu-id="8376b-182">The sample app includes a method that returns a `List<IdeaDTO>` for a given session `id`.</span></span> <span data-ttu-id="8376b-183">Pokud relace `id` neexistuje, vrátí řadič <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>:</span><span class="sxs-lookup"><span data-stu-id="8376b-183">If the session `id` doesn't exist, the controller returns <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>:</span></span>

[!code-csharp[](testing/sample/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

<span data-ttu-id="8376b-184">Dvě testů `ForSessionActionResult` řadiče jsou součástí `ApiIdeasControllerTests`.</span><span class="sxs-lookup"><span data-stu-id="8376b-184">Two tests of the `ForSessionActionResult` controller are included in the `ApiIdeasControllerTests`.</span></span>

<span data-ttu-id="8376b-185">První test potvrdí, že kontroler vrací `ActionResult` , ale ne neexistující seznamu nápadů pro neexistující relace `id`:</span><span class="sxs-lookup"><span data-stu-id="8376b-185">The first test confirms that the controller returns an `ActionResult` but not a nonexistent list of ideas for a nonexistent session `id`:</span></span>

* <span data-ttu-id="8376b-186">`ActionResult` Typ je `ActionResult<List<IdeaDTO>>`.</span><span class="sxs-lookup"><span data-stu-id="8376b-186">The `ActionResult` type is `ActionResult<List<IdeaDTO>>`.</span></span>
* <span data-ttu-id="8376b-187"><xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> Je <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.</span><span class="sxs-lookup"><span data-stu-id="8376b-187">The <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> is a <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.</span></span>

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

<span data-ttu-id="8376b-188">Pro relaci platný `id`, druhý test potvrdí, že metoda vrací:</span><span class="sxs-lookup"><span data-stu-id="8376b-188">For a valid session `id`, the second test confirms that the method returns:</span></span>

* <span data-ttu-id="8376b-189">`ActionResult` s `List<IdeaDTO>` typu.</span><span class="sxs-lookup"><span data-stu-id="8376b-189">An `ActionResult` with a `List<IdeaDTO>` type.</span></span>
* <span data-ttu-id="8376b-190">[ActionResult&lt;T&gt;. Hodnota](xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Value*) je `List<IdeaDTO>` typu.</span><span class="sxs-lookup"><span data-stu-id="8376b-190">The [ActionResult&lt;T&gt;.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Value*) is a `List<IdeaDTO>` type.</span></span>
* <span data-ttu-id="8376b-191">První položka v seznamu je platný odpovídající nápad uložené v mock relace (získán voláním `GetTestSession`).</span><span class="sxs-lookup"><span data-stu-id="8376b-191">The first item in the list is a valid idea matching the idea stored in the mock session (obtained by calling `GetTestSession`).</span></span>

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

<span data-ttu-id="8376b-192">Ukázková aplikace také zahrnuje metodu pro vytvoření nového `Idea` pro dané relace.</span><span class="sxs-lookup"><span data-stu-id="8376b-192">The sample app also includes a method to create a new `Idea` for a given session.</span></span> <span data-ttu-id="8376b-193">Vrátí kontroleru:</span><span class="sxs-lookup"><span data-stu-id="8376b-193">The controller returns:</span></span>

* <span data-ttu-id="8376b-194"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> Neplatný model.</span><span class="sxs-lookup"><span data-stu-id="8376b-194"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> for an invalid model.</span></span>
* <span data-ttu-id="8376b-195"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> Pokud relace neexistuje.</span><span class="sxs-lookup"><span data-stu-id="8376b-195"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> if the session doesn't exist.</span></span>
* <span data-ttu-id="8376b-196"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> Při aktualizaci relace s nový nápad.</span><span class="sxs-lookup"><span data-stu-id="8376b-196"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> when the session is updated with the new idea.</span></span>

[!code-csharp[](testing/sample/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

<span data-ttu-id="8376b-197">Tři testů `CreateActionResult` jsou součástí `ApiIdeasControllerTests`.</span><span class="sxs-lookup"><span data-stu-id="8376b-197">Three tests of `CreateActionResult` are included in the `ApiIdeasControllerTests`.</span></span>

<span data-ttu-id="8376b-198">První textový potvrdí, že <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> vrátil neplatný model.</span><span class="sxs-lookup"><span data-stu-id="8376b-198">The first text confirms that a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> is returned for an invalid model.</span></span>

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

<span data-ttu-id="8376b-199">Druhý test kontroluje, zda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> je vrácena, pokud relace neexistuje.</span><span class="sxs-lookup"><span data-stu-id="8376b-199">The second test checks that a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> is returned if the session doesn't exist.</span></span>

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

<span data-ttu-id="8376b-200">Pro relaci platný `id`, finální testování potvrdí, že:</span><span class="sxs-lookup"><span data-stu-id="8376b-200">For a valid session `id`, the final test confirms that:</span></span>

* <span data-ttu-id="8376b-201">Metoda vrátí `ActionResult` s `BrainstormSession` typu.</span><span class="sxs-lookup"><span data-stu-id="8376b-201">The method returns an `ActionResult` with a `BrainstormSession` type.</span></span>
* <span data-ttu-id="8376b-202">[ActionResult&lt;T&gt;. Výsledek](xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*) je <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult>.</span><span class="sxs-lookup"><span data-stu-id="8376b-202">The [ActionResult&lt;T&gt;.Result](xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*) is a <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult>.</span></span> <span data-ttu-id="8376b-203">`CreatedAtActionResult` je obdobou *201 – vytvořeno* odpověď `Location` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="8376b-203">`CreatedAtActionResult` is analogous to a *201 Created* response with a `Location` header.</span></span>
* <span data-ttu-id="8376b-204">[ActionResult&lt;T&gt;. Hodnota](xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Value*) je `BrainstormSession` typu.</span><span class="sxs-lookup"><span data-stu-id="8376b-204">The [ActionResult&lt;T&gt;.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Value*) is a `BrainstormSession` type.</span></span>
* <span data-ttu-id="8376b-205">Aktualizujte relaci, mock voláním `UpdateAsync(testSession)`, byla vyvolána.</span><span class="sxs-lookup"><span data-stu-id="8376b-205">The mock call to update the session, `UpdateAsync(testSession)`, was invoked.</span></span> <span data-ttu-id="8376b-206">`Verifiable` Volání metody je zaškrtnuté políčko spuštěním `mockRepo.Verify()` v kontrolní výrazy.</span><span class="sxs-lookup"><span data-stu-id="8376b-206">The `Verifiable` method call is checked by executing `mockRepo.Verify()` in the assertions.</span></span>
* <span data-ttu-id="8376b-207">Dvě `Idea` objektů pro relaci.</span><span class="sxs-lookup"><span data-stu-id="8376b-207">Two `Idea` objects are returned for the session.</span></span>
* <span data-ttu-id="8376b-208">Poslední položky ( `Idea` přidal mock volání `UpdateAsync`) odpovídá `newIdea` přidat do relace v testu.</span><span class="sxs-lookup"><span data-stu-id="8376b-208">The last item (the `Idea` added by the mock call to `UpdateAsync`) matches the `newIdea` added to the session in the test.</span></span>

[!code-csharp[](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="8376b-209">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="8376b-209">Additional resources</span></span>

* <xref:test/integration-tests>
* <span data-ttu-id="8376b-210">[Vytváření a spouštění testování částí pomocí sady Visual Studio](/visualstudio/test/unit-test-your-code).</span><span class="sxs-lookup"><span data-stu-id="8376b-210">[Create and run unit tests with Visual Studio](/visualstudio/test/unit-test-your-code).</span></span>
