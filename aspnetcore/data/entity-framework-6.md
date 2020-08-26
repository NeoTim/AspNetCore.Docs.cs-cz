---
title: ASP.NET Core a Entity Framework 6
author: rick-anderson
description: Entity Framework 6,3 a novější pracuje s ASP.NET Core 3,1 a novějším.
ms.author: riande
ms.custom: mvc
ms.date: 7/14/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/entity-framework-6
ms.openlocfilehash: 64d420d7076f1da453ee423cc4a3732eeb47b221
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876695"
---
# <a name="aspnet-core-and-entity-framework-6"></a><span data-ttu-id="c356e-103">ASP.NET Core a Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="c356e-103">ASP.NET Core and Entity Framework 6</span></span>
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c356e-104">Po [Goodeu](https://github.com/attrib75)</span><span class="sxs-lookup"><span data-stu-id="c356e-104">By [Patrick Goode](https://github.com/attrib75)</span></span>

## <a name="using-entity-framework-6-with-aspnet-core"></a><span data-ttu-id="c356e-105">Použití Entity Framework 6 s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c356e-105">Using Entity Framework 6 with ASP.NET Core</span></span>

<span data-ttu-id="c356e-106">[Entity Framework Core](/ef/) by se měly používat pro nový vývoj.</span><span class="sxs-lookup"><span data-stu-id="c356e-106">[Entity Framework Core](/ef/) should be used for new development.</span></span> <span data-ttu-id="c356e-107">[Ukázka stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/3.xsample) používá [Entity Framework 6 (EF6)](/ef/ef6), která se dá použít k migraci ukončování aplikací na ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c356e-107">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/3.xsample) uses [Entity Framework 6 (EF6)](/ef/ef6), which can be used to migrate exiting apps to ASP.NET Core.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c356e-108">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c356e-108">Additional resources</span></span>

* [<span data-ttu-id="c356e-109">Konfigurace na základě kódu Entity Framework</span><span class="sxs-lookup"><span data-stu-id="c356e-109">Entity Framework - Code-Based Configuration</span></span>](/ef/ef6/fundamentals/configuring/code-based)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c356e-110">[Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex)a [Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="c356e-110">By [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="c356e-111">Tento článek ukazuje, jak použít Entity Framework 6 v aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c356e-111">This article shows how to use Entity Framework 6 in an ASP.NET Core application.</span></span>    

## <a name="overview"></a><span data-ttu-id="c356e-112">Přehled</span><span class="sxs-lookup"><span data-stu-id="c356e-112">Overview</span></span> 

<span data-ttu-id="c356e-113">Chcete-li použít Entity Framework 6, projekt musí být zkompilován proti .NET Framework, protože Entity Framework 6 nepodporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c356e-113">To use Entity Framework 6, your project has to compile against .NET Framework, as Entity Framework 6 doesn't support .NET Core.</span></span> <span data-ttu-id="c356e-114">Pokud potřebujete funkce pro více platforem, budete muset upgradovat na [Entity Framework Core](/ef/).</span><span class="sxs-lookup"><span data-stu-id="c356e-114">If you need cross-platform features you will need to upgrade to [Entity Framework Core](/ef/).</span></span>  

<span data-ttu-id="c356e-115">Doporučený způsob použití Entity Framework 6 v aplikaci ASP.NET Core je umístit kontext EF6 a třídy modelu do projektu knihovny tříd, který cílí na .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="c356e-115">The recommended way to use Entity Framework 6 in an ASP.NET Core application is to put the EF6 context and model classes in a class library project that targets .NET Framework.</span></span> <span data-ttu-id="c356e-116">Do knihovny tříd přidejte odkaz z projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c356e-116">Add a reference to the class library from the ASP.NET Core project.</span></span> <span data-ttu-id="c356e-117">Podívejte se na ukázkové [řešení sady Visual Studio s projekty EF6 a ASP.NET Core](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span><span class="sxs-lookup"><span data-stu-id="c356e-117">See the sample [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span></span>  

