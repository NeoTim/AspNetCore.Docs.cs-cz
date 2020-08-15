---
title: Testovací komponenty v ASP.NET Core Blazor
author: guardrex
description: Naučte se testovat componments v Blazor aplikacích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/10/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/test
ms.openlocfilehash: 30c5ead98c5da934c1e76577c5dc1a39c7224a79
ms.sourcegitcommit: 4df445e7d49a99f81625430f728c28e5d6bf2107
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2020
ms.locfileid: "88253719"
---
# <a name="test-components-in-aspnet-core-no-locblazor"></a><span data-ttu-id="b7b07-103">Testovací komponenty v ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="b7b07-103">Test components in ASP.NET Core Blazor</span></span>

[<span data-ttu-id="b7b07-104">Egil Hansen</span><span class="sxs-lookup"><span data-stu-id="b7b07-104">Egil Hansen</span></span>](https://egilhansen.com/)

<span data-ttu-id="b7b07-105">Testování je důležitým aspektem budování stabilního a udržovatelného softwaru.</span><span class="sxs-lookup"><span data-stu-id="b7b07-105">Testing is an important aspect of building stable and maintainable software.</span></span>

<span data-ttu-id="b7b07-106">Chcete-li otestovat Blazor součást, je zkoušená *součást* (vyjmout):</span><span class="sxs-lookup"><span data-stu-id="b7b07-106">To test a Blazor component, the *Component Under Test* (CUT) is:</span></span>

* <span data-ttu-id="b7b07-107">Vykresleno s relevantním vstupem pro test.</span><span class="sxs-lookup"><span data-stu-id="b7b07-107">Rendered with relevant input for the test.</span></span>
* <span data-ttu-id="b7b07-108">V závislosti na typu provedeného testu může být ovlivněna interakce nebo modifikace.</span><span class="sxs-lookup"><span data-stu-id="b7b07-108">Depending on the type of test performed, possibly subject to interaction or modification.</span></span> <span data-ttu-id="b7b07-109">Například lze aktivovat obslužné rutiny událostí, jako například `onclick` událost pro tlačítko.</span><span class="sxs-lookup"><span data-stu-id="b7b07-109">For example, event handlers can be triggered, such as an `onclick` event for a button.</span></span>
* <span data-ttu-id="b7b07-110">Zkontrolováno očekávaných hodnot.</span><span class="sxs-lookup"><span data-stu-id="b7b07-110">Inspected for expected values.</span></span>

## <a name="test-approaches"></a><span data-ttu-id="b7b07-111">Test přístupů</span><span class="sxs-lookup"><span data-stu-id="b7b07-111">Test approaches</span></span>

<span data-ttu-id="b7b07-112">Dva běžné přístupy k testování Blazor komponent jsou komplexní testování (e2e) a testování částí:</span><span class="sxs-lookup"><span data-stu-id="b7b07-112">Two common approaches for testing Blazor components are end-to-end (E2E) testing and unit testing:</span></span>

* <span data-ttu-id="b7b07-113">**Testování částí**: [testy jednotek](/dotnet/core/testing/) jsou zapisovány pomocí knihovny testování částí, která poskytuje:</span><span class="sxs-lookup"><span data-stu-id="b7b07-113">**Unit testing**: [Unit tests](/dotnet/core/testing/) are written with a unit testing library that provides:</span></span>
  * <span data-ttu-id="b7b07-114">Vykreslování součásti.</span><span class="sxs-lookup"><span data-stu-id="b7b07-114">Component rendering.</span></span>
  * <span data-ttu-id="b7b07-115">Kontrola výstupu součásti a stavu.</span><span class="sxs-lookup"><span data-stu-id="b7b07-115">Inspection of component output and state.</span></span>
  * <span data-ttu-id="b7b07-116">Aktivace obslužných rutin událostí a metod životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="b7b07-116">Triggering of event handlers and life cycle methods.</span></span>
  * <span data-ttu-id="b7b07-117">Kontrolní výrazy, které chování komponenty jsou správné.</span><span class="sxs-lookup"><span data-stu-id="b7b07-117">Assertions that component behavior is correct.</span></span>

  <span data-ttu-id="b7b07-118">[bUnit](https://github.com/egil/bUnit) je příklad knihovny, která umožňuje testování částí Razor komponent.</span><span class="sxs-lookup"><span data-stu-id="b7b07-118">[bUnit](https://github.com/egil/bUnit) is an example of a library that enables Razor component unit testing.</span></span>

* <span data-ttu-id="b7b07-119">**Testování e2e**: spouštěč testů spustí Blazor aplikaci obsahující vyjmutí a automatizuje instanci prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="b7b07-119">**E2E testing**: A test runner runs a Blazor app containing the CUT and automates a browser instance.</span></span> <span data-ttu-id="b7b07-120">Testovací nástroj kontroluje a komunikuje s OŘÍZNUTím prostřednictvím prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="b7b07-120">The testing tool inspects and interacts with the CUT through the browser.</span></span> <span data-ttu-id="b7b07-121">[Selen](https://github.com/SeleniumHQ/selenium) je příkladem testovacího rozhraní e2e, které lze použít s Blazor aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="b7b07-121">[Selenium](https://github.com/SeleniumHQ/selenium) is an example of an E2E testing framework that can be used with Blazor apps.</span></span>

<span data-ttu-id="b7b07-122">V testování částí Blazor Razor je zapojena pouze komponenta (/c #).</span><span class="sxs-lookup"><span data-stu-id="b7b07-122">In unit testing, only the Blazor component (Razor/C#) is involved.</span></span> <span data-ttu-id="b7b07-123">Externí závislosti, například interoperabilita služeb a JS, musí být napsaný.</span><span class="sxs-lookup"><span data-stu-id="b7b07-123">External dependencies, such as services and JS interop, must be mocked.</span></span> <span data-ttu-id="b7b07-124">V testování E2E jsou součástí Blazor testu součást a všechna pomocná infrastruktura, včetně šablon stylů CSS, js a DOM a rozhraní API prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="b7b07-124">In E2E testing, the Blazor component and all of it's auxiliary infrastructure are part of the test, including CSS, JS, and the DOM and browser APIs.</span></span>

<span data-ttu-id="b7b07-125">*Rozsah testu* popisuje, jak velký je počet testů.</span><span class="sxs-lookup"><span data-stu-id="b7b07-125">*Test scope* describes how extensive the tests are.</span></span> <span data-ttu-id="b7b07-126">Rozsah testu má obvykle vliv na rychlost testů.</span><span class="sxs-lookup"><span data-stu-id="b7b07-126">Test scope typically has an influence on the speed of the tests.</span></span> <span data-ttu-id="b7b07-127">Testy jednotek se spouštějí v podmnožině subsystémů aplikace a obvykle se spouštějí v milisekundách.</span><span class="sxs-lookup"><span data-stu-id="b7b07-127">Unit tests run on a subset of the app's subsystems and usually execute in milliseconds.</span></span> <span data-ttu-id="b7b07-128">Testy E2E, které testují širokou skupinu subsystémů aplikace, můžou dokončit několik sekund.</span><span class="sxs-lookup"><span data-stu-id="b7b07-128">E2E tests, which test a broad group of the app's subsystems, can take several seconds to complete.</span></span> 

<span data-ttu-id="b7b07-129">Testování částí také poskytuje přístup k instanci řezu, umožňující kontrole a ověření vnitřního stavu komponenty.</span><span class="sxs-lookup"><span data-stu-id="b7b07-129">Unit testing also provides access to the instance of the CUT, allowing for inspection and verification of the component's internal state.</span></span> <span data-ttu-id="b7b07-130">To obvykle není možné při testování E2E.</span><span class="sxs-lookup"><span data-stu-id="b7b07-130">This normally isn't possible in E2E testing.</span></span>

<span data-ttu-id="b7b07-131">V souvislosti s prostředím komponenty musí testy E2E před zahájením ověřování zajistěte, aby byl dosažen očekávaný stav prostředí.</span><span class="sxs-lookup"><span data-stu-id="b7b07-131">With regard to the component's environment, E2E tests must make sure that the expected environmental state has been reached before verification starts.</span></span> <span data-ttu-id="b7b07-132">V opačném případě se výsledek nepředpokládá.</span><span class="sxs-lookup"><span data-stu-id="b7b07-132">Otherwise, the result is unpredictable.</span></span> <span data-ttu-id="b7b07-133">Při testování částí jsou vykreslování funkce vyjmout a životní cyklus testu více integrovány, což zlepšuje stabilitu testu.</span><span class="sxs-lookup"><span data-stu-id="b7b07-133">In unit testing, the rendering of the CUT and the life cycle of the test are more integrated, which improves test stability.</span></span>

<span data-ttu-id="b7b07-134">E2E testování zahrnuje spouštění více procesů, vstupně-výstupních operací v síti a disku a další činnosti subsystému, které často vedou k nízké spolehlivosti testů.</span><span class="sxs-lookup"><span data-stu-id="b7b07-134">E2E testing involves launching multiple processes, network and disk I/O, and other subsystem activity that often lead to poor test reliability.</span></span> <span data-ttu-id="b7b07-135">Testy jednotek jsou obvykle izolované z těchto druhů problémů.</span><span class="sxs-lookup"><span data-stu-id="b7b07-135">Unit tests are typically insulated from these sorts of issues.</span></span>

<span data-ttu-id="b7b07-136">Následující tabulka shrnuje rozdíl mezi dvěma testovacími přístupy.</span><span class="sxs-lookup"><span data-stu-id="b7b07-136">The following table summarizes the difference between the two testing approaches.</span></span>

| <span data-ttu-id="b7b07-137">Schopnost</span><span class="sxs-lookup"><span data-stu-id="b7b07-137">Capability</span></span>                       | <span data-ttu-id="b7b07-138">Testování jednotek</span><span class="sxs-lookup"><span data-stu-id="b7b07-138">Unit testing</span></span>                     | <span data-ttu-id="b7b07-139">E2E testování</span><span class="sxs-lookup"><span data-stu-id="b7b07-139">E2E testing</span></span>                             |
| -------------------------------- | -------------------------------- | --------------------------------------- |
| <span data-ttu-id="b7b07-140">Rozsah testu</span><span class="sxs-lookup"><span data-stu-id="b7b07-140">Test scope</span></span>                       | <span data-ttu-id="b7b07-141">Blazor jenom součást ( Razor /c #)</span><span class="sxs-lookup"><span data-stu-id="b7b07-141">Blazor component (Razor/C#) only</span></span> | <span data-ttu-id="b7b07-142">Blazor součást ( Razor /c #) s CSS/JS</span><span class="sxs-lookup"><span data-stu-id="b7b07-142">Blazor component (Razor/C#) with CSS/JS</span></span> |
| <span data-ttu-id="b7b07-143">Čas spuštění testu</span><span class="sxs-lookup"><span data-stu-id="b7b07-143">Test execution time</span></span>              | <span data-ttu-id="b7b07-144">Milisekund</span><span class="sxs-lookup"><span data-stu-id="b7b07-144">Milliseconds</span></span>                     | <span data-ttu-id="b7b07-145">Sekundy</span><span class="sxs-lookup"><span data-stu-id="b7b07-145">Seconds</span></span>                                 |
| <span data-ttu-id="b7b07-146">Přístup k instanci komponenty</span><span class="sxs-lookup"><span data-stu-id="b7b07-146">Access to the component instance</span></span> | <span data-ttu-id="b7b07-147">Ano</span><span class="sxs-lookup"><span data-stu-id="b7b07-147">Yes</span></span>                              | <span data-ttu-id="b7b07-148">No</span><span class="sxs-lookup"><span data-stu-id="b7b07-148">No</span></span>                                      |
| <span data-ttu-id="b7b07-149">Citlivá na prostředí</span><span class="sxs-lookup"><span data-stu-id="b7b07-149">Sensitive to the environment</span></span>     | <span data-ttu-id="b7b07-150">No</span><span class="sxs-lookup"><span data-stu-id="b7b07-150">No</span></span>                               | <span data-ttu-id="b7b07-151">Ano</span><span class="sxs-lookup"><span data-stu-id="b7b07-151">Yes</span></span>                                     |
| <span data-ttu-id="b7b07-152">Spolehlivost</span><span class="sxs-lookup"><span data-stu-id="b7b07-152">Reliability</span></span>                      | <span data-ttu-id="b7b07-153">Spolehlivější</span><span class="sxs-lookup"><span data-stu-id="b7b07-153">More reliable</span></span>                    | <span data-ttu-id="b7b07-154">Méně spolehlivé</span><span class="sxs-lookup"><span data-stu-id="b7b07-154">Less reliable</span></span>                           |

## <a name="choose-the-most-appropriate-test-approach"></a><span data-ttu-id="b7b07-155">Volba nejvhodnějšího testovacího přístupu</span><span class="sxs-lookup"><span data-stu-id="b7b07-155">Choose the most appropriate test approach</span></span>

<span data-ttu-id="b7b07-156">Při výběru typu testování, který se má provést, vezměte v úvahu scénář.</span><span class="sxs-lookup"><span data-stu-id="b7b07-156">Consider the scenario when choosing the type of testing to perform.</span></span> <span data-ttu-id="b7b07-157">Některé okolnosti jsou popsány v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="b7b07-157">Some considerations are described in the following table.</span></span>

| <span data-ttu-id="b7b07-158">Scénář</span><span class="sxs-lookup"><span data-stu-id="b7b07-158">Scenario</span></span> | <span data-ttu-id="b7b07-159">Navrhovaný přístup</span><span class="sxs-lookup"><span data-stu-id="b7b07-159">Suggested approach</span></span> | <span data-ttu-id="b7b07-160">Poznámky</span><span class="sxs-lookup"><span data-stu-id="b7b07-160">Remarks</span></span> |
| -------- | ------------------ | ------- |
| <span data-ttu-id="b7b07-161">Součást bez logiky spolupráce JS</span><span class="sxs-lookup"><span data-stu-id="b7b07-161">Component without JS interop logic</span></span> | <span data-ttu-id="b7b07-162">Testování jednotek</span><span class="sxs-lookup"><span data-stu-id="b7b07-162">Unit testing</span></span> | <span data-ttu-id="b7b07-163">Pokud neexistuje žádná závislost na zprostředkovateli komunikace s JS v Blazor komponentě, může být komponenta testována bez přístupu k JS nebo rozhraní API modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="b7b07-163">When there's no dependency on JS interop in a Blazor component, the component can be tested without access to JS or the DOM API.</span></span> <span data-ttu-id="b7b07-164">V tomto scénáři nehrozí žádné nevýhody pro výběr testování částí.</span><span class="sxs-lookup"><span data-stu-id="b7b07-164">In this scenario, there are no disadvantages to choosing unit testing.</span></span> |
| <span data-ttu-id="b7b07-165">Komponenta s jednoduchou logikou spolupráce JS</span><span class="sxs-lookup"><span data-stu-id="b7b07-165">Component with simple JS interop logic</span></span> | <span data-ttu-id="b7b07-166">Testování jednotek</span><span class="sxs-lookup"><span data-stu-id="b7b07-166">Unit testing</span></span> | <span data-ttu-id="b7b07-167">Je běžné, že komponenty pro dotazování na model DOM nebo spouštějí animace prostřednictvím zprostředkovatele komunikace JS.</span><span class="sxs-lookup"><span data-stu-id="b7b07-167">It's common for components to query the DOM or trigger animations through JS interop.</span></span> <span data-ttu-id="b7b07-168">Testování částí je obvykle upřednostňováno v tomto scénáři, protože je jednoduché k napodobení interakce JS přes <xref:Microsoft.JSInterop.IJSRuntime> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b7b07-168">Unit testing is usually preferred in this scenario, since it's straightforward to mock the JS interaction through the <xref:Microsoft.JSInterop.IJSRuntime> interface.</span></span> |
| <span data-ttu-id="b7b07-169">Komponenta, která závisí na komplexním kódu JS</span><span class="sxs-lookup"><span data-stu-id="b7b07-169">Component that depends on complex JS code</span></span> | <span data-ttu-id="b7b07-170">Testování částí a samostatné testování JS</span><span class="sxs-lookup"><span data-stu-id="b7b07-170">Unit testing and separate JS testing</span></span> | <span data-ttu-id="b7b07-171">Pokud komponenta používá zprostředkovatele komunikace JS pro volání rozsáhlých nebo složitých knihoven JS, ale interakce mezi Blazor knihovnou komponent a js je jednoduchá, pak nejlepší přístup je pravděpodobným způsobem zacházet s knihovnou komponenty a JS nebo s kódem jako dvěma samostatnými částmi a testovat každou jednotlivě.</span><span class="sxs-lookup"><span data-stu-id="b7b07-171">If a component uses JS interop to call a large or complex JS library but the interaction between the Blazor component and JS library is simple, then the best approach is likely to treat the component and JS library or code as two separate parts and test each individually.</span></span> <span data-ttu-id="b7b07-172">Otestujte Blazor komponentu pomocí knihovny testování částí a otestujte js pomocí knihovny testování js.</span><span class="sxs-lookup"><span data-stu-id="b7b07-172">Test the Blazor component with a unit testing library, and test the JS with a JS testing library.</span></span> |
| <span data-ttu-id="b7b07-173">Komponenta s logikou, která závisí na manipulaci JS modelu DOM v prohlížeči</span><span class="sxs-lookup"><span data-stu-id="b7b07-173">Component with logic that depends on JS manipulation of the browser DOM</span></span> | <span data-ttu-id="b7b07-174">E2E testování</span><span class="sxs-lookup"><span data-stu-id="b7b07-174">E2E testing</span></span> | <span data-ttu-id="b7b07-175">Je-li funkce komponenty závislá na JS a její manipulace s modelem DOM, ověřte, jak JS, tak i Blazor kód společně v e2e testu.</span><span class="sxs-lookup"><span data-stu-id="b7b07-175">When a component's functionality is dependent on JS and its manipulation of the DOM, verify both the JS and Blazor code together in an E2E test.</span></span> <span data-ttu-id="b7b07-176">Toto je přístup, který vývojáři architektury povedli Blazor s Blazor logikou vykreslování v prohlížeči, která má pevně spojený kód C# a js.</span><span class="sxs-lookup"><span data-stu-id="b7b07-176">This is the approach that the Blazor framework developers have taken with Blazor's browser rendering logic, which has tightly-coupled C# and JS code.</span></span> <span data-ttu-id="b7b07-177">Kód v jazyce C# a JS musí spolupracovat, aby bylo možné správně vykreslovat Blazor komponenty v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="b7b07-177">The C# and JS code must work together to correctly render Blazor components in a browser.</span></span>
| <span data-ttu-id="b7b07-178">Komponenta, která závisí na knihovně komponent třetích stran s pevnými závislostmi</span><span class="sxs-lookup"><span data-stu-id="b7b07-178">Component that depends on 3rd party component library with hard-to-mock dependencies</span></span> | <span data-ttu-id="b7b07-179">E2E testování</span><span class="sxs-lookup"><span data-stu-id="b7b07-179">E2E testing</span></span> | <span data-ttu-id="b7b07-180">Pokud je funkce součásti závislá na knihovně komponent třetí strany, která má pevné závislosti, jako je například zprostředkovatel komunikace JS, může být testování E2E jedinou možností pro otestování komponenty.</span><span class="sxs-lookup"><span data-stu-id="b7b07-180">When a component's functionality is dependent on a 3rd party component library that has hard-to-mock dependencies, such as JS interop, E2E testing might be the only option to test the component.</span></span> |

## <a name="test-components-with-bunit"></a><span data-ttu-id="b7b07-181">Testování komponent pomocí bUnit</span><span class="sxs-lookup"><span data-stu-id="b7b07-181">Test components with bUnit</span></span>

<span data-ttu-id="b7b07-182">K dispozici není oficiální testovací rozhraní Microsoftu pro Blazor , ale projekt založený na komunitě [bUnit](https://github.com/egil/bUnit) poskytuje pohodlný způsob, jak jednotky testování Blazor částí.</span><span class="sxs-lookup"><span data-stu-id="b7b07-182">There's no official Microsoft testing framework for Blazor, but the community-driven project [bUnit](https://github.com/egil/bUnit) provides a convenient way to unit test Blazor components.</span></span>

> [!NOTE]
> <span data-ttu-id="b7b07-183">bUnit je knihovna pro testování třetí strany, kterou Microsoft nepodporuje nebo nespravuje.</span><span class="sxs-lookup"><span data-stu-id="b7b07-183">bUnit is a third-party testing library and isn't supported or maintained by Microsoft.</span></span>

<span data-ttu-id="b7b07-184">bUnit pracuje s rozhraními pro obecné účely testování, jako je [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [nunit](https://nunit.org/)a [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="b7b07-184">bUnit works with general-purpose testing frameworks, such as [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [NUnit](https://nunit.org/), and [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="b7b07-185">Tyto testovací architektury provedou testy bUnit a vypadají stejně jako běžné testy jednotek.</span><span class="sxs-lookup"><span data-stu-id="b7b07-185">These testing frameworks make bUnit tests look and feel like regular unit tests.</span></span> <span data-ttu-id="b7b07-186">testy bUnit integrované s testovacím rozhraním pro obecné účely jsou obvykle spouštěny pomocí:</span><span class="sxs-lookup"><span data-stu-id="b7b07-186">bUnit tests integrated with a general-purpose testing framework are ordinarily executed with:</span></span>

* <span data-ttu-id="b7b07-187">[Průzkumník testů sady Visual Studio](/visualstudio/test/run-unit-tests-with-test-explorer).</span><span class="sxs-lookup"><span data-stu-id="b7b07-187">[Visual Studio's Test Explorer](/visualstudio/test/run-unit-tests-with-test-explorer).</span></span>
* <span data-ttu-id="b7b07-188">[`dotnet test`](/dotnet/core/tools/dotnet-test) Příkaz CLI v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="b7b07-188">[`dotnet test`](/dotnet/core/tools/dotnet-test) CLI command in a command shell.</span></span>
* <span data-ttu-id="b7b07-189">Automatizovaný kanál testování DevOps</span><span class="sxs-lookup"><span data-stu-id="b7b07-189">An automated DevOps testing pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="b7b07-190">Koncepty testů a testovací implementace v různých testovacích architekturách jsou podobné, ale nejsou totožné.</span><span class="sxs-lookup"><span data-stu-id="b7b07-190">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span> <span data-ttu-id="b7b07-191">Pokyny najdete v dokumentaci k testovacímu rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b7b07-191">Refer to the test framework's documentation for guidance.</span></span>

<span data-ttu-id="b7b07-192">Následující příklad ukazuje strukturu testu bUnit pro `Counter` komponentu v aplikaci na základě Blazor šablony projektu.</span><span class="sxs-lookup"><span data-stu-id="b7b07-192">The following demonstrates the structure of a bUnit test on the `Counter` component in an app based on a Blazor project template.</span></span> <span data-ttu-id="b7b07-193">`Counter`Komponenta zobrazí a zvýší čítač na základě toho, že uživatel vybere tlačítko na stránce:</span><span class="sxs-lookup"><span data-stu-id="b7b07-193">The `Counter` component displays and increments a counter based on the user selecting a button in the page:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="b7b07-194">Následující test bUnit ověří, zda je čítač vyjmutí správně zvýšen, když je vybráno tlačítko:</span><span class="sxs-lookup"><span data-stu-id="b7b07-194">The following bUnit test verifies that the CUT's counter is incremented correctly when the button is selected:</span></span>

```csharp
[Fact]
public void CounterShouldIncrementWhenSelected()
{
    // Arrange
    using var cxt = new TestContext();
    var cut = ctx.RenderComponent<Counter>();
    var paraElm = cut.Find("p");

    // Act
    cut.Find("button").Click();
    var paraElmText = paraElm.TextContent;

    // Assert
    paraElmText.MarkupMatches("Current count: 1");
}
```

<span data-ttu-id="b7b07-195">V každém kroku testu probíhají následující akce:</span><span class="sxs-lookup"><span data-stu-id="b7b07-195">The following actions take place at each step of the test:</span></span>

* <span data-ttu-id="b7b07-196">*Uspořádat*: `Counter` Komponenta se vykresluje pomocí bUnit `TestContext` .</span><span class="sxs-lookup"><span data-stu-id="b7b07-196">*Arrange*: The `Counter` component is rendered using bUnit's `TestContext`.</span></span> <span data-ttu-id="b7b07-197">`<p>`Byl nalezen a přiřazen element řezu () pro vyjmutí `paraElm` .</span><span class="sxs-lookup"><span data-stu-id="b7b07-197">The CUT's paragraph element (`<p>`) is found and assigned to `paraElm`.</span></span>

* <span data-ttu-id="b7b07-198">*Act*: element tlačítka ( `<button>` ) se nachází a pak se vybere podle volání `Click` , které by mělo zvýšit počítadlo a aktualizovat obsah značky odstavce ( `<p>` ).</span><span class="sxs-lookup"><span data-stu-id="b7b07-198">*Act*: The button's element (`<button>`) is located and then selected by calling `Click`, which should increment the counter and update the content of the paragraph tag (`<p>`).</span></span> <span data-ttu-id="b7b07-199">Textový obsah prvku odstavce je získán voláním `TextContent` .</span><span class="sxs-lookup"><span data-stu-id="b7b07-199">The paragraph element text content is obtained by calling `TextContent`.</span></span>

* <span data-ttu-id="b7b07-200">*Assert*: `MarkupMatches` je volána u textového obsahu pro ověření, že se shoduje s očekávaným řetězcem, který je `Current count: 1` .</span><span class="sxs-lookup"><span data-stu-id="b7b07-200">*Assert*: `MarkupMatches` is called on the text content to verify that it matches the expected string, which is `Current count: 1`.</span></span>

> [!NOTE]
> <span data-ttu-id="b7b07-201">`MarkupMatches`Metoda Assert se liší od kontrolního výrazu regulárního porovnání řetězců (například `Assert.Equal("Current count: 1", paraElmText);` ) `MarkupMatches` provede sémantické porovnání vstupu a očekávaného kódu HTML.</span><span class="sxs-lookup"><span data-stu-id="b7b07-201">The `MarkupMatches` assert method differs from a regular string comparison assertion (for example, `Assert.Equal("Current count: 1", paraElmText);`) `MarkupMatches` performs a semantic comparison of the input and expected HTML markup.</span></span> <span data-ttu-id="b7b07-202">Sémantické porovnání je vědomo sémantiky HTML, což znamená, že se ignorují nevýznamné prázdné znaky.</span><span class="sxs-lookup"><span data-stu-id="b7b07-202">A semantic comparison is aware of HTML semantics, meaning things like insignificant whitespace is ignored.</span></span> <span data-ttu-id="b7b07-203">Výsledkem je více stabilních testů.</span><span class="sxs-lookup"><span data-stu-id="b7b07-203">This results in more stable tests.</span></span> <span data-ttu-id="b7b07-204">Další informace najdete v tématu [přizpůsobení sémantického porovnání HTML](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).</span><span class="sxs-lookup"><span data-stu-id="b7b07-204">For more information, see [Customizing the Semantic HTML Comparison](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b7b07-205">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b7b07-205">Additional resources</span></span>

* <span data-ttu-id="b7b07-206">[Začínáme s bUnit](https://bunit.egilhansen.com/docs/getting-started/): pokyny bUnit zahrnují pokyny k vytvoření testovacího projektu, odkazování na testovací balíčky rozhraní a sestavování a spouštění testů.</span><span class="sxs-lookup"><span data-stu-id="b7b07-206">[Getting Started with bUnit](https://bunit.egilhansen.com/docs/getting-started/): bUnit instructions include guidance on creating a test project, referencing testing framework packages, and building and running tests.</span></span>
