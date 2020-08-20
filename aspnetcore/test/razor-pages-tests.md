---
title: Razor Testování částí stránky v ASP.NET Core
author: rick-anderson
description: Naučte se vytvářet testy jednotek pro Razor aplikace stránek.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/22/2020
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
uid: test/razor-pages-tests
ms.openlocfilehash: bb376412daafd49b307c3c13ea7c88f34628f1c3
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634836"
---
# <a name="no-locrazor-pages-unit-tests-in-aspnet-core"></a><span data-ttu-id="2d397-103">Razor Testování částí stránky v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2d397-103">Razor Pages unit tests in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2d397-104">ASP.NET Core podporuje testování částí Razor aplikací Pages.</span><span class="sxs-lookup"><span data-stu-id="2d397-104">ASP.NET Core supports unit tests of Razor Pages apps.</span></span> <span data-ttu-id="2d397-105">Testy vrstvy přístupu k datům (DAL) a modelů stránek vám pomůžou zajistit:</span><span class="sxs-lookup"><span data-stu-id="2d397-105">Tests of the data access layer (DAL) and page models help ensure:</span></span>

* <span data-ttu-id="2d397-106">Části Razor aplikace stránky fungují nezávisle a společně jako jednotka během vytváření aplikací.</span><span class="sxs-lookup"><span data-stu-id="2d397-106">Parts of a Razor Pages app work independently and together as a unit during app construction.</span></span>
* <span data-ttu-id="2d397-107">Třídy a metody mají omezené obory zodpovědnosti.</span><span class="sxs-lookup"><span data-stu-id="2d397-107">Classes and methods have limited scopes of responsibility.</span></span>
* <span data-ttu-id="2d397-108">Další dokumentace existuje v tom, jak se má aplikace chovat.</span><span class="sxs-lookup"><span data-stu-id="2d397-108">Additional documentation exists on how the app should behave.</span></span>
* <span data-ttu-id="2d397-109">V průběhu automatizovaného sestavování a nasazování se nacházejí regrese, které se týkají chyb v rámci aktualizací kódu.</span><span class="sxs-lookup"><span data-stu-id="2d397-109">Regressions, which are errors brought about by updates to the code, are found during automated building and deployment.</span></span>

