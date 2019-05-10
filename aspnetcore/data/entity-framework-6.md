---
title: Začínáme s ASP.NET Core a Entity Framework 6
author: rick-anderson
description: Tento článek ukazuje, jak pomocí Entity Framework 6 v aplikaci ASP.NET Core.
ms.author: tdykstra
ms.custom: mvc
ms.date: 10/24/2018
uid: data/entity-framework-6
ms.openlocfilehash: 50f51a72341cbad017872df581da214211b9920a
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64900615"
---
# <a name="get-started-with-aspnet-core-and-entity-framework-6"></a><span data-ttu-id="4ceed-103">Začínáme s ASP.NET Core a Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="4ceed-103">Get Started with ASP.NET Core and Entity Framework 6</span></span>

<span data-ttu-id="4ceed-104">Podle [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), a [Petr Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="4ceed-104">By [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="4ceed-105">Tento článek ukazuje, jak pomocí Entity Framework 6 v aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ceed-105">This article shows how to use Entity Framework 6 in an ASP.NET Core application.</span></span>

## <a name="overview"></a><span data-ttu-id="4ceed-106">Přehled</span><span class="sxs-lookup"><span data-stu-id="4ceed-106">Overview</span></span>

<span data-ttu-id="4ceed-107">Pokud chcete používat Entity Framework 6, projekt obsahuje kompilovat proti rozhraní .NET Framework jako Entity Framework 6 nepodporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ceed-107">To use Entity Framework 6, your project has to compile against .NET Framework, as Entity Framework 6 doesn't support .NET Core.</span></span> <span data-ttu-id="4ceed-108">Pokud potřebujete funkce napříč platformami budete muset upgradovat na [Entity Framework Core](/ef/).</span><span class="sxs-lookup"><span data-stu-id="4ceed-108">If you need cross-platform features you will need to upgrade to [Entity Framework Core](/ef/).</span></span>

<span data-ttu-id="4ceed-109">Je doporučeným způsobem, jak pomocí Entity Framework 6 v aplikaci ASP.NET Core do kontextu EF6 a tříd modelu v knihovně tříd projektu, který cílí na úplné rozhraní framework.</span><span class="sxs-lookup"><span data-stu-id="4ceed-109">The recommended way to use Entity Framework 6 in an ASP.NET Core application is to put the EF6 context and model classes in a class library project that targets the full framework.</span></span> <span data-ttu-id="4ceed-110">Přidejte odkaz na knihovnu tříd z projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ceed-110">Add a reference to the class library from the ASP.NET Core project.</span></span> <span data-ttu-id="4ceed-111">Najdete v ukázce [řešení sady Visual Studio s EF6 a ASP.NET Core projekty](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span><span class="sxs-lookup"><span data-stu-id="4ceed-111">See the sample [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span></span>

<span data-ttu-id="4ceed-112">Nelze vložit objekt context EF6 v projektu aplikace ASP.NET Core, protože projekty .NET Core nepodporují všechny funkce, která EF6 příkazů, jako *povolení migrace* vyžadují.</span><span class="sxs-lookup"><span data-stu-id="4ceed-112">You can't put an EF6 context in an ASP.NET Core project because .NET Core projects don't support all of the functionality that EF6 commands such as *Enable-Migrations* require.</span></span>

<span data-ttu-id="4ceed-113">Bez ohledu na typ projektu, ve kterém můžete najít váš kontext EF6 pouze nástroje pro příkazový řádek EF6 pracovat EF6 kontextu.</span><span class="sxs-lookup"><span data-stu-id="4ceed-113">Regardless of project type in which you locate your EF6 context, only EF6 command-line tools work with an EF6 context.</span></span> <span data-ttu-id="4ceed-114">Například `Scaffold-DbContext` dostupná jenom v Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="4ceed-114">For example, `Scaffold-DbContext` is only available in Entity Framework Core.</span></span> <span data-ttu-id="4ceed-115">Pokud potřebujete zpětná analýza databáze do modelu EF6, přečtěte si téma [Code First pro existující databázi](https://msdn.microsoft.com/jj200620).</span><span class="sxs-lookup"><span data-stu-id="4ceed-115">If you need to do reverse engineering of a database into an EF6 model, see [Code First to an Existing Database](https://msdn.microsoft.com/jj200620).</span></span>

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a><span data-ttu-id="4ceed-116">Odkaz na úplné rozhraní framework a EF6 v projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4ceed-116">Reference full framework and EF6 in the ASP.NET Core project</span></span>

<span data-ttu-id="4ceed-117">Váš projekt ASP.NET Core musí odkazovat na rozhraní .NET framework a EF6.</span><span class="sxs-lookup"><span data-stu-id="4ceed-117">Your ASP.NET Core project needs to reference .NET framework and EF6.</span></span> <span data-ttu-id="4ceed-118">Například *.csproj* bude vypadat podobně jako v následujícím příkladu soubor projektu ASP.NET Core (jsou zobrazeny pouze relevantní části souboru).</span><span class="sxs-lookup"><span data-stu-id="4ceed-118">For example, the *.csproj* file of your ASP.NET Core project will look similar to the following example (only relevant parts of the file are shown).</span></span>

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]

<span data-ttu-id="4ceed-119">Při vytváření nového projektu, použijte **webová aplikace ASP.NET Core (.NET Framework)** šablony.</span><span class="sxs-lookup"><span data-stu-id="4ceed-119">When creating a new project, use the **ASP.NET Core Web Application (.NET Framework)** template.</span></span>

## <a name="handle-connection-strings"></a><span data-ttu-id="4ceed-120">Popisovač připojovacích řetězců</span><span class="sxs-lookup"><span data-stu-id="4ceed-120">Handle connection strings</span></span>

<span data-ttu-id="4ceed-121">EF6 nástroje příkazového řádku, které budete používat v projektu knihovny tříd EF6 vyžadují výchozí konstruktor, takže jejich instance kontextu.</span><span class="sxs-lookup"><span data-stu-id="4ceed-121">The EF6 command-line tools that you'll use in the EF6 class library project require a default constructor so they can instantiate the context.</span></span> <span data-ttu-id="4ceed-122">Ale budete pravděpodobně chtít zadat připojovací řetězec k použití v projektu ASP.NET Core, v takovém případě váš kontext konstruktor musí mít parametr, který umožňuje předat připojovací řetězec.</span><span class="sxs-lookup"><span data-stu-id="4ceed-122">But you'll probably want to specify the connection string to use in the ASP.NET Core project, in which case your context constructor must have a parameter that lets you pass in the connection string.</span></span> <span data-ttu-id="4ceed-123">Tady je příklad.</span><span class="sxs-lookup"><span data-stu-id="4ceed-123">Here's an example.</span></span>

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]

<span data-ttu-id="4ceed-124">Vzhledem k tomu, že váš kontext EF6 nemá konstruktor bez parametrů, musí poskytnout implementaci položky projektu EF6 [IDbContextFactory](https://msdn.microsoft.com/library/hh506876).</span><span class="sxs-lookup"><span data-stu-id="4ceed-124">Since your EF6 context doesn't have a parameterless constructor, your EF6 project has to provide an implementation of [IDbContextFactory](https://msdn.microsoft.com/library/hh506876).</span></span> <span data-ttu-id="4ceed-125">Nástroje příkazového řádku EF6 vyhledá a použít tuto implementaci, takže se můžete vytvořit instanci kontextu.</span><span class="sxs-lookup"><span data-stu-id="4ceed-125">The EF6 command-line tools will find and use that implementation so they can instantiate the context.</span></span> <span data-ttu-id="4ceed-126">Tady je příklad.</span><span class="sxs-lookup"><span data-stu-id="4ceed-126">Here's an example.</span></span>

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]

<span data-ttu-id="4ceed-127">V tomto ukázkovém kódu `IDbContextFactory` implementace předá pevně zakódovaných propojovacích řetězců.</span><span class="sxs-lookup"><span data-stu-id="4ceed-127">In this sample code, the `IDbContextFactory` implementation passes in a hard-coded connection string.</span></span> <span data-ttu-id="4ceed-128">Toto je připojovací řetězec, který bude používat nástroje příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="4ceed-128">This is the connection string that the command-line tools will use.</span></span> <span data-ttu-id="4ceed-129">Bude potřeba implementovat strategii Ujistěte se, že knihovna tříd používá stejný připojovací řetězec, který používá volající aplikace.</span><span class="sxs-lookup"><span data-stu-id="4ceed-129">You'll want to implement a strategy to ensure that the class library uses the same connection string that the calling application uses.</span></span> <span data-ttu-id="4ceed-130">Například může získat hodnotu z proměnné prostředí v obou projektů.</span><span class="sxs-lookup"><span data-stu-id="4ceed-130">For example, you could get the value from an environment variable in both projects.</span></span>

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a><span data-ttu-id="4ceed-131">Nastavit injektáž závislostí v projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4ceed-131">Set up dependency injection in the ASP.NET Core project</span></span>

<span data-ttu-id="4ceed-132">V projektu Core *Startup.cs* soubor nastavení EF6 kontext pro injektáž závislostí (DI) `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="4ceed-132">In the Core project's *Startup.cs* file, set up the EF6 context for dependency injection (DI) in `ConfigureServices`.</span></span> <span data-ttu-id="4ceed-133">EF kontextové objekty by měly být omezeny na celý život jednotlivých žádostí.</span><span class="sxs-lookup"><span data-stu-id="4ceed-133">EF context objects should be scoped for a per-request lifetime.</span></span>

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

<span data-ttu-id="4ceed-134">S použitím DI pak můžete získat instance kontextu ve vašich kontrolerech.</span><span class="sxs-lookup"><span data-stu-id="4ceed-134">You can then get an instance of the context in your controllers by using DI.</span></span> <span data-ttu-id="4ceed-135">Kód se podobá byste napsat pro kontext EF Core:</span><span class="sxs-lookup"><span data-stu-id="4ceed-135">The code is similar to what you'd write for an EF Core context:</span></span>

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]

## <a name="sample-application"></a><span data-ttu-id="4ceed-136">Ukázkové aplikace</span><span class="sxs-lookup"><span data-stu-id="4ceed-136">Sample application</span></span>

<span data-ttu-id="4ceed-137">Ukázkové aplikace práci, najdete v článku [ukázkové řešení sady Visual Studio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) , který doprovází tento článek.</span><span class="sxs-lookup"><span data-stu-id="4ceed-137">For a working sample application, see the [sample Visual Studio solution](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) that accompanies this article.</span></span>

<span data-ttu-id="4ceed-138">Tato ukázka je vytvořit úplně od začátku podle následujících kroků v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="4ceed-138">This sample can be created from scratch by the following steps in Visual Studio:</span></span>

* <span data-ttu-id="4ceed-139">Vytvořte řešení.</span><span class="sxs-lookup"><span data-stu-id="4ceed-139">Create a solution.</span></span>

* <span data-ttu-id="4ceed-140">**Přidat** > **nový projekt** > **webové** > **webová aplikace ASP.NET Core**</span><span class="sxs-lookup"><span data-stu-id="4ceed-140">**Add** > **New Project** > **Web** > **ASP.NET Core Web Application**</span></span>
  * <span data-ttu-id="4ceed-141">V dialogovém okně Výběr šablony projektu vyberte v rozevíracím seznamu rozhraní API a rozhraní .NET Framework</span><span class="sxs-lookup"><span data-stu-id="4ceed-141">In project template selection dialog, select API and .NET Framework in dropdown</span></span>

* <span data-ttu-id="4ceed-142">**Přidat** > **nový projekt** > **Windows Desktop** > **třídy knihovna (.NET Framework)**</span><span class="sxs-lookup"><span data-stu-id="4ceed-142">**Add** > **New Project** > **Windows Desktop** > **Class Library (.NET Framework)**</span></span>

* <span data-ttu-id="4ceed-143">V **Konzola správce balíčků** (PMC) u obou projektů, spusťte příkaz `Install-Package Entityframework`.</span><span class="sxs-lookup"><span data-stu-id="4ceed-143">In **Package Manager Console** (PMC) for both projects, run the command `Install-Package Entityframework`.</span></span>

* <span data-ttu-id="4ceed-144">V projektu knihovny tříd, vytvoření tříd datových modelů a třídu kontextu a implementace `IDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="4ceed-144">In the class library project, create data model classes and a context class, and an implementation of `IDbContextFactory`.</span></span>

* <span data-ttu-id="4ceed-145">V konzole PMC pro projekt knihovny tříd, spusťte příkazy `Enable-Migrations` a `Add-Migration Initial`.</span><span class="sxs-lookup"><span data-stu-id="4ceed-145">In PMC for the class library project, run the commands `Enable-Migrations` and `Add-Migration Initial`.</span></span> <span data-ttu-id="4ceed-146">Pokud nastavíte projekt ASP.NET Core jako spouštěný projekt, přidejte `-StartupProjectName EF6` těchto příkazů.</span><span class="sxs-lookup"><span data-stu-id="4ceed-146">If you have set the ASP.NET Core project as the startup project, add `-StartupProjectName EF6` to these commands.</span></span>

* <span data-ttu-id="4ceed-147">V Core projektu přidejte odkaz na projekt knihovny tříd.</span><span class="sxs-lookup"><span data-stu-id="4ceed-147">In the Core project, add a project reference to the class library project.</span></span>

* <span data-ttu-id="4ceed-148">V projektu jader v *Startup.cs*, zaregistrujte DI kontextu.</span><span class="sxs-lookup"><span data-stu-id="4ceed-148">In the Core project, in *Startup.cs*, register the context for DI.</span></span>

* <span data-ttu-id="4ceed-149">V projektu jader v *appsettings.json*, přidejte připojovací řetězec.</span><span class="sxs-lookup"><span data-stu-id="4ceed-149">In the Core project, in *appsettings.json*, add the connection string.</span></span>

* <span data-ttu-id="4ceed-150">V projektu Core přidáte kontroler a zobrazení, chcete-li ověřit, že může číst a zapisovat data.</span><span class="sxs-lookup"><span data-stu-id="4ceed-150">In the Core project, add a controller and view(s) to verify that you can read and write data.</span></span> <span data-ttu-id="4ceed-151">(Všimněte si, že generování uživatelského rozhraní ASP.NET Core MVC nebude fungovat s EF6 kontextu odkazován z knihovny tříd.)</span><span class="sxs-lookup"><span data-stu-id="4ceed-151">(Note that ASP.NET Core MVC scaffolding won't work with the EF6 context referenced from the class library.)</span></span>

## <a name="summary"></a><span data-ttu-id="4ceed-152">Souhrn</span><span class="sxs-lookup"><span data-stu-id="4ceed-152">Summary</span></span>

<span data-ttu-id="4ceed-153">Tento článek poskytuje základní pokyny pro používání Entity Framework 6 v aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ceed-153">This article has provided basic guidance for using Entity Framework 6 in an ASP.NET Core application.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4ceed-154">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4ceed-154">Additional resources</span></span>

* [<span data-ttu-id="4ceed-155">Entity Framework – konfigurace založená na kódu</span><span class="sxs-lookup"><span data-stu-id="4ceed-155">Entity Framework - Code-Based Configuration</span></span>](https://msdn.microsoft.com/data/jj680699.aspx)
