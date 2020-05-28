---
<span data-ttu-id="0e117-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-102">'Blazor'</span></span>
- <span data-ttu-id="0e117-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-103">'Identity'</span></span>
- <span data-ttu-id="0e117-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-105">'Razor'</span></span>
- <span data-ttu-id="0e117-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-106">'SignalR' uid:</span></span> 

---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="0e117-107">Integrační testy v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0e117-107">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="0e117-108">[Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/)a [Jos Van der dlaždicí](https://jvandertil.nl)</span><span class="sxs-lookup"><span data-stu-id="0e117-108">By [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/), and [Jos van der Til](https://jvandertil.nl)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0e117-109">Testy integrace zajistí správné fungování komponent aplikace na úrovni, která zahrnuje podpůrnou infrastrukturu aplikace, jako je třeba databáze, systém souborů a síť.</span><span class="sxs-lookup"><span data-stu-id="0e117-109">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="0e117-110">ASP.NET Core podporuje testy integrace pomocí architektury testování částí s testovacím webovým hostitelem a testovacím serverem v paměti.</span><span class="sxs-lookup"><span data-stu-id="0e117-110">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="0e117-111">Toto téma předpokládá základní znalost testů jednotek.</span><span class="sxs-lookup"><span data-stu-id="0e117-111">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="0e117-112">Pokud neznáte koncepty testování, přečtěte si téma [testování částí v .NET Core a .NET Standard](/dotnet/core/testing/) a jeho propojený obsah.</span><span class="sxs-lookup"><span data-stu-id="0e117-112">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="0e117-113">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0e117-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0e117-114">Ukázková aplikace je Razor aplikace stránky a předpokládá základní znalost Razor stránek.</span><span class="sxs-lookup"><span data-stu-id="0e117-114">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="0e117-115">Pokud neznáte Razor stránky, přečtěte si následující témata:</span><span class="sxs-lookup"><span data-stu-id="0e117-115">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="0e117-116">[Úvod ke Razor stránkám](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="0e117-116">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="0e117-117">[Začínáme se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="0e117-117">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="0e117-118">[RazorTestování částí stránek](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="0e117-118">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="0e117-119">Pro testování jednostránkové doporučujeme nástroj, jako je například [selen](https://www.seleniumhq.org/), který může automatizovat prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="0e117-119">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="0e117-120">Úvod do integračních testů</span><span class="sxs-lookup"><span data-stu-id="0e117-120">Introduction to integration tests</span></span>

<span data-ttu-id="0e117-121">Testy integrace vyhodnocují komponenty aplikace na širší úrovni než [testy jednotek](/dotnet/core/testing/).</span><span class="sxs-lookup"><span data-stu-id="0e117-121">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="0e117-122">Testy jednotek se používají k testování izolovaných softwarových komponent, jako jsou například jednotlivé metody třídy.</span><span class="sxs-lookup"><span data-stu-id="0e117-122">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="0e117-123">Testy integrace potvrzují, že dvě nebo více součástí aplikace společně tvoří očekávaný výsledek, případně včetně všech komponent vyžadovaných k úplnému zpracování žádosti.</span><span class="sxs-lookup"><span data-stu-id="0e117-123">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="0e117-124">Tyto širší testy se používají k testování infrastruktury a celé architektury aplikace, často včetně následujících komponent:</span><span class="sxs-lookup"><span data-stu-id="0e117-124">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="0e117-125">databáze</span><span class="sxs-lookup"><span data-stu-id="0e117-125">Database</span></span>
* <span data-ttu-id="0e117-126">Systém souborů</span><span class="sxs-lookup"><span data-stu-id="0e117-126">File system</span></span>
* <span data-ttu-id="0e117-127">Síťová zařízení</span><span class="sxs-lookup"><span data-stu-id="0e117-127">Network appliances</span></span>
* <span data-ttu-id="0e117-128">Kanál požadavků a odpovědí</span><span class="sxs-lookup"><span data-stu-id="0e117-128">Request-response pipeline</span></span>

<span data-ttu-id="0e117-129">Testy jednotek používají prefabrikované komponenty, označované jako *napodobeniny* nebo *makety objektů*, místo součástí infrastruktury.</span><span class="sxs-lookup"><span data-stu-id="0e117-129">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="0e117-130">Na rozdíl od testování částí, integrační testy:</span><span class="sxs-lookup"><span data-stu-id="0e117-130">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="0e117-131">Používejte skutečné komponenty, které aplikace používá v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="0e117-131">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="0e117-132">Vyžadovat další zpracování kódu a dat.</span><span class="sxs-lookup"><span data-stu-id="0e117-132">Require more code and data processing.</span></span>
* <span data-ttu-id="0e117-133">Spuštění trvá déle.</span><span class="sxs-lookup"><span data-stu-id="0e117-133">Take longer to run.</span></span>

<span data-ttu-id="0e117-134">Proto omezte použití integračních testů na nejdůležitější scénáře infrastruktury.</span><span class="sxs-lookup"><span data-stu-id="0e117-134">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="0e117-135">Pokud může být chování Testováno pomocí testu jednotky nebo testu integrace, vyberte test jednotky.</span><span class="sxs-lookup"><span data-stu-id="0e117-135">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="0e117-136">Nepište integrační testy pro všechny možné permutace dat a přístup k souborům s databázemi a systémy souborů.</span><span class="sxs-lookup"><span data-stu-id="0e117-136">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="0e117-137">Bez ohledu na to, kolik míst v rámci aplikace komunikuje s databázemi a systémy souborů, je obvykle schopnost provádět odpovídající testování součástí integračních testů pro čtení, zápis, aktualizace a odstraňování.</span><span class="sxs-lookup"><span data-stu-id="0e117-137">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="0e117-138">Testování částí použijte pro rutinní testy logiky metod, které s těmito komponentami pracují.</span><span class="sxs-lookup"><span data-stu-id="0e117-138">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="0e117-139">Při testování částí je použití falešných nebo pokusů infrastruktury výsledkem rychlejšího spuštění testu.</span><span class="sxs-lookup"><span data-stu-id="0e117-139">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="0e117-140">V diskuzích o integračních testech se testovaný projekt často nazývá testovaný *systém*nebo "SUT" pro krátké účely.</span><span class="sxs-lookup"><span data-stu-id="0e117-140">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="0e117-141">*"SUT" se v tomto tématu používá pro odkaz na testované aplikace ASP.NET Core.*</span><span class="sxs-lookup"><span data-stu-id="0e117-141">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="0e117-142">ASP.NET Core testy integrace</span><span class="sxs-lookup"><span data-stu-id="0e117-142">ASP.NET Core integration tests</span></span>

<span data-ttu-id="0e117-143">Integrační testy v ASP.NET Core vyžadují následující:</span><span class="sxs-lookup"><span data-stu-id="0e117-143">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="0e117-144">Testovací projekt se používá k zahrnutí a spuštění testů.</span><span class="sxs-lookup"><span data-stu-id="0e117-144">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="0e117-145">Testovací projekt má odkaz na SUT.</span><span class="sxs-lookup"><span data-stu-id="0e117-145">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="0e117-146">Testovací projekt vytvoří testovací webový hostitel pro SUT a používá klienta testovacího serveru ke zpracování požadavků a odpovědí s SUT.</span><span class="sxs-lookup"><span data-stu-id="0e117-146">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="0e117-147">Test Runner se používá ke spuštění testů a hlášení výsledků testu.</span><span class="sxs-lookup"><span data-stu-id="0e117-147">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="0e117-148">Integrační testy následují sekvenci událostí, která zahrnuje běžné kroky pro *uspořádání*, *jednání*a *vyhodnocení* :</span><span class="sxs-lookup"><span data-stu-id="0e117-148">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="0e117-149">Webový hostitel SUT je nakonfigurován.</span><span class="sxs-lookup"><span data-stu-id="0e117-149">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="0e117-150">Je vytvořen klient testovacího serveru, který odesílá žádosti do aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e117-150">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="0e117-151">Krok *Uspořádat* test se spustí: testovací aplikace připraví požadavek.</span><span class="sxs-lookup"><span data-stu-id="0e117-151">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="0e117-152">Testovací krok *Act* se spustí: klient odešle požadavek a přijme odpověď.</span><span class="sxs-lookup"><span data-stu-id="0e117-152">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="0e117-153">Testovací krok *kontrolního výrazu* je proveden: *skutečná* odpověď je ověřena jako *Pass* nebo *selže* na základě *očekávané* odpovědi.</span><span class="sxs-lookup"><span data-stu-id="0e117-153">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="0e117-154">Proces pokračuje, dokud nebudou provedeny všechny testy.</span><span class="sxs-lookup"><span data-stu-id="0e117-154">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="0e117-155">Výsledky testu jsou hlášeny.</span><span class="sxs-lookup"><span data-stu-id="0e117-155">The test results are reported.</span></span>

<span data-ttu-id="0e117-156">Obvykle je testovací webový hostitel nakonfigurovaný jinak než normální webový hostitel aplikace pro testovací běhy.</span><span class="sxs-lookup"><span data-stu-id="0e117-156">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="0e117-157">Pro testy lze například použít jinou databázi nebo jiná nastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e117-157">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="0e117-158">Komponenty infrastruktury, jako je testovací webový hostitel a[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)(In-Memory test server), jsou poskytovány nebo spravovány balíčkem [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) .</span><span class="sxs-lookup"><span data-stu-id="0e117-158">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="0e117-159">Použití tohoto balíčku zjednodušuje vytváření a spouštění testů.</span><span class="sxs-lookup"><span data-stu-id="0e117-159">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="0e117-160">`Microsoft.AspNetCore.Mvc.Testing`Balíček zpracovává následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="0e117-160">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="0e117-161">Zkopíruje soubor závislosti (*. DEPS*) z SUT do adresáře *bin* testovacího projektu.</span><span class="sxs-lookup"><span data-stu-id="0e117-161">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="0e117-162">Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na kořen projektu SUT, aby při spuštění testů byly nalezeny statické soubory a stránky/zobrazení.</span><span class="sxs-lookup"><span data-stu-id="0e117-162">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="0e117-163">Poskytuje třídu [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) pro zjednodušení zavádění SUT pomocí `TestServer` .</span><span class="sxs-lookup"><span data-stu-id="0e117-163">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="0e117-164">Dokumentace k testování [částí](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) popisuje, jak nastavit testovací projekt a Test Runner spolu s podrobnými pokyny ke spuštění testů a doporučení pro pojmenování testů a testovacích tříd.</span><span class="sxs-lookup"><span data-stu-id="0e117-164">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="0e117-165">Při vytváření testovacího projektu pro aplikaci oddělte testy jednotek od testů integrace do různých projektů.</span><span class="sxs-lookup"><span data-stu-id="0e117-165">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="0e117-166">To pomáhá zajistit, že komponenty pro testování infrastruktury nejsou omylem zahrnuty do testů jednotek.</span><span class="sxs-lookup"><span data-stu-id="0e117-166">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="0e117-167">Oddělení testů jednotek a integračních testů také umožňuje kontrolu nad tím, která sada testů je spuštěna.</span><span class="sxs-lookup"><span data-stu-id="0e117-167">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="0e117-168">Konfigurace pro testy Razor stránek aplikace a aplikace MVC se prakticky neliší.</span><span class="sxs-lookup"><span data-stu-id="0e117-168">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="0e117-169">Jediný rozdíl je v tom, jak jsou testy pojmenovány.</span><span class="sxs-lookup"><span data-stu-id="0e117-169">The only difference is in how the tests are named.</span></span> <span data-ttu-id="0e117-170">V Razor aplikaci Pages jsou testy koncových bodů stránky obvykle pojmenovány za třídou modelu stránky (například `IndexPageTests` k otestování integrace komponent pro stránku indexu).</span><span class="sxs-lookup"><span data-stu-id="0e117-170">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="0e117-171">V aplikaci MVC jsou testy obvykle uspořádány podle tříd kontroleru a pojmenovány po kontrolách, které otestuje (například `HomeControllerTests` pro otestování integrace komponent pro domovský kontroler).</span><span class="sxs-lookup"><span data-stu-id="0e117-171">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="0e117-172">Požadavky na test aplikace</span><span class="sxs-lookup"><span data-stu-id="0e117-172">Test app prerequisites</span></span>

<span data-ttu-id="0e117-173">Testovací projekt musí:</span><span class="sxs-lookup"><span data-stu-id="0e117-173">The test project must:</span></span>

