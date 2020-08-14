---
title: ASP.NET Core a Entity Framework 6
author: rick-anderson
description: Entity Framework 6,3 a novější pracuje s ASP.NET Core 3,1 a novějším.
ms.author: riande
ms.custom: mvc
ms.date: 7/14/2020
no-loc:
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/entity-framework-6
ms.openlocfilehash: c65a1ae7cc67ec7e15a2b732184bc05803eb40ce
ms.sourcegitcommit: ec41ab354952b75557240923756a8c2ac79b49f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2020
ms.locfileid: "88202765"
---
# <a name="aspnet-core-and-entity-framework-6"></a><span data-ttu-id="b3c17-103">ASP.NET Core a Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="b3c17-103">ASP.NET Core and Entity Framework 6</span></span>
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b3c17-104">[Entity Framework](/ef/ef6/) 6,3 a novější pracuje s ASP.NET Core 3,1 a novějším.</span><span class="sxs-lookup"><span data-stu-id="b3c17-104">[Entity Framework](/ef/ef6/) 6.3 and later works with ASP.NET Core 3.1 and later.</span></span> <span data-ttu-id="b3c17-105">Další informace najdete v blogovém příspěvku [s oznámením Entity Framework 6,3 s podporou .NET Core](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-6-3-preview-with-net-core-support/).</span><span class="sxs-lookup"><span data-stu-id="b3c17-105">For more information, see the blog post [Announcing Entity Framework 6.3 with .NET Core Support](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-6-3-preview-with-net-core-support/).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b3c17-106">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b3c17-106">Additional resources</span></span>

* [<span data-ttu-id="b3c17-107">Konfigurace na základě kódu Entity Framework</span><span class="sxs-lookup"><span data-stu-id="b3c17-107">Entity Framework - Code-Based Configuration</span></span>](/ef/ef6/fundamentals/configuring/code-based)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b3c17-108">[Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex)a [Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="b3c17-108">By [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="b3c17-109">Tento článek ukazuje, jak použít Entity Framework 6 v aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b3c17-109">This article shows how to use Entity Framework 6 in an ASP.NET Core application.</span></span>    

## <a name="overview"></a><span data-ttu-id="b3c17-110">Přehled</span><span class="sxs-lookup"><span data-stu-id="b3c17-110">Overview</span></span> 

<span data-ttu-id="b3c17-111">Chcete-li použít Entity Framework 6, projekt musí být zkompilován proti .NET Framework, protože Entity Framework 6 nepodporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b3c17-111">To use Entity Framework 6, your project has to compile against .NET Framework, as Entity Framework 6 doesn't support .NET Core.</span></span> <span data-ttu-id="b3c17-112">Pokud potřebujete funkce pro více platforem, budete muset upgradovat na [Entity Framework Core](/ef/).</span><span class="sxs-lookup"><span data-stu-id="b3c17-112">If you need cross-platform features you will need to upgrade to [Entity Framework Core](/ef/).</span></span>  

<span data-ttu-id="b3c17-113">Doporučený způsob použití Entity Framework 6 v aplikaci ASP.NET Core je umístit kontext EF6 a třídy modelu do projektu knihovny tříd, který cílí na .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="b3c17-113">The recommended way to use Entity Framework 6 in an ASP.NET Core application is to put the EF6 context and model classes in a class library project that targets .NET Framework.</span></span> <span data-ttu-id="b3c17-114">Do knihovny tříd přidejte odkaz z projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b3c17-114">Add a reference to the class library from the ASP.NET Core project.</span></span> <span data-ttu-id="b3c17-115">Podívejte se na ukázkové [řešení sady Visual Studio s projekty EF6 a ASP.NET Core](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span><span class="sxs-lookup"><span data-stu-id="b3c17-115">See the sample [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span></span>  

