---
title: Testování částí stránky Razor v ASP.NET Core
author: guardrex
description: Zjistěte, jak vytvořit testy jednotek pro aplikace stránky Razor.
ms.author: riande
ms.custom: mvc
ms.date: 11/27/2017
uid: test/razor-pages-tests
ms.openlocfilehash: f0e47f975579dc114eaeda375028ec62696f58ed
ms.sourcegitcommit: 763af2cbdab0da62d1f1cfef4bcf787f251dfb5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2019
ms.locfileid: "67394742"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a><span data-ttu-id="75251-103">Testování částí stránky Razor v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="75251-103">Razor Pages unit tests in ASP.NET Core</span></span>

<span data-ttu-id="75251-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="75251-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="75251-105">ASP.NET Core podporuje testů jednotek aplikací pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="75251-105">ASP.NET Core supports unit tests of Razor Pages apps.</span></span> <span data-ttu-id="75251-106">Testy dat přístup layer (DAL) a zajištění modely stránky:</span><span class="sxs-lookup"><span data-stu-id="75251-106">Tests of the data access layer (DAL) and page models help ensure:</span></span>

* <span data-ttu-id="75251-107">Součástí aplikace Razor Pages pracovat nezávisle a společně jako celek během vytváření aplikace.</span><span class="sxs-lookup"><span data-stu-id="75251-107">Parts of a Razor Pages app work independently and together as a unit during app construction.</span></span>
* <span data-ttu-id="75251-108">Třídy a metody mají omezenou obory odpovědnosti.</span><span class="sxs-lookup"><span data-stu-id="75251-108">Classes and methods have limited scopes of responsibility.</span></span>
* <span data-ttu-id="75251-109">Další dokumentaci k existuje v chování aplikace.</span><span class="sxs-lookup"><span data-stu-id="75251-109">Additional documentation exists on how the app should behave.</span></span>
* <span data-ttu-id="75251-110">Regrese, které jsou způsobené aktualizace s kódem chyby, byly nalezeny během automatické vytváření a nasazení.</span><span class="sxs-lookup"><span data-stu-id="75251-110">Regressions, which are errors brought about by updates to the code, are found during automated building and deployment.</span></span>

<span data-ttu-id="75251-111">Toto téma předpokládá, že máte základní znalosti o aplikace Razor Pages a testy jednotek.</span><span class="sxs-lookup"><span data-stu-id="75251-111">This topic assumes that you have a basic understanding of Razor Pages apps and unit tests.</span></span> <span data-ttu-id="75251-112">Pokud nejste obeznámeni s Razor Pages aplikace nebo koncepty testu, naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="75251-112">If you're unfamiliar with Razor Pages apps or test concepts, see the following topics:</span></span>