<span data-ttu-id="2d397-110">V tomto tématu se předpokládá, že máte základní znalosti o Razor aplikacích stránky a testování částí.</span><span class="sxs-lookup"><span data-stu-id="2d397-110">This topic assumes that you have a basic understanding of Razor Pages apps and unit tests.</span></span> <span data-ttu-id="2d397-111">Pokud nejste obeznámeni s Razor aplikacemi Pages nebo koncepty testování, přečtěte si následující témata:</span><span class="sxs-lookup"><span data-stu-id="2d397-111">If you're unfamiliar with Razor Pages apps or test concepts, see the following topics:</span></span>

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [<span data-ttu-id="2d397-112">Testování částí C# v .NET Core pomocí příkazu dotnet test a xUnit</span><span class="sxs-lookup"><span data-stu-id="2d397-112">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

<span data-ttu-id="2d397-113">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2d397-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2d397-114">Vzorový projekt se skládá ze dvou aplikací:</span><span class="sxs-lookup"><span data-stu-id="2d397-114">The sample project is composed of two apps:</span></span>

| <span data-ttu-id="2d397-115">Aplikace</span><span class="sxs-lookup"><span data-stu-id="2d397-115">App</span></span>         | <span data-ttu-id="2d397-116">Složka projektu</span><span class="sxs-lookup"><span data-stu-id="2d397-116">Project folder</span></span>                     | <span data-ttu-id="2d397-117">Popis</span><span class="sxs-lookup"><span data-stu-id="2d397-117">Description</span></span> |
| ----------- | ---------------------------------- | ----------- |
| <span data-ttu-id="2d397-118">Aplikace zprávy</span><span class="sxs-lookup"><span data-stu-id="2d397-118">Message app</span></span> | <span data-ttu-id="2d397-119">*src/ Razor PagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="2d397-119">*src/RazorPagesTestSample*</span></span>         | <span data-ttu-id="2d397-120">Umožňuje uživateli přidat zprávu, odstranit jednu zprávu, odstranit všechny zprávy a analyzovat zprávy (najít průměrný počet slov na zprávu).</span><span class="sxs-lookup"><span data-stu-id="2d397-120">Allows a user to add a message, delete one message, delete all messages, and analyze messages (find the average number of words per message).</span></span> |
| <span data-ttu-id="2d397-121">Testovací aplikace</span><span class="sxs-lookup"><span data-stu-id="2d397-121">Test app</span></span>    | <span data-ttu-id="2d397-122">*testuje/ Razor PagesTestSample. Tests*</span><span class="sxs-lookup"><span data-stu-id="2d397-122">*tests/RazorPagesTestSample.Tests*</span></span> | <span data-ttu-id="2d397-123">Slouží k testování modelu DAL a stránky indexu aplikace zprávy.</span><span class="sxs-lookup"><span data-stu-id="2d397-123">Used to unit test the DAL and Index page model of the message app.</span></span> |

<span data-ttu-id="2d397-124">Testy lze spustit pomocí integrovaných funkcí testu integrovaného vývojového prostředí (IDE), jako je například [Visual Studio](/visualstudio/test/unit-test-your-code) nebo [Visual Studio pro Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).</span><span class="sxs-lookup"><span data-stu-id="2d397-124">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](/visualstudio/test/unit-test-your-code) or [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).</span></span> <span data-ttu-id="2d397-125">Pokud používáte [Visual Studio Code](https://code.visualstudio.com/) nebo příkazový řádek, spusťte následující příkaz na příkazovém řádku ve složce *Tests/ Razor PagesTestSample. Tests* :</span><span class="sxs-lookup"><span data-stu-id="2d397-125">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesTestSample.Tests* folder:</span></span>

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a><span data-ttu-id="2d397-126">Organizace aplikace zprávy</span><span class="sxs-lookup"><span data-stu-id="2d397-126">Message app organization</span></span>

<span data-ttu-id="2d397-127">Zpráva aplikace je Razor systém zpráv s následujícími charakteristikami:</span><span class="sxs-lookup"><span data-stu-id="2d397-127">The message app is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="2d397-128">Stránka indexu aplikace (*pages/index. cshtml* a *pages/index. cshtml. cs*) poskytuje metody uživatelského rozhraní a modelu stránky pro řízení přidávání, odstraňování a analýzy zpráv (hledání průměrného počtu slov na jednu zprávu).</span><span class="sxs-lookup"><span data-stu-id="2d397-128">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (find the average number of words per message).</span></span>
* <span data-ttu-id="2d397-129">Zpráva je popsána `Message` třídou (*data/Message. cs*) se dvěma vlastnostmi: `Id` (klíč) a `Text` (zpráva).</span><span class="sxs-lookup"><span data-stu-id="2d397-129">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="2d397-130">`Text`Vlastnost je povinná a omezená na 200 znaků.</span><span class="sxs-lookup"><span data-stu-id="2d397-130">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="2d397-131">Zprávy se ukládají pomocí&#8224; [databáze Entity Framework v paměti](/ef/core/providers/in-memory/) .</span><span class="sxs-lookup"><span data-stu-id="2d397-131">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="2d397-132">Aplikace obsahuje ve své třídě kontext databáze DAL `AppDbContext` (*data/AppDbContext. cs*).</span><span class="sxs-lookup"><span data-stu-id="2d397-132">The app contains a DAL in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span> <span data-ttu-id="2d397-133">Metody DAL jsou označeny `virtual` , což umožňuje napodobovat metody pro použití v testech.</span><span class="sxs-lookup"><span data-stu-id="2d397-133">The DAL methods are marked `virtual`, which allows mocking the methods for use in the tests.</span></span>
* <span data-ttu-id="2d397-134">Pokud je databáze při spuštění aplikace prázdná, úložiště zpráv se inicializuje se třemi zprávami.</span><span class="sxs-lookup"><span data-stu-id="2d397-134">If the database is empty on app startup, the message store is initialized with three messages.</span></span> <span data-ttu-id="2d397-135">Tyto *osazené zprávy* se používají také v testech.</span><span class="sxs-lookup"><span data-stu-id="2d397-135">These *seeded messages* are also used in tests.</span></span>

<span data-ttu-id="2d397-136">&#8224;tématu EF, [test s pamětí](/ef/core/miscellaneous/testing/in-memory), vysvětluje, jak používat databázi v paměti pro testy pomocí MSTest.</span><span class="sxs-lookup"><span data-stu-id="2d397-136">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="2d397-137">Toto téma používá testovací rozhraní [xUnit](https://xunit.github.io/) .</span><span class="sxs-lookup"><span data-stu-id="2d397-137">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="2d397-138">Koncepty testů a testovací implementace v různých testovacích architekturách jsou podobné, ale nejsou totožné.</span><span class="sxs-lookup"><span data-stu-id="2d397-138">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="2d397-139">I když ukázková aplikace nepoužívá vzor úložiště a není efektivním příkladem [vzoru jednotka práce (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor stránky tyto vzory vývoje podporují.</span><span class="sxs-lookup"><span data-stu-id="2d397-139">Although the sample app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="2d397-140">Další informace najdete v tématu [navrhování vrstvy trvalosti infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) a <xref:mvc/controllers/testing> (ukázka implementuje vzor úložiště).</span><span class="sxs-lookup"><span data-stu-id="2d397-140">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and <xref:mvc/controllers/testing> (the sample implements the repository pattern).</span></span>

## <a name="test-app-organization"></a><span data-ttu-id="2d397-141">Organizace testovací aplikace</span><span class="sxs-lookup"><span data-stu-id="2d397-141">Test app organization</span></span>

<span data-ttu-id="2d397-142">Testovací aplikace je Konzolová aplikace ve složce *Tests/ Razor PagesTestSample. Tests* .</span><span class="sxs-lookup"><span data-stu-id="2d397-142">The test app is a console app inside the *tests/RazorPagesTestSample.Tests* folder.</span></span>

| <span data-ttu-id="2d397-143">Složka testovací aplikace</span><span class="sxs-lookup"><span data-stu-id="2d397-143">Test app folder</span></span> | <span data-ttu-id="2d397-144">Popis</span><span class="sxs-lookup"><span data-stu-id="2d397-144">Description</span></span> |
| --------------- | ----------- |
| <span data-ttu-id="2d397-145">*UnitTests*</span><span class="sxs-lookup"><span data-stu-id="2d397-145">*UnitTests*</span></span>     | <ul><li><span data-ttu-id="2d397-146">*DataAccessLayerTest.cs* obsahuje testy jednotek pro dal.</span><span class="sxs-lookup"><span data-stu-id="2d397-146">*DataAccessLayerTest.cs* contains the unit tests for the DAL.</span></span></li><li><span data-ttu-id="2d397-147">*IndexPageTests.cs* obsahuje testy jednotek pro model stránky indexu.</span><span class="sxs-lookup"><span data-stu-id="2d397-147">*IndexPageTests.cs* contains the unit tests for the Index page model.</span></span></li></ul> |
| <span data-ttu-id="2d397-148">*Nástroje*</span><span class="sxs-lookup"><span data-stu-id="2d397-148">*Utilities*</span></span>     | <span data-ttu-id="2d397-149">Obsahuje `TestDbContextOptions` metodu použitou k vytvoření nové možnosti kontextu databáze pro každý test jednotky dal, aby se databáze obnovila na svůj základní stav pro každý test.</span><span class="sxs-lookup"><span data-stu-id="2d397-149">Contains the `TestDbContextOptions` method used to create new database context options for each DAL unit test so that the database is reset to its baseline condition for each test.</span></span> |

<span data-ttu-id="2d397-150">Testovací rozhraní je [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="2d397-150">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="2d397-151">Rozhraní objektu pro napodobení je [MOQ](https://github.com/moq/moq4).</span><span class="sxs-lookup"><span data-stu-id="2d397-151">The object mocking framework is [Moq](https://github.com/moq/moq4).</span></span>

## <a name="unit-tests-of-the-data-access-layer-dal"></a><span data-ttu-id="2d397-152">Testování částí vrstvy přístupu k datům (DAL)</span><span class="sxs-lookup"><span data-stu-id="2d397-152">Unit tests of the data access layer (DAL)</span></span>

<span data-ttu-id="2d397-153">Aplikace zprávy obsahuje DAL se čtyřmi metodami, které jsou obsaženy ve `AppDbContext` třídě (*Src/ Razor PagesTestSample/data/AppDbContext. cs*).</span><span class="sxs-lookup"><span data-stu-id="2d397-153">The message app has a DAL with four methods contained in the `AppDbContext` class (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span></span> <span data-ttu-id="2d397-154">Každá metoda má jednu nebo dvě testy jednotek v testovací aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2d397-154">Each method has one or two unit tests in the test app.</span></span>

| <span data-ttu-id="2d397-155">DAL – metoda</span><span class="sxs-lookup"><span data-stu-id="2d397-155">DAL method</span></span>               | <span data-ttu-id="2d397-156">Funkce</span><span class="sxs-lookup"><span data-stu-id="2d397-156">Function</span></span>                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | <span data-ttu-id="2d397-157">Získá `List<Message>` z databáze seřazené podle `Text` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="2d397-157">Obtains a `List<Message>` from the database sorted by the `Text` property.</span></span> |
| `AddMessageAsync`        | <span data-ttu-id="2d397-158">Přidá do `Message` databáze.</span><span class="sxs-lookup"><span data-stu-id="2d397-158">Adds a `Message` to the database.</span></span>                                          |
| `DeleteAllMessagesAsync` | <span data-ttu-id="2d397-159">Odstraní všechny `Message` položky z databáze.</span><span class="sxs-lookup"><span data-stu-id="2d397-159">Deletes all `Message` entries from the database.</span></span>                           |
| `DeleteMessageAsync`     | <span data-ttu-id="2d397-160">Odstraní `Message` z databáze jednu z nich `Id` .</span><span class="sxs-lookup"><span data-stu-id="2d397-160">Deletes a single `Message` from the database by `Id`.</span></span>                      |

<span data-ttu-id="2d397-161">Testování částí DAL vyžaduje <xref:Microsoft.EntityFrameworkCore.DbContextOptions> při vytváření nového `AppDbContext` pro každý test.</span><span class="sxs-lookup"><span data-stu-id="2d397-161">Unit tests of the DAL require <xref:Microsoft.EntityFrameworkCore.DbContextOptions> when creating a new `AppDbContext` for each test.</span></span> <span data-ttu-id="2d397-162">Jedním ze způsobů, jak vytvořit `DbContextOptions` pro každý test, je použít <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> :</span><span class="sxs-lookup"><span data-stu-id="2d397-162">One approach to creating the `DbContextOptions` for each test is to use a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="2d397-163">Problém s tímto přístupem je, že každý test obdrží databázi v jakémkoli stavu, který předchozí test opustil.</span><span class="sxs-lookup"><span data-stu-id="2d397-163">The problem with this approach is that each test receives the database in whatever state the previous test left it.</span></span> <span data-ttu-id="2d397-164">To může být problematické při pokusu o zápis atomických testů, které mezi sebou nekolidují.</span><span class="sxs-lookup"><span data-stu-id="2d397-164">This can be problematic when trying to write atomic unit tests that don't interfere with each other.</span></span> <span data-ttu-id="2d397-165">Chcete-li vynutit `AppDbContext` použití nového kontextu databáze pro každý test, zadejte `DbContextOptions` instanci, která je založena na novém poskytovateli služeb.</span><span class="sxs-lookup"><span data-stu-id="2d397-165">To force the `AppDbContext` to use a new database context for each test, supply a `DbContextOptions` instance that's based on a new service provider.</span></span> <span data-ttu-id="2d397-166">Testovací aplikace ukazuje, jak to provést pomocí `Utilities` metody třídy `TestDbContextOptions` (*Tests/ Razor PagesTestSample. Tests/Utilities/Utilities. cs*):</span><span class="sxs-lookup"><span data-stu-id="2d397-166">The test app shows how to do this using its `Utilities` class method `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

<span data-ttu-id="2d397-167">Použití funkce `DbContextOptions` v testech jednotek dal umožňuje, aby každý test běžel atomicky s novou instancí databáze:</span><span class="sxs-lookup"><span data-stu-id="2d397-167">Using the `DbContextOptions` in the DAL unit tests allows each test to run atomically with a fresh database instance:</span></span>

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="2d397-168">Každá testovací metoda ve `DataAccessLayerTest` třídě (*UnitTests/DataAccessLayerTest. cs*) se řídí podobným vzorem příkazu uspořádat – Act-Assert:</span><span class="sxs-lookup"><span data-stu-id="2d397-168">Each test method in the `DataAccessLayerTest` class (*UnitTests/DataAccessLayerTest.cs*) follows a similar Arrange-Act-Assert pattern:</span></span>

1. <span data-ttu-id="2d397-169">Uspořádat: databáze je nakonfigurována pro test a/nebo je definován očekávaný výsledek.</span><span class="sxs-lookup"><span data-stu-id="2d397-169">Arrange: The database is configured for the test and/or the expected outcome is defined.</span></span>
1. <span data-ttu-id="2d397-170">ACT: test je proveden.</span><span class="sxs-lookup"><span data-stu-id="2d397-170">Act: The test is executed.</span></span>
1. <span data-ttu-id="2d397-171">Assert: kontrolní výrazy jsou určeny k určení, zda je výsledek testu úspěch.</span><span class="sxs-lookup"><span data-stu-id="2d397-171">Assert: Assertions are made to determine if the test result is a success.</span></span>

<span data-ttu-id="2d397-172">Například `DeleteMessageAsync` Metoda zodpovídá za odebrání jedné zprávy identifikované její `Id` (*Src/ Razor PagesTestSample/data/AppDbContext. cs*):</span><span class="sxs-lookup"><span data-stu-id="2d397-172">For example, the `DeleteMessageAsync` method is responsible for removing a single message identified by its `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

<span data-ttu-id="2d397-173">Existují dva testy pro tuto metodu.</span><span class="sxs-lookup"><span data-stu-id="2d397-173">There are two tests for this method.</span></span> <span data-ttu-id="2d397-174">Jeden test kontroluje, zda metoda odstraní zprávu, když se zpráva nachází v databázi.</span><span class="sxs-lookup"><span data-stu-id="2d397-174">One test checks that the method deletes a message when the message is present in the database.</span></span> <span data-ttu-id="2d397-175">Druhá metoda Testuje, že se databáze nemění, pokud zpráva `Id` pro odstranění neexistuje.</span><span class="sxs-lookup"><span data-stu-id="2d397-175">The other method tests that the database doesn't change if the message `Id` for deletion doesn't exist.</span></span> <span data-ttu-id="2d397-176">`DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`Metoda je zobrazena níže:</span><span class="sxs-lookup"><span data-stu-id="2d397-176">The `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` method is shown below:</span></span>

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="2d397-177">Nejprve metoda provede krok uspořádat, kde se provádí příprava pro krok Act.</span><span class="sxs-lookup"><span data-stu-id="2d397-177">First, the method performs the Arrange step, where preparation for the Act step takes place.</span></span> <span data-ttu-id="2d397-178">Zprávy o osazení se získávají a uchovávají v `seedMessages` .</span><span class="sxs-lookup"><span data-stu-id="2d397-178">The seeding messages are obtained and held in `seedMessages`.</span></span> <span data-ttu-id="2d397-179">Počáteční zprávy se ukládají do databáze nástroje.</span><span class="sxs-lookup"><span data-stu-id="2d397-179">The seeding messages are saved into the database.</span></span> <span data-ttu-id="2d397-180">Zpráva s objektem `Id` `1` je nastavena pro odstranění.</span><span class="sxs-lookup"><span data-stu-id="2d397-180">The message with an `Id` of `1` is set for deletion.</span></span> <span data-ttu-id="2d397-181">Při `DeleteMessageAsync` spuštění metody musí mít očekávané zprávy všechny zprávy s výjimkou jednoho s `Id` `1` .</span><span class="sxs-lookup"><span data-stu-id="2d397-181">When the `DeleteMessageAsync` method is executed, the expected messages should have all of the messages except for the one with an `Id` of `1`.</span></span> <span data-ttu-id="2d397-182">`expectedMessages`Proměnná představuje tento očekávaný výsledek.</span><span class="sxs-lookup"><span data-stu-id="2d397-182">The `expectedMessages` variable represents this expected outcome.</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="2d397-183">Metoda funguje: `DeleteMessageAsync` Metoda je prováděna předáním v `recId` `1` :</span><span class="sxs-lookup"><span data-stu-id="2d397-183">The method acts: The `DeleteMessageAsync` method is executed passing in the `recId` of `1`:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

<span data-ttu-id="2d397-184">Nakonec Metoda získá `Messages` z kontextu a porovná ho s `expectedMessages` kontrolním výrazem, že obě jsou stejné:</span><span class="sxs-lookup"><span data-stu-id="2d397-184">Finally, the method obtains the `Messages` from the context and compares it to the `expectedMessages` asserting that the two are equal:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

<span data-ttu-id="2d397-185">Aby bylo možné porovnat tyto dvě `List<Message>` stejné:</span><span class="sxs-lookup"><span data-stu-id="2d397-185">In order to compare that the two `List<Message>` are the same:</span></span>

* <span data-ttu-id="2d397-186">Zprávy jsou seřazeny podle `Id` .</span><span class="sxs-lookup"><span data-stu-id="2d397-186">The messages are ordered by `Id`.</span></span>
* <span data-ttu-id="2d397-187">Páry zpráv jsou porovnány s `Text` vlastností.</span><span class="sxs-lookup"><span data-stu-id="2d397-187">Message pairs are compared on the `Text` property.</span></span>

<span data-ttu-id="2d397-188">Podobná testovací metoda `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` kontroluje výsledek pokusu o odstranění neexistující zprávy.</span><span class="sxs-lookup"><span data-stu-id="2d397-188">A similar test method, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` checks the result of attempting to delete a message that doesn't exist.</span></span> <span data-ttu-id="2d397-189">V takovém případě by měly být očekávané zprávy v databázi rovny skutečným zprávám po `DeleteMessageAsync` provedení metody.</span><span class="sxs-lookup"><span data-stu-id="2d397-189">In this case, the expected messages in the database should be equal to the actual messages after the `DeleteMessageAsync` method is executed.</span></span> <span data-ttu-id="2d397-190">Obsah databáze by neměl být změněn:</span><span class="sxs-lookup"><span data-stu-id="2d397-190">There should be no change to the database's content:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a><span data-ttu-id="2d397-191">Testování částí metod modelu stránky</span><span class="sxs-lookup"><span data-stu-id="2d397-191">Unit tests of the page model methods</span></span>

<span data-ttu-id="2d397-192">Další sada testů jednotek zodpovídá za testy metod modelu stránky.</span><span class="sxs-lookup"><span data-stu-id="2d397-192">Another set of unit tests is responsible for tests of page model methods.</span></span> <span data-ttu-id="2d397-193">V aplikaci zprávy jsou modely stránek indexu nalezeny ve `IndexModel` třídě v *Src/ Razor PagesTestSample/pages/index. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="2d397-193">In the message app, the Index page models are found in the `IndexModel` class in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.</span></span>

| <span data-ttu-id="2d397-194">Metoda modelu stránky</span><span class="sxs-lookup"><span data-stu-id="2d397-194">Page model method</span></span> | <span data-ttu-id="2d397-195">Funkce</span><span class="sxs-lookup"><span data-stu-id="2d397-195">Function</span></span> |
| ----------------- | -------- |
| `OnGetAsync` | <span data-ttu-id="2d397-196">Získá zprávy z DAL pro uživatelské rozhraní pomocí `GetMessagesAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="2d397-196">Obtains the messages from the DAL for the UI using the `GetMessagesAsync` method.</span></span> |
| `OnPostAddMessageAsync` | <span data-ttu-id="2d397-197">Pokud je [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) platný, volání `AddMessageAsync` pro přidání zprávy do databáze.</span><span class="sxs-lookup"><span data-stu-id="2d397-197">If the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is valid, calls `AddMessageAsync` to add a message to the database.</span></span> |
| `OnPostDeleteAllMessagesAsync` | <span data-ttu-id="2d397-198">Volá `DeleteAllMessagesAsync` se, aby se odstranily všechny zprávy v databázi.</span><span class="sxs-lookup"><span data-stu-id="2d397-198">Calls `DeleteAllMessagesAsync` to delete all of the messages in the database.</span></span> |
| `OnPostDeleteMessageAsync` | <span data-ttu-id="2d397-199">Provede `DeleteMessageAsync` odstranění zprávy se `Id` zadaným.</span><span class="sxs-lookup"><span data-stu-id="2d397-199">Executes `DeleteMessageAsync` to delete a message with the `Id` specified.</span></span> |
| `OnPostAnalyzeMessagesAsync` | <span data-ttu-id="2d397-200">Pokud je v databázi jedna nebo více zpráv, vypočítá průměrný počet slov na jednu zprávu.</span><span class="sxs-lookup"><span data-stu-id="2d397-200">If one or more messages are in the database, calculates the average number of words per message.</span></span> |

<span data-ttu-id="2d397-201">Metody modelu stránky jsou testovány pomocí sedmi testů ve `IndexPageTests` třídě (*Tests/ Razor PagesTestSample. Tests/UnitTests/IndexPageTests. cs*).</span><span class="sxs-lookup"><span data-stu-id="2d397-201">The page model methods are tested using seven tests in the `IndexPageTests` class (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span></span> <span data-ttu-id="2d397-202">Testy používají známý vzor uspořádání a vyhodnocení – Act.</span><span class="sxs-lookup"><span data-stu-id="2d397-202">The tests use the familiar Arrange-Assert-Act pattern.</span></span> <span data-ttu-id="2d397-203">Tyto testy se zaměřují na:</span><span class="sxs-lookup"><span data-stu-id="2d397-203">These tests focus on:</span></span>

* <span data-ttu-id="2d397-204">Určení, zda metody dodrží správné chování, pokud je [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) neplatné.</span><span class="sxs-lookup"><span data-stu-id="2d397-204">Determining if the methods follow the correct behavior when the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is invalid.</span></span>
* <span data-ttu-id="2d397-205">Potvrzení metod vyprodukuje správné <xref:Microsoft.AspNetCore.Mvc.IActionResult> .</span><span class="sxs-lookup"><span data-stu-id="2d397-205">Confirming the methods produce the correct <xref:Microsoft.AspNetCore.Mvc.IActionResult>.</span></span>
* <span data-ttu-id="2d397-206">Kontroluje se, jestli jsou přiřazení hodnot vlastností správně vytvořená.</span><span class="sxs-lookup"><span data-stu-id="2d397-206">Checking that property value assignments are made correctly.</span></span>

<span data-ttu-id="2d397-207">Tato skupina testů často napodobuje metody DAL, aby vytvořila očekávaná data pro krok Act, kde je spuštěna metoda modelu stránky.</span><span class="sxs-lookup"><span data-stu-id="2d397-207">This group of tests often mock the methods of the DAL to produce expected data for the Act step where a page model method is executed.</span></span> <span data-ttu-id="2d397-208">Například `GetMessagesAsync` Metoda `AppDbContext` je napodobná, aby vytvořila výstup.</span><span class="sxs-lookup"><span data-stu-id="2d397-208">For example, the `GetMessagesAsync` method of the `AppDbContext` is mocked to produce output.</span></span> <span data-ttu-id="2d397-209">Když metoda modelu stránky spustí tuto metodu, vrátí výsledek.</span><span class="sxs-lookup"><span data-stu-id="2d397-209">When a page model method executes this method, the mock returns the result.</span></span> <span data-ttu-id="2d397-210">Data nepocházejí z databáze.</span><span class="sxs-lookup"><span data-stu-id="2d397-210">The data doesn't come from the database.</span></span> <span data-ttu-id="2d397-211">Tím se vytvoří předvídatelné a spolehlivé testovací podmínky pro použití DAL v testech modelu stránky.</span><span class="sxs-lookup"><span data-stu-id="2d397-211">This creates predictable, reliable test conditions for using the DAL in the page model tests.</span></span>

<span data-ttu-id="2d397-212">`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`Test ukazuje, jak je metoda popsána `GetMessagesAsync` pro model stránky:</span><span class="sxs-lookup"><span data-stu-id="2d397-212">The `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test shows how the `GetMessagesAsync` method is mocked for the page model:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="2d397-213">Při `OnGetAsync` spuštění metody v kroku Act volá metodu modelu stránky `GetMessagesAsync` .</span><span class="sxs-lookup"><span data-stu-id="2d397-213">When the `OnGetAsync` method is executed in the Act step, it calls the page model's `GetMessagesAsync` method.</span></span>

<span data-ttu-id="2d397-214">Krok testu jednotek krok (*Tests/ Razor PagesTestSample. Tests/UnitTests/IndexPageTests. cs*):</span><span class="sxs-lookup"><span data-stu-id="2d397-214">Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="2d397-215">`IndexPage` Metoda modelu stránky `OnGetAsync` (*Src/ Razor PagesTestSample/pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="2d397-215">`IndexPage` page model's `OnGetAsync` method (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

<span data-ttu-id="2d397-216">`GetMessagesAsync`Metoda v metodě dal nevrací výsledek pro toto volání metody.</span><span class="sxs-lookup"><span data-stu-id="2d397-216">The `GetMessagesAsync` method in the DAL doesn't return the result for this method call.</span></span> <span data-ttu-id="2d397-217">Napodobovaná verze metody vrátí výsledek.</span><span class="sxs-lookup"><span data-stu-id="2d397-217">The mocked version of the method returns the result.</span></span>

<span data-ttu-id="2d397-218">V `Assert` kroku jsou skutečné zprávy ( `actualMessages` ) přiřazeny z `Messages` vlastnosti modelu stránky.</span><span class="sxs-lookup"><span data-stu-id="2d397-218">In the `Assert` step, the actual messages (`actualMessages`) are assigned from the `Messages` property of the page model.</span></span> <span data-ttu-id="2d397-219">Při přiřazení zpráv se také provádí ověření typu.</span><span class="sxs-lookup"><span data-stu-id="2d397-219">A type check is also performed when the messages are assigned.</span></span> <span data-ttu-id="2d397-220">Očekávané a skutečné zprávy jsou porovnány podle jejich `Text` vlastností.</span><span class="sxs-lookup"><span data-stu-id="2d397-220">The expected and actual messages are compared by their `Text` properties.</span></span> <span data-ttu-id="2d397-221">Test vyhodnotí, že dvě `List<Message>` instance obsahují stejné zprávy.</span><span class="sxs-lookup"><span data-stu-id="2d397-221">The test asserts that the two `List<Message>` instances contain the same messages.</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

<span data-ttu-id="2d397-222">Jiné testy v této skupině vytvoří objekty modelu stránky, které obsahují <xref:Microsoft.AspNetCore.Http.DefaultHttpContext> , a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> , <xref:Microsoft.AspNetCore.Mvc.ActionContext> k vytvoření, a `PageContext` `ViewDataDictionary` `PageContext` .</span><span class="sxs-lookup"><span data-stu-id="2d397-222">Other tests in this group create page model objects that include the <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, an <xref:Microsoft.AspNetCore.Mvc.ActionContext> to establish the `PageContext`, a `ViewDataDictionary`, and a `PageContext`.</span></span> <span data-ttu-id="2d397-223">Ty jsou užitečné při provádění testů.</span><span class="sxs-lookup"><span data-stu-id="2d397-223">These are useful in conducting tests.</span></span> <span data-ttu-id="2d397-224">Například aplikace zprávy naváže `ModelState` chybu s <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> cílem ověřit, že <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> je při spuštění vrácena platná `OnPostAddMessageAsync` :</span><span class="sxs-lookup"><span data-stu-id="2d397-224">For example, the message app establishes a `ModelState` error with <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> to check that a valid <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> is returned when `OnPostAddMessageAsync` is executed:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a><span data-ttu-id="2d397-225">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="2d397-225">Additional resources</span></span>

* [<span data-ttu-id="2d397-226">Testování částí C# v .NET Core pomocí příkazu dotnet test a xUnit</span><span class="sxs-lookup"><span data-stu-id="2d397-226">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* <span data-ttu-id="2d397-227">[Testování částí kódu](/visualstudio/test/unit-test-your-code) (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="2d397-227">[Unit Test Your Code](/visualstudio/test/unit-test-your-code) (Visual Studio)</span></span>
* <xref:test/integration-tests>
* [<span data-ttu-id="2d397-228">xUnit.net</span><span class="sxs-lookup"><span data-stu-id="2d397-228">xUnit.net</span></span>](https://xunit.github.io/)
* [<span data-ttu-id="2d397-229">Vytvoření kompletního řešení .NET Core v systému macOS pomocí sady Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="2d397-229">Building a complete .NET Core solution on macOS using Visual Studio for Mac</span></span>](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [<span data-ttu-id="2d397-230">Začínáme s xUnit.net: použití .NET Core s příkazovým řádkem sady .NET SDK</span><span class="sxs-lookup"><span data-stu-id="2d397-230">Getting started with xUnit.net: Using .NET Core with the .NET SDK command line</span></span>](https://xunit.github.io/docs/getting-started-dotnet-core)
* [<span data-ttu-id="2d397-231">Moq</span><span class="sxs-lookup"><span data-stu-id="2d397-231">Moq</span></span>](https://github.com/moq/moq4)
* [<span data-ttu-id="2d397-232">Rychlý Start MOQ</span><span class="sxs-lookup"><span data-stu-id="2d397-232">Moq Quickstart</span></span>](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2d397-233">ASP.NET Core podporuje testování částí Razor aplikací Pages.</span><span class="sxs-lookup"><span data-stu-id="2d397-233">ASP.NET Core supports unit tests of Razor Pages apps.</span></span> <span data-ttu-id="2d397-234">Testy vrstvy přístupu k datům (DAL) a modelů stránek vám pomůžou zajistit:</span><span class="sxs-lookup"><span data-stu-id="2d397-234">Tests of the data access layer (DAL) and page models help ensure:</span></span>

* <span data-ttu-id="2d397-235">Části Razor aplikace stránky fungují nezávisle a společně jako jednotka během vytváření aplikací.</span><span class="sxs-lookup"><span data-stu-id="2d397-235">Parts of a Razor Pages app work independently and together as a unit during app construction.</span></span>
* <span data-ttu-id="2d397-236">Třídy a metody mají omezené obory zodpovědnosti.</span><span class="sxs-lookup"><span data-stu-id="2d397-236">Classes and methods have limited scopes of responsibility.</span></span>
* <span data-ttu-id="2d397-237">Další dokumentace existuje v tom, jak se má aplikace chovat.</span><span class="sxs-lookup"><span data-stu-id="2d397-237">Additional documentation exists on how the app should behave.</span></span>
* <span data-ttu-id="2d397-238">V průběhu automatizovaného sestavování a nasazování se nacházejí regrese, které se týkají chyb v rámci aktualizací kódu.</span><span class="sxs-lookup"><span data-stu-id="2d397-238">Regressions, which are errors brought about by updates to the code, are found during automated building and deployment.</span></span>

<span data-ttu-id="2d397-239">V tomto tématu se předpokládá, že máte základní znalosti o Razor aplikacích stránky a testování částí.</span><span class="sxs-lookup"><span data-stu-id="2d397-239">This topic assumes that you have a basic understanding of Razor Pages apps and unit tests.</span></span> <span data-ttu-id="2d397-240">Pokud nejste obeznámeni s Razor aplikacemi Pages nebo koncepty testování, přečtěte si následující témata:</span><span class="sxs-lookup"><span data-stu-id="2d397-240">If you're unfamiliar with Razor Pages apps or test concepts, see the following topics:</span></span>

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [<span data-ttu-id="2d397-241">Testování částí C# v .NET Core pomocí příkazu dotnet test a xUnit</span><span class="sxs-lookup"><span data-stu-id="2d397-241">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

<span data-ttu-id="2d397-242">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2d397-242">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2d397-243">Vzorový projekt se skládá ze dvou aplikací:</span><span class="sxs-lookup"><span data-stu-id="2d397-243">The sample project is composed of two apps:</span></span>

| <span data-ttu-id="2d397-244">Aplikace</span><span class="sxs-lookup"><span data-stu-id="2d397-244">App</span></span>         | <span data-ttu-id="2d397-245">Složka projektu</span><span class="sxs-lookup"><span data-stu-id="2d397-245">Project folder</span></span>                     | <span data-ttu-id="2d397-246">Popis</span><span class="sxs-lookup"><span data-stu-id="2d397-246">Description</span></span> |
| ----------- | ---------------------------------- | ----------- |
| <span data-ttu-id="2d397-247">Aplikace zprávy</span><span class="sxs-lookup"><span data-stu-id="2d397-247">Message app</span></span> | <span data-ttu-id="2d397-248">*src/ Razor PagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="2d397-248">*src/RazorPagesTestSample*</span></span>         | <span data-ttu-id="2d397-249">Umožňuje uživateli přidat zprávu, odstranit jednu zprávu, odstranit všechny zprávy a analyzovat zprávy (najít průměrný počet slov na zprávu).</span><span class="sxs-lookup"><span data-stu-id="2d397-249">Allows a user to add a message, delete one message, delete all messages, and analyze messages (find the average number of words per message).</span></span> |
| <span data-ttu-id="2d397-250">Testovací aplikace</span><span class="sxs-lookup"><span data-stu-id="2d397-250">Test app</span></span>    | <span data-ttu-id="2d397-251">*testuje/ Razor PagesTestSample. Tests*</span><span class="sxs-lookup"><span data-stu-id="2d397-251">*tests/RazorPagesTestSample.Tests*</span></span> | <span data-ttu-id="2d397-252">Slouží k testování modelu DAL a stránky indexu aplikace zprávy.</span><span class="sxs-lookup"><span data-stu-id="2d397-252">Used to unit test the DAL and Index page model of the message app.</span></span> |

<span data-ttu-id="2d397-253">Testy lze spustit pomocí integrovaných funkcí testu integrovaného vývojového prostředí (IDE), jako je například [Visual Studio](/visualstudio/test/unit-test-your-code) nebo [Visual Studio pro Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).</span><span class="sxs-lookup"><span data-stu-id="2d397-253">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](/visualstudio/test/unit-test-your-code) or [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).</span></span> <span data-ttu-id="2d397-254">Pokud používáte [Visual Studio Code](https://code.visualstudio.com/) nebo příkazový řádek, spusťte následující příkaz na příkazovém řádku ve složce *Tests/ Razor PagesTestSample. Tests* :</span><span class="sxs-lookup"><span data-stu-id="2d397-254">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesTestSample.Tests* folder:</span></span>

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a><span data-ttu-id="2d397-255">Organizace aplikace zprávy</span><span class="sxs-lookup"><span data-stu-id="2d397-255">Message app organization</span></span>

<span data-ttu-id="2d397-256">Zpráva aplikace je Razor systém zpráv s následujícími charakteristikami:</span><span class="sxs-lookup"><span data-stu-id="2d397-256">The message app is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="2d397-257">Stránka indexu aplikace (*pages/index. cshtml* a *pages/index. cshtml. cs*) poskytuje metody uživatelského rozhraní a modelu stránky pro řízení přidávání, odstraňování a analýzy zpráv (hledání průměrného počtu slov na jednu zprávu).</span><span class="sxs-lookup"><span data-stu-id="2d397-257">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (find the average number of words per message).</span></span>
* <span data-ttu-id="2d397-258">Zpráva je popsána `Message` třídou (*data/Message. cs*) se dvěma vlastnostmi: `Id` (klíč) a `Text` (zpráva).</span><span class="sxs-lookup"><span data-stu-id="2d397-258">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="2d397-259">`Text`Vlastnost je povinná a omezená na 200 znaků.</span><span class="sxs-lookup"><span data-stu-id="2d397-259">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="2d397-260">Zprávy se ukládají pomocí&#8224; [databáze Entity Framework v paměti](/ef/core/providers/in-memory/) .</span><span class="sxs-lookup"><span data-stu-id="2d397-260">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="2d397-261">Aplikace obsahuje ve své třídě kontext databáze DAL `AppDbContext` (*data/AppDbContext. cs*).</span><span class="sxs-lookup"><span data-stu-id="2d397-261">The app contains a DAL in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span> <span data-ttu-id="2d397-262">Metody DAL jsou označeny `virtual` , což umožňuje napodobovat metody pro použití v testech.</span><span class="sxs-lookup"><span data-stu-id="2d397-262">The DAL methods are marked `virtual`, which allows mocking the methods for use in the tests.</span></span>
* <span data-ttu-id="2d397-263">Pokud je databáze při spuštění aplikace prázdná, úložiště zpráv se inicializuje se třemi zprávami.</span><span class="sxs-lookup"><span data-stu-id="2d397-263">If the database is empty on app startup, the message store is initialized with three messages.</span></span> <span data-ttu-id="2d397-264">Tyto *osazené zprávy* se používají také v testech.</span><span class="sxs-lookup"><span data-stu-id="2d397-264">These *seeded messages* are also used in tests.</span></span>

<span data-ttu-id="2d397-265">&#8224;tématu EF, [test s pamětí](/ef/core/miscellaneous/testing/in-memory), vysvětluje, jak používat databázi v paměti pro testy pomocí MSTest.</span><span class="sxs-lookup"><span data-stu-id="2d397-265">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="2d397-266">Toto téma používá testovací rozhraní [xUnit](https://xunit.github.io/) .</span><span class="sxs-lookup"><span data-stu-id="2d397-266">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="2d397-267">Koncepty testů a testovací implementace v různých testovacích architekturách jsou podobné, ale nejsou totožné.</span><span class="sxs-lookup"><span data-stu-id="2d397-267">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="2d397-268">I když ukázková aplikace nepoužívá vzor úložiště a není efektivním příkladem [vzoru jednotka práce (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor stránky tyto vzory vývoje podporují.</span><span class="sxs-lookup"><span data-stu-id="2d397-268">Although the sample app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="2d397-269">Další informace najdete v tématu [navrhování vrstvy trvalosti infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) a <xref:mvc/controllers/testing> (ukázka implementuje vzor úložiště).</span><span class="sxs-lookup"><span data-stu-id="2d397-269">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and <xref:mvc/controllers/testing> (the sample implements the repository pattern).</span></span>

## <a name="test-app-organization"></a><span data-ttu-id="2d397-270">Organizace testovací aplikace</span><span class="sxs-lookup"><span data-stu-id="2d397-270">Test app organization</span></span>

<span data-ttu-id="2d397-271">Testovací aplikace je Konzolová aplikace ve složce *Tests/ Razor PagesTestSample. Tests* .</span><span class="sxs-lookup"><span data-stu-id="2d397-271">The test app is a console app inside the *tests/RazorPagesTestSample.Tests* folder.</span></span>

| <span data-ttu-id="2d397-272">Složka testovací aplikace</span><span class="sxs-lookup"><span data-stu-id="2d397-272">Test app folder</span></span> | <span data-ttu-id="2d397-273">Popis</span><span class="sxs-lookup"><span data-stu-id="2d397-273">Description</span></span> |
| --------------- | ----------- |
| <span data-ttu-id="2d397-274">*UnitTests*</span><span class="sxs-lookup"><span data-stu-id="2d397-274">*UnitTests*</span></span>     | <ul><li><span data-ttu-id="2d397-275">*DataAccessLayerTest.cs* obsahuje testy jednotek pro dal.</span><span class="sxs-lookup"><span data-stu-id="2d397-275">*DataAccessLayerTest.cs* contains the unit tests for the DAL.</span></span></li><li><span data-ttu-id="2d397-276">*IndexPageTests.cs* obsahuje testy jednotek pro model stránky indexu.</span><span class="sxs-lookup"><span data-stu-id="2d397-276">*IndexPageTests.cs* contains the unit tests for the Index page model.</span></span></li></ul> |
| <span data-ttu-id="2d397-277">*Nástroje*</span><span class="sxs-lookup"><span data-stu-id="2d397-277">*Utilities*</span></span>     | <span data-ttu-id="2d397-278">Obsahuje `TestDbContextOptions` metodu použitou k vytvoření nové možnosti kontextu databáze pro každý test jednotky dal, aby se databáze obnovila na svůj základní stav pro každý test.</span><span class="sxs-lookup"><span data-stu-id="2d397-278">Contains the `TestDbContextOptions` method used to create new database context options for each DAL unit test so that the database is reset to its baseline condition for each test.</span></span> |

<span data-ttu-id="2d397-279">Testovací rozhraní je [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="2d397-279">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="2d397-280">Rozhraní objektu pro napodobení je [MOQ](https://github.com/moq/moq4).</span><span class="sxs-lookup"><span data-stu-id="2d397-280">The object mocking framework is [Moq](https://github.com/moq/moq4).</span></span>

## <a name="unit-tests-of-the-data-access-layer-dal"></a><span data-ttu-id="2d397-281">Testování částí vrstvy přístupu k datům (DAL)</span><span class="sxs-lookup"><span data-stu-id="2d397-281">Unit tests of the data access layer (DAL)</span></span>

<span data-ttu-id="2d397-282">Aplikace zprávy obsahuje DAL se čtyřmi metodami, které jsou obsaženy ve `AppDbContext` třídě (*Src/ Razor PagesTestSample/data/AppDbContext. cs*).</span><span class="sxs-lookup"><span data-stu-id="2d397-282">The message app has a DAL with four methods contained in the `AppDbContext` class (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span></span> <span data-ttu-id="2d397-283">Každá metoda má jednu nebo dvě testy jednotek v testovací aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2d397-283">Each method has one or two unit tests in the test app.</span></span>

| <span data-ttu-id="2d397-284">DAL – metoda</span><span class="sxs-lookup"><span data-stu-id="2d397-284">DAL method</span></span>               | <span data-ttu-id="2d397-285">Funkce</span><span class="sxs-lookup"><span data-stu-id="2d397-285">Function</span></span>                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | <span data-ttu-id="2d397-286">Získá `List<Message>` z databáze seřazené podle `Text` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="2d397-286">Obtains a `List<Message>` from the database sorted by the `Text` property.</span></span> |
| `AddMessageAsync`        | <span data-ttu-id="2d397-287">Přidá do `Message` databáze.</span><span class="sxs-lookup"><span data-stu-id="2d397-287">Adds a `Message` to the database.</span></span>                                          |
| `DeleteAllMessagesAsync` | <span data-ttu-id="2d397-288">Odstraní všechny `Message` položky z databáze.</span><span class="sxs-lookup"><span data-stu-id="2d397-288">Deletes all `Message` entries from the database.</span></span>                           |
| `DeleteMessageAsync`     | <span data-ttu-id="2d397-289">Odstraní `Message` z databáze jednu z nich `Id` .</span><span class="sxs-lookup"><span data-stu-id="2d397-289">Deletes a single `Message` from the database by `Id`.</span></span>                      |

<span data-ttu-id="2d397-290">Testování částí DAL vyžaduje <xref:Microsoft.EntityFrameworkCore.DbContextOptions> při vytváření nového `AppDbContext` pro každý test.</span><span class="sxs-lookup"><span data-stu-id="2d397-290">Unit tests of the DAL require <xref:Microsoft.EntityFrameworkCore.DbContextOptions> when creating a new `AppDbContext` for each test.</span></span> <span data-ttu-id="2d397-291">Jedním ze způsobů, jak vytvořit `DbContextOptions` pro každý test, je použít <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> :</span><span class="sxs-lookup"><span data-stu-id="2d397-291">One approach to creating the `DbContextOptions` for each test is to use a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="2d397-292">Problém s tímto přístupem je, že každý test obdrží databázi v jakémkoli stavu, který předchozí test opustil.</span><span class="sxs-lookup"><span data-stu-id="2d397-292">The problem with this approach is that each test receives the database in whatever state the previous test left it.</span></span> <span data-ttu-id="2d397-293">To může být problematické při pokusu o zápis atomických testů, které mezi sebou nekolidují.</span><span class="sxs-lookup"><span data-stu-id="2d397-293">This can be problematic when trying to write atomic unit tests that don't interfere with each other.</span></span> <span data-ttu-id="2d397-294">Chcete-li vynutit `AppDbContext` použití nového kontextu databáze pro každý test, zadejte `DbContextOptions` instanci, která je založena na novém poskytovateli služeb.</span><span class="sxs-lookup"><span data-stu-id="2d397-294">To force the `AppDbContext` to use a new database context for each test, supply a `DbContextOptions` instance that's based on a new service provider.</span></span> <span data-ttu-id="2d397-295">Testovací aplikace ukazuje, jak to provést pomocí `Utilities` metody třídy `TestDbContextOptions` (*Tests/ Razor PagesTestSample. Tests/Utilities/Utilities. cs*):</span><span class="sxs-lookup"><span data-stu-id="2d397-295">The test app shows how to do this using its `Utilities` class method `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

<span data-ttu-id="2d397-296">Použití funkce `DbContextOptions` v testech jednotek dal umožňuje, aby každý test běžel atomicky s novou instancí databáze:</span><span class="sxs-lookup"><span data-stu-id="2d397-296">Using the `DbContextOptions` in the DAL unit tests allows each test to run atomically with a fresh database instance:</span></span>

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="2d397-297">Každá testovací metoda ve `DataAccessLayerTest` třídě (*UnitTests/DataAccessLayerTest. cs*) se řídí podobným vzorem příkazu uspořádat – Act-Assert:</span><span class="sxs-lookup"><span data-stu-id="2d397-297">Each test method in the `DataAccessLayerTest` class (*UnitTests/DataAccessLayerTest.cs*) follows a similar Arrange-Act-Assert pattern:</span></span>

1. <span data-ttu-id="2d397-298">Uspořádat: databáze je nakonfigurována pro test a/nebo je definován očekávaný výsledek.</span><span class="sxs-lookup"><span data-stu-id="2d397-298">Arrange: The database is configured for the test and/or the expected outcome is defined.</span></span>
1. <span data-ttu-id="2d397-299">ACT: test je proveden.</span><span class="sxs-lookup"><span data-stu-id="2d397-299">Act: The test is executed.</span></span>
1. <span data-ttu-id="2d397-300">Assert: kontrolní výrazy jsou určeny k určení, zda je výsledek testu úspěch.</span><span class="sxs-lookup"><span data-stu-id="2d397-300">Assert: Assertions are made to determine if the test result is a success.</span></span>

<span data-ttu-id="2d397-301">Například `DeleteMessageAsync` Metoda zodpovídá za odebrání jedné zprávy identifikované její `Id` (*Src/ Razor PagesTestSample/data/AppDbContext. cs*):</span><span class="sxs-lookup"><span data-stu-id="2d397-301">For example, the `DeleteMessageAsync` method is responsible for removing a single message identified by its `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

<span data-ttu-id="2d397-302">Existují dva testy pro tuto metodu.</span><span class="sxs-lookup"><span data-stu-id="2d397-302">There are two tests for this method.</span></span> <span data-ttu-id="2d397-303">Jeden test kontroluje, zda metoda odstraní zprávu, když se zpráva nachází v databázi.</span><span class="sxs-lookup"><span data-stu-id="2d397-303">One test checks that the method deletes a message when the message is present in the database.</span></span> <span data-ttu-id="2d397-304">Druhá metoda Testuje, že se databáze nemění, pokud zpráva `Id` pro odstranění neexistuje.</span><span class="sxs-lookup"><span data-stu-id="2d397-304">The other method tests that the database doesn't change if the message `Id` for deletion doesn't exist.</span></span> <span data-ttu-id="2d397-305">`DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`Metoda je zobrazena níže:</span><span class="sxs-lookup"><span data-stu-id="2d397-305">The `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` method is shown below:</span></span>

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="2d397-306">Nejprve metoda provede krok uspořádat, kde se provádí příprava pro krok Act.</span><span class="sxs-lookup"><span data-stu-id="2d397-306">First, the method performs the Arrange step, where preparation for the Act step takes place.</span></span> <span data-ttu-id="2d397-307">Zprávy o osazení se získávají a uchovávají v `seedMessages` .</span><span class="sxs-lookup"><span data-stu-id="2d397-307">The seeding messages are obtained and held in `seedMessages`.</span></span> <span data-ttu-id="2d397-308">Počáteční zprávy se ukládají do databáze nástroje.</span><span class="sxs-lookup"><span data-stu-id="2d397-308">The seeding messages are saved into the database.</span></span> <span data-ttu-id="2d397-309">Zpráva s objektem `Id` `1` je nastavena pro odstranění.</span><span class="sxs-lookup"><span data-stu-id="2d397-309">The message with an `Id` of `1` is set for deletion.</span></span> <span data-ttu-id="2d397-310">Při `DeleteMessageAsync` spuštění metody musí mít očekávané zprávy všechny zprávy s výjimkou jednoho s `Id` `1` .</span><span class="sxs-lookup"><span data-stu-id="2d397-310">When the `DeleteMessageAsync` method is executed, the expected messages should have all of the messages except for the one with an `Id` of `1`.</span></span> <span data-ttu-id="2d397-311">`expectedMessages`Proměnná představuje tento očekávaný výsledek.</span><span class="sxs-lookup"><span data-stu-id="2d397-311">The `expectedMessages` variable represents this expected outcome.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="2d397-312">Metoda funguje: `DeleteMessageAsync` Metoda je prováděna předáním v `recId` `1` :</span><span class="sxs-lookup"><span data-stu-id="2d397-312">The method acts: The `DeleteMessageAsync` method is executed passing in the `recId` of `1`:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

<span data-ttu-id="2d397-313">Nakonec Metoda získá `Messages` z kontextu a porovná ho s `expectedMessages` kontrolním výrazem, že obě jsou stejné:</span><span class="sxs-lookup"><span data-stu-id="2d397-313">Finally, the method obtains the `Messages` from the context and compares it to the `expectedMessages` asserting that the two are equal:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

<span data-ttu-id="2d397-314">Aby bylo možné porovnat tyto dvě `List<Message>` stejné:</span><span class="sxs-lookup"><span data-stu-id="2d397-314">In order to compare that the two `List<Message>` are the same:</span></span>

* <span data-ttu-id="2d397-315">Zprávy jsou seřazeny podle `Id` .</span><span class="sxs-lookup"><span data-stu-id="2d397-315">The messages are ordered by `Id`.</span></span>
* <span data-ttu-id="2d397-316">Páry zpráv jsou porovnány s `Text` vlastností.</span><span class="sxs-lookup"><span data-stu-id="2d397-316">Message pairs are compared on the `Text` property.</span></span>

<span data-ttu-id="2d397-317">Podobná testovací metoda `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` kontroluje výsledek pokusu o odstranění neexistující zprávy.</span><span class="sxs-lookup"><span data-stu-id="2d397-317">A similar test method, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` checks the result of attempting to delete a message that doesn't exist.</span></span> <span data-ttu-id="2d397-318">V takovém případě by měly být očekávané zprávy v databázi rovny skutečným zprávám po `DeleteMessageAsync` provedení metody.</span><span class="sxs-lookup"><span data-stu-id="2d397-318">In this case, the expected messages in the database should be equal to the actual messages after the `DeleteMessageAsync` method is executed.</span></span> <span data-ttu-id="2d397-319">Obsah databáze by neměl být změněn:</span><span class="sxs-lookup"><span data-stu-id="2d397-319">There should be no change to the database's content:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a><span data-ttu-id="2d397-320">Testování částí metod modelu stránky</span><span class="sxs-lookup"><span data-stu-id="2d397-320">Unit tests of the page model methods</span></span>

<span data-ttu-id="2d397-321">Další sada testů jednotek zodpovídá za testy metod modelu stránky.</span><span class="sxs-lookup"><span data-stu-id="2d397-321">Another set of unit tests is responsible for tests of page model methods.</span></span> <span data-ttu-id="2d397-322">V aplikaci zprávy jsou modely stránek indexu nalezeny ve `IndexModel` třídě v *Src/ Razor PagesTestSample/pages/index. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="2d397-322">In the message app, the Index page models are found in the `IndexModel` class in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.</span></span>

| <span data-ttu-id="2d397-323">Metoda modelu stránky</span><span class="sxs-lookup"><span data-stu-id="2d397-323">Page model method</span></span> | <span data-ttu-id="2d397-324">Funkce</span><span class="sxs-lookup"><span data-stu-id="2d397-324">Function</span></span> |
| ----------------- | -------- |
| `OnGetAsync` | <span data-ttu-id="2d397-325">Získá zprávy z DAL pro uživatelské rozhraní pomocí `GetMessagesAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="2d397-325">Obtains the messages from the DAL for the UI using the `GetMessagesAsync` method.</span></span> |
| `OnPostAddMessageAsync` | <span data-ttu-id="2d397-326">Pokud je [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) platný, volání `AddMessageAsync` pro přidání zprávy do databáze.</span><span class="sxs-lookup"><span data-stu-id="2d397-326">If the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is valid, calls `AddMessageAsync` to add a message to the database.</span></span> |
| `OnPostDeleteAllMessagesAsync` | <span data-ttu-id="2d397-327">Volá `DeleteAllMessagesAsync` se, aby se odstranily všechny zprávy v databázi.</span><span class="sxs-lookup"><span data-stu-id="2d397-327">Calls `DeleteAllMessagesAsync` to delete all of the messages in the database.</span></span> |
| `OnPostDeleteMessageAsync` | <span data-ttu-id="2d397-328">Provede `DeleteMessageAsync` odstranění zprávy se `Id` zadaným.</span><span class="sxs-lookup"><span data-stu-id="2d397-328">Executes `DeleteMessageAsync` to delete a message with the `Id` specified.</span></span> |
| `OnPostAnalyzeMessagesAsync` | <span data-ttu-id="2d397-329">Pokud je v databázi jedna nebo více zpráv, vypočítá průměrný počet slov na jednu zprávu.</span><span class="sxs-lookup"><span data-stu-id="2d397-329">If one or more messages are in the database, calculates the average number of words per message.</span></span> |

<span data-ttu-id="2d397-330">Metody modelu stránky jsou testovány pomocí sedmi testů ve `IndexPageTests` třídě (*Tests/ Razor PagesTestSample. Tests/UnitTests/IndexPageTests. cs*).</span><span class="sxs-lookup"><span data-stu-id="2d397-330">The page model methods are tested using seven tests in the `IndexPageTests` class (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span></span> <span data-ttu-id="2d397-331">Testy používají známý vzor uspořádání a vyhodnocení – Act.</span><span class="sxs-lookup"><span data-stu-id="2d397-331">The tests use the familiar Arrange-Assert-Act pattern.</span></span> <span data-ttu-id="2d397-332">Tyto testy se zaměřují na:</span><span class="sxs-lookup"><span data-stu-id="2d397-332">These tests focus on:</span></span>

* <span data-ttu-id="2d397-333">Určení, zda metody dodrží správné chování, pokud je [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) neplatné.</span><span class="sxs-lookup"><span data-stu-id="2d397-333">Determining if the methods follow the correct behavior when the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is invalid.</span></span>
* <span data-ttu-id="2d397-334">Potvrzení metod vyprodukuje správné <xref:Microsoft.AspNetCore.Mvc.IActionResult> .</span><span class="sxs-lookup"><span data-stu-id="2d397-334">Confirming the methods produce the correct <xref:Microsoft.AspNetCore.Mvc.IActionResult>.</span></span>
* <span data-ttu-id="2d397-335">Kontroluje se, jestli jsou přiřazení hodnot vlastností správně vytvořená.</span><span class="sxs-lookup"><span data-stu-id="2d397-335">Checking that property value assignments are made correctly.</span></span>

<span data-ttu-id="2d397-336">Tato skupina testů často napodobuje metody DAL, aby vytvořila očekávaná data pro krok Act, kde je spuštěna metoda modelu stránky.</span><span class="sxs-lookup"><span data-stu-id="2d397-336">This group of tests often mock the methods of the DAL to produce expected data for the Act step where a page model method is executed.</span></span> <span data-ttu-id="2d397-337">Například `GetMessagesAsync` Metoda `AppDbContext` je napodobná, aby vytvořila výstup.</span><span class="sxs-lookup"><span data-stu-id="2d397-337">For example, the `GetMessagesAsync` method of the `AppDbContext` is mocked to produce output.</span></span> <span data-ttu-id="2d397-338">Když metoda modelu stránky spustí tuto metodu, vrátí výsledek.</span><span class="sxs-lookup"><span data-stu-id="2d397-338">When a page model method executes this method, the mock returns the result.</span></span> <span data-ttu-id="2d397-339">Data nepocházejí z databáze.</span><span class="sxs-lookup"><span data-stu-id="2d397-339">The data doesn't come from the database.</span></span> <span data-ttu-id="2d397-340">Tím se vytvoří předvídatelné a spolehlivé testovací podmínky pro použití DAL v testech modelu stránky.</span><span class="sxs-lookup"><span data-stu-id="2d397-340">This creates predictable, reliable test conditions for using the DAL in the page model tests.</span></span>

<span data-ttu-id="2d397-341">`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`Test ukazuje, jak je metoda popsána `GetMessagesAsync` pro model stránky:</span><span class="sxs-lookup"><span data-stu-id="2d397-341">The `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test shows how the `GetMessagesAsync` method is mocked for the page model:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="2d397-342">Při `OnGetAsync` spuštění metody v kroku Act volá metodu modelu stránky `GetMessagesAsync` .</span><span class="sxs-lookup"><span data-stu-id="2d397-342">When the `OnGetAsync` method is executed in the Act step, it calls the page model's `GetMessagesAsync` method.</span></span>

<span data-ttu-id="2d397-343">Krok testu jednotek krok (*Tests/ Razor PagesTestSample. Tests/UnitTests/IndexPageTests. cs*):</span><span class="sxs-lookup"><span data-stu-id="2d397-343">Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="2d397-344">`IndexPage` Metoda modelu stránky `OnGetAsync` (*Src/ Razor PagesTestSample/pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="2d397-344">`IndexPage` page model's `OnGetAsync` method (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

<span data-ttu-id="2d397-345">`GetMessagesAsync`Metoda v metodě dal nevrací výsledek pro toto volání metody.</span><span class="sxs-lookup"><span data-stu-id="2d397-345">The `GetMessagesAsync` method in the DAL doesn't return the result for this method call.</span></span> <span data-ttu-id="2d397-346">Napodobovaná verze metody vrátí výsledek.</span><span class="sxs-lookup"><span data-stu-id="2d397-346">The mocked version of the method returns the result.</span></span>

<span data-ttu-id="2d397-347">V `Assert` kroku jsou skutečné zprávy ( `actualMessages` ) přiřazeny z `Messages` vlastnosti modelu stránky.</span><span class="sxs-lookup"><span data-stu-id="2d397-347">In the `Assert` step, the actual messages (`actualMessages`) are assigned from the `Messages` property of the page model.</span></span> <span data-ttu-id="2d397-348">Při přiřazení zpráv se také provádí ověření typu.</span><span class="sxs-lookup"><span data-stu-id="2d397-348">A type check is also performed when the messages are assigned.</span></span> <span data-ttu-id="2d397-349">Očekávané a skutečné zprávy jsou porovnány podle jejich `Text` vlastností.</span><span class="sxs-lookup"><span data-stu-id="2d397-349">The expected and actual messages are compared by their `Text` properties.</span></span> <span data-ttu-id="2d397-350">Test vyhodnotí, že dvě `List<Message>` instance obsahují stejné zprávy.</span><span class="sxs-lookup"><span data-stu-id="2d397-350">The test asserts that the two `List<Message>` instances contain the same messages.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

<span data-ttu-id="2d397-351">Jiné testy v této skupině vytvoří objekty modelu stránky, které obsahují <xref:Microsoft.AspNetCore.Http.DefaultHttpContext> , a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> , <xref:Microsoft.AspNetCore.Mvc.ActionContext> k vytvoření, a `PageContext` `ViewDataDictionary` `PageContext` .</span><span class="sxs-lookup"><span data-stu-id="2d397-351">Other tests in this group create page model objects that include the <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, an <xref:Microsoft.AspNetCore.Mvc.ActionContext> to establish the `PageContext`, a `ViewDataDictionary`, and a `PageContext`.</span></span> <span data-ttu-id="2d397-352">Ty jsou užitečné při provádění testů.</span><span class="sxs-lookup"><span data-stu-id="2d397-352">These are useful in conducting tests.</span></span> <span data-ttu-id="2d397-353">Například aplikace zprávy naváže `ModelState` chybu s <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> cílem ověřit, že <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> je při spuštění vrácena platná `OnPostAddMessageAsync` :</span><span class="sxs-lookup"><span data-stu-id="2d397-353">For example, the message app establishes a `ModelState` error with <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> to check that a valid <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> is returned when `OnPostAddMessageAsync` is executed:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a><span data-ttu-id="2d397-354">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="2d397-354">Additional resources</span></span>

* [<span data-ttu-id="2d397-355">Testování částí C# v .NET Core pomocí příkazu dotnet test a xUnit</span><span class="sxs-lookup"><span data-stu-id="2d397-355">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* <span data-ttu-id="2d397-356">[Testování částí kódu](/visualstudio/test/unit-test-your-code) (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="2d397-356">[Unit Test Your Code](/visualstudio/test/unit-test-your-code) (Visual Studio)</span></span>
* <xref:test/integration-tests>
* [<span data-ttu-id="2d397-357">xUnit.net</span><span class="sxs-lookup"><span data-stu-id="2d397-357">xUnit.net</span></span>](https://xunit.github.io/)
* [<span data-ttu-id="2d397-358">Vytvoření kompletního řešení .NET Core v systému macOS pomocí sady Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="2d397-358">Building a complete .NET Core solution on macOS using Visual Studio for Mac</span></span>](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [<span data-ttu-id="2d397-359">Začínáme s xUnit.net: použití .NET Core s příkazovým řádkem sady .NET SDK</span><span class="sxs-lookup"><span data-stu-id="2d397-359">Getting started with xUnit.net: Using .NET Core with the .NET SDK command line</span></span>](https://xunit.github.io/docs/getting-started-dotnet-core)
* [<span data-ttu-id="2d397-360">Moq</span><span class="sxs-lookup"><span data-stu-id="2d397-360">Moq</span></span>](https://github.com/moq/moq4)
* [<span data-ttu-id="2d397-361">Rychlý Start MOQ</span><span class="sxs-lookup"><span data-stu-id="2d397-361">Moq Quickstart</span></span>](https://github.com/Moq/moq4/wiki/Quickstart)
* <span data-ttu-id="2d397-362">[JustMockLite](https://github.com/telerik/JustMockLite): podobná architektura pro vývojáře na platformě .NET.</span><span class="sxs-lookup"><span data-stu-id="2d397-362">[JustMockLite](https://github.com/telerik/JustMockLite): A mocking framework for .NET developers.</span></span> <span data-ttu-id="2d397-363">(*Společnost Microsoft nespravuje ani nepodporuje.*)</span><span class="sxs-lookup"><span data-stu-id="2d397-363">(*Not maintained or supported by Microsoft.*)</span></span>

::: moniker-end