* <span data-ttu-id="0e117-174">Odkázat na balíček [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) .</span><span class="sxs-lookup"><span data-stu-id="0e117-174">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="0e117-175">Zadejte webovou sadu SDK v souboru projektu ( `<Project Sdk="Microsoft.NET.Sdk.Web">` ).</span><span class="sxs-lookup"><span data-stu-id="0e117-175">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="0e117-176">Tyto požadavky se dají zobrazit v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span><span class="sxs-lookup"><span data-stu-id="0e117-176">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="0e117-177">Zkontrolujte soubor *Tests/RazorPagesProject. Tests/RazorPagesProject. Tests. csproj* .</span><span class="sxs-lookup"><span data-stu-id="0e117-177">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="0e117-178">Ukázková aplikace používá [xUnit](https://xunit.github.io/) test Framework a knihovnu analyzátoru [AngleSharp](https://anglesharp.github.io/) , takže ukázková aplikace také odkazuje na:</span><span class="sxs-lookup"><span data-stu-id="0e117-178">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="0e117-179">xUnit</span><span class="sxs-lookup"><span data-stu-id="0e117-179">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="0e117-180">xUnit. Runner. VisualStudio</span><span class="sxs-lookup"><span data-stu-id="0e117-180">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="0e117-181">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="0e117-181">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="0e117-182">Entity Framework Core se používá také v testech.</span><span class="sxs-lookup"><span data-stu-id="0e117-182">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="0e117-183">Odkazy na aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0e117-183">The app references:</span></span>

* [<span data-ttu-id="0e117-184">Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="0e117-184">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* <span data-ttu-id="0e117-185">[Microsoft. AspNetCore. Identity . EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span><span class="sxs-lookup"><span data-stu-id="0e117-185">[Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span></span>
* [<span data-ttu-id="0e117-186">Microsoft. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="0e117-186">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="0e117-187">Microsoft. EntityFrameworkCore. inMemory</span><span class="sxs-lookup"><span data-stu-id="0e117-187">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="0e117-188">Microsoft. EntityFrameworkCore. Tools</span><span class="sxs-lookup"><span data-stu-id="0e117-188">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="0e117-189">Prostředí SUT</span><span class="sxs-lookup"><span data-stu-id="0e117-189">SUT environment</span></span>

<span data-ttu-id="0e117-190">Pokud není [prostředí](xref:fundamentals/environments) SUT nastaveno, prostředí se standardně vyvíjí.</span><span class="sxs-lookup"><span data-stu-id="0e117-190">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="0e117-191">Základní testy s výchozím WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="0e117-191">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="0e117-192">[WebApplicationFactory \<TEntryPoint> ](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) slouží k vytvoření [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) pro integrační testy.</span><span class="sxs-lookup"><span data-stu-id="0e117-192">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="0e117-193">`TEntryPoint`je třída vstupního bodu třídy SUT, obvykle `Startup` Třída.</span><span class="sxs-lookup"><span data-stu-id="0e117-193">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="0e117-194">Třídy testu implementují rozhraní[IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)( *Class* ) k označení třídy obsahuje testy a poskytování instancí sdíleného objektu napříč testy ve třídě.</span><span class="sxs-lookup"><span data-stu-id="0e117-194">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="0e117-195">Následující testovací třída, `BasicTests` používá `WebApplicationFactory` k zavedení SUT a poskytuje [HttpClient](/dotnet/api/system.net.http.httpclient) testovací metodě, `Get_EndpointsReturnSuccessAndCorrectContentType` .</span><span class="sxs-lookup"><span data-stu-id="0e117-195">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="0e117-196">Metoda zkontroluje, jestli je kód stavu odpovědi úspěšný (stavové kódy v rozsahu 200-299), a `Content-Type` záhlaví je `text/html; charset=utf-8` pro několik stránek aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e117-196">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="0e117-197">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) vytvoří instanci `HttpClient` , která automaticky následuje přesměrování a zpracovává soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="0e117-197">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="0e117-198">Ve výchozím nastavení se soubory cookie, které nejsou nezbytné, nezachovají mezi požadavky, pokud je povolená [zásada pro vyjádření souhlasu GDPR](xref:security/gdpr) .</span><span class="sxs-lookup"><span data-stu-id="0e117-198">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="0e117-199">Chcete-li zachovat soubory cookie, které nejsou nezbytné, například ty, které používá poskytovatel TempData, označte je jako nezbytné v testech.</span><span class="sxs-lookup"><span data-stu-id="0e117-199">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="0e117-200">Pokyny k označení souboru cookie jako nezbytného najdete v tématu [základní soubory cookie](xref:security/gdpr#essential-cookies).</span><span class="sxs-lookup"><span data-stu-id="0e117-200">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="0e117-201">Přizpůsobení WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="0e117-201">Customize WebApplicationFactory</span></span>

<span data-ttu-id="0e117-202">Konfiguraci webového hostitele lze vytvořit nezávisle na testovacích třídách děděním z `WebApplicationFactory` k vytvoření jednoho nebo více vlastních továren:</span><span class="sxs-lookup"><span data-stu-id="0e117-202">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="0e117-203">Dědit z `WebApplicationFactory` a přepsat [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span><span class="sxs-lookup"><span data-stu-id="0e117-203">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="0e117-204">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) umožňuje konfiguraci kolekce služeb s [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span><span class="sxs-lookup"><span data-stu-id="0e117-204">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="0e117-205">Vytvoření podsady databází v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) provádí `InitializeDbForTests` metoda.</span><span class="sxs-lookup"><span data-stu-id="0e117-205">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="0e117-206">Tato metoda je popsána v části [Ukázka integračních testů: organizace testovacích aplikací](#test-app-organization) .</span><span class="sxs-lookup"><span data-stu-id="0e117-206">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

   <span data-ttu-id="0e117-207">Kontext databáze SUT je zaregistrován ve své `Startup.ConfigureServices` metodě.</span><span class="sxs-lookup"><span data-stu-id="0e117-207">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="0e117-208">Zpětné volání testovací aplikace `builder.ConfigureServices` se provede *po* `Startup.ConfigureServices` spuštění kódu aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e117-208">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="0e117-209">Pořadí spouštění je zásadní změnou pro [obecného hostitele](xref:fundamentals/host/generic-host) s vydáním ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="0e117-209">The execution order is a breaking change for the [Generic Host](xref:fundamentals/host/generic-host) with the release of ASP.NET Core 3.0.</span></span> <span data-ttu-id="0e117-210">Pro použití jiné databáze pro testy, než je databáze aplikace, musí být kontext databáze aplikace nahrazen v `builder.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0e117-210">To use a different database for the tests than the app's database, the app's database context must be replaced in `builder.ConfigureServices`.</span></span>

   <span data-ttu-id="0e117-211">Pro SUTs, který stále používá [webový hostitel](xref:fundamentals/host/web-host), `builder.ConfigureServices` je zpětné volání testovací aplikace spuštěno *před* `Startup.ConfigureServices` kódem SUT.</span><span class="sxs-lookup"><span data-stu-id="0e117-211">For SUTs that still use the [Web Host](xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="0e117-212">Zpětné volání testovací aplikace `builder.ConfigureTestServices` je provedeno *po*.</span><span class="sxs-lookup"><span data-stu-id="0e117-212">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

   <span data-ttu-id="0e117-213">Ukázková aplikace vyhledá popisovač služby pro kontext databáze a použije popisovač k odebrání registrace služby.</span><span class="sxs-lookup"><span data-stu-id="0e117-213">The sample app finds the service descriptor for the database context and uses the descriptor to remove the service registration.</span></span> <span data-ttu-id="0e117-214">V dalším kroku továrna přidá novou `ApplicationDbContext` , která pro testy používá databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="0e117-214">Next, the factory adds a new `ApplicationDbContext` that uses an in-memory database for the tests.</span></span>

   <span data-ttu-id="0e117-215">Chcete-li se připojit k jiné databázi než databáze v paměti, změňte `UseInMemoryDatabase` volání pro připojení kontextu k jiné databázi.</span><span class="sxs-lookup"><span data-stu-id="0e117-215">To connect to a different database than the in-memory database, change the `UseInMemoryDatabase` call to connect the context to a different database.</span></span> <span data-ttu-id="0e117-216">Použití testovací databáze SQL Server:</span><span class="sxs-lookup"><span data-stu-id="0e117-216">To use a SQL Server test database:</span></span>

   * <span data-ttu-id="0e117-217">V souboru projektu se odkázat na balíček NuGet [Microsoft. EntityFrameworkCore. SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) .</span><span class="sxs-lookup"><span data-stu-id="0e117-217">Reference the [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet package in the project file.</span></span>
   * <span data-ttu-id="0e117-218">Volání `UseSqlServer` s připojovacím řetězcem k databázi.</span><span class="sxs-lookup"><span data-stu-id="0e117-218">Call `UseSqlServer` with a connection string to the database.</span></span>

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. <span data-ttu-id="0e117-219">Použijte vlastní `CustomWebApplicationFactory` v testovacích třídách.</span><span class="sxs-lookup"><span data-stu-id="0e117-219">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="0e117-220">Následující příklad používá objekt factory ve `IndexPageTests` třídě:</span><span class="sxs-lookup"><span data-stu-id="0e117-220">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="0e117-221">Klient ukázkové aplikace je nakonfigurován tak, aby zabránil `HttpClient` následujícímu přesměrování.</span><span class="sxs-lookup"><span data-stu-id="0e117-221">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="0e117-222">Jak je vysvětleno dále v části [model ověřování](#mock-authentication) , umožňuje testům kontrolovat výsledek první odpovědi aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e117-222">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="0e117-223">První odpověď je přesměrování v mnoha těchto testech s `Location` hlavičkou.</span><span class="sxs-lookup"><span data-stu-id="0e117-223">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="0e117-224">Typický test používá `HttpClient` pomocné metody a ke zpracování žádosti a odpovědi:</span><span class="sxs-lookup"><span data-stu-id="0e117-224">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="0e117-225">Jakýkoli požadavek POST na SUT musí splňovat kontrolu proti padělání, kterou automaticky provádí [systém ochrany dat proti padělání dat](xref:security/data-protection/introduction)aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e117-225">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="0e117-226">Aby bylo možné uspořádat požadavek POST testu, musí aplikace testů:</span><span class="sxs-lookup"><span data-stu-id="0e117-226">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="0e117-227">Vytvořte požadavek na stránku.</span><span class="sxs-lookup"><span data-stu-id="0e117-227">Make a request for the page.</span></span>
1. <span data-ttu-id="0e117-228">Analyzovat soubor cookie pro antipadělání a žádat ověřovací token z odpovědi.</span><span class="sxs-lookup"><span data-stu-id="0e117-228">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="0e117-229">Proveďte požadavek POST se souborem cookie antipadělání a ověřovacím tokenem žádosti.</span><span class="sxs-lookup"><span data-stu-id="0e117-229">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="0e117-230">`SendAsync`Pomocné metody rozšíření (*helpers/HttpClientExtensions. cs*) a `GetDocumentAsync` pomocná metoda (*helps/HtmlHelpers. cs*) v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) používají analyzátor [AngleSharp](https://anglesharp.github.io/) pro zpracování kontroly proti falšování pomocí následujících metod:</span><span class="sxs-lookup"><span data-stu-id="0e117-230">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="0e117-231">`GetDocumentAsync`: Přijme [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) a vrátí `IHtmlDocument` .</span><span class="sxs-lookup"><span data-stu-id="0e117-231">`GetDocumentAsync`: Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="0e117-232">`GetDocumentAsync`používá objekt pro vytváření, který připraví *virtuální odpověď* na základě originálu `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="0e117-232">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="0e117-233">Další informace najdete v [dokumentaci k AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).</span><span class="sxs-lookup"><span data-stu-id="0e117-233">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="0e117-234">`SendAsync`metody rozšíření pro `HttpClient` vytvoření [zprávy HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) a volání [SendAsync (zprávy HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) pro odeslání požadavků do SUT.</span><span class="sxs-lookup"><span data-stu-id="0e117-234">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="0e117-235">Přetížení pro `SendAsync` přijměte formulář HTML ( `IHtmlFormElement` ) a následující:</span><span class="sxs-lookup"><span data-stu-id="0e117-235">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="0e117-236">Tlačítko Odeslat formuláře ( `IHtmlElement` )</span><span class="sxs-lookup"><span data-stu-id="0e117-236">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="0e117-237">Kolekce hodnot formulářů ( `IEnumerable<KeyValuePair<string, string>>` )</span><span class="sxs-lookup"><span data-stu-id="0e117-237">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="0e117-238">Odeslat tlačítko ( `IHtmlElement` ) a hodnoty formuláře ( `IEnumerable<KeyValuePair<string, string>>` )</span><span class="sxs-lookup"><span data-stu-id="0e117-238">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="0e117-239">[AngleSharp](https://anglesharp.github.io/) je knihovna analýzy třetí strany, která se používá pro demonstrační účely v tomto tématu a v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0e117-239">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="0e117-240">AngleSharp se nepodporuje nebo nevyžaduje pro testování integrace ASP.NET Corech aplikací.</span><span class="sxs-lookup"><span data-stu-id="0e117-240">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="0e117-241">Lze použít jiné analyzátory, jako je například [HTML flexibility Pack (HAP)](https://html-agility-pack.net/).</span><span class="sxs-lookup"><span data-stu-id="0e117-241">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="0e117-242">Další možností je napsat kód pro zpracování tokenu žádosti o ověření požadavku systému antipadělání a souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="0e117-242">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="0e117-243">Přizpůsobení klienta pomocí WithWebHostBuilder</span><span class="sxs-lookup"><span data-stu-id="0e117-243">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="0e117-244">Pokud je v rámci testovací metody vyžadována další konfigurace, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) vytvoří nový `WebApplicationFactory` s [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) , který je dále přizpůsoben konfigurací.</span><span class="sxs-lookup"><span data-stu-id="0e117-244">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="0e117-245">`Post_DeleteMessageHandler_ReturnsRedirectToRoot`Testovací metoda [ukázkové aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstruje použití `WithWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0e117-245">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="0e117-246">Tento test provede v databázi odstranění záznamu aktivací formuláře v SUT.</span><span class="sxs-lookup"><span data-stu-id="0e117-246">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="0e117-247">Vzhledem k tomu, že jiný test ve `IndexPageTests` třídě provádí operaci, která odstraní všechny záznamy v databázi a může běžet před `Post_DeleteMessageHandler_ReturnsRedirectToRoot` metodou, databáze je v této testovací metodě znovu osazena, aby se zajistilo, že SUT k odstranění bude přítomen záznam.</span><span class="sxs-lookup"><span data-stu-id="0e117-247">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="0e117-248">Výběr prvního tlačítka odstranit `messages` ve formuláři v SUT se simuluje v požadavku na SUT:</span><span class="sxs-lookup"><span data-stu-id="0e117-248">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="0e117-249">Možnosti klienta</span><span class="sxs-lookup"><span data-stu-id="0e117-249">Client options</span></span>

<span data-ttu-id="0e117-250">Následující tabulka ukazuje výchozí [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) , který je k dispozici při vytváření `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="0e117-250">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="0e117-251">Možnost</span><span class="sxs-lookup"><span data-stu-id="0e117-251">Option</span></span> | <span data-ttu-id="0e117-252">Description</span><span class="sxs-lookup"><span data-stu-id="0e117-252">Description</span></span> | <span data-ttu-id="0e117-253">Výchozí</span><span class="sxs-lookup"><span data-stu-id="0e117-253">Default</span></span> |
| ---
<span data-ttu-id="0e117-254">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-255">'Blazor'</span></span>
- <span data-ttu-id="0e117-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-256">'Identity'</span></span>
- <span data-ttu-id="0e117-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-258">'Razor'</span></span>
- <span data-ttu-id="0e117-259">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-259">'SignalR' uid:</span></span> 

<span data-ttu-id="0e117-260">--- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-260">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-261">'Blazor'</span></span>
- <span data-ttu-id="0e117-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-262">'Identity'</span></span>
- <span data-ttu-id="0e117-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-264">'Razor'</span></span>
- <span data-ttu-id="0e117-265">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-266">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-267">'Blazor'</span></span>
- <span data-ttu-id="0e117-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-268">'Identity'</span></span>
- <span data-ttu-id="0e117-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-270">'Razor'</span></span>
- <span data-ttu-id="0e117-271">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-272">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-273">'Blazor'</span></span>
- <span data-ttu-id="0e117-274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-274">'Identity'</span></span>
- <span data-ttu-id="0e117-275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-275">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-276">'Razor'</span></span>
- <span data-ttu-id="0e117-277">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-277">'SignalR' uid:</span></span> 

<span data-ttu-id="0e117-278">------ | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-278">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-279">'Blazor'</span></span>
- <span data-ttu-id="0e117-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-280">'Identity'</span></span>
- <span data-ttu-id="0e117-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-282">'Razor'</span></span>
- <span data-ttu-id="0e117-283">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-283">'SignalR' uid:</span></span> 

<span data-ttu-id="0e117-284">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Získá nebo nastaví, jestli se `HttpClient` mají instance automaticky sledovat prostřednictvím odpovědí přesměrování.</span><span class="sxs-lookup"><span data-stu-id="0e117-284">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span><span data-ttu-id="0e117-285"> | `true`| | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Získá nebo nastaví základní adresu `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="0e117-285"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Gets or sets the base address of `HttpClient` instances.</span></span><span data-ttu-id="0e117-286"> | `http://localhost`| | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Získá nebo nastaví, jestli `HttpClient` by instance měly zpracovávat soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="0e117-286"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Gets or sets whether `HttpClient` instances should handle cookies.</span></span><span data-ttu-id="0e117-287"> | `true`| | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Získá nebo nastaví maximální počet odpovědí přesměrování, které `HttpClient` by měly instance následovat.</span><span class="sxs-lookup"><span data-stu-id="0e117-287"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> <span data-ttu-id="0e117-288">| 7 |</span><span class="sxs-lookup"><span data-stu-id="0e117-288">| 7 |</span></span>

<span data-ttu-id="0e117-289">Vytvořte `WebApplicationFactoryClientOptions` třídu a předejte ji metodě [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (výchozí hodnoty jsou uvedeny v příkladu kódu):</span><span class="sxs-lookup"><span data-stu-id="0e117-289">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="0e117-290">Vložení makety služeb</span><span class="sxs-lookup"><span data-stu-id="0e117-290">Inject mock services</span></span>

<span data-ttu-id="0e117-291">Služby lze přepsat v rámci testu voláním [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) v Tvůrci hostitele.</span><span class="sxs-lookup"><span data-stu-id="0e117-291">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="0e117-292">**Pro vložení makety služeb musí mít SUT `Startup` třídu s `Startup.ConfigureServices` metodou.**</span><span class="sxs-lookup"><span data-stu-id="0e117-292">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="0e117-293">Vzorový SUT obsahuje oborovou službu, která vrací citát.</span><span class="sxs-lookup"><span data-stu-id="0e117-293">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="0e117-294">Nabídka je vložena do skrytého pole na stránce index, když je požadována stránka indexu.</span><span class="sxs-lookup"><span data-stu-id="0e117-294">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="0e117-295">*Services/IQuoteService. cs*:</span><span class="sxs-lookup"><span data-stu-id="0e117-295">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="0e117-296">*Services/QuoteService. cs*:</span><span class="sxs-lookup"><span data-stu-id="0e117-296">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="0e117-297">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="0e117-297">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="0e117-298">*Pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="0e117-298">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="0e117-299">*Pages/index. cs*:</span><span class="sxs-lookup"><span data-stu-id="0e117-299">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="0e117-300">Při spuštění aplikace SUT se vygeneruje následující kód:</span><span class="sxs-lookup"><span data-stu-id="0e117-300">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="0e117-301">Chcete-li otestovat vkládání služby a uvozovek v rámci integračního testu, je do SUTu vložena přípravou služba.</span><span class="sxs-lookup"><span data-stu-id="0e117-301">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="0e117-302">Napodobná služba nahradí aplikaci `QuoteService` službou poskytnutou testovací aplikací, která se nazývá `TestQuoteService` :</span><span class="sxs-lookup"><span data-stu-id="0e117-302">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="0e117-303">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="0e117-303">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="0e117-304">`ConfigureTestServices`je volána a zaregistrována Oborová služba:</span><span class="sxs-lookup"><span data-stu-id="0e117-304">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="0e117-305">Označení vyprodukované během provádění testu odráží text citace, který je dodaný pomocí `TestQuoteService` , takže kontrolní výraz projde:</span><span class="sxs-lookup"><span data-stu-id="0e117-305">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="0e117-306">Modely ověřování</span><span class="sxs-lookup"><span data-stu-id="0e117-306">Mock authentication</span></span>

<span data-ttu-id="0e117-307">Testy ve `AuthTests` třídě zkontrolují zabezpečený koncový bod:</span><span class="sxs-lookup"><span data-stu-id="0e117-307">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="0e117-308">Přesměruje neověřeného uživatele na přihlašovací stránku aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e117-308">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="0e117-309">Vrátí obsah pro ověřeného uživatele.</span><span class="sxs-lookup"><span data-stu-id="0e117-309">Returns content for an authenticated user.</span></span>

<span data-ttu-id="0e117-310">V SUT `/SecurePage` stránka používá konvenci [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) k použití [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) na stránku.</span><span class="sxs-lookup"><span data-stu-id="0e117-310">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="0e117-311">Další informace najdete v tématu [ Razor autorizační konvence pro stránky](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span><span class="sxs-lookup"><span data-stu-id="0e117-311">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="0e117-312">V `Get_SecurePageRedirectsAnUnauthenticatedUser` testu je [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) nastaveno na zakázat přesměrování nastavením [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) na `false` :</span><span class="sxs-lookup"><span data-stu-id="0e117-312">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="0e117-313">Když klientovi zakážete postup přesměrování, můžete provést následující kontroly:</span><span class="sxs-lookup"><span data-stu-id="0e117-313">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="0e117-314">Stavový kód vrácený SUT může být zkontrolován na základě očekávaného výsledku [HttpStatusCode. Redirect](/dotnet/api/system.net.httpstatuscode) , nikoli konečného stavového kódu po přesměrování na přihlašovací stránku, která by byla [HttpStatusCode. ok](/dotnet/api/system.net.httpstatuscode).</span><span class="sxs-lookup"><span data-stu-id="0e117-314">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="0e117-315">`Location`Hodnota hlavičky v hlavičkách odpovědi je zaškrtnuta, aby se ověřilo, že začíná `http://localhost/Identity/Account/Login` , a ne konečná odpověď přihlašovací stránky, kde by tato hlavička nebyla k `Location` dispozici.</span><span class="sxs-lookup"><span data-stu-id="0e117-315">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="0e117-316">Testovací aplikace může <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> napodobovat v [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) , aby mohla testovat aspekty ověřování a autorizace.</span><span class="sxs-lookup"><span data-stu-id="0e117-316">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="0e117-317">Minimální scénář vrátí [AuthenticateResult. Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span><span class="sxs-lookup"><span data-stu-id="0e117-317">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="0e117-318">`TestAuthHandler`Je volána k ověření uživatele, když je schéma ověřování nastaveno na místo, `Test` kde `AddAuthentication` je zaregistrováno pro `ConfigureTestServices` :</span><span class="sxs-lookup"><span data-stu-id="0e117-318">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="0e117-319">Další informace o naleznete v `WebApplicationFactoryClientOptions` části [Možnosti klienta](#client-options) .</span><span class="sxs-lookup"><span data-stu-id="0e117-319">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="0e117-320">Nastavení prostředí</span><span class="sxs-lookup"><span data-stu-id="0e117-320">Set the environment</span></span>

<span data-ttu-id="0e117-321">Ve výchozím nastavení je hostitel a prostředí aplikace SUT nakonfigurovaná tak, aby používala vývojové prostředí.</span><span class="sxs-lookup"><span data-stu-id="0e117-321">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="0e117-322">Přepsání prostředí SUT při použití `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="0e117-322">To override the SUT's environment when using `IHostBuilder`:</span></span>

* <span data-ttu-id="0e117-323">Nastavte `ASPNETCORE_ENVIRONMENT` proměnnou prostředí (například, `Staging` `Production` nebo jinou vlastní hodnotu, například `Testing` ).</span><span class="sxs-lookup"><span data-stu-id="0e117-323">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="0e117-324">Přepsání `CreateHostBuilder` v testovací aplikaci pro čtení proměnných prostředí s předponou `ASPNETCORE` .</span><span class="sxs-lookup"><span data-stu-id="0e117-324">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() =>
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

<span data-ttu-id="0e117-325">Pokud SUT používá webového hostitele ( `IWebHostBuilder` ), přepište `CreateWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="0e117-325">If the SUT uses the Web Host (`IWebHostBuilder`), override `CreateWebHostBuilder`:</span></span>

```csharp
protected override IWebHostBuilder CreateWebHostBuilder() =>
    base.CreateWebHostBuilder().UseEnvironment("Testing");
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="0e117-326">Jak testovací infrastruktura odvodí kořenovou cestu obsahu aplikace</span><span class="sxs-lookup"><span data-stu-id="0e117-326">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="0e117-327">`WebApplicationFactory`Konstruktor odvodí [kořenovou cestu obsahu](xref:fundamentals/index#content-root) aplikace hledáním [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) na sestavení obsahujícího testy integrace s klíčem, který se rovná `TEntryPoint` sestavení `System.Reflection.Assembly.FullName` .</span><span class="sxs-lookup"><span data-stu-id="0e117-327">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="0e117-328">V případě, že atribut se správným klíčem nebyl nalezen, `WebApplicationFactory` vrátí se zpět k hledání souboru řešení (*. sln*) a připojí `TEntryPoint` název sestavení k adresáři řešení.</span><span class="sxs-lookup"><span data-stu-id="0e117-328">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="0e117-329">Kořenový adresář aplikace (kořenová cesta obsahu) se používá ke zjišťování zobrazení a souborů obsahu.</span><span class="sxs-lookup"><span data-stu-id="0e117-329">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="0e117-330">Zakázat stínové kopírování</span><span class="sxs-lookup"><span data-stu-id="0e117-330">Disable shadow copying</span></span>

<span data-ttu-id="0e117-331">Stínové kopírování způsobí, že se testy spustí v jiném adresáři než výstupní adresář.</span><span class="sxs-lookup"><span data-stu-id="0e117-331">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="0e117-332">Aby testy správně fungovaly, musí být stínové kopírování zakázané.</span><span class="sxs-lookup"><span data-stu-id="0e117-332">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="0e117-333">[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) používá xUnit a zakáže stínové kopírování pro xUnit zahrnutím souboru *xUnit. Runner. JSON* se správným nastavením konfigurace.</span><span class="sxs-lookup"><span data-stu-id="0e117-333">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="0e117-334">Další informace najdete v tématu [Konfigurace xUnit pomocí formátu JSON](https://xunit.github.io/docs/configuring-with-json.html).</span><span class="sxs-lookup"><span data-stu-id="0e117-334">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="0e117-335">Do kořenového adresáře testovacího projektu přidejte soubor *xUnit. Runner. JSON* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="0e117-335">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="0e117-336">Vyřazení objektů</span><span class="sxs-lookup"><span data-stu-id="0e117-336">Disposal of objects</span></span>

<span data-ttu-id="0e117-337">Po provedení testů pro `IClassFixture` implementaci jsou [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) a [HttpClient](/dotnet/api/system.net.http.httpclient) uvolněny, když xUnit vyřadí [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span><span class="sxs-lookup"><span data-stu-id="0e117-337">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="0e117-338">Pokud objekty, které vytvořil vývojář, vyžadují likvidaci, jejich uvolnění v `IClassFixture` implementaci.</span><span class="sxs-lookup"><span data-stu-id="0e117-338">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="0e117-339">Další informace naleznete v tématu [implementace metody Dispose](/dotnet/standard/garbage-collection/implementing-dispose).</span><span class="sxs-lookup"><span data-stu-id="0e117-339">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="0e117-340">Ukázka integračních testů</span><span class="sxs-lookup"><span data-stu-id="0e117-340">Integration tests sample</span></span>

<span data-ttu-id="0e117-341">[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) se skládá ze dvou aplikací:</span><span class="sxs-lookup"><span data-stu-id="0e117-341">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="0e117-342">Aplikace</span><span class="sxs-lookup"><span data-stu-id="0e117-342">App</span></span> | <span data-ttu-id="0e117-343">Adresář projektu</span><span class="sxs-lookup"><span data-stu-id="0e117-343">Project directory</span></span> | <span data-ttu-id="0e117-344">Description</span><span class="sxs-lookup"><span data-stu-id="0e117-344">Description</span></span> |
| --- | ---
<span data-ttu-id="0e117-345">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-346">'Blazor'</span></span>
- <span data-ttu-id="0e117-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-347">'Identity'</span></span>
- <span data-ttu-id="0e117-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-349">'Razor'</span></span>
- <span data-ttu-id="0e117-350">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-351">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-352">'Blazor'</span></span>
- <span data-ttu-id="0e117-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-353">'Identity'</span></span>
- <span data-ttu-id="0e117-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-355">'Razor'</span></span>
- <span data-ttu-id="0e117-356">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-357">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-358">'Blazor'</span></span>
- <span data-ttu-id="0e117-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-359">'Identity'</span></span>
- <span data-ttu-id="0e117-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-361">'Razor'</span></span>
- <span data-ttu-id="0e117-362">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-363">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-364">'Blazor'</span></span>
- <span data-ttu-id="0e117-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-365">'Identity'</span></span>
- <span data-ttu-id="0e117-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-367">'Razor'</span></span>
- <span data-ttu-id="0e117-368">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-369">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-370">'Blazor'</span></span>
- <span data-ttu-id="0e117-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-371">'Identity'</span></span>
- <span data-ttu-id="0e117-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-373">'Razor'</span></span>
- <span data-ttu-id="0e117-374">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-374">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-375">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-376">'Blazor'</span></span>
- <span data-ttu-id="0e117-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-377">'Identity'</span></span>
- <span data-ttu-id="0e117-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-379">'Razor'</span></span>
- <span data-ttu-id="0e117-380">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-380">'SignalR' uid:</span></span> 

<span data-ttu-id="0e117-381">--------- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-381">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-382">'Blazor'</span></span>
- <span data-ttu-id="0e117-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-383">'Identity'</span></span>
- <span data-ttu-id="0e117-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-385">'Razor'</span></span>
- <span data-ttu-id="0e117-386">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-387">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-388">'Blazor'</span></span>
- <span data-ttu-id="0e117-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-389">'Identity'</span></span>
- <span data-ttu-id="0e117-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-391">'Razor'</span></span>
- <span data-ttu-id="0e117-392">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-393">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-394">'Blazor'</span></span>
- <span data-ttu-id="0e117-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-395">'Identity'</span></span>
- <span data-ttu-id="0e117-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-397">'Razor'</span></span>
- <span data-ttu-id="0e117-398">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-398">'SignalR' uid:</span></span> 

<span data-ttu-id="0e117-399">------ | | Aplikace zprávy (SUT) | *Src/RazorPagesProject* | Povoluje uživateli přidávat, odstraňovat a analyzovat zprávy a analyzovat je.</span><span class="sxs-lookup"><span data-stu-id="0e117-399">------ | | Message app (the SUT) | *src/RazorPagesProject* | Allows a user to add, delete one, delete all, and analyze messages.</span></span> <span data-ttu-id="0e117-400">| | Testovací aplikace | *testuje/RazorPagesProject. Tests* | Slouží k integraci testu SUT.</span><span class="sxs-lookup"><span data-stu-id="0e117-400">| | Test app | *tests/RazorPagesProject.Tests* | Used to integration test the SUT.</span></span> |

<span data-ttu-id="0e117-401">Testy lze spustit pomocí vestavěných funkcí testu integrovaného vývojového prostředí (IDE), jako je například [Visual Studio](https://visualstudio.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="0e117-401">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="0e117-402">Pokud používáte [Visual Studio Code](https://code.visualstudio.com/) nebo příkazový řádek, spusťte následující příkaz v příkazovém řádku v adresáři *Tests/RazorPagesProject. Tests* :</span><span class="sxs-lookup"><span data-stu-id="0e117-402">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="0e117-403">Organizace pro aplikace zprávy (SUT)</span><span class="sxs-lookup"><span data-stu-id="0e117-403">Message app (SUT) organization</span></span>

<span data-ttu-id="0e117-404">SUT je Razor systém zpráv s následujícími charakteristikami:</span><span class="sxs-lookup"><span data-stu-id="0e117-404">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="0e117-405">Stránka indexu aplikace (*stránky/index. cshtml* a *pages/index. cshtml. cs*) poskytuje metody uživatelského rozhraní a modelu stránky pro řízení přidávání, odstraňování a analýzy zpráv (průměrná slova na zprávu).</span><span class="sxs-lookup"><span data-stu-id="0e117-405">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="0e117-406">Zpráva je popsána `Message` třídou (*data/Message. cs*) se dvěma vlastnostmi: `Id` (klíč) a `Text` (zpráva).</span><span class="sxs-lookup"><span data-stu-id="0e117-406">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="0e117-407">`Text`Vlastnost je povinná a omezená na 200 znaků.</span><span class="sxs-lookup"><span data-stu-id="0e117-407">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="0e117-408">Zprávy se ukládají pomocí&#8224; [databáze Entity Framework v paměti](/ef/core/providers/in-memory/) .</span><span class="sxs-lookup"><span data-stu-id="0e117-408">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="0e117-409">Aplikace obsahuje vrstvu pro přístup k datům (DAL) ve třídě kontextu databáze `AppDbContext` (*data/AppDbContext. cs*).</span><span class="sxs-lookup"><span data-stu-id="0e117-409">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="0e117-410">Pokud je databáze při spuštění aplikace prázdná, úložiště zpráv se inicializuje se třemi zprávami.</span><span class="sxs-lookup"><span data-stu-id="0e117-410">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="0e117-411">Aplikace obsahuje, k `/SecurePage` němuž může mít oprávnění pouze ověřený uživatel.</span><span class="sxs-lookup"><span data-stu-id="0e117-411">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="0e117-412">&#8224;tématu EF, [test s pamětí](/ef/core/miscellaneous/testing/in-memory), vysvětluje, jak používat databázi v paměti pro testy pomocí MSTest.</span><span class="sxs-lookup"><span data-stu-id="0e117-412">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="0e117-413">Toto téma používá testovací rozhraní [xUnit](https://xunit.github.io/) .</span><span class="sxs-lookup"><span data-stu-id="0e117-413">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="0e117-414">Koncepty testů a testovací implementace v různých testovacích architekturách jsou podobné, ale nejsou totožné.</span><span class="sxs-lookup"><span data-stu-id="0e117-414">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="0e117-415">I když aplikace nepoužívá vzor úložiště a není efektivním příkladem [vzoru jednotky práce (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor stránky tyto vzory vývoje podporují.</span><span class="sxs-lookup"><span data-stu-id="0e117-415">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="0e117-416">Další informace najdete v tématu [navrhování vrstvy trvalosti infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) a [testovacího kontroleru testů](/aspnet/core/mvc/controllers/testing) (ukázka implementuje vzor úložiště).</span><span class="sxs-lookup"><span data-stu-id="0e117-416">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="0e117-417">Organizace testovací aplikace</span><span class="sxs-lookup"><span data-stu-id="0e117-417">Test app organization</span></span>

<span data-ttu-id="0e117-418">Testovací aplikace je Konzolová aplikace v adresáři *Tests/RazorPagesProject. Tests* .</span><span class="sxs-lookup"><span data-stu-id="0e117-418">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="0e117-419">Testovací adresář aplikace</span><span class="sxs-lookup"><span data-stu-id="0e117-419">Test app directory</span></span> | <span data-ttu-id="0e117-420">Description</span><span class="sxs-lookup"><span data-stu-id="0e117-420">Description</span></span> |
| ---
<span data-ttu-id="0e117-421">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-422">'Blazor'</span></span>
- <span data-ttu-id="0e117-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-423">'Identity'</span></span>
- <span data-ttu-id="0e117-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-425">'Razor'</span></span>
- <span data-ttu-id="0e117-426">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-427">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-428">'Blazor'</span></span>
- <span data-ttu-id="0e117-429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-429">'Identity'</span></span>
- <span data-ttu-id="0e117-430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-430">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-431">'Razor'</span></span>
- <span data-ttu-id="0e117-432">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-433">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-434">'Blazor'</span></span>
- <span data-ttu-id="0e117-435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-435">'Identity'</span></span>
- <span data-ttu-id="0e117-436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-436">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-437">'Razor'</span></span>
- <span data-ttu-id="0e117-438">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-439">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-440">'Blazor'</span></span>
- <span data-ttu-id="0e117-441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-441">'Identity'</span></span>
- <span data-ttu-id="0e117-442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-442">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-443">'Razor'</span></span>
- <span data-ttu-id="0e117-444">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-444">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-445">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-446">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-446">'Blazor'</span></span>
- <span data-ttu-id="0e117-447">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-447">'Identity'</span></span>
- <span data-ttu-id="0e117-448">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-448">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-449">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-449">'Razor'</span></span>
- <span data-ttu-id="0e117-450">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-450">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-451">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-452">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-452">'Blazor'</span></span>
- <span data-ttu-id="0e117-453">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-453">'Identity'</span></span>
- <span data-ttu-id="0e117-454">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-454">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-455">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-455">'Razor'</span></span>
- <span data-ttu-id="0e117-456">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-456">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-457">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-458">'Blazor'</span></span>
- <span data-ttu-id="0e117-459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-459">'Identity'</span></span>
- <span data-ttu-id="0e117-460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-460">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-461">'Razor'</span></span>
- <span data-ttu-id="0e117-462">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-462">'SignalR' uid:</span></span> 

<span data-ttu-id="0e117-463">--------- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-463">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-464">'Blazor'</span></span>
- <span data-ttu-id="0e117-465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-465">'Identity'</span></span>
- <span data-ttu-id="0e117-466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-466">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-467">'Razor'</span></span>
- <span data-ttu-id="0e117-468">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-469">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-470">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-470">'Blazor'</span></span>
- <span data-ttu-id="0e117-471">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-471">'Identity'</span></span>
- <span data-ttu-id="0e117-472">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-472">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-473">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-473">'Razor'</span></span>
- <span data-ttu-id="0e117-474">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-474">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-475">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-476">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-476">'Blazor'</span></span>
- <span data-ttu-id="0e117-477">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-477">'Identity'</span></span>
- <span data-ttu-id="0e117-478">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-478">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-479">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-479">'Razor'</span></span>
- <span data-ttu-id="0e117-480">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-480">'SignalR' uid:</span></span> 

<span data-ttu-id="0e117-481">------ | | *AuthTests* | Obsahuje testovací metody pro:</span><span class="sxs-lookup"><span data-stu-id="0e117-481">------ | | *AuthTests* | Contains test methods for:</span></span><ul><li><span data-ttu-id="0e117-482">Přístup k zabezpečené stránce neověřenému uživateli</span><span class="sxs-lookup"><span data-stu-id="0e117-482">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="0e117-483">Přístup k zabezpečené stránce ověřeným uživatelem s přípravou <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .</span><span class="sxs-lookup"><span data-stu-id="0e117-483">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="0e117-484">Získání profilu uživatele GitHub a kontrola přihlášení uživatele profilu.</span><span class="sxs-lookup"><span data-stu-id="0e117-484">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> <span data-ttu-id="0e117-485">| | *BasicTests* | Obsahuje testovací metodu pro typ směrování a obsahu.</span><span class="sxs-lookup"><span data-stu-id="0e117-485">| | *BasicTests* | Contains a test method for routing and content type.</span></span> <span data-ttu-id="0e117-486">| | *IntegrationTests* | Obsahuje testy integrace pro stránku indexu pomocí vlastní `WebApplicationFactory` třídy.</span><span class="sxs-lookup"><span data-stu-id="0e117-486">| | *IntegrationTests* | Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> <span data-ttu-id="0e117-487">| | *Pomocné programy a pomůcky* | </span><span class="sxs-lookup"><span data-stu-id="0e117-487">| | *Helpers/Utilities* | </span></span><ul><li><span data-ttu-id="0e117-488">*Utilities.cs* obsahuje `InitializeDbForTests` metodu použitou k osazení databáze testovacími daty.</span><span class="sxs-lookup"><span data-stu-id="0e117-488">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="0e117-489">*HtmlHelpers.cs* poskytuje metodu, která vrátí AngleSharp `IHtmlDocument` pro použití testovacími metodami.</span><span class="sxs-lookup"><span data-stu-id="0e117-489">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="0e117-490">*HttpClientExtensions.cs* poskytují přetížení pro `SendAsync` odeslání požadavků do SUT.</span><span class="sxs-lookup"><span data-stu-id="0e117-490">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="0e117-491">Testovací rozhraní je [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="0e117-491">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="0e117-492">Integrační testy se provádějí pomocí [Microsoft. AspNetCore. TestHost](/dotnet/api/microsoft.aspnetcore.testhost), který zahrnuje [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span><span class="sxs-lookup"><span data-stu-id="0e117-492">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="0e117-493">Vzhledem k tomu, že se balíček [Microsoft. AspNetCore. Mvc. test](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) používá ke konfiguraci testovacího hostitele a testovacího serveru, `TestHost` `TestServer` balíčky a nevyžadují přímé odkazy na balíčky v souboru projektu testovací aplikace nebo v konfiguraci vývojáře v testovací aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0e117-493">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="0e117-494">**Dosazení databáze pro testování**</span><span class="sxs-lookup"><span data-stu-id="0e117-494">**Seeding the database for testing**</span></span>

<span data-ttu-id="0e117-495">Integrační testy obvykle vyžadují malou datovou sadu v databázi před spuštěním testu.</span><span class="sxs-lookup"><span data-stu-id="0e117-495">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="0e117-496">Například odstraňovací test volá pro odstranění záznamu databáze, takže databáze musí mít alespoň jeden záznam, aby žádost o odstranění proběhla úspěšně.</span><span class="sxs-lookup"><span data-stu-id="0e117-496">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="0e117-497">Ukázková aplikace se dosazuje do databáze se třemi zprávami v *Utilities.cs* , které testy můžou použít, když se spustí:</span><span class="sxs-lookup"><span data-stu-id="0e117-497">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

<span data-ttu-id="0e117-498">Kontext databáze SUT je zaregistrován ve své `Startup.ConfigureServices` metodě.</span><span class="sxs-lookup"><span data-stu-id="0e117-498">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="0e117-499">Zpětné volání testovací aplikace `builder.ConfigureServices` se provede *po* `Startup.ConfigureServices` spuštění kódu aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e117-499">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="0e117-500">Chcete-li pro testy použít jinou databázi, musí být kontext databáze aplikace nahrazen v `builder.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0e117-500">To use a different database for the tests, the app's database context must be replaced in `builder.ConfigureServices`.</span></span> <span data-ttu-id="0e117-501">Další informace najdete v části [přizpůsobení WebApplicationFactory](#customize-webapplicationfactory) .</span><span class="sxs-lookup"><span data-stu-id="0e117-501">For more information, see the [Customize WebApplicationFactory](#customize-webapplicationfactory) section.</span></span>

<span data-ttu-id="0e117-502">Pro SUTs, který stále používá [webový hostitel](xref:fundamentals/host/web-host), `builder.ConfigureServices` je zpětné volání testovací aplikace spuštěno *před* `Startup.ConfigureServices` kódem SUT.</span><span class="sxs-lookup"><span data-stu-id="0e117-502">For SUTs that still use the [Web Host](xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="0e117-503">Zpětné volání testovací aplikace `builder.ConfigureTestServices` je provedeno *po*.</span><span class="sxs-lookup"><span data-stu-id="0e117-503">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0e117-504">Testy integrace zajistí správné fungování komponent aplikace na úrovni, která zahrnuje podpůrnou infrastrukturu aplikace, jako je třeba databáze, systém souborů a síť.</span><span class="sxs-lookup"><span data-stu-id="0e117-504">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="0e117-505">ASP.NET Core podporuje testy integrace pomocí architektury testování částí s testovacím webovým hostitelem a testovacím serverem v paměti.</span><span class="sxs-lookup"><span data-stu-id="0e117-505">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="0e117-506">Toto téma předpokládá základní znalost testů jednotek.</span><span class="sxs-lookup"><span data-stu-id="0e117-506">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="0e117-507">Pokud neznáte koncepty testování, přečtěte si téma [testování částí v .NET Core a .NET Standard](/dotnet/core/testing/) a jeho propojený obsah.</span><span class="sxs-lookup"><span data-stu-id="0e117-507">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="0e117-508">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0e117-508">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0e117-509">Ukázková aplikace je Razor aplikace stránky a předpokládá základní znalost Razor stránek.</span><span class="sxs-lookup"><span data-stu-id="0e117-509">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="0e117-510">Pokud neznáte Razor stránky, přečtěte si následující témata:</span><span class="sxs-lookup"><span data-stu-id="0e117-510">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="0e117-511">[Úvod ke Razor stránkám](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="0e117-511">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="0e117-512">[Začínáme se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="0e117-512">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="0e117-513">[RazorTestování částí stránek](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="0e117-513">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="0e117-514">Pro testování jednostránkové doporučujeme nástroj, jako je například [selen](https://www.seleniumhq.org/), který může automatizovat prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="0e117-514">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="0e117-515">Úvod do integračních testů</span><span class="sxs-lookup"><span data-stu-id="0e117-515">Introduction to integration tests</span></span>

<span data-ttu-id="0e117-516">Testy integrace vyhodnocují komponenty aplikace na širší úrovni než [testy jednotek](/dotnet/core/testing/).</span><span class="sxs-lookup"><span data-stu-id="0e117-516">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="0e117-517">Testy jednotek se používají k testování izolovaných softwarových komponent, jako jsou například jednotlivé metody třídy.</span><span class="sxs-lookup"><span data-stu-id="0e117-517">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="0e117-518">Testy integrace potvrzují, že dvě nebo více součástí aplikace společně tvoří očekávaný výsledek, případně včetně všech komponent vyžadovaných k úplnému zpracování žádosti.</span><span class="sxs-lookup"><span data-stu-id="0e117-518">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="0e117-519">Tyto širší testy se používají k testování infrastruktury a celé architektury aplikace, často včetně následujících komponent:</span><span class="sxs-lookup"><span data-stu-id="0e117-519">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="0e117-520">databáze</span><span class="sxs-lookup"><span data-stu-id="0e117-520">Database</span></span>
* <span data-ttu-id="0e117-521">Systém souborů</span><span class="sxs-lookup"><span data-stu-id="0e117-521">File system</span></span>
* <span data-ttu-id="0e117-522">Síťová zařízení</span><span class="sxs-lookup"><span data-stu-id="0e117-522">Network appliances</span></span>
* <span data-ttu-id="0e117-523">Kanál požadavků a odpovědí</span><span class="sxs-lookup"><span data-stu-id="0e117-523">Request-response pipeline</span></span>

<span data-ttu-id="0e117-524">Testy jednotek používají prefabrikované komponenty, označované jako *napodobeniny* nebo *makety objektů*, místo součástí infrastruktury.</span><span class="sxs-lookup"><span data-stu-id="0e117-524">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="0e117-525">Na rozdíl od testování částí, integrační testy:</span><span class="sxs-lookup"><span data-stu-id="0e117-525">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="0e117-526">Používejte skutečné komponenty, které aplikace používá v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="0e117-526">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="0e117-527">Vyžadovat další zpracování kódu a dat.</span><span class="sxs-lookup"><span data-stu-id="0e117-527">Require more code and data processing.</span></span>
* <span data-ttu-id="0e117-528">Spuštění trvá déle.</span><span class="sxs-lookup"><span data-stu-id="0e117-528">Take longer to run.</span></span>

<span data-ttu-id="0e117-529">Proto omezte použití integračních testů na nejdůležitější scénáře infrastruktury.</span><span class="sxs-lookup"><span data-stu-id="0e117-529">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="0e117-530">Pokud může být chování Testováno pomocí testu jednotky nebo testu integrace, vyberte test jednotky.</span><span class="sxs-lookup"><span data-stu-id="0e117-530">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="0e117-531">Nepište integrační testy pro všechny možné permutace dat a přístup k souborům s databázemi a systémy souborů.</span><span class="sxs-lookup"><span data-stu-id="0e117-531">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="0e117-532">Bez ohledu na to, kolik míst v rámci aplikace komunikuje s databázemi a systémy souborů, je obvykle schopnost provádět odpovídající testování součástí integračních testů pro čtení, zápis, aktualizace a odstraňování.</span><span class="sxs-lookup"><span data-stu-id="0e117-532">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="0e117-533">Testování částí použijte pro rutinní testy logiky metod, které s těmito komponentami pracují.</span><span class="sxs-lookup"><span data-stu-id="0e117-533">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="0e117-534">Při testování částí je použití falešných nebo pokusů infrastruktury výsledkem rychlejšího spuštění testu.</span><span class="sxs-lookup"><span data-stu-id="0e117-534">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="0e117-535">V diskuzích o integračních testech se testovaný projekt často nazývá testovaný *systém*nebo "SUT" pro krátké účely.</span><span class="sxs-lookup"><span data-stu-id="0e117-535">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="0e117-536">*"SUT" se v tomto tématu používá pro odkaz na testované aplikace ASP.NET Core.*</span><span class="sxs-lookup"><span data-stu-id="0e117-536">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="0e117-537">ASP.NET Core testy integrace</span><span class="sxs-lookup"><span data-stu-id="0e117-537">ASP.NET Core integration tests</span></span>

<span data-ttu-id="0e117-538">Integrační testy v ASP.NET Core vyžadují následující:</span><span class="sxs-lookup"><span data-stu-id="0e117-538">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="0e117-539">Testovací projekt se používá k zahrnutí a spuštění testů.</span><span class="sxs-lookup"><span data-stu-id="0e117-539">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="0e117-540">Testovací projekt má odkaz na SUT.</span><span class="sxs-lookup"><span data-stu-id="0e117-540">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="0e117-541">Testovací projekt vytvoří testovací webový hostitel pro SUT a používá klienta testovacího serveru ke zpracování požadavků a odpovědí s SUT.</span><span class="sxs-lookup"><span data-stu-id="0e117-541">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="0e117-542">Test Runner se používá ke spuštění testů a hlášení výsledků testu.</span><span class="sxs-lookup"><span data-stu-id="0e117-542">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="0e117-543">Integrační testy následují sekvenci událostí, která zahrnuje běžné kroky pro *uspořádání*, *jednání*a *vyhodnocení* :</span><span class="sxs-lookup"><span data-stu-id="0e117-543">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="0e117-544">Webový hostitel SUT je nakonfigurován.</span><span class="sxs-lookup"><span data-stu-id="0e117-544">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="0e117-545">Je vytvořen klient testovacího serveru, který odesílá žádosti do aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e117-545">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="0e117-546">Krok *Uspořádat* test se spustí: testovací aplikace připraví požadavek.</span><span class="sxs-lookup"><span data-stu-id="0e117-546">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="0e117-547">Testovací krok *Act* se spustí: klient odešle požadavek a přijme odpověď.</span><span class="sxs-lookup"><span data-stu-id="0e117-547">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="0e117-548">Testovací krok *kontrolního výrazu* je proveden: *skutečná* odpověď je ověřena jako *Pass* nebo *selže* na základě *očekávané* odpovědi.</span><span class="sxs-lookup"><span data-stu-id="0e117-548">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="0e117-549">Proces pokračuje, dokud nebudou provedeny všechny testy.</span><span class="sxs-lookup"><span data-stu-id="0e117-549">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="0e117-550">Výsledky testu jsou hlášeny.</span><span class="sxs-lookup"><span data-stu-id="0e117-550">The test results are reported.</span></span>

<span data-ttu-id="0e117-551">Obvykle je testovací webový hostitel nakonfigurovaný jinak než normální webový hostitel aplikace pro testovací běhy.</span><span class="sxs-lookup"><span data-stu-id="0e117-551">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="0e117-552">Pro testy lze například použít jinou databázi nebo jiná nastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e117-552">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="0e117-553">Komponenty infrastruktury, jako je testovací webový hostitel a[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)(In-Memory test server), jsou poskytovány nebo spravovány balíčkem [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) .</span><span class="sxs-lookup"><span data-stu-id="0e117-553">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="0e117-554">Použití tohoto balíčku zjednodušuje vytváření a spouštění testů.</span><span class="sxs-lookup"><span data-stu-id="0e117-554">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="0e117-555">`Microsoft.AspNetCore.Mvc.Testing`Balíček zpracovává následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="0e117-555">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="0e117-556">Zkopíruje soubor závislosti (*. DEPS*) z SUT do adresáře *bin* testovacího projektu.</span><span class="sxs-lookup"><span data-stu-id="0e117-556">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="0e117-557">Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na kořen projektu SUT, aby při spuštění testů byly nalezeny statické soubory a stránky/zobrazení.</span><span class="sxs-lookup"><span data-stu-id="0e117-557">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="0e117-558">Poskytuje třídu [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) pro zjednodušení zavádění SUT pomocí `TestServer` .</span><span class="sxs-lookup"><span data-stu-id="0e117-558">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="0e117-559">Dokumentace k testování [částí](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) popisuje, jak nastavit testovací projekt a Test Runner spolu s podrobnými pokyny ke spuštění testů a doporučení pro pojmenování testů a testovacích tříd.</span><span class="sxs-lookup"><span data-stu-id="0e117-559">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="0e117-560">Při vytváření testovacího projektu pro aplikaci oddělte testy jednotek od testů integrace do různých projektů.</span><span class="sxs-lookup"><span data-stu-id="0e117-560">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="0e117-561">To pomáhá zajistit, že komponenty pro testování infrastruktury nejsou omylem zahrnuty do testů jednotek.</span><span class="sxs-lookup"><span data-stu-id="0e117-561">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="0e117-562">Oddělení testů jednotek a integračních testů také umožňuje kontrolu nad tím, která sada testů je spuštěna.</span><span class="sxs-lookup"><span data-stu-id="0e117-562">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="0e117-563">Konfigurace pro testy Razor stránek aplikace a aplikace MVC se prakticky neliší.</span><span class="sxs-lookup"><span data-stu-id="0e117-563">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="0e117-564">Jediný rozdíl je v tom, jak jsou testy pojmenovány.</span><span class="sxs-lookup"><span data-stu-id="0e117-564">The only difference is in how the tests are named.</span></span> <span data-ttu-id="0e117-565">V Razor aplikaci Pages jsou testy koncových bodů stránky obvykle pojmenovány za třídou modelu stránky (například `IndexPageTests` k otestování integrace komponent pro stránku indexu).</span><span class="sxs-lookup"><span data-stu-id="0e117-565">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="0e117-566">V aplikaci MVC jsou testy obvykle uspořádány podle tříd kontroleru a pojmenovány po kontrolách, které otestuje (například `HomeControllerTests` pro otestování integrace komponent pro domovský kontroler).</span><span class="sxs-lookup"><span data-stu-id="0e117-566">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="0e117-567">Požadavky na test aplikace</span><span class="sxs-lookup"><span data-stu-id="0e117-567">Test app prerequisites</span></span>

<span data-ttu-id="0e117-568">Testovací projekt musí:</span><span class="sxs-lookup"><span data-stu-id="0e117-568">The test project must:</span></span>

* <span data-ttu-id="0e117-569">Odkázat na následující balíčky:</span><span class="sxs-lookup"><span data-stu-id="0e117-569">Reference the following packages:</span></span>
  * [<span data-ttu-id="0e117-570">Microsoft. AspNetCore. app</span><span class="sxs-lookup"><span data-stu-id="0e117-570">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="0e117-571">Microsoft. AspNetCore. Mvc. testování</span><span class="sxs-lookup"><span data-stu-id="0e117-571">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="0e117-572">Zadejte webovou sadu SDK v souboru projektu ( `<Project Sdk="Microsoft.NET.Sdk.Web">` ).</span><span class="sxs-lookup"><span data-stu-id="0e117-572">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="0e117-573">Webová sada SDK se vyžaduje při odkazování na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="0e117-573">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="0e117-574">Tyto požadavky se dají zobrazit v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span><span class="sxs-lookup"><span data-stu-id="0e117-574">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="0e117-575">Zkontrolujte soubor *Tests/RazorPagesProject. Tests/RazorPagesProject. Tests. csproj* .</span><span class="sxs-lookup"><span data-stu-id="0e117-575">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="0e117-576">Ukázková aplikace používá [xUnit](https://xunit.github.io/) test Framework a knihovnu analyzátoru [AngleSharp](https://anglesharp.github.io/) , takže ukázková aplikace také odkazuje na:</span><span class="sxs-lookup"><span data-stu-id="0e117-576">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="0e117-577">xUnit</span><span class="sxs-lookup"><span data-stu-id="0e117-577">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="0e117-578">xUnit. Runner. VisualStudio</span><span class="sxs-lookup"><span data-stu-id="0e117-578">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="0e117-579">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="0e117-579">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="0e117-580">Prostředí SUT</span><span class="sxs-lookup"><span data-stu-id="0e117-580">SUT environment</span></span>

<span data-ttu-id="0e117-581">Pokud není [prostředí](xref:fundamentals/environments) SUT nastaveno, prostředí se standardně vyvíjí.</span><span class="sxs-lookup"><span data-stu-id="0e117-581">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="0e117-582">Základní testy s výchozím WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="0e117-582">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="0e117-583">[WebApplicationFactory \<TEntryPoint> ](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) slouží k vytvoření [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) pro integrační testy.</span><span class="sxs-lookup"><span data-stu-id="0e117-583">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="0e117-584">`TEntryPoint`je třída vstupního bodu třídy SUT, obvykle `Startup` Třída.</span><span class="sxs-lookup"><span data-stu-id="0e117-584">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="0e117-585">Třídy testu implementují rozhraní[IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)( *Class* ) k označení třídy obsahuje testy a poskytování instancí sdíleného objektu napříč testy ve třídě.</span><span class="sxs-lookup"><span data-stu-id="0e117-585">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="0e117-586">Následující testovací třída, `BasicTests` používá `WebApplicationFactory` k zavedení SUT a poskytuje [HttpClient](/dotnet/api/system.net.http.httpclient) testovací metodě, `Get_EndpointsReturnSuccessAndCorrectContentType` .</span><span class="sxs-lookup"><span data-stu-id="0e117-586">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="0e117-587">Metoda zkontroluje, jestli je kód stavu odpovědi úspěšný (stavové kódy v rozsahu 200-299), a `Content-Type` záhlaví je `text/html; charset=utf-8` pro několik stránek aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e117-587">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="0e117-588">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) vytvoří instanci `HttpClient` , která automaticky následuje přesměrování a zpracovává soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="0e117-588">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="0e117-589">Ve výchozím nastavení se soubory cookie, které nejsou nezbytné, nezachovají mezi požadavky, pokud je povolená [zásada pro vyjádření souhlasu GDPR](xref:security/gdpr) .</span><span class="sxs-lookup"><span data-stu-id="0e117-589">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="0e117-590">Chcete-li zachovat soubory cookie, které nejsou nezbytné, například ty, které používá poskytovatel TempData, označte je jako nezbytné v testech.</span><span class="sxs-lookup"><span data-stu-id="0e117-590">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="0e117-591">Pokyny k označení souboru cookie jako nezbytného najdete v tématu [základní soubory cookie](xref:security/gdpr#essential-cookies).</span><span class="sxs-lookup"><span data-stu-id="0e117-591">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="0e117-592">Přizpůsobení WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="0e117-592">Customize WebApplicationFactory</span></span>

<span data-ttu-id="0e117-593">Konfiguraci webového hostitele lze vytvořit nezávisle na testovacích třídách děděním z `WebApplicationFactory` k vytvoření jednoho nebo více vlastních továren:</span><span class="sxs-lookup"><span data-stu-id="0e117-593">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="0e117-594">Dědit z `WebApplicationFactory` a přepsat [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span><span class="sxs-lookup"><span data-stu-id="0e117-594">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="0e117-595">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) umožňuje konfiguraci kolekce služeb pomocí [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), která se spustí před aplikací `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0e117-595">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), which is executed before the app's `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0e117-596">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) umožňuje přepsat a upravit registrované služby v kolekci služeb pomocí [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices):</span><span class="sxs-lookup"><span data-stu-id="0e117-596">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows overriding and modifying registered services in the service collection with [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="0e117-597">Vytvoření podsady databází v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) provádí `InitializeDbForTests` metoda.</span><span class="sxs-lookup"><span data-stu-id="0e117-597">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="0e117-598">Tato metoda je popsána v části [Ukázka integračních testů: organizace testovacích aplikací](#test-app-organization) .</span><span class="sxs-lookup"><span data-stu-id="0e117-598">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="0e117-599">Použijte vlastní `CustomWebApplicationFactory` v testovacích třídách.</span><span class="sxs-lookup"><span data-stu-id="0e117-599">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="0e117-600">Následující příklad používá objekt factory ve `IndexPageTests` třídě:</span><span class="sxs-lookup"><span data-stu-id="0e117-600">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="0e117-601">Klient ukázkové aplikace je nakonfigurován tak, aby zabránil `HttpClient` následujícímu přesměrování.</span><span class="sxs-lookup"><span data-stu-id="0e117-601">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="0e117-602">Jak je vysvětleno dále v části [model ověřování](#mock-authentication) , umožňuje testům kontrolovat výsledek první odpovědi aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e117-602">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="0e117-603">První odpověď je přesměrování v mnoha těchto testech s `Location` hlavičkou.</span><span class="sxs-lookup"><span data-stu-id="0e117-603">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="0e117-604">Typický test používá `HttpClient` pomocné metody a ke zpracování žádosti a odpovědi:</span><span class="sxs-lookup"><span data-stu-id="0e117-604">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="0e117-605">Jakýkoli požadavek POST na SUT musí splňovat kontrolu proti padělání, kterou automaticky provádí [systém ochrany dat proti padělání dat](xref:security/data-protection/introduction)aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e117-605">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="0e117-606">Aby bylo možné uspořádat požadavek POST testu, musí aplikace testů:</span><span class="sxs-lookup"><span data-stu-id="0e117-606">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="0e117-607">Vytvořte požadavek na stránku.</span><span class="sxs-lookup"><span data-stu-id="0e117-607">Make a request for the page.</span></span>
1. <span data-ttu-id="0e117-608">Analyzovat soubor cookie pro antipadělání a žádat ověřovací token z odpovědi.</span><span class="sxs-lookup"><span data-stu-id="0e117-608">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="0e117-609">Proveďte požadavek POST se souborem cookie antipadělání a ověřovacím tokenem žádosti.</span><span class="sxs-lookup"><span data-stu-id="0e117-609">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="0e117-610">`SendAsync`Pomocné metody rozšíření (*helpers/HttpClientExtensions. cs*) a `GetDocumentAsync` pomocná metoda (*helps/HtmlHelpers. cs*) v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) používají analyzátor [AngleSharp](https://anglesharp.github.io/) pro zpracování kontroly proti falšování pomocí následujících metod:</span><span class="sxs-lookup"><span data-stu-id="0e117-610">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="0e117-611">`GetDocumentAsync`: Přijme [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) a vrátí `IHtmlDocument` .</span><span class="sxs-lookup"><span data-stu-id="0e117-611">`GetDocumentAsync`: Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="0e117-612">`GetDocumentAsync`používá objekt pro vytváření, který připraví *virtuální odpověď* na základě originálu `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="0e117-612">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="0e117-613">Další informace najdete v [dokumentaci k AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).</span><span class="sxs-lookup"><span data-stu-id="0e117-613">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="0e117-614">`SendAsync`metody rozšíření pro `HttpClient` vytvoření [zprávy HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) a volání [SendAsync (zprávy HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) pro odeslání požadavků do SUT.</span><span class="sxs-lookup"><span data-stu-id="0e117-614">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="0e117-615">Přetížení pro `SendAsync` přijměte formulář HTML ( `IHtmlFormElement` ) a následující:</span><span class="sxs-lookup"><span data-stu-id="0e117-615">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="0e117-616">Tlačítko Odeslat formuláře ( `IHtmlElement` )</span><span class="sxs-lookup"><span data-stu-id="0e117-616">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="0e117-617">Kolekce hodnot formulářů ( `IEnumerable<KeyValuePair<string, string>>` )</span><span class="sxs-lookup"><span data-stu-id="0e117-617">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="0e117-618">Odeslat tlačítko ( `IHtmlElement` ) a hodnoty formuláře ( `IEnumerable<KeyValuePair<string, string>>` )</span><span class="sxs-lookup"><span data-stu-id="0e117-618">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="0e117-619">[AngleSharp](https://anglesharp.github.io/) je knihovna analýzy třetí strany, která se používá pro demonstrační účely v tomto tématu a v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0e117-619">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="0e117-620">AngleSharp se nepodporuje nebo nevyžaduje pro testování integrace ASP.NET Corech aplikací.</span><span class="sxs-lookup"><span data-stu-id="0e117-620">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="0e117-621">Lze použít jiné analyzátory, jako je například [HTML flexibility Pack (HAP)](https://html-agility-pack.net/).</span><span class="sxs-lookup"><span data-stu-id="0e117-621">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="0e117-622">Další možností je napsat kód pro zpracování tokenu žádosti o ověření požadavku systému antipadělání a souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="0e117-622">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="0e117-623">Přizpůsobení klienta pomocí WithWebHostBuilder</span><span class="sxs-lookup"><span data-stu-id="0e117-623">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="0e117-624">Pokud je v rámci testovací metody vyžadována další konfigurace, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) vytvoří nový `WebApplicationFactory` s [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) , který je dále přizpůsoben konfigurací.</span><span class="sxs-lookup"><span data-stu-id="0e117-624">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="0e117-625">`Post_DeleteMessageHandler_ReturnsRedirectToRoot`Testovací metoda [ukázkové aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstruje použití `WithWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0e117-625">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="0e117-626">Tento test provede v databázi odstranění záznamu aktivací formuláře v SUT.</span><span class="sxs-lookup"><span data-stu-id="0e117-626">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="0e117-627">Vzhledem k tomu, že jiný test ve `IndexPageTests` třídě provádí operaci, která odstraní všechny záznamy v databázi a může běžet před `Post_DeleteMessageHandler_ReturnsRedirectToRoot` metodou, databáze je v této testovací metodě znovu osazena, aby se zajistilo, že SUT k odstranění bude přítomen záznam.</span><span class="sxs-lookup"><span data-stu-id="0e117-627">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="0e117-628">Výběr prvního tlačítka odstranit `messages` ve formuláři v SUT se simuluje v požadavku na SUT:</span><span class="sxs-lookup"><span data-stu-id="0e117-628">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="0e117-629">Možnosti klienta</span><span class="sxs-lookup"><span data-stu-id="0e117-629">Client options</span></span>

<span data-ttu-id="0e117-630">Následující tabulka ukazuje výchozí [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) , který je k dispozici při vytváření `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="0e117-630">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="0e117-631">Možnost</span><span class="sxs-lookup"><span data-stu-id="0e117-631">Option</span></span> | <span data-ttu-id="0e117-632">Description</span><span class="sxs-lookup"><span data-stu-id="0e117-632">Description</span></span> | <span data-ttu-id="0e117-633">Výchozí</span><span class="sxs-lookup"><span data-stu-id="0e117-633">Default</span></span> |
| ---
<span data-ttu-id="0e117-634">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-634">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-635">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-635">'Blazor'</span></span>
- <span data-ttu-id="0e117-636">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-636">'Identity'</span></span>
- <span data-ttu-id="0e117-637">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-638">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-638">'Razor'</span></span>
- <span data-ttu-id="0e117-639">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-639">'SignalR' uid:</span></span> 

<span data-ttu-id="0e117-640">--- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-640">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-641">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-641">'Blazor'</span></span>
- <span data-ttu-id="0e117-642">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-642">'Identity'</span></span>
- <span data-ttu-id="0e117-643">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-644">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-644">'Razor'</span></span>
- <span data-ttu-id="0e117-645">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-645">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-646">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-646">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-647">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-647">'Blazor'</span></span>
- <span data-ttu-id="0e117-648">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-648">'Identity'</span></span>
- <span data-ttu-id="0e117-649">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-649">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-650">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-650">'Razor'</span></span>
- <span data-ttu-id="0e117-651">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-651">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-652">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-652">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-653">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-653">'Blazor'</span></span>
- <span data-ttu-id="0e117-654">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-654">'Identity'</span></span>
- <span data-ttu-id="0e117-655">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-655">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-656">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-656">'Razor'</span></span>
- <span data-ttu-id="0e117-657">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-657">'SignalR' uid:</span></span> 

<span data-ttu-id="0e117-658">------ | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-658">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-659">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-659">'Blazor'</span></span>
- <span data-ttu-id="0e117-660">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-660">'Identity'</span></span>
- <span data-ttu-id="0e117-661">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-661">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-662">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-662">'Razor'</span></span>
- <span data-ttu-id="0e117-663">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-663">'SignalR' uid:</span></span> 

<span data-ttu-id="0e117-664">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Získá nebo nastaví, jestli se `HttpClient` mají instance automaticky sledovat prostřednictvím odpovědí přesměrování.</span><span class="sxs-lookup"><span data-stu-id="0e117-664">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span><span data-ttu-id="0e117-665"> | `true`| | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Získá nebo nastaví základní adresu `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="0e117-665"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Gets or sets the base address of `HttpClient` instances.</span></span><span data-ttu-id="0e117-666"> | `http://localhost`| | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Získá nebo nastaví, jestli `HttpClient` by instance měly zpracovávat soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="0e117-666"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Gets or sets whether `HttpClient` instances should handle cookies.</span></span><span data-ttu-id="0e117-667"> | `true`| | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Získá nebo nastaví maximální počet odpovědí přesměrování, které `HttpClient` by měly instance následovat.</span><span class="sxs-lookup"><span data-stu-id="0e117-667"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> <span data-ttu-id="0e117-668">| 7 |</span><span class="sxs-lookup"><span data-stu-id="0e117-668">| 7 |</span></span>

<span data-ttu-id="0e117-669">Vytvořte `WebApplicationFactoryClientOptions` třídu a předejte ji metodě [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (výchozí hodnoty jsou uvedeny v příkladu kódu):</span><span class="sxs-lookup"><span data-stu-id="0e117-669">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="0e117-670">Vložení makety služeb</span><span class="sxs-lookup"><span data-stu-id="0e117-670">Inject mock services</span></span>

<span data-ttu-id="0e117-671">Služby lze přepsat v rámci testu voláním [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) v Tvůrci hostitele.</span><span class="sxs-lookup"><span data-stu-id="0e117-671">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="0e117-672">**Pro vložení makety služeb musí mít SUT `Startup` třídu s `Startup.ConfigureServices` metodou.**</span><span class="sxs-lookup"><span data-stu-id="0e117-672">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="0e117-673">Vzorový SUT obsahuje oborovou službu, která vrací citát.</span><span class="sxs-lookup"><span data-stu-id="0e117-673">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="0e117-674">Nabídka je vložena do skrytého pole na stránce index, když je požadována stránka indexu.</span><span class="sxs-lookup"><span data-stu-id="0e117-674">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="0e117-675">*Services/IQuoteService. cs*:</span><span class="sxs-lookup"><span data-stu-id="0e117-675">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="0e117-676">*Services/QuoteService. cs*:</span><span class="sxs-lookup"><span data-stu-id="0e117-676">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="0e117-677">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="0e117-677">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="0e117-678">*Pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="0e117-678">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="0e117-679">*Pages/index. cs*:</span><span class="sxs-lookup"><span data-stu-id="0e117-679">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="0e117-680">Při spuštění aplikace SUT se vygeneruje následující kód:</span><span class="sxs-lookup"><span data-stu-id="0e117-680">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="0e117-681">Chcete-li otestovat vkládání služby a uvozovek v rámci integračního testu, je do SUTu vložena přípravou služba.</span><span class="sxs-lookup"><span data-stu-id="0e117-681">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="0e117-682">Napodobná služba nahradí aplikaci `QuoteService` službou poskytnutou testovací aplikací, která se nazývá `TestQuoteService` :</span><span class="sxs-lookup"><span data-stu-id="0e117-682">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="0e117-683">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="0e117-683">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="0e117-684">`ConfigureTestServices`je volána a zaregistrována Oborová služba:</span><span class="sxs-lookup"><span data-stu-id="0e117-684">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="0e117-685">Označení vyprodukované během provádění testu odráží text citace, který je dodaný pomocí `TestQuoteService` , takže kontrolní výraz projde:</span><span class="sxs-lookup"><span data-stu-id="0e117-685">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="0e117-686">Modely ověřování</span><span class="sxs-lookup"><span data-stu-id="0e117-686">Mock authentication</span></span>

<span data-ttu-id="0e117-687">Testy ve `AuthTests` třídě zkontrolují zabezpečený koncový bod:</span><span class="sxs-lookup"><span data-stu-id="0e117-687">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="0e117-688">Přesměruje neověřeného uživatele na přihlašovací stránku aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e117-688">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="0e117-689">Vrátí obsah pro ověřeného uživatele.</span><span class="sxs-lookup"><span data-stu-id="0e117-689">Returns content for an authenticated user.</span></span>

<span data-ttu-id="0e117-690">V SUT `/SecurePage` stránka používá konvenci [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) k použití [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) na stránku.</span><span class="sxs-lookup"><span data-stu-id="0e117-690">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="0e117-691">Další informace najdete v tématu [ Razor autorizační konvence pro stránky](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span><span class="sxs-lookup"><span data-stu-id="0e117-691">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="0e117-692">V `Get_SecurePageRedirectsAnUnauthenticatedUser` testu je [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) nastaveno na zakázat přesměrování nastavením [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) na `false` :</span><span class="sxs-lookup"><span data-stu-id="0e117-692">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="0e117-693">Když klientovi zakážete postup přesměrování, můžete provést následující kontroly:</span><span class="sxs-lookup"><span data-stu-id="0e117-693">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="0e117-694">Stavový kód vrácený SUT může být zkontrolován na základě očekávaného výsledku [HttpStatusCode. Redirect](/dotnet/api/system.net.httpstatuscode) , nikoli konečného stavového kódu po přesměrování na přihlašovací stránku, která by byla [HttpStatusCode. ok](/dotnet/api/system.net.httpstatuscode).</span><span class="sxs-lookup"><span data-stu-id="0e117-694">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="0e117-695">`Location`Hodnota hlavičky v hlavičkách odpovědi je zaškrtnuta, aby se ověřilo, že začíná `http://localhost/Identity/Account/Login` , a ne konečná odpověď přihlašovací stránky, kde by tato hlavička nebyla k `Location` dispozici.</span><span class="sxs-lookup"><span data-stu-id="0e117-695">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="0e117-696">Testovací aplikace může <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> napodobovat v [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) , aby mohla testovat aspekty ověřování a autorizace.</span><span class="sxs-lookup"><span data-stu-id="0e117-696">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="0e117-697">Minimální scénář vrátí [AuthenticateResult. Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span><span class="sxs-lookup"><span data-stu-id="0e117-697">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="0e117-698">`TestAuthHandler`Je volána k ověření uživatele, když je schéma ověřování nastaveno na místo, `Test` kde `AddAuthentication` je zaregistrováno pro `ConfigureTestServices` :</span><span class="sxs-lookup"><span data-stu-id="0e117-698">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="0e117-699">Další informace o naleznete v `WebApplicationFactoryClientOptions` části [Možnosti klienta](#client-options) .</span><span class="sxs-lookup"><span data-stu-id="0e117-699">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="0e117-700">Nastavení prostředí</span><span class="sxs-lookup"><span data-stu-id="0e117-700">Set the environment</span></span>

<span data-ttu-id="0e117-701">Ve výchozím nastavení je hostitel a prostředí aplikace SUT nakonfigurovaná tak, aby používala vývojové prostředí.</span><span class="sxs-lookup"><span data-stu-id="0e117-701">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="0e117-702">Přepsání prostředí SUT:</span><span class="sxs-lookup"><span data-stu-id="0e117-702">To override the SUT's environment:</span></span>

* <span data-ttu-id="0e117-703">Nastavte `ASPNETCORE_ENVIRONMENT` proměnnou prostředí (například, `Staging` `Production` nebo jinou vlastní hodnotu, například `Testing` ).</span><span class="sxs-lookup"><span data-stu-id="0e117-703">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="0e117-704">Přepsáním `CreateWebHostBuilder` v testovací aplikaci si přečtěte `ASPNETCORE_ENVIRONMENT` proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="0e117-704">Override `CreateWebHostBuilder` in the test app to read the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override IWebHostBuilder CreateWebHostBuilder()
    {
        return base.CreateWebHostBuilder()
            .UseEnvironment(
                Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    }

    ...
}
```

<span data-ttu-id="0e117-705">Prostředí je také možné nastavit přímo na tvůrci hostitele v vlastní <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="0e117-705">The environment can also be set directly on the host builder in a custom <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601>:</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.UseEnvironment(
            Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    
        ...
    }

    ...
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="0e117-706">Jak testovací infrastruktura odvodí kořenovou cestu obsahu aplikace</span><span class="sxs-lookup"><span data-stu-id="0e117-706">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="0e117-707">`WebApplicationFactory`Konstruktor odvodí [kořenovou cestu obsahu](xref:fundamentals/index#content-root) aplikace hledáním [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) na sestavení obsahujícího testy integrace s klíčem, který se rovná `TEntryPoint` sestavení `System.Reflection.Assembly.FullName` .</span><span class="sxs-lookup"><span data-stu-id="0e117-707">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="0e117-708">V případě, že atribut se správným klíčem nebyl nalezen, `WebApplicationFactory` vrátí se zpět k hledání souboru řešení (*. sln*) a připojí `TEntryPoint` název sestavení k adresáři řešení.</span><span class="sxs-lookup"><span data-stu-id="0e117-708">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="0e117-709">Kořenový adresář aplikace (kořenová cesta obsahu) se používá ke zjišťování zobrazení a souborů obsahu.</span><span class="sxs-lookup"><span data-stu-id="0e117-709">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="0e117-710">Zakázat stínové kopírování</span><span class="sxs-lookup"><span data-stu-id="0e117-710">Disable shadow copying</span></span>

<span data-ttu-id="0e117-711">Stínové kopírování způsobí, že se testy spustí v jiném adresáři než výstupní adresář.</span><span class="sxs-lookup"><span data-stu-id="0e117-711">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="0e117-712">Aby testy správně fungovaly, musí být stínové kopírování zakázané.</span><span class="sxs-lookup"><span data-stu-id="0e117-712">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="0e117-713">[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) používá xUnit a zakáže stínové kopírování pro xUnit zahrnutím souboru *xUnit. Runner. JSON* se správným nastavením konfigurace.</span><span class="sxs-lookup"><span data-stu-id="0e117-713">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="0e117-714">Další informace najdete v tématu [Konfigurace xUnit pomocí formátu JSON](https://xunit.github.io/docs/configuring-with-json.html).</span><span class="sxs-lookup"><span data-stu-id="0e117-714">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="0e117-715">Do kořenového adresáře testovacího projektu přidejte soubor *xUnit. Runner. JSON* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="0e117-715">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="0e117-716">Pokud používáte sadu Visual Studio, nastavte vlastnost **Kopírovat do výstupního adresáře** na **Kopírovat vždy**.</span><span class="sxs-lookup"><span data-stu-id="0e117-716">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="0e117-717">Pokud se Visual Studio nepoužívá, přidejte `Content` cíl do souboru projektu testovací aplikace:</span><span class="sxs-lookup"><span data-stu-id="0e117-717">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="0e117-718">Vyřazení objektů</span><span class="sxs-lookup"><span data-stu-id="0e117-718">Disposal of objects</span></span>

<span data-ttu-id="0e117-719">Po provedení testů pro `IClassFixture` implementaci jsou [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) a [HttpClient](/dotnet/api/system.net.http.httpclient) uvolněny, když xUnit vyřadí [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span><span class="sxs-lookup"><span data-stu-id="0e117-719">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="0e117-720">Pokud objekty, které vytvořil vývojář, vyžadují likvidaci, jejich uvolnění v `IClassFixture` implementaci.</span><span class="sxs-lookup"><span data-stu-id="0e117-720">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="0e117-721">Další informace naleznete v tématu [implementace metody Dispose](/dotnet/standard/garbage-collection/implementing-dispose).</span><span class="sxs-lookup"><span data-stu-id="0e117-721">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="0e117-722">Ukázka integračních testů</span><span class="sxs-lookup"><span data-stu-id="0e117-722">Integration tests sample</span></span>

<span data-ttu-id="0e117-723">[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) se skládá ze dvou aplikací:</span><span class="sxs-lookup"><span data-stu-id="0e117-723">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="0e117-724">Aplikace</span><span class="sxs-lookup"><span data-stu-id="0e117-724">App</span></span> | <span data-ttu-id="0e117-725">Adresář projektu</span><span class="sxs-lookup"><span data-stu-id="0e117-725">Project directory</span></span> | <span data-ttu-id="0e117-726">Description</span><span class="sxs-lookup"><span data-stu-id="0e117-726">Description</span></span> |
| --- | ---
<span data-ttu-id="0e117-727">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-728">'Blazor'</span></span>
- <span data-ttu-id="0e117-729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-729">'Identity'</span></span>
- <span data-ttu-id="0e117-730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-730">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-731">'Razor'</span></span>
- <span data-ttu-id="0e117-732">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-733">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-734">'Blazor'</span></span>
- <span data-ttu-id="0e117-735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-735">'Identity'</span></span>
- <span data-ttu-id="0e117-736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-736">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-737">'Razor'</span></span>
- <span data-ttu-id="0e117-738">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-739">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-740">'Blazor'</span></span>
- <span data-ttu-id="0e117-741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-741">'Identity'</span></span>
- <span data-ttu-id="0e117-742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-742">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-743">'Razor'</span></span>
- <span data-ttu-id="0e117-744">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-745">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-746">'Blazor'</span></span>
- <span data-ttu-id="0e117-747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-747">'Identity'</span></span>
- <span data-ttu-id="0e117-748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-748">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-749">'Razor'</span></span>
- <span data-ttu-id="0e117-750">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-751">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-752">'Blazor'</span></span>
- <span data-ttu-id="0e117-753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-753">'Identity'</span></span>
- <span data-ttu-id="0e117-754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-754">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-755">'Razor'</span></span>
- <span data-ttu-id="0e117-756">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-757">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-758">'Blazor'</span></span>
- <span data-ttu-id="0e117-759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-759">'Identity'</span></span>
- <span data-ttu-id="0e117-760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-760">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-761">'Razor'</span></span>
- <span data-ttu-id="0e117-762">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-762">'SignalR' uid:</span></span> 

<span data-ttu-id="0e117-763">--------- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-763">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-764">'Blazor'</span></span>
- <span data-ttu-id="0e117-765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-765">'Identity'</span></span>
- <span data-ttu-id="0e117-766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-766">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-767">'Razor'</span></span>
- <span data-ttu-id="0e117-768">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-768">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-769">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-769">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-770">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-770">'Blazor'</span></span>
- <span data-ttu-id="0e117-771">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-771">'Identity'</span></span>
- <span data-ttu-id="0e117-772">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-772">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-773">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-773">'Razor'</span></span>
- <span data-ttu-id="0e117-774">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-774">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-775">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-775">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-776">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-776">'Blazor'</span></span>
- <span data-ttu-id="0e117-777">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-777">'Identity'</span></span>
- <span data-ttu-id="0e117-778">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-778">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-779">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-779">'Razor'</span></span>
- <span data-ttu-id="0e117-780">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-780">'SignalR' uid:</span></span> 

<span data-ttu-id="0e117-781">------ | | Aplikace zprávy (SUT) | *Src/RazorPagesProject* | Povoluje uživateli přidávat, odstraňovat a analyzovat zprávy a analyzovat je.</span><span class="sxs-lookup"><span data-stu-id="0e117-781">------ | | Message app (the SUT) | *src/RazorPagesProject* | Allows a user to add, delete one, delete all, and analyze messages.</span></span> <span data-ttu-id="0e117-782">| | Testovací aplikace | *testuje/RazorPagesProject. Tests* | Slouží k integraci testu SUT.</span><span class="sxs-lookup"><span data-stu-id="0e117-782">| | Test app | *tests/RazorPagesProject.Tests* | Used to integration test the SUT.</span></span> |

<span data-ttu-id="0e117-783">Testy lze spustit pomocí vestavěných funkcí testu integrovaného vývojového prostředí (IDE), jako je například [Visual Studio](https://visualstudio.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="0e117-783">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="0e117-784">Pokud používáte [Visual Studio Code](https://code.visualstudio.com/) nebo příkazový řádek, spusťte následující příkaz v příkazovém řádku v adresáři *Tests/RazorPagesProject. Tests* :</span><span class="sxs-lookup"><span data-stu-id="0e117-784">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="0e117-785">Organizace pro aplikace zprávy (SUT)</span><span class="sxs-lookup"><span data-stu-id="0e117-785">Message app (SUT) organization</span></span>

<span data-ttu-id="0e117-786">SUT je Razor systém zpráv s následujícími charakteristikami:</span><span class="sxs-lookup"><span data-stu-id="0e117-786">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="0e117-787">Stránka indexu aplikace (*stránky/index. cshtml* a *pages/index. cshtml. cs*) poskytuje metody uživatelského rozhraní a modelu stránky pro řízení přidávání, odstraňování a analýzy zpráv (průměrná slova na zprávu).</span><span class="sxs-lookup"><span data-stu-id="0e117-787">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="0e117-788">Zpráva je popsána `Message` třídou (*data/Message. cs*) se dvěma vlastnostmi: `Id` (klíč) a `Text` (zpráva).</span><span class="sxs-lookup"><span data-stu-id="0e117-788">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="0e117-789">`Text`Vlastnost je povinná a omezená na 200 znaků.</span><span class="sxs-lookup"><span data-stu-id="0e117-789">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="0e117-790">Zprávy se ukládají pomocí&#8224; [databáze Entity Framework v paměti](/ef/core/providers/in-memory/) .</span><span class="sxs-lookup"><span data-stu-id="0e117-790">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="0e117-791">Aplikace obsahuje vrstvu pro přístup k datům (DAL) ve třídě kontextu databáze `AppDbContext` (*data/AppDbContext. cs*).</span><span class="sxs-lookup"><span data-stu-id="0e117-791">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="0e117-792">Pokud je databáze při spuštění aplikace prázdná, úložiště zpráv se inicializuje se třemi zprávami.</span><span class="sxs-lookup"><span data-stu-id="0e117-792">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="0e117-793">Aplikace obsahuje, k `/SecurePage` němuž může mít oprávnění pouze ověřený uživatel.</span><span class="sxs-lookup"><span data-stu-id="0e117-793">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="0e117-794">&#8224;tématu EF, [test s pamětí](/ef/core/miscellaneous/testing/in-memory), vysvětluje, jak používat databázi v paměti pro testy pomocí MSTest.</span><span class="sxs-lookup"><span data-stu-id="0e117-794">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="0e117-795">Toto téma používá testovací rozhraní [xUnit](https://xunit.github.io/) .</span><span class="sxs-lookup"><span data-stu-id="0e117-795">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="0e117-796">Koncepty testů a testovací implementace v různých testovacích architekturách jsou podobné, ale nejsou totožné.</span><span class="sxs-lookup"><span data-stu-id="0e117-796">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="0e117-797">I když aplikace nepoužívá vzor úložiště a není efektivním příkladem [vzoru jednotky práce (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor stránky tyto vzory vývoje podporují.</span><span class="sxs-lookup"><span data-stu-id="0e117-797">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="0e117-798">Další informace najdete v tématu [navrhování vrstvy trvalosti infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) a [testovacího kontroleru testů](/aspnet/core/mvc/controllers/testing) (ukázka implementuje vzor úložiště).</span><span class="sxs-lookup"><span data-stu-id="0e117-798">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="0e117-799">Organizace testovací aplikace</span><span class="sxs-lookup"><span data-stu-id="0e117-799">Test app organization</span></span>

<span data-ttu-id="0e117-800">Testovací aplikace je Konzolová aplikace v adresáři *Tests/RazorPagesProject. Tests* .</span><span class="sxs-lookup"><span data-stu-id="0e117-800">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="0e117-801">Testovací adresář aplikace</span><span class="sxs-lookup"><span data-stu-id="0e117-801">Test app directory</span></span> | <span data-ttu-id="0e117-802">Description</span><span class="sxs-lookup"><span data-stu-id="0e117-802">Description</span></span> |
| ---
<span data-ttu-id="0e117-803">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-803">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-804">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-804">'Blazor'</span></span>
- <span data-ttu-id="0e117-805">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-805">'Identity'</span></span>
- <span data-ttu-id="0e117-806">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-806">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-807">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-807">'Razor'</span></span>
- <span data-ttu-id="0e117-808">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-808">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-809">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-809">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-810">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-810">'Blazor'</span></span>
- <span data-ttu-id="0e117-811">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-811">'Identity'</span></span>
- <span data-ttu-id="0e117-812">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-812">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-813">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-813">'Razor'</span></span>
- <span data-ttu-id="0e117-814">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-814">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-815">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-815">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-816">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-816">'Blazor'</span></span>
- <span data-ttu-id="0e117-817">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-817">'Identity'</span></span>
- <span data-ttu-id="0e117-818">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-818">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-819">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-819">'Razor'</span></span>
- <span data-ttu-id="0e117-820">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-820">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-821">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-821">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-822">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-822">'Blazor'</span></span>
- <span data-ttu-id="0e117-823">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-823">'Identity'</span></span>
- <span data-ttu-id="0e117-824">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-824">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-825">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-825">'Razor'</span></span>
- <span data-ttu-id="0e117-826">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-826">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-827">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-827">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-828">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-828">'Blazor'</span></span>
- <span data-ttu-id="0e117-829">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-829">'Identity'</span></span>
- <span data-ttu-id="0e117-830">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-830">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-831">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-831">'Razor'</span></span>
- <span data-ttu-id="0e117-832">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-832">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-833">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-833">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-834">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-834">'Blazor'</span></span>
- <span data-ttu-id="0e117-835">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-835">'Identity'</span></span>
- <span data-ttu-id="0e117-836">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-836">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-837">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-837">'Razor'</span></span>
- <span data-ttu-id="0e117-838">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-838">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-839">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-839">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-840">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-840">'Blazor'</span></span>
- <span data-ttu-id="0e117-841">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-841">'Identity'</span></span>
- <span data-ttu-id="0e117-842">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-842">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-843">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-843">'Razor'</span></span>
- <span data-ttu-id="0e117-844">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-844">'SignalR' uid:</span></span> 

<span data-ttu-id="0e117-845">--------- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-845">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-846">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-846">'Blazor'</span></span>
- <span data-ttu-id="0e117-847">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-847">'Identity'</span></span>
- <span data-ttu-id="0e117-848">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-848">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-849">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-849">'Razor'</span></span>
- <span data-ttu-id="0e117-850">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-850">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-851">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-851">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-852">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-852">'Blazor'</span></span>
- <span data-ttu-id="0e117-853">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-853">'Identity'</span></span>
- <span data-ttu-id="0e117-854">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-854">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-855">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-855">'Razor'</span></span>
- <span data-ttu-id="0e117-856">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-856">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0e117-857">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0e117-857">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e117-858">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e117-858">'Blazor'</span></span>
- <span data-ttu-id="0e117-859">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e117-859">'Identity'</span></span>
- <span data-ttu-id="0e117-860">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e117-860">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e117-861">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e117-861">'Razor'</span></span>
- <span data-ttu-id="0e117-862">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0e117-862">'SignalR' uid:</span></span> 

<span data-ttu-id="0e117-863">------ | | *AuthTests* | Obsahuje testovací metody pro:</span><span class="sxs-lookup"><span data-stu-id="0e117-863">------ | | *AuthTests* | Contains test methods for:</span></span><ul><li><span data-ttu-id="0e117-864">Přístup k zabezpečené stránce neověřenému uživateli</span><span class="sxs-lookup"><span data-stu-id="0e117-864">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="0e117-865">Přístup k zabezpečené stránce ověřeným uživatelem s přípravou <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .</span><span class="sxs-lookup"><span data-stu-id="0e117-865">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="0e117-866">Získání profilu uživatele GitHub a kontrola přihlášení uživatele profilu.</span><span class="sxs-lookup"><span data-stu-id="0e117-866">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> <span data-ttu-id="0e117-867">| | *BasicTests* | Obsahuje testovací metodu pro typ směrování a obsahu.</span><span class="sxs-lookup"><span data-stu-id="0e117-867">| | *BasicTests* | Contains a test method for routing and content type.</span></span> <span data-ttu-id="0e117-868">| | *IntegrationTests* | Obsahuje testy integrace pro stránku indexu pomocí vlastní `WebApplicationFactory` třídy.</span><span class="sxs-lookup"><span data-stu-id="0e117-868">| | *IntegrationTests* | Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> <span data-ttu-id="0e117-869">| | *Pomocné programy a pomůcky* | </span><span class="sxs-lookup"><span data-stu-id="0e117-869">| | *Helpers/Utilities* | </span></span><ul><li><span data-ttu-id="0e117-870">*Utilities.cs* obsahuje `InitializeDbForTests` metodu použitou k osazení databáze testovacími daty.</span><span class="sxs-lookup"><span data-stu-id="0e117-870">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="0e117-871">*HtmlHelpers.cs* poskytuje metodu, která vrátí AngleSharp `IHtmlDocument` pro použití testovacími metodami.</span><span class="sxs-lookup"><span data-stu-id="0e117-871">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="0e117-872">*HttpClientExtensions.cs* poskytují přetížení pro `SendAsync` odeslání požadavků do SUT.</span><span class="sxs-lookup"><span data-stu-id="0e117-872">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="0e117-873">Testovací rozhraní je [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="0e117-873">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="0e117-874">Integrační testy se provádějí pomocí [Microsoft. AspNetCore. TestHost](/dotnet/api/microsoft.aspnetcore.testhost), který zahrnuje [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span><span class="sxs-lookup"><span data-stu-id="0e117-874">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="0e117-875">Vzhledem k tomu, že se balíček [Microsoft. AspNetCore. Mvc. test](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) používá ke konfiguraci testovacího hostitele a testovacího serveru, `TestHost` `TestServer` balíčky a nevyžadují přímé odkazy na balíčky v souboru projektu testovací aplikace nebo v konfiguraci vývojáře v testovací aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0e117-875">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="0e117-876">**Dosazení databáze pro testování**</span><span class="sxs-lookup"><span data-stu-id="0e117-876">**Seeding the database for testing**</span></span>

<span data-ttu-id="0e117-877">Integrační testy obvykle vyžadují malou datovou sadu v databázi před spuštěním testu.</span><span class="sxs-lookup"><span data-stu-id="0e117-877">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="0e117-878">Například odstraňovací test volá pro odstranění záznamu databáze, takže databáze musí mít alespoň jeden záznam, aby žádost o odstranění proběhla úspěšně.</span><span class="sxs-lookup"><span data-stu-id="0e117-878">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="0e117-879">Ukázková aplikace se dosazuje do databáze se třemi zprávami v *Utilities.cs* , které testy můžou použít, když se spustí:</span><span class="sxs-lookup"><span data-stu-id="0e117-879">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="0e117-880">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0e117-880">Additional resources</span></span>

* [<span data-ttu-id="0e117-881">Testování částí</span><span class="sxs-lookup"><span data-stu-id="0e117-881">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
