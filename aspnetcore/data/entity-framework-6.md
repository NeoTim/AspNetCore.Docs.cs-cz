---
title: Začínáme s ASP.NET core a entity Framework 6
author: rick-anderson
description: Tento článek ukazuje, jak používat entity Framework 6 v ASP.NET základní aplikace.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/entity-framework-6
ms.openlocfilehash: 85cf86dcb22ef94cfc87975abaab176e4f1227d3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656385"
---
# <a name="get-started-with-aspnet-core-and-entity-framework-6"></a><span data-ttu-id="0aaaf-103">Začínáme s ASP.NET core a entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="0aaaf-103">Get Started with ASP.NET Core and Entity Framework 6</span></span>

<span data-ttu-id="0aaaf-104">[Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex)a [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="0aaaf-104">By [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="0aaaf-105">Tento článek ukazuje, jak používat entity Framework 6 v ASP.NET základní aplikace.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-105">This article shows how to use Entity Framework 6 in an ASP.NET Core application.</span></span>

## <a name="overview"></a><span data-ttu-id="0aaaf-106">Přehled</span><span class="sxs-lookup"><span data-stu-id="0aaaf-106">Overview</span></span>

<span data-ttu-id="0aaaf-107">Chcete-li použít entity Framework 6, váš projekt musí zkompilovat proti rozhraní .NET Framework, protože entity Framework 6 nepodporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-107">To use Entity Framework 6, your project has to compile against .NET Framework, as Entity Framework 6 doesn't support .NET Core.</span></span> <span data-ttu-id="0aaaf-108">Pokud potřebujete funkce pro různé platformy, budete muset upgradovat na [core entity frameworku](/ef/).</span><span class="sxs-lookup"><span data-stu-id="0aaaf-108">If you need cross-platform features you will need to upgrade to [Entity Framework Core](/ef/).</span></span>

<span data-ttu-id="0aaaf-109">Doporučený způsob, jak použít entity Framework 6 v ASP.NET základní aplikace je umístit EF6 kontextu a třídy modelu v projektu knihovny tříd, který cíle .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-109">The recommended way to use Entity Framework 6 in an ASP.NET Core application is to put the EF6 context and model classes in a class library project that targets .NET Framework.</span></span> <span data-ttu-id="0aaaf-110">Přidejte odkaz na knihovnu tříd z projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-110">Add a reference to the class library from the ASP.NET Core project.</span></span> <span data-ttu-id="0aaaf-111">Podívejte se na ukázkové [řešení Visual Studio s EF6 a ASP.NET základní projekty](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span><span class="sxs-lookup"><span data-stu-id="0aaaf-111">See the sample [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span></span>

<span data-ttu-id="0aaaf-112">Nelze umístit kontextu EF6 v ASP.NET základní projekt, protože projekty .NET Core nepodporují všechny funkce, které ef6 příkazy, jako je *například Enable-Migrations* vyžadují.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-112">You can't put an EF6 context in an ASP.NET Core project because .NET Core projects don't support all of the functionality that EF6 commands such as *Enable-Migrations* require.</span></span>

<span data-ttu-id="0aaaf-113">Bez ohledu na typ projektu, ve kterém najdete kontext EF6, pouze EF6 nástroje příkazového řádku pracovat s kontextem EF6.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-113">Regardless of project type in which you locate your EF6 context, only EF6 command-line tools work with an EF6 context.</span></span> <span data-ttu-id="0aaaf-114">Například `Scaffold-DbContext` je k dispozici pouze v jádru entity frameworku.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-114">For example, `Scaffold-DbContext` is only available in Entity Framework Core.</span></span> <span data-ttu-id="0aaaf-115">Pokud potřebujete provést zpětnou analýzu databáze do modelu EF6, naleznete [v tématu Code First to a Existing Database](https://msdn.microsoft.com/jj200620).</span><span class="sxs-lookup"><span data-stu-id="0aaaf-115">If you need to do reverse engineering of a database into an EF6 model, see [Code First to an Existing Database](https://msdn.microsoft.com/jj200620).</span></span>

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a><span data-ttu-id="0aaaf-116">Odkaz na úplný rámec a EF6 v projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0aaaf-116">Reference full framework and EF6 in the ASP.NET Core project</span></span>

<span data-ttu-id="0aaaf-117">Váš ASP.NET základní projekt musí cílit na rozhraní .NET Framework a odkazovat na EF6.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-117">Your ASP.NET Core project needs to target .NET Framework and reference EF6.</span></span> <span data-ttu-id="0aaaf-118">Například soubor *.csproj* projektu ASP.NET Core bude vypadat podobně jako v následujícím příkladu (zobrazí se pouze relevantní části souboru).</span><span class="sxs-lookup"><span data-stu-id="0aaaf-118">For example, the *.csproj* file of your ASP.NET Core project will look similar to the following example (only relevant parts of the file are shown).</span></span>

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]

<span data-ttu-id="0aaaf-119">Při vytváření nového projektu použijte šablonu **ASP.NET Core Web Application (.NET Framework).**</span><span class="sxs-lookup"><span data-stu-id="0aaaf-119">When creating a new project, use the **ASP.NET Core Web Application (.NET Framework)** template.</span></span>

## <a name="handle-connection-strings"></a><span data-ttu-id="0aaaf-120">Zpracování připojovacích řetězců</span><span class="sxs-lookup"><span data-stu-id="0aaaf-120">Handle connection strings</span></span>

<span data-ttu-id="0aaaf-121">Nástroje příkazového řádku EF6, které budete používat v projektu knihovny tříd EF6, vyžadují výchozí konstruktor, aby mohly vytvořit konkretizovat kontext.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-121">The EF6 command-line tools that you'll use in the EF6 class library project require a default constructor so they can instantiate the context.</span></span> <span data-ttu-id="0aaaf-122">Ale budete pravděpodobně chtít zadat připojovací řetězec pro použití v projektu ASP.NET Core, v takovém případě váš konstruktor kontextu musí mít parametr, který umožňuje předat v připojovacím řetězci.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-122">But you'll probably want to specify the connection string to use in the ASP.NET Core project, in which case your context constructor must have a parameter that lets you pass in the connection string.</span></span> <span data-ttu-id="0aaaf-123">Tady je příklad.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-123">Here's an example.</span></span>

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]

<span data-ttu-id="0aaaf-124">Vzhledem k tomu, že váš kontext EF6 nemá konstruktor bez parametrů, váš projekt EF6 musí poskytnout implementaci [IDbContextFactory](https://msdn.microsoft.com/library/hh506876).</span><span class="sxs-lookup"><span data-stu-id="0aaaf-124">Since your EF6 context doesn't have a parameterless constructor, your EF6 project has to provide an implementation of [IDbContextFactory](https://msdn.microsoft.com/library/hh506876).</span></span> <span data-ttu-id="0aaaf-125">Ef6 nástroje příkazového řádku najde a použít tuto implementaci tak, aby mohly vytvořit konkretizovat kontext.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-125">The EF6 command-line tools will find and use that implementation so they can instantiate the context.</span></span> <span data-ttu-id="0aaaf-126">Tady je příklad.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-126">Here's an example.</span></span>

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]

<span data-ttu-id="0aaaf-127">V tomto ukázkovém `IDbContextFactory` kódu implementace předá v pevně zakódovaném připojovacím řetězci.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-127">In this sample code, the `IDbContextFactory` implementation passes in a hard-coded connection string.</span></span> <span data-ttu-id="0aaaf-128">Toto je připojovací řetězec, který budou používat nástroje příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-128">This is the connection string that the command-line tools will use.</span></span> <span data-ttu-id="0aaaf-129">Budete chtít implementovat strategii k zajištění, že knihovna tříd používá stejný připojovací řetězec, který používá volající aplikace.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-129">You'll want to implement a strategy to ensure that the class library uses the same connection string that the calling application uses.</span></span> <span data-ttu-id="0aaaf-130">Můžete například získat hodnotu z proměnné prostředí v obou projektech.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-130">For example, you could get the value from an environment variable in both projects.</span></span>

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a><span data-ttu-id="0aaaf-131">Nastavení vkládání závislostí v projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0aaaf-131">Set up dependency injection in the ASP.NET Core project</span></span>

<span data-ttu-id="0aaaf-132">V souboru *Startup.cs* základního projektu nastavte kontext EF6 pro vkládání závislostí (DI) v `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-132">In the Core project's *Startup.cs* file, set up the EF6 context for dependency injection (DI) in `ConfigureServices`.</span></span> <span data-ttu-id="0aaaf-133">Objekty kontextu EF by měly být vymezeny pro životnost požadavku.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-133">EF context objects should be scoped for a per-request lifetime.</span></span>

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

<span data-ttu-id="0aaaf-134">Potom můžete získat instanci kontextu v řadičích pomocí DI.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-134">You can then get an instance of the context in your controllers by using DI.</span></span> <span data-ttu-id="0aaaf-135">Kód je podobný tomu, co byste napsali pro kontext EF Core:</span><span class="sxs-lookup"><span data-stu-id="0aaaf-135">The code is similar to what you'd write for an EF Core context:</span></span>

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]

## <a name="sample-application"></a><span data-ttu-id="0aaaf-136">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="0aaaf-136">Sample application</span></span>

<span data-ttu-id="0aaaf-137">Pracovní ukázkové aplikace naleznete v ukázkové řešení [sady Visual Studio,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) které doprovází tento článek.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-137">For a working sample application, see the [sample Visual Studio solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) that accompanies this article.</span></span>

<span data-ttu-id="0aaaf-138">Tuto ukázku lze vytvořit od začátku pomocí následujících kroků v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="0aaaf-138">This sample can be created from scratch by the following steps in Visual Studio:</span></span>

* <span data-ttu-id="0aaaf-139">Vytvořte řešení.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-139">Create a solution.</span></span>

* <span data-ttu-id="0aaaf-140">**Přidat** > novou**webovou** > **ASP.NET základní webovou aplikaci** **projectu** > </span><span class="sxs-lookup"><span data-stu-id="0aaaf-140">**Add** > **New Project** > **Web** > **ASP.NET Core Web Application**</span></span>
  * <span data-ttu-id="0aaaf-141">V dialogovém okně výběru šablony projektu vyberte v rozevíracím seznami rozhraní API a rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-141">In project template selection dialog, select API and .NET Framework in dropdown</span></span>

* <span data-ttu-id="0aaaf-142">**Přidat** > **novou knihovnu** > tříd**desktopového prostředí** > **windows (.NET Framework)**</span><span class="sxs-lookup"><span data-stu-id="0aaaf-142">**Add** > **New Project** > **Windows Desktop** > **Class Library (.NET Framework)**</span></span>

* <span data-ttu-id="0aaaf-143">V **konzole Správce balíčků** (PMC) pro `Install-Package Entityframework`oba projekty spusťte příkaz .</span><span class="sxs-lookup"><span data-stu-id="0aaaf-143">In **Package Manager Console** (PMC) for both projects, run the command `Install-Package Entityframework`.</span></span>

* <span data-ttu-id="0aaaf-144">V projektu knihovny tříd vytvořte třídy datového modelu `IDbContextFactory`a třídu kontextu a implementaci aplikace .</span><span class="sxs-lookup"><span data-stu-id="0aaaf-144">In the class library project, create data model classes and a context class, and an implementation of `IDbContextFactory`.</span></span>

* <span data-ttu-id="0aaaf-145">V PMC pro projekt knihovny tříd `Enable-Migrations` `Add-Migration Initial`spusťte příkazy a .</span><span class="sxs-lookup"><span data-stu-id="0aaaf-145">In PMC for the class library project, run the commands `Enable-Migrations` and `Add-Migration Initial`.</span></span> <span data-ttu-id="0aaaf-146">Pokud jste nastavili ASP.NET základní projekt jako `-StartupProjectName EF6` projekt při spuštění, přidejte do těchto příkazů.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-146">If you have set the ASP.NET Core project as the startup project, add `-StartupProjectName EF6` to these commands.</span></span>

* <span data-ttu-id="0aaaf-147">V projektu Core přidejte odkaz na projekt projektu knihovny tříd.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-147">In the Core project, add a project reference to the class library project.</span></span>

* <span data-ttu-id="0aaaf-148">V projektu Core v *Startup.cs*zaregistrujte kontext pro DI.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-148">In the Core project, in *Startup.cs*, register the context for DI.</span></span>

* <span data-ttu-id="0aaaf-149">V projektu Core přidejte v *souboru appsettings.json*připojovací řetězec.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-149">In the Core project, in *appsettings.json*, add the connection string.</span></span>

* <span data-ttu-id="0aaaf-150">V projektu Core přidejte řadič a zobrazení, abyste ověřili, že můžete číst a zapisovat data.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-150">In the Core project, add a controller and view(s) to verify that you can read and write data.</span></span> <span data-ttu-id="0aaaf-151">(Všimněte si, že ASP.NET základní hosti cítění MVC nebude fungovat s kontextem EF6 odkazovaném z knihovny tříd.)</span><span class="sxs-lookup"><span data-stu-id="0aaaf-151">(Note that ASP.NET Core MVC scaffolding won't work with the EF6 context referenced from the class library.)</span></span>

## <a name="summary"></a><span data-ttu-id="0aaaf-152">Souhrn</span><span class="sxs-lookup"><span data-stu-id="0aaaf-152">Summary</span></span>

<span data-ttu-id="0aaaf-153">Tento článek obsahuje základní pokyny pro použití entity Framework 6 v ASP.NET základní aplikace.</span><span class="sxs-lookup"><span data-stu-id="0aaaf-153">This article has provided basic guidance for using Entity Framework 6 in an ASP.NET Core application.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0aaaf-154">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0aaaf-154">Additional resources</span></span>

* [<span data-ttu-id="0aaaf-155">Entity Framework – konfigurace založená na kódu</span><span class="sxs-lookup"><span data-stu-id="0aaaf-155">Entity Framework - Code-Based Configuration</span></span>](https://msdn.microsoft.com/data/jj680699.aspx)