<span data-ttu-id="c356e-118">EF6 kontext nemůžete vložit do projektu ASP.NET Core, protože projekty .NET Core nepodporují všechny funkce, které EF6 příkazy, jako je třeba *Enable – migrace* .</span><span class="sxs-lookup"><span data-stu-id="c356e-118">You can't put an EF6 context in an ASP.NET Core project because .NET Core projects don't support all of the functionality that EF6 commands such as *Enable-Migrations* require.</span></span>    

<span data-ttu-id="c356e-119">Bez ohledu na typ projektu, ve kterém najdete kontext EF6, fungují pouze nástroje příkazového řádku EF6 s kontextem EF6.</span><span class="sxs-lookup"><span data-stu-id="c356e-119">Regardless of project type in which you locate your EF6 context, only EF6 command-line tools work with an EF6 context.</span></span> <span data-ttu-id="c356e-120">Například `Scaffold-DbContext` je k dispozici pouze v Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="c356e-120">For example, `Scaffold-DbContext` is only available in Entity Framework Core.</span></span> <span data-ttu-id="c356e-121">Pokud potřebujete provést zpětnou přípravu databáze na model EF6, přečtěte si téma <https://docs.microsoft.com/ef/ef6/modeling/code-first/workflows/existing-database> .</span><span class="sxs-lookup"><span data-stu-id="c356e-121">If you need to do reverse engineering of a database into an EF6 model, see <https://docs.microsoft.com/ef/ef6/modeling/code-first/workflows/existing-database>.</span></span>    

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a><span data-ttu-id="c356e-122">Odkaz na úplné rozhraní a EF6 v projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c356e-122">Reference full framework and EF6 in the ASP.NET Core project</span></span> 

<span data-ttu-id="c356e-123">Váš projekt ASP.NET Core musí cílit na .NET Framework a odkazovat EF6.</span><span class="sxs-lookup"><span data-stu-id="c356e-123">Your ASP.NET Core project needs to target .NET Framework and reference EF6.</span></span> <span data-ttu-id="c356e-124">Například soubor *. csproj* vašeho projektu ASP.NET Core bude vypadat podobně jako v následujícím příkladu (zobrazují se pouze relevantní části souboru).</span><span class="sxs-lookup"><span data-stu-id="c356e-124">For example, the *.csproj* file of your ASP.NET Core project will look similar to the following example (only relevant parts of the file are shown).</span></span>    

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]   

<span data-ttu-id="c356e-125">Při vytváření nového projektu použijte šablonu **ASP.NET Core webové aplikace (.NET Framework)** .</span><span class="sxs-lookup"><span data-stu-id="c356e-125">When creating a new project, use the **ASP.NET Core Web Application (.NET Framework)** template.</span></span>    

## <a name="handle-connection-strings"></a><span data-ttu-id="c356e-126">Zpracování připojovacích řetězců</span><span class="sxs-lookup"><span data-stu-id="c356e-126">Handle connection strings</span></span>    

<span data-ttu-id="c356e-127">Nástroje příkazového řádku EF6, které použijete v projektu knihovny tříd EF6, vyžadují výchozí konstruktor, aby mohli vytvářet instance kontextu.</span><span class="sxs-lookup"><span data-stu-id="c356e-127">The EF6 command-line tools that you'll use in the EF6 class library project require a default constructor so they can instantiate the context.</span></span> <span data-ttu-id="c356e-128">Ale pravděpodobně budete chtít zadat připojovací řetězec pro použití v ASP.NET Core projektu. v takovém případě musí mít konstruktor kontextu parametr, který vám umožní předat připojovací řetězec.</span><span class="sxs-lookup"><span data-stu-id="c356e-128">But you'll probably want to specify the connection string to use in the ASP.NET Core project, in which case your context constructor must have a parameter that lets you pass in the connection string.</span></span> <span data-ttu-id="c356e-129">Tady je příklad.</span><span class="sxs-lookup"><span data-stu-id="c356e-129">Here's an example.</span></span>   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]   