<span data-ttu-id="b3c17-116">EF6 kontext nemůžete vložit do projektu ASP.NET Core, protože projekty .NET Core nepodporují všechny funkce, které EF6 příkazy, jako je třeba *Enable – migrace* .</span><span class="sxs-lookup"><span data-stu-id="b3c17-116">You can't put an EF6 context in an ASP.NET Core project because .NET Core projects don't support all of the functionality that EF6 commands such as *Enable-Migrations* require.</span></span>    

<span data-ttu-id="b3c17-117">Bez ohledu na typ projektu, ve kterém najdete kontext EF6, fungují pouze nástroje příkazového řádku EF6 s kontextem EF6.</span><span class="sxs-lookup"><span data-stu-id="b3c17-117">Regardless of project type in which you locate your EF6 context, only EF6 command-line tools work with an EF6 context.</span></span> <span data-ttu-id="b3c17-118">Například `Scaffold-DbContext` je k dispozici pouze v Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="b3c17-118">For example, `Scaffold-DbContext` is only available in Entity Framework Core.</span></span> <span data-ttu-id="b3c17-119">Pokud potřebujete provést zpětnou přípravu databáze na model EF6, přečtěte si téma [Code First do existující databáze](https://msdn.microsoft.com/jj200620).</span><span class="sxs-lookup"><span data-stu-id="b3c17-119">If you need to do reverse engineering of a database into an EF6 model, see [Code First to an Existing Database](https://msdn.microsoft.com/jj200620).</span></span>  

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a><span data-ttu-id="b3c17-120">Odkaz na úplné rozhraní a EF6 v projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b3c17-120">Reference full framework and EF6 in the ASP.NET Core project</span></span> 

<span data-ttu-id="b3c17-121">Váš projekt ASP.NET Core musí cílit na .NET Framework a odkazovat EF6.</span><span class="sxs-lookup"><span data-stu-id="b3c17-121">Your ASP.NET Core project needs to target .NET Framework and reference EF6.</span></span> <span data-ttu-id="b3c17-122">Například soubor *. csproj* vašeho projektu ASP.NET Core bude vypadat podobně jako v následujícím příkladu (zobrazují se pouze relevantní části souboru).</span><span class="sxs-lookup"><span data-stu-id="b3c17-122">For example, the *.csproj* file of your ASP.NET Core project will look similar to the following example (only relevant parts of the file are shown).</span></span>    

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]   

<span data-ttu-id="b3c17-123">Při vytváření nového projektu použijte šablonu **ASP.NET Core webové aplikace (.NET Framework)** .</span><span class="sxs-lookup"><span data-stu-id="b3c17-123">When creating a new project, use the **ASP.NET Core Web Application (.NET Framework)** template.</span></span>    

## <a name="handle-connection-strings"></a><span data-ttu-id="b3c17-124">Zpracování připojovacích řetězců</span><span class="sxs-lookup"><span data-stu-id="b3c17-124">Handle connection strings</span></span>    

<span data-ttu-id="b3c17-125">Nástroje příkazového řádku EF6, které použijete v projektu knihovny tříd EF6, vyžadují výchozí konstruktor, aby mohli vytvářet instance kontextu.</span><span class="sxs-lookup"><span data-stu-id="b3c17-125">The EF6 command-line tools that you'll use in the EF6 class library project require a default constructor so they can instantiate the context.</span></span> <span data-ttu-id="b3c17-126">Ale pravděpodobně budete chtít zadat připojovací řetězec pro použití v ASP.NET Core projektu. v takovém případě musí mít konstruktor kontextu parametr, který vám umožní předat připojovací řetězec.</span><span class="sxs-lookup"><span data-stu-id="b3c17-126">But you'll probably want to specify the connection string to use in the ASP.NET Core project, in which case your context constructor must have a parameter that lets you pass in the connection string.</span></span> <span data-ttu-id="b3c17-127">Tady je příklad.</span><span class="sxs-lookup"><span data-stu-id="b3c17-127">Here's an example.</span></span>   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]   