* [<span data-ttu-id="75251-113">Úvod do Razor Pages</span><span class="sxs-lookup"><span data-stu-id="75251-113">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="75251-114">Začínáme se stránkami Razor</span><span class="sxs-lookup"><span data-stu-id="75251-114">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="75251-115">Testování jednotek C# v .NET Core pomocí příkazu dotnet test a xUnit</span><span class="sxs-lookup"><span data-stu-id="75251-115">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

<span data-ttu-id="75251-116">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="75251-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="75251-117">Ukázkový projekt se skládá ze dvou aplikací:</span><span class="sxs-lookup"><span data-stu-id="75251-117">The sample project is composed of two apps:</span></span>

| <span data-ttu-id="75251-118">Aplikace</span><span class="sxs-lookup"><span data-stu-id="75251-118">App</span></span>         | <span data-ttu-id="75251-119">Složka projektu</span><span class="sxs-lookup"><span data-stu-id="75251-119">Project folder</span></span>                        | <span data-ttu-id="75251-120">Popis</span><span class="sxs-lookup"><span data-stu-id="75251-120">Description</span></span> |
| ----------- | ------------------------------------- | ----------- |
| <span data-ttu-id="75251-121">Aplikace zprávy</span><span class="sxs-lookup"><span data-stu-id="75251-121">Message app</span></span> | <span data-ttu-id="75251-122">*src/RazorPagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="75251-122">*src/RazorPagesTestSample*</span></span>            | <span data-ttu-id="75251-123">Umožňuje uživateli přidat, toku nějaký tok odstranit, odstraňte všechny a analyzovat zprávy.</span><span class="sxs-lookup"><span data-stu-id="75251-123">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="75251-124">Test aplikace</span><span class="sxs-lookup"><span data-stu-id="75251-124">Test app</span></span>    | <span data-ttu-id="75251-125">*tests/RazorPagesTestSample.Tests*</span><span class="sxs-lookup"><span data-stu-id="75251-125">*tests/RazorPagesTestSample.Tests*</span></span>    | <span data-ttu-id="75251-126">Používá se pro testování částí aplikace zprávu: Vrstva přístupu k datům (DAL) a model Index stránky.</span><span class="sxs-lookup"><span data-stu-id="75251-126">Used to unit test the message app: Data access layer (DAL) and Index page model.</span></span> |

<span data-ttu-id="75251-127">Testy můžete spustit pomocí integrované testovací funkce integrované vývojové prostředí, například [sady Visual Studio](https://visualstudio.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="75251-127">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="75251-128">Pokud používáte [Visual Studio Code](https://code.visualstudio.com/) nebo příkazového řádku, spusťte následující příkaz na příkazovém řádku v *tests/RazorPagesTestSample.Tests* složky:</span><span class="sxs-lookup"><span data-stu-id="75251-128">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesTestSample.Tests* folder:</span></span>

```console
dotnet test
```

## <a name="message-app-organization"></a><span data-ttu-id="75251-129">Zpráva aplikace organizace</span><span class="sxs-lookup"><span data-stu-id="75251-129">Message app organization</span></span>

<span data-ttu-id="75251-130">Zpráva aplikace je jednoduchý systém zpráv pro stránky Razor s následujícími charakteristikami:</span><span class="sxs-lookup"><span data-stu-id="75251-130">The message app is a simple Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="75251-131">Index stránky aplikace (*Pages/Index.cshtml* a *Pages/Index.cshtml.cs*) poskytuje uživatelské rozhraní a stránky model metody řídit přidání, odstranění a analýzy zprávy (průměrná slov za zprávy) .</span><span class="sxs-lookup"><span data-stu-id="75251-131">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="75251-132">Zprávu je popsán `Message` třídy (*Data/Message.cs*) s dvě vlastnosti: `Id` (klíč) a `Text` (zprávy).</span><span class="sxs-lookup"><span data-stu-id="75251-132">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="75251-133">`Text` Vlastnost je požadováno a omezené na 200 znaků.</span><span class="sxs-lookup"><span data-stu-id="75251-133">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="75251-134">Zprávy jsou bezpečně uložené pomocí [databáze v paměti rozhraní Entity Framework](/ef/core/providers/in-memory/)&#8224;.</span><span class="sxs-lookup"><span data-stu-id="75251-134">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="75251-135">Aplikace obsahuje vrstvy přístupu k datům (DAL) ve své třídě kontext databáze `AppDbContext` (*Data/AppDbContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="75251-135">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span> <span data-ttu-id="75251-136">DAL metody jsou označeny `virtual`, což umožňuje napodobování metody pro použití v testech.</span><span class="sxs-lookup"><span data-stu-id="75251-136">The DAL methods are marked `virtual`, which allows mocking the methods for use in the tests.</span></span>
* <span data-ttu-id="75251-137">Pokud je při spuštění aplikace prázdné databáze, úložiště zpráv je inicializován pomocí tří zpráv.</span><span class="sxs-lookup"><span data-stu-id="75251-137">If the database is empty on app startup, the message store is initialized with three messages.</span></span> <span data-ttu-id="75251-138">Tyto *nasadí zprávy* se také používají v testech.</span><span class="sxs-lookup"><span data-stu-id="75251-138">These *seeded messages* are also used in tests.</span></span>

<span data-ttu-id="75251-139">&#8224;Téma EF [Test s InMemory](/ef/core/miscellaneous/testing/in-memory), vysvětluje, jak používat databázi v paměti pro testy s použitím MSTest.</span><span class="sxs-lookup"><span data-stu-id="75251-139">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="75251-140">Toto téma používá [xUnit](https://xunit.github.io/) rozhraní pro testování.</span><span class="sxs-lookup"><span data-stu-id="75251-140">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="75251-141">Koncepty testu a testovací implementace napříč různými testovacími architektury jsou podobné, ale nejsou identické.</span><span class="sxs-lookup"><span data-stu-id="75251-141">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="75251-142">I když se aplikace nepoužívá model úložiště a není efektivní příklad [pracovní jednotka (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), stránky Razor podporuje tyto způsoby vývoje.</span><span class="sxs-lookup"><span data-stu-id="75251-142">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="75251-143">Další informace najdete v tématu [návrh vrstvy trvalosti infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) a [testovací kontroler logiku](/aspnet/core/mvc/controllers/testing) (ukázka implementuje vzor úložiště).</span><span class="sxs-lookup"><span data-stu-id="75251-143">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

## <a name="test-app-organization"></a><span data-ttu-id="75251-144">Testování aplikace organizace</span><span class="sxs-lookup"><span data-stu-id="75251-144">Test app organization</span></span>

<span data-ttu-id="75251-145">Aplikace testů je konzolová aplikace uvnitř *tests/RazorPagesTestSample.Tests* složky.</span><span class="sxs-lookup"><span data-stu-id="75251-145">The test app is a console app inside the *tests/RazorPagesTestSample.Tests* folder.</span></span>

| <span data-ttu-id="75251-146">Složky aplikace testu</span><span class="sxs-lookup"><span data-stu-id="75251-146">Test app folder</span></span> | <span data-ttu-id="75251-147">Popis</span><span class="sxs-lookup"><span data-stu-id="75251-147">Description</span></span> |
| --------------- | ----------- |
| <span data-ttu-id="75251-148">*UnitTests*</span><span class="sxs-lookup"><span data-stu-id="75251-148">*UnitTests*</span></span>     | <ul><li><span data-ttu-id="75251-149">*DataAccessLayerTest.cs* obsahuje testy jednotek pro vrstvy DAL.</span><span class="sxs-lookup"><span data-stu-id="75251-149">*DataAccessLayerTest.cs* contains the unit tests for the DAL.</span></span></li><li><span data-ttu-id="75251-150">*IndexPageTests.cs* obsahuje testy jednotek pro model Index stránky.</span><span class="sxs-lookup"><span data-stu-id="75251-150">*IndexPageTests.cs* contains the unit tests for the Index page model.</span></span></li></ul> |
| <span data-ttu-id="75251-151">*Nástroje*</span><span class="sxs-lookup"><span data-stu-id="75251-151">*Utilities*</span></span>     | <span data-ttu-id="75251-152">Obsahuje `TestDbContextOptions` metodu použitou k vytvoření nové databáze možnosti kontextu pro každý Jednotkový test DAL tak, aby se obnovení databáze do stavu směrný plán pro každý test.</span><span class="sxs-lookup"><span data-stu-id="75251-152">Contains the `TestDbContextOptions` method used to create new database context options for each DAL unit test so that the database is reset to its baseline condition for each test.</span></span> |

<span data-ttu-id="75251-153">Je rozhraní pro testování [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="75251-153">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="75251-154">Objekt napodobování framework [Moq](https://github.com/moq/moq4).</span><span class="sxs-lookup"><span data-stu-id="75251-154">The object mocking framework is [Moq](https://github.com/moq/moq4).</span></span>

## <a name="unit-tests-of-the-data-access-layer-dal"></a><span data-ttu-id="75251-155">Testování částí dat přístup layer (DAL)</span><span class="sxs-lookup"><span data-stu-id="75251-155">Unit tests of the data access layer (DAL)</span></span>

<span data-ttu-id="75251-156">Zpráva k němu má aplikace DAL se čtyři metody obsažené v `AppDbContext` třídy (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="75251-156">The message app has a DAL with four methods contained in the `AppDbContext` class (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span></span> <span data-ttu-id="75251-157">Každá metoda má jeden nebo dva testování částí v aplikaci test.</span><span class="sxs-lookup"><span data-stu-id="75251-157">Each method has one or two unit tests in the test app.</span></span>

| <span data-ttu-id="75251-158">DAL – metoda</span><span class="sxs-lookup"><span data-stu-id="75251-158">DAL method</span></span>               | <span data-ttu-id="75251-159">Funkce</span><span class="sxs-lookup"><span data-stu-id="75251-159">Function</span></span>                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | <span data-ttu-id="75251-160">Získá `List<Message>` z databáze, seřazené podle `Text` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="75251-160">Obtains a `List<Message>` from the database sorted by the `Text` property.</span></span> |
| `AddMessageAsync`        | <span data-ttu-id="75251-161">Přidá `Message` do databáze.</span><span class="sxs-lookup"><span data-stu-id="75251-161">Adds a `Message` to the database.</span></span>                                          |
| `DeleteAllMessagesAsync` | <span data-ttu-id="75251-162">Odstraní všechny `Message` položky z databáze.</span><span class="sxs-lookup"><span data-stu-id="75251-162">Deletes all `Message` entries from the database.</span></span>                           |
| `DeleteMessageAsync`     | <span data-ttu-id="75251-163">Odstraní jeden `Message` z databáze pomocí `Id`.</span><span class="sxs-lookup"><span data-stu-id="75251-163">Deletes a single `Message` from the database by `Id`.</span></span>                      |

<span data-ttu-id="75251-164">Testy jednotek DAL vyžadují [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) při vytváření nového `AppDbContext` pro každý test.</span><span class="sxs-lookup"><span data-stu-id="75251-164">Unit tests of the DAL require [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) when creating a new `AppDbContext` for each test.</span></span> <span data-ttu-id="75251-165">Jedním z přístupů k vytváření `DbContextOptions` pro každý test, je použít [DbContextOptionsBuilder](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptionsbuilder):</span><span class="sxs-lookup"><span data-stu-id="75251-165">One approach to creating the `DbContextOptions` for each test is to use a [DbContextOptionsBuilder](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptionsbuilder):</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="75251-166">Problém s tímto přístupem je, že každý test přijme databáze v libovolné stavu předchozí test nacházela.</span><span class="sxs-lookup"><span data-stu-id="75251-166">The problem with this approach is that each test receives the database in whatever state the previous test left it.</span></span> <span data-ttu-id="75251-167">To může být problematické, při pokusu o zápis atomickou jednotku testy, které není konfliktu mezi sebou.</span><span class="sxs-lookup"><span data-stu-id="75251-167">This can be problematic when trying to write atomic unit tests that don't interfere with each other.</span></span> <span data-ttu-id="75251-168">K vynucení `AppDbContext` pro každý test použít nový kontext databáze, zadejte `DbContextOptions` instanci, která je založena na nového poskytovatele služby.</span><span class="sxs-lookup"><span data-stu-id="75251-168">To force the `AppDbContext` to use a new database context for each test, supply a `DbContextOptions` instance that's based on a new service provider.</span></span> <span data-ttu-id="75251-169">Test aplikace ukazuje, jak to udělat pomocí jeho `Utilities` metoda třídy `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span><span class="sxs-lookup"><span data-stu-id="75251-169">The test app shows how to do this using its `Utilities` class method `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

<span data-ttu-id="75251-170">Použití `DbContextOptions` v jednotce DAL testů umožňuje každého testu ke spuštění atomicky s novou databází instancí:</span><span class="sxs-lookup"><span data-stu-id="75251-170">Using the `DbContextOptions` in the DAL unit tests allows each test to run atomically with a fresh database instance:</span></span>

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="75251-171">Jednotlivých testovacích metod v `DataAccessLayerTest` třídy (*UnitTests/DataAccessLayerTest.cs*) používá podobně jako kontrolní výraz uspořádat Act vzor:</span><span class="sxs-lookup"><span data-stu-id="75251-171">Each test method in the `DataAccessLayerTest` class (*UnitTests/DataAccessLayerTest.cs*) follows a similar Arrange-Act-Assert pattern:</span></span>

1. <span data-ttu-id="75251-172">Uspořádání: Databáze je nakonfigurovaná pro test a/nebo si očekávaný výsledek je definován.</span><span class="sxs-lookup"><span data-stu-id="75251-172">Arrange: The database is configured for the test and/or the expected outcome is defined.</span></span>
1. <span data-ttu-id="75251-173">Akce: Spuštění testu.</span><span class="sxs-lookup"><span data-stu-id="75251-173">Act: The test is executed.</span></span>
1. <span data-ttu-id="75251-174">Vyhodnocení: Kontrolní výrazy byly k určení, zda výsledek testu je úspěšné.</span><span class="sxs-lookup"><span data-stu-id="75251-174">Assert: Assertions are made to determine if the test result is a success.</span></span>

<span data-ttu-id="75251-175">Například `DeleteMessageAsync` metoda je zodpovědná za odebrání jedné zprávy identifikován jeho `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span><span class="sxs-lookup"><span data-stu-id="75251-175">For example, the `DeleteMessageAsync` method is responsible for removing a single message identified by its `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

<span data-ttu-id="75251-176">Existují dva testy pro tuto metodu.</span><span class="sxs-lookup"><span data-stu-id="75251-176">There are two tests for this method.</span></span> <span data-ttu-id="75251-177">Jeden test ověří, že metoda odstraní zprávu při zprávy se nachází v databázi.</span><span class="sxs-lookup"><span data-stu-id="75251-177">One test checks that the method deletes a message when the message is present in the database.</span></span> <span data-ttu-id="75251-178">Další metoda testy, které databázi se nemění, pokud zpráva `Id` pro odstranění neexistuje.</span><span class="sxs-lookup"><span data-stu-id="75251-178">The other method tests that the database doesn't change if the message `Id` for deletion doesn't exist.</span></span> <span data-ttu-id="75251-179">`DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` Metody jsou uvedené níže:</span><span class="sxs-lookup"><span data-stu-id="75251-179">The `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` method is shown below:</span></span>

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="75251-180">Nejprve provádí metoda uspořádat krok, kde probíhá příprava na krok Act.</span><span class="sxs-lookup"><span data-stu-id="75251-180">First, the method performs the Arrange step, where preparation for the Act step takes place.</span></span> <span data-ttu-id="75251-181">Získat a uchovávat v datovém typu osazení zpráv `seedMessages`.</span><span class="sxs-lookup"><span data-stu-id="75251-181">The seeding messages are obtained and held in `seedMessages`.</span></span> <span data-ttu-id="75251-182">Osazení zprávy se uloží do databáze.</span><span class="sxs-lookup"><span data-stu-id="75251-182">The seeding messages are saved into the database.</span></span> <span data-ttu-id="75251-183">Zpráva s `Id` z `1` nastavený pro odstranění.</span><span class="sxs-lookup"><span data-stu-id="75251-183">The message with an `Id` of `1` is set for deletion.</span></span> <span data-ttu-id="75251-184">Když `DeleteMessageAsync` provedení metody, očekávané zprávy musí mít všechny zprávy s kategorií s výjimkou `Id` z `1`.</span><span class="sxs-lookup"><span data-stu-id="75251-184">When the `DeleteMessageAsync` method is executed, the expected messages should have all of the messages except for the one with an `Id` of `1`.</span></span> <span data-ttu-id="75251-185">`expectedMessages` Proměnná představuje tento očekávaný výsledek.</span><span class="sxs-lookup"><span data-stu-id="75251-185">The `expectedMessages` variable represents this expected outcome.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="75251-186">Metoda funguje: `DeleteMessageAsync` Provedení metody předáním `recId` z `1`:</span><span class="sxs-lookup"><span data-stu-id="75251-186">The method acts: The `DeleteMessageAsync` method is executed passing in the `recId` of `1`:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

<span data-ttu-id="75251-187">Nakonec získá metodu `Messages` z kontextu a porovná ji `expectedMessages` potvrzující, že jsou dva stejné:</span><span class="sxs-lookup"><span data-stu-id="75251-187">Finally, the method obtains the `Messages` from the context and compares it to the `expectedMessages` asserting that the two are equal:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

<span data-ttu-id="75251-188">Aby bylo možné porovnat, který dva `List<Message>` jsou stejné:</span><span class="sxs-lookup"><span data-stu-id="75251-188">In order to compare that the two `List<Message>` are the same:</span></span>

* <span data-ttu-id="75251-189">Zprávy jsou řazeny podle `Id`.</span><span class="sxs-lookup"><span data-stu-id="75251-189">The messages are ordered by `Id`.</span></span>
* <span data-ttu-id="75251-190">Dvojice zprávy jsou porovnány na `Text` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="75251-190">Message pairs are compared on the `Text` property.</span></span>

<span data-ttu-id="75251-191">Podobně jako testovací metoda `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` zkontroluje výsledek pokusu o odstranění zprávy, která neexistuje.</span><span class="sxs-lookup"><span data-stu-id="75251-191">A similar test method, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` checks the result of attempting to delete a message that doesn't exist.</span></span> <span data-ttu-id="75251-192">V takovém případě musí být roven skutečné zprávy po očekávané zprávy v databázi `DeleteMessageAsync` provedení metody.</span><span class="sxs-lookup"><span data-stu-id="75251-192">In this case, the expected messages in the database should be equal to the actual messages after the `DeleteMessageAsync` method is executed.</span></span> <span data-ttu-id="75251-193">Měla by existovat bez nutnosti změn obsahu databáze:</span><span class="sxs-lookup"><span data-stu-id="75251-193">There should be no change to the database's content:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a><span data-ttu-id="75251-194">Testy jednotek metody modelu stránky</span><span class="sxs-lookup"><span data-stu-id="75251-194">Unit tests of the page model methods</span></span>

<span data-ttu-id="75251-195">Další sady testů jednotek je zodpovědná za testy model metody stránky.</span><span class="sxs-lookup"><span data-stu-id="75251-195">Another set of unit tests is responsible for tests of page model methods.</span></span> <span data-ttu-id="75251-196">V aplikaci zprávy jsou součástí modely Index stránky `IndexModel` třídy v *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="75251-196">In the message app, the Index page models are found in the `IndexModel` class in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.</span></span>

| <span data-ttu-id="75251-197">Metoda modelu stránky</span><span class="sxs-lookup"><span data-stu-id="75251-197">Page model method</span></span> | <span data-ttu-id="75251-198">Funkce</span><span class="sxs-lookup"><span data-stu-id="75251-198">Function</span></span> |
| ----------------- | -------- |
| `OnGetAsync` | <span data-ttu-id="75251-199">Získá zprávy z vrstvy DAL pomocí uživatelského rozhraní `GetMessagesAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="75251-199">Obtains the messages from the DAL for the UI using the `GetMessagesAsync` method.</span></span> |
| `OnPostAddMessageAsync` | <span data-ttu-id="75251-200">Pokud `ModelState` je platná, volá `AddMessageAsync` k přidání zprávy do databáze.</span><span class="sxs-lookup"><span data-stu-id="75251-200">If the `ModelState` is valid, calls `AddMessageAsync` to add a message to the database.</span></span> |
| `OnPostDeleteAllMessagesAsync` | <span data-ttu-id="75251-201">Volání `DeleteAllMessagesAsync` odstranit všechny zprávy v databázi.</span><span class="sxs-lookup"><span data-stu-id="75251-201">Calls `DeleteAllMessagesAsync` to delete all of the messages in the database.</span></span> |
| `OnPostDeleteMessageAsync` | <span data-ttu-id="75251-202">Spustí `DeleteMessageAsync` se má odstranit zpráva s `Id` zadané.</span><span class="sxs-lookup"><span data-stu-id="75251-202">Executes `DeleteMessageAsync` to delete a message with the `Id` specified.</span></span> |
| `OnPostAnalyzeMessagesAsync` | <span data-ttu-id="75251-203">Pokud jeden nebo více zpráv v databázi, vypočítá průměrný počet slov za zprávy.</span><span class="sxs-lookup"><span data-stu-id="75251-203">If one or more messages are in the database, calculates the average number of words per message.</span></span> |

<span data-ttu-id="75251-204">Model metody stránky jsou testovány pomocí sedm testy v `IndexPageTests` třídy (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span><span class="sxs-lookup"><span data-stu-id="75251-204">The page model methods are tested using seven tests in the `IndexPageTests` class (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span></span> <span data-ttu-id="75251-205">Testy pomocí známých uspořádat vyhodnocení Act vzor.</span><span class="sxs-lookup"><span data-stu-id="75251-205">The tests use the familiar Arrange-Assert-Act pattern.</span></span> <span data-ttu-id="75251-206">Zaměřte se na tyto testy:</span><span class="sxs-lookup"><span data-stu-id="75251-206">These tests focus on:</span></span>

* <span data-ttu-id="75251-207">Určení, pokud podle metody správné chování při `ModelState` je neplatný.</span><span class="sxs-lookup"><span data-stu-id="75251-207">Determining if the methods follow the correct behavior when the `ModelState` is invalid.</span></span>
* <span data-ttu-id="75251-208">Potvrzují se metody vytvoření správné `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="75251-208">Confirming the methods produce the correct `IActionResult`.</span></span>
* <span data-ttu-id="75251-209">Kontroluje se, že jsou správně provedené přiřazení hodnoty vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="75251-209">Checking that property value assignments are made correctly.</span></span>

<span data-ttu-id="75251-210">Tato skupina testy často napodobení metody vrstvy DAL k vytvoření očekávaná data Act krok, kde provedení metody modelu stránky.</span><span class="sxs-lookup"><span data-stu-id="75251-210">This group of tests often mock the methods of the DAL to produce expected data for the Act step where a page model method is executed.</span></span> <span data-ttu-id="75251-211">Například `GetMessagesAsync` metodu `AppDbContext` imitace výstup.</span><span class="sxs-lookup"><span data-stu-id="75251-211">For example, the `GetMessagesAsync` method of the `AppDbContext` is mocked to produce output.</span></span> <span data-ttu-id="75251-212">Jakmile model metoda stránky spustí tuto metodu, model vrátí výsledek.</span><span class="sxs-lookup"><span data-stu-id="75251-212">When a page model method executes this method, the mock returns the result.</span></span> <span data-ttu-id="75251-213">Data nepochází od databáze.</span><span class="sxs-lookup"><span data-stu-id="75251-213">The data doesn't come from the database.</span></span> <span data-ttu-id="75251-214">Tím se vytvoří předvídatelný a spolehlivý testovací podmínky pro použití vrstvy DAL v testech modelu stránky.</span><span class="sxs-lookup"><span data-stu-id="75251-214">This creates predictable, reliable test conditions for using the DAL in the page model tests.</span></span>

<span data-ttu-id="75251-215">`OnGetAsync_PopulatesThePageModel_WithAListOfMessages` Testování ukazuje jak `GetMessagesAsync` metoda je imitace modelu stránky:</span><span class="sxs-lookup"><span data-stu-id="75251-215">The `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test shows how the `GetMessagesAsync` method is mocked for the page model:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="75251-216">Když `OnGetAsync` provedení metody v kroku Act, volá model stránky `GetMessagesAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="75251-216">When the `OnGetAsync` method is executed in the Act step, it calls the page model's `GetMessagesAsync` method.</span></span>

<span data-ttu-id="75251-217">Act krok testu jednotek (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span><span class="sxs-lookup"><span data-stu-id="75251-217">Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="75251-218">`IndexPage` model stránky `OnGetAsync` – metoda (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="75251-218">`IndexPage` page model's `OnGetAsync` method (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

<span data-ttu-id="75251-219">`GetMessagesAsync` Metoda ve DAL nevrací výsledek pro toto volání metody.</span><span class="sxs-lookup"><span data-stu-id="75251-219">The `GetMessagesAsync` method in the DAL doesn't return the result for this method call.</span></span> <span data-ttu-id="75251-220">Imitaci verzi metody vrátí výsledek.</span><span class="sxs-lookup"><span data-stu-id="75251-220">The mocked version of the method returns the result.</span></span>

<span data-ttu-id="75251-221">V `Assert` krok, skutečné zprávy (`actualMessages`) se přidělují `Messages` vlastnost modelu stránky.</span><span class="sxs-lookup"><span data-stu-id="75251-221">In the `Assert` step, the actual messages (`actualMessages`) are assigned from the `Messages` property of the page model.</span></span> <span data-ttu-id="75251-222">Kontrola typu se také provádí při zprávy jsou přiřazeny.</span><span class="sxs-lookup"><span data-stu-id="75251-222">A type check is also performed when the messages are assigned.</span></span> <span data-ttu-id="75251-223">Očekávaných a aktuálních zpráv jsou porovnány pomocí jejich `Text` vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="75251-223">The expected and actual messages are compared by their `Text` properties.</span></span> <span data-ttu-id="75251-224">Test, který vyhodnotí dva `List<Message>` instance obsahují stejné zprávy.</span><span class="sxs-lookup"><span data-stu-id="75251-224">The test asserts that the two `List<Message>` instances contain the same messages.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

<span data-ttu-id="75251-225">Ostatní testy v této skupině vytvořit stránku objekty modelu, které zahrnují `DefaultHttpContext`, `ModelStateDictionary`, `ActionContext` navázat `PageContext`, `ViewDataDictionary`a `PageContext`.</span><span class="sxs-lookup"><span data-stu-id="75251-225">Other tests in this group create page model objects that include the `DefaultHttpContext`, the `ModelStateDictionary`, an `ActionContext` to establish the `PageContext`, a `ViewDataDictionary`, and a `PageContext`.</span></span> <span data-ttu-id="75251-226">Toto jsou užitečné při provádění testů.</span><span class="sxs-lookup"><span data-stu-id="75251-226">These are useful in conducting tests.</span></span> <span data-ttu-id="75251-227">Například vytváří aplikace zprávy `ModelState` chyba s `AddModelError` zkontroluje, jestli platný `PageResult` je vrácena, pokud `OnPostAddMessageAsync` spuštění:</span><span class="sxs-lookup"><span data-stu-id="75251-227">For example, the message app establishes a `ModelState` error with `AddModelError` to check that a valid `PageResult` is returned when `OnPostAddMessageAsync` is executed:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a><span data-ttu-id="75251-228">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="75251-228">Additional resources</span></span>

* [<span data-ttu-id="75251-229">Testování jednotek C# v .NET Core pomocí příkazu dotnet test a xUnit</span><span class="sxs-lookup"><span data-stu-id="75251-229">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [<span data-ttu-id="75251-230">Testovací kontrolery</span><span class="sxs-lookup"><span data-stu-id="75251-230">Test controllers</span></span>](xref:mvc/controllers/testing)
* <span data-ttu-id="75251-231">[Testování částí kódu](/visualstudio/test/unit-test-your-code) (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="75251-231">[Unit Test Your Code](/visualstudio/test/unit-test-your-code) (Visual Studio)</span></span>
* [<span data-ttu-id="75251-232">Integrační testy</span><span class="sxs-lookup"><span data-stu-id="75251-232">Integration tests</span></span>](xref:test/integration-tests)
* [<span data-ttu-id="75251-233">xUnit.net</span><span class="sxs-lookup"><span data-stu-id="75251-233">xUnit.net</span></span>](https://xunit.github.io/)
* [<span data-ttu-id="75251-234">Začínáme s xUnit.net (.NET Core/ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="75251-234">Getting started with xUnit.net (.NET Core/ASP.NET Core)</span></span>](https://xunit.github.io/docs/getting-started-dotnet-core)
* [<span data-ttu-id="75251-235">Moq</span><span class="sxs-lookup"><span data-stu-id="75251-235">Moq</span></span>](https://github.com/moq/moq4)
* [<span data-ttu-id="75251-236">Rychlý start Moq</span><span class="sxs-lookup"><span data-stu-id="75251-236">Moq Quickstart</span></span>](https://github.com/Moq/moq4/wiki/Quickstart)