<span data-ttu-id="c356e-130">Vzhledem k tomu, že váš kontext EF6 nemá konstruktor bez parametrů, projekt EF6 musí poskytovat implementaci <https://docs.microsoft.com/dotnet/api/system.data.entity.infrastructure.idbcontextfactory-1?view=entity-framework-6.2.0> .</span><span class="sxs-lookup"><span data-stu-id="c356e-130">Since your EF6 context doesn't have a parameterless constructor, your EF6 project has to provide an implementation of <https://docs.microsoft.com/dotnet/api/system.data.entity.infrastructure.idbcontextfactory-1?view=entity-framework-6.2.0>.</span></span> <span data-ttu-id="c356e-131">Nástroje příkazového řádku EF6 vyhledají a použijí tuto implementaci, aby mohli vytvářet instance kontextu.</span><span class="sxs-lookup"><span data-stu-id="c356e-131">The EF6 command-line tools will find and use that implementation so they can instantiate the context.</span></span> <span data-ttu-id="c356e-132">Tady je příklad.</span><span class="sxs-lookup"><span data-stu-id="c356e-132">Here's an example.</span></span>   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]  

<span data-ttu-id="c356e-133">V tomto ukázkovém kódu `IDbContextFactory` implementace projde pevně zakódovaným připojovacím řetězcem.</span><span class="sxs-lookup"><span data-stu-id="c356e-133">In this sample code, the `IDbContextFactory` implementation passes in a hard-coded connection string.</span></span> <span data-ttu-id="c356e-134">Toto je připojovací řetězec, který budou používat nástroje příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="c356e-134">This is the connection string that the command-line tools will use.</span></span> <span data-ttu-id="c356e-135">Chcete-li zajistit, aby knihovna tříd používala stejný připojovací řetězec, jaký používá volající aplikace, budete chtít implementovat strategii.</span><span class="sxs-lookup"><span data-stu-id="c356e-135">You'll want to implement a strategy to ensure that the class library uses the same connection string that the calling application uses.</span></span> <span data-ttu-id="c356e-136">Můžete například získat hodnotu z proměnné prostředí v obou projektech.</span><span class="sxs-lookup"><span data-stu-id="c356e-136">For example, you could get the value from an environment variable in both projects.</span></span>   

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a><span data-ttu-id="c356e-137">Nastavení injektáže závislosti v projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c356e-137">Set up dependency injection in the ASP.NET Core project</span></span>  