<span data-ttu-id="b3c17-128">Vzhledem k tomu, že váš kontext EF6 nemá konstruktor bez parametrů, musí váš projekt EF6 poskytovat implementaci [IDbContextFactory](https://msdn.microsoft.com/library/hh506876).</span><span class="sxs-lookup"><span data-stu-id="b3c17-128">Since your EF6 context doesn't have a parameterless constructor, your EF6 project has to provide an implementation of [IDbContextFactory](https://msdn.microsoft.com/library/hh506876).</span></span> <span data-ttu-id="b3c17-129">Nástroje příkazového řádku EF6 vyhledají a použijí tuto implementaci, aby mohli vytvářet instance kontextu.</span><span class="sxs-lookup"><span data-stu-id="b3c17-129">The EF6 command-line tools will find and use that implementation so they can instantiate the context.</span></span> <span data-ttu-id="b3c17-130">Tady je příklad.</span><span class="sxs-lookup"><span data-stu-id="b3c17-130">Here's an example.</span></span>    

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]  

<span data-ttu-id="b3c17-131">V tomto ukázkovém kódu `IDbContextFactory` implementace projde pevně zakódovaným připojovacím řetězcem.</span><span class="sxs-lookup"><span data-stu-id="b3c17-131">In this sample code, the `IDbContextFactory` implementation passes in a hard-coded connection string.</span></span> <span data-ttu-id="b3c17-132">Toto je připojovací řetězec, který budou používat nástroje příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="b3c17-132">This is the connection string that the command-line tools will use.</span></span> <span data-ttu-id="b3c17-133">Chcete-li zajistit, aby knihovna tříd používala stejný připojovací řetězec, jaký používá volající aplikace, budete chtít implementovat strategii.</span><span class="sxs-lookup"><span data-stu-id="b3c17-133">You'll want to implement a strategy to ensure that the class library uses the same connection string that the calling application uses.</span></span> <span data-ttu-id="b3c17-134">Můžete například získat hodnotu z proměnné prostředí v obou projektech.</span><span class="sxs-lookup"><span data-stu-id="b3c17-134">For example, you could get the value from an environment variable in both projects.</span></span>   

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a><span data-ttu-id="b3c17-135">Nastavení injektáže závislosti v projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b3c17-135">Set up dependency injection in the ASP.NET Core project</span></span>  

<span data-ttu-id="b3c17-136">V souboru *Startup.cs* základního projektu nastavte kontext EF6 pro vkládání závislostí (di) v `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b3c17-136">In the Core project's *Startup.cs* file, set up the EF6 context for dependency injection (DI) in `ConfigureServices`.</span></span> <span data-ttu-id="b3c17-137">Objekty kontextu EF by měly být v oboru pro dobu života žádosti.</span><span class="sxs-lookup"><span data-stu-id="b3c17-137">EF context objects should be scoped for a per-request lifetime.</span></span>   

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]   

<span data-ttu-id="b3c17-138">Pak můžete získat instanci kontextu v řadičích do řadičů pomocí DI.</span><span class="sxs-lookup"><span data-stu-id="b3c17-138">You can then get an instance of the context in your controllers by using DI.</span></span> <span data-ttu-id="b3c17-139">Kód je podobný tomu, co píšete pro EF Coreho kontextu:</span><span class="sxs-lookup"><span data-stu-id="b3c17-139">The code is similar to what you'd write for an EF Core context:</span></span>    

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]  

## <a name="sample-application"></a><span data-ttu-id="b3c17-140">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="b3c17-140">Sample application</span></span>   

<span data-ttu-id="b3c17-141">Pracovní ukázkovou aplikaci najdete v [ukázkovém řešení sady Visual Studio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) , které doprovází tento článek.</span><span class="sxs-lookup"><span data-stu-id="b3c17-141">For a working sample application, see the [sample Visual Studio solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) that accompanies this article.</span></span>  

<span data-ttu-id="b3c17-142">Tuto ukázku můžete vytvořit úplně od začátku pomocí následujících kroků v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="b3c17-142">This sample can be created from scratch by the following steps in Visual Studio:</span></span>    

* <span data-ttu-id="b3c17-143">Vytvořte řešení.</span><span class="sxs-lookup"><span data-stu-id="b3c17-143">Create a solution.</span></span>    

* <span data-ttu-id="b3c17-144">**Přidat** > **Nový projekt** > **Webové** > **ASP.NET Core webové aplikace**</span><span class="sxs-lookup"><span data-stu-id="b3c17-144">**Add** > **New Project** > **Web** > **ASP.NET Core Web Application**</span></span>    
  * <span data-ttu-id="b3c17-145">V dialogovém okně Výběr šablony projektu vyberte v rozevíracím seznamu položku rozhraní API a .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="b3c17-145">In project template selection dialog, select API and .NET Framework in dropdown</span></span> 

* <span data-ttu-id="b3c17-146">**Přidat** > **Nový projekt** > **Plocha Windows** > **Knihovna tříd (.NET Framework)**</span><span class="sxs-lookup"><span data-stu-id="b3c17-146">**Add** > **New Project** > **Windows Desktop** > **Class Library (.NET Framework)**</span></span>  

* <span data-ttu-id="b3c17-147">V **konzole správce balíčků** (PMC) pro oba projekty spusťte příkaz `Install-Package Entityframework` .</span><span class="sxs-lookup"><span data-stu-id="b3c17-147">In **Package Manager Console** (PMC) for both projects, run the command `Install-Package Entityframework`.</span></span>    

* <span data-ttu-id="b3c17-148">V projektu knihovny tříd vytvořte třídy datového modelu a třídu kontextu a implementaci `IDbContextFactory` .</span><span class="sxs-lookup"><span data-stu-id="b3c17-148">In the class library project, create data model classes and a context class, and an implementation of `IDbContextFactory`.</span></span>    

* <span data-ttu-id="b3c17-149">V PMC pro projekt knihovny tříd spusťte příkazy `Enable-Migrations` a `Add-Migration Initial` .</span><span class="sxs-lookup"><span data-stu-id="b3c17-149">In PMC for the class library project, run the commands `Enable-Migrations` and `Add-Migration Initial`.</span></span> <span data-ttu-id="b3c17-150">Pokud jste nastavili ASP.NET Core projekt jako spouštěný projekt, přidejte `-StartupProjectName EF6` k těmto příkazům.</span><span class="sxs-lookup"><span data-stu-id="b3c17-150">If you have set the ASP.NET Core project as the startup project, add `-StartupProjectName EF6` to these commands.</span></span> 

* <span data-ttu-id="b3c17-151">V projektu Core přidejte odkaz na projekt do projektu knihovny tříd.</span><span class="sxs-lookup"><span data-stu-id="b3c17-151">In the Core project, add a project reference to the class library project.</span></span>    

* <span data-ttu-id="b3c17-152">V projektu Core Zaregistrujte v *Startup.cs*kontext pro di.</span><span class="sxs-lookup"><span data-stu-id="b3c17-152">In the Core project, in *Startup.cs*, register the context for DI.</span></span>    

* <span data-ttu-id="b3c17-153">V projektu Core v části *appsettings.jsna*přidejte připojovací řetězec.</span><span class="sxs-lookup"><span data-stu-id="b3c17-153">In the Core project, in *appsettings.json*, add the connection string.</span></span>    

* <span data-ttu-id="b3c17-154">V projektu Core přidejte kontroler a zobrazení, abyste ověřili, že můžete číst a zapisovat data.</span><span class="sxs-lookup"><span data-stu-id="b3c17-154">In the Core project, add a controller and view(s) to verify that you can read and write data.</span></span> <span data-ttu-id="b3c17-155">(Všimněte si, že ASP.NET Core generování uživatelského rozhraní MVC nebude fungovat s kontextem EF6, na který se odkazuje z knihovny tříd.)</span><span class="sxs-lookup"><span data-stu-id="b3c17-155">(Note that ASP.NET Core MVC scaffolding won't work with the EF6 context referenced from the class library.)</span></span>

::: moniker-end