<span data-ttu-id="c356e-138">V souboru *Startup.cs* základního projektu nastavte kontext EF6 pro vkládání závislostí (di) v `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c356e-138">In the Core project's *Startup.cs* file, set up the EF6 context for dependency injection (DI) in `ConfigureServices`.</span></span> <span data-ttu-id="c356e-139">Objekty kontextu EF by měly být v oboru pro dobu života žádosti.</span><span class="sxs-lookup"><span data-stu-id="c356e-139">EF context objects should be scoped for a per-request lifetime.</span></span>   

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]   

<span data-ttu-id="c356e-140">Pak můžete získat instanci kontextu v řadičích do řadičů pomocí DI.</span><span class="sxs-lookup"><span data-stu-id="c356e-140">You can then get an instance of the context in your controllers by using DI.</span></span> <span data-ttu-id="c356e-141">Kód je podobný tomu, co píšete pro EF Coreho kontextu:</span><span class="sxs-lookup"><span data-stu-id="c356e-141">The code is similar to what you'd write for an EF Core context:</span></span>    

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]  

## <a name="sample-application"></a><span data-ttu-id="c356e-142">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="c356e-142">Sample application</span></span>   

<span data-ttu-id="c356e-143">Pracovní ukázkovou aplikaci najdete v [ukázkovém řešení sady Visual Studio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) , které doprovází tento článek.</span><span class="sxs-lookup"><span data-stu-id="c356e-143">For a working sample application, see the [sample Visual Studio solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) that accompanies this article.</span></span>  

<span data-ttu-id="c356e-144">Tuto ukázku můžete vytvořit úplně od začátku pomocí následujících kroků v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="c356e-144">This sample can be created from scratch by the following steps in Visual Studio:</span></span>    

* <span data-ttu-id="c356e-145">Vytvořte řešení.</span><span class="sxs-lookup"><span data-stu-id="c356e-145">Create a solution.</span></span>    

* <span data-ttu-id="c356e-146">**Přidat** > **Nový projekt** > **Webové** > **ASP.NET Core webové aplikace**</span><span class="sxs-lookup"><span data-stu-id="c356e-146">**Add** > **New Project** > **Web** > **ASP.NET Core Web Application**</span></span>    
  * <span data-ttu-id="c356e-147">V dialogovém okně Výběr šablony projektu vyberte v rozevíracím seznamu položku rozhraní API a .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="c356e-147">In project template selection dialog, select API and .NET Framework in dropdown</span></span> 

* <span data-ttu-id="c356e-148">**Přidat** > **Nový projekt** > **Plocha Windows** > **Knihovna tříd (.NET Framework)**</span><span class="sxs-lookup"><span data-stu-id="c356e-148">**Add** > **New Project** > **Windows Desktop** > **Class Library (.NET Framework)**</span></span>  

* <span data-ttu-id="c356e-149">V **konzole správce balíčků** (PMC) pro oba projekty spusťte příkaz `Install-Package Entityframework` .</span><span class="sxs-lookup"><span data-stu-id="c356e-149">In **Package Manager Console** (PMC) for both projects, run the command `Install-Package Entityframework`.</span></span>    

* <span data-ttu-id="c356e-150">V projektu knihovny tříd vytvořte třídy datového modelu a třídu kontextu a implementaci `IDbContextFactory` .</span><span class="sxs-lookup"><span data-stu-id="c356e-150">In the class library project, create data model classes and a context class, and an implementation of `IDbContextFactory`.</span></span>    

* <span data-ttu-id="c356e-151">V PMC pro projekt knihovny tříd spusťte příkazy `Enable-Migrations` a `Add-Migration Initial` .</span><span class="sxs-lookup"><span data-stu-id="c356e-151">In PMC for the class library project, run the commands `Enable-Migrations` and `Add-Migration Initial`.</span></span> <span data-ttu-id="c356e-152">Pokud jste nastavili ASP.NET Core projekt jako spouštěný projekt, přidejte `-StartupProjectName EF6` k těmto příkazům.</span><span class="sxs-lookup"><span data-stu-id="c356e-152">If you have set the ASP.NET Core project as the startup project, add `-StartupProjectName EF6` to these commands.</span></span> 

* <span data-ttu-id="c356e-153">V projektu Core přidejte odkaz na projekt do projektu knihovny tříd.</span><span class="sxs-lookup"><span data-stu-id="c356e-153">In the Core project, add a project reference to the class library project.</span></span>    

* <span data-ttu-id="c356e-154">V projektu Core Zaregistrujte v *Startup.cs*kontext pro di.</span><span class="sxs-lookup"><span data-stu-id="c356e-154">In the Core project, in *Startup.cs*, register the context for DI.</span></span>    

* <span data-ttu-id="c356e-155">V projektu Core v části *appsettings.jsna*přidejte připojovací řetězec.</span><span class="sxs-lookup"><span data-stu-id="c356e-155">In the Core project, in *appsettings.json*, add the connection string.</span></span>    

* <span data-ttu-id="c356e-156">V projektu Core přidejte kontroler a zobrazení, abyste ověřili, že můžete číst a zapisovat data.</span><span class="sxs-lookup"><span data-stu-id="c356e-156">In the Core project, add a controller and view(s) to verify that you can read and write data.</span></span> <span data-ttu-id="c356e-157">(Všimněte si, že ASP.NET Core generování uživatelského rozhraní MVC nebude fungovat s kontextem EF6, na který se odkazuje z knihovny tříd.)</span><span class="sxs-lookup"><span data-stu-id="c356e-157">(Note that ASP.NET Core MVC scaffolding won't work with the EF6 context referenced from the class library.)</span></span>

::: moniker-end
