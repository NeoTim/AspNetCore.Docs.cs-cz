---
title: Přidání modelu do aplikace Razor Pages v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak přidat třídy pro správu filmů v databázi pomocí entity framework core (EF Core).
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: f6dbac81b4efceb30c379ab06dd715005d879228
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658933"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="7f0bb-103">Přidání modelu do aplikace Razor Pages v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7f0bb-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="7f0bb-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7f0bb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="7f0bb-105">V této části jsou přidány třídy pro správu filmů v [databázi SQLite](https://www.sqlite.org/index.html)napříč platformami .</span><span class="sxs-lookup"><span data-stu-id="7f0bb-105">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="7f0bb-106">Aplikace vytvořené ze ASP.NET šablony Core používají databázi SQLite.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-106">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="7f0bb-107">Třídy modelu aplikace se používají s [entity Framework Core (EF Core)](/ef/core) [(SQLite EF Základní databáze zprostředkovatele)](/ef/core/providers/sqlite)pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-107">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="7f0bb-108">EF Core je objekt-relační mapování (ORM) framework, který zjednodušuje přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-108">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="7f0bb-109">Třídy modelu jsou označovány jako třídy POCO (z "prostý staré objekty CLR"), protože nemají žádnou závislost na EF Core.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-109">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="7f0bb-110">Definují vlastnosti dat, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-110">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="7f0bb-111">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="7f0bb-111">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7f0bb-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f0bb-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7f0bb-113">Klepněte pravým tlačítkem myši na projekt **RazorPagesMovie** > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="7f0bb-114">Pojmenujte složku *Modely*.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-114">Name the folder *Models*.</span></span>

<span data-ttu-id="7f0bb-115">Klikněte pravým tlačítkem myši na složku *Modely.*</span><span class="sxs-lookup"><span data-stu-id="7f0bb-115">Right click the *Models* folder.</span></span> <span data-ttu-id="7f0bb-116">Vyberte **Přidat** > **třídu**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="7f0bb-117">Pojmenujte třídu **Film**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7f0bb-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f0bb-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7f0bb-119">Přidejte složku s názvem *Modely*.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="7f0bb-120">Přidejte třídu do složky *Models* s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7f0bb-121">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7f0bb-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7f0bb-122">V panelu řešení klepněte pravým tlačítkem myši na projekt **RazorPagesMovie** a pak vyberte **Přidat** > **novou složku...**. Pojmenujte složku *Modely*.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-122">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="7f0bb-123">Klikněte pravým tlačítkem myši na složku *Modely* a potom vyberte **Přidat** > **nový soubor...**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-123">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="7f0bb-124">V dialogovém okně **Nový soubor:**</span><span class="sxs-lookup"><span data-stu-id="7f0bb-124">In the **New File** dialog:</span></span>

  * <span data-ttu-id="7f0bb-125">V levém podokně vyberte **Obecné.**</span><span class="sxs-lookup"><span data-stu-id="7f0bb-125">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="7f0bb-126">V prostředním podokně vyberte **Vyprázdnit třídu.**</span><span class="sxs-lookup"><span data-stu-id="7f0bb-126">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="7f0bb-127">Pojmenujte třídu **Film** a vyberte **Nový**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-127">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="7f0bb-128">Sestavení projektu ověřte, že neexistují žádné chyby kompilace.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-128">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="7f0bb-129">Lešení filmového modelu</span><span class="sxs-lookup"><span data-stu-id="7f0bb-129">Scaffold the movie model</span></span>

<span data-ttu-id="7f0bb-130">V této části je filmový model scaffolded.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-130">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="7f0bb-131">To znamená, že nástroj lešení vytváří stránky pro operace Vytvořit, Číst, Aktualizovat a Odstranit (CRUD) pro filmový model.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-131">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7f0bb-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f0bb-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7f0bb-133">Vytvoření složky *Stránky a filmy:*</span><span class="sxs-lookup"><span data-stu-id="7f0bb-133">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="7f0bb-134">Klikněte pravým tlačítkem myši na složku *Stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-134">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="7f0bb-135">Pojmenujte složku *Filmy*</span><span class="sxs-lookup"><span data-stu-id="7f0bb-135">Name the folder *Movies*</span></span>

<span data-ttu-id="7f0bb-136">Klikněte pravým tlačítkem myši na složku *Stránky/filmy* > **Přidat** > **novou scaffolded položku**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-136">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obrázek z předchozích pokynů.](model/_static/sca.png)

<span data-ttu-id="7f0bb-138">V dialogovém okně **Přidat pomocí okna Písmo šaten** vyberte **Razor Pages pomocí entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-138">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obrázek z předchozích pokynů.](model/_static/add_scaffold.png)

<span data-ttu-id="7f0bb-140">Dokončete dialogové okno **Přidat žiletky pomocí entity frameworku (CRUD):**</span><span class="sxs-lookup"><span data-stu-id="7f0bb-140">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="7f0bb-141">V rozevíracím seznamu **Třída Model** vyberte **Film (RazorPagesMovie.Models)**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-141">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="7f0bb-142">V řádku **třídy kontext** **+** dat vyberte znaménko (plus) a změňte generovaný název z RazorPagesMovie. **Modely**. RazorPagesMovieContext na RazorPagesMovie. **Data**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-142">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="7f0bb-143">[Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) není nutná.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-143">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="7f0bb-144">Vytvoří databázovou třídu kontextu se správným oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-144">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="7f0bb-145">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-145">Select **Add**.</span></span>

![Obrázek z předchozích pokynů.](model/_static/3/arp.png)

<span data-ttu-id="7f0bb-147">Soubor *appsettings.json* je aktualizován pomocí připojovacího řetězce používaného k připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-147">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7f0bb-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f0bb-148">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="7f0bb-149">Otevřete příkazové okno v adresáři projektu (Adresář obsahující soubory *Program.cs*, *Startup.cs*a *.csproj).*</span><span class="sxs-lookup"><span data-stu-id="7f0bb-149">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="7f0bb-150">Nainstalujte nástroj lešení:</span><span class="sxs-lookup"><span data-stu-id="7f0bb-150">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="7f0bb-151">**Pro Windows**: Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="7f0bb-151">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="7f0bb-152">**Pro macOS a Linux**: Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="7f0bb-152">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7f0bb-153">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7f0bb-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7f0bb-154">Vytvoření složky *Stránky a filmy:*</span><span class="sxs-lookup"><span data-stu-id="7f0bb-154">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="7f0bb-155">Klikněte pravým tlačítkem myši na složku *Stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-155">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="7f0bb-156">Pojmenujte složku *Filmy*</span><span class="sxs-lookup"><span data-stu-id="7f0bb-156">Name the folder *Movies*</span></span>

<span data-ttu-id="7f0bb-157">Klikněte pravým tlačítkem myši na složku *Stránky/Filmy* > **Přidat** > **nové lešení...**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-157">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Obrázek z předchozích pokynů.](model/_static/scaMac.png)

<span data-ttu-id="7f0bb-159">V dialogovém okně **Nové lešení** vyberte **Razor Pages using Entity Framework (CRUD)** > **Next**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-159">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Obrázek z předchozích pokynů.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="7f0bb-161">Dokončete dialogové okno **Přidat žiletky pomocí entity frameworku (CRUD):**</span><span class="sxs-lookup"><span data-stu-id="7f0bb-161">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="7f0bb-162">V rozevíracím seznamu **Třídy Model** vyberte nebo zadejte **film (RazorPagesMovie.Models)**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-162">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="7f0bb-163">Do řádku **třídy Kontext dat** zadejte název nové třídy RazorPagesMovie. **Data**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-163">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="7f0bb-164">[Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) není nutná.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-164">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="7f0bb-165">Vytvoří databázovou třídu kontextu se správným oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-165">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="7f0bb-166">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-166">Select **Add**.</span></span>

![Obrázek z předchozích pokynů.](model/_static/arpMac.png)

<span data-ttu-id="7f0bb-168">Soubor *appsettings.json* je aktualizován pomocí připojovacího řetězce používaného k připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-168">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="7f0bb-169">Přidat nástroje EF</span><span class="sxs-lookup"><span data-stu-id="7f0bb-169">Add EF tools</span></span>

<span data-ttu-id="7f0bb-170">Spusťte následující příkaz CLI jádra .NET:</span><span class="sxs-lookup"><span data-stu-id="7f0bb-170">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="7f0bb-171">Předchozí příkaz přidá základní nástroje entity frameworku pro rozhraní příkazového příkazu .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-171">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="7f0bb-172">Vytvořené soubory</span><span class="sxs-lookup"><span data-stu-id="7f0bb-172">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7f0bb-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f0bb-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7f0bb-174">Proces vytváření uživatelského příkazu vytvoří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="7f0bb-174">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="7f0bb-175">*Stránky/filmy:* Vytvoření, odstranění, podrobnosti, úpravy a rejstřík.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-175">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="7f0bb-176">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="7f0bb-176">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="7f0bb-177">Aktualizováno</span><span class="sxs-lookup"><span data-stu-id="7f0bb-177">Updated</span></span>

* <span data-ttu-id="7f0bb-178">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="7f0bb-178">*Startup.cs*</span></span>

<span data-ttu-id="7f0bb-179">Vytvořené a aktualizované soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-179">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7f0bb-180">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7f0bb-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7f0bb-181">Proces vytváření uživatelského příkazu vytvoří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="7f0bb-181">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="7f0bb-182">*Stránky/filmy:* Vytvoření, odstranění, podrobnosti, úpravy a rejstřík.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-182">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="7f0bb-183">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="7f0bb-183">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="7f0bb-184">Aktualizováno</span><span class="sxs-lookup"><span data-stu-id="7f0bb-184">Updated</span></span>

* <span data-ttu-id="7f0bb-185">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="7f0bb-185">*Startup.cs*</span></span>

<span data-ttu-id="7f0bb-186">Vytvořené a aktualizované soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-186">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7f0bb-187">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f0bb-187">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7f0bb-188">Proces vytváření uživatelského příkazu vytvoří následující soubory:</span><span class="sxs-lookup"><span data-stu-id="7f0bb-188">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="7f0bb-189">*Stránky/filmy:* Vytvoření, odstranění, podrobnosti, úpravy a rejstřík.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-189">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="7f0bb-190">Vytvořené soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-190">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="7f0bb-191">Počáteční migrace</span><span class="sxs-lookup"><span data-stu-id="7f0bb-191">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7f0bb-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f0bb-192">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7f0bb-193">V této části se konzola Správce balíčků (PMC) používá k:</span><span class="sxs-lookup"><span data-stu-id="7f0bb-193">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="7f0bb-194">Přidejte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-194">Add an initial migration.</span></span>
* <span data-ttu-id="7f0bb-195">Aktualizujte databázi počáteční migrací.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-195">Update the database with the initial migration.</span></span>

<span data-ttu-id="7f0bb-196">V nabídce **Nástroje** vyberte konzolu **Správce balíčků** **Aplikace NuGet .** ></span><span class="sxs-lookup"><span data-stu-id="7f0bb-196">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Nabídka PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="7f0bb-198">Do pmc zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="7f0bb-198">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="7f0bb-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f0bb-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7f0bb-200">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7f0bb-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="7f0bb-201">Předchozí příkazy generují následující upozornění: "Pro desítkový sloupec Cena nebyl zadán žádný typ.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-201">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="7f0bb-202">To způsobí, že hodnoty, které mají být tiše zkrácena, pokud se nevejdou do výchozí přesnosti a měřítka.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-202">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="7f0bb-203">Explicitně určete typ sloupce serveru SQL, který může pojmout všechny hodnoty pomocí 'HasColumnType()'."</span><span class="sxs-lookup"><span data-stu-id="7f0bb-203">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="7f0bb-204">Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-204">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="7f0bb-205">Příkaz Migrace generuje kód pro vytvoření počátečního schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-205">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="7f0bb-206">Schéma je založeno na modelu zadaném `DbContext`v .</span><span class="sxs-lookup"><span data-stu-id="7f0bb-206">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="7f0bb-207">Argument `InitialCreate` se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-207">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="7f0bb-208">Lze použít libovolný název, ale podle konvence je vybrán název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-208">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="7f0bb-209">Příkaz `update` spustí `Up` metodu v migracích, které nebyly použity.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-209">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="7f0bb-210">V tomto `update` případě `Up` spustí metodu v *migraci /\<časové razítko>_InitialCreate.cs* souboru, který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-210">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7f0bb-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f0bb-211">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="7f0bb-212">Prozkoumejte kontext registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="7f0bb-212">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="7f0bb-213">ASP.NET Core je postaven s [vstřikování závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7f0bb-213">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7f0bb-214">Služby (například kontext EF Core DB) jsou registrovány s vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-214">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="7f0bb-215">Součásti, které vyžadují tyto služby (například Razor Pages) jsou poskytovány tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-215">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="7f0bb-216">Kód konstruktoru, který získá instanci kontextu DB je zobrazen dále v kurzu.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-216">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="7f0bb-217">Nástroj pro vytváření uživatelského funkce automaticky vytvořil kontext DB a zaregistroval jej do kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-217">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="7f0bb-218">Zkontrolujte `Startup.ConfigureServices` metodu.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-218">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="7f0bb-219">Zvýrazněný řádek byl přidán lešení:</span><span class="sxs-lookup"><span data-stu-id="7f0bb-219">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="7f0bb-220">Souřadnice `RazorPagesMovieContext` EF core funkce (vytvořit, číst, aktualizovat, `Movie` odstranit, atd.) pro model.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-220">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="7f0bb-221">Kontext dat`RazorPagesMovieContext`( ) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="7f0bb-221">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="7f0bb-222">Kontext dat určuje, které entity jsou zahrnuty do datového modelu.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-222">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="7f0bb-223">Předchozí kód vytvoří [vlastnost\<DbSet Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-223">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="7f0bb-224">V terminologii entity framework sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-224">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="7f0bb-225">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-225">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="7f0bb-226">Název připojovacího řetězce je předán do kontextu voláním metody na [dbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-226">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="7f0bb-227">Pro místní vývoj [ASP.NET konfiguračnísystém Core](xref:fundamentals/configuration/index) přečte připojovací řetězec ze souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="7f0bb-227">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7f0bb-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f0bb-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7f0bb-229">Zkontrolujte `Up` metodu.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-229">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7f0bb-230">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7f0bb-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7f0bb-231">Zkontrolujte `Up` metodu.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-231">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="7f0bb-232">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="7f0bb-232">Test the app</span></span>

* <span data-ttu-id="7f0bb-233">Spusťte aplikaci `/Movies` a připojte`http://localhost:port/movies`adresu URL v prohlížeči ( ).</span><span class="sxs-lookup"><span data-stu-id="7f0bb-233">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="7f0bb-234">Pokud se zobrazí chyba:</span><span class="sxs-lookup"><span data-stu-id="7f0bb-234">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="7f0bb-235">Jste vynechal [krok migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="7f0bb-235">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="7f0bb-236">Otestujte odkaz **Vytvořit.**</span><span class="sxs-lookup"><span data-stu-id="7f0bb-236">Test the **Create** link.</span></span>

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="7f0bb-238">Je možné, že do pole nebudete moci `Price` zadat desetinná čárka.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="7f0bb-239">Chcete-li podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, která používají čárku (",") pro desetinnou čárku a pro formáty kalendářních dat mimo americkou angličtinu, musí být aplikace globalizovaná.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="7f0bb-240">Pokyny pro globalizaci najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="7f0bb-240">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="7f0bb-241">Otestujte odkazy **Upravit**, **Podrobnosti** a **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-241">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="7f0bb-242">Další kurz vysvětluje soubory vytvořené pomocí lešení.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-242">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7f0bb-243">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="7f0bb-243">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="7f0bb-244">[Předchozí: Začínáme](xref:tutorials/razor-pages/razor-pages-start)
> [Další: Scaffolded Razor Stránky](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="7f0bb-244">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7f0bb-245">V této části jsou přidány třídy pro správu filmů v [databázi SQLite](https://www.sqlite.org/index.html)napříč platformami .</span><span class="sxs-lookup"><span data-stu-id="7f0bb-245">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="7f0bb-246">Aplikace vytvořené ze ASP.NET šablony Core používají databázi SQLite.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-246">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="7f0bb-247">Třídy modelu aplikace se používají s [entity Framework Core (EF Core)](/ef/core) [(SQLite EF Základní databáze zprostředkovatele)](/ef/core/providers/sqlite)pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-247">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="7f0bb-248">EF Core je objekt-relační mapování (ORM) framework, který zjednodušuje přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-248">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="7f0bb-249">Třídy modelu jsou označovány jako třídy POCO (z "prostý staré objekty CLR"), protože nemají žádnou závislost na EF Core.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-249">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="7f0bb-250">Definují vlastnosti dat, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-250">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="7f0bb-251">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="7f0bb-251">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7f0bb-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f0bb-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7f0bb-253">Klepněte pravým tlačítkem myši na projekt **RazorPagesMovie** > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-253">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="7f0bb-254">Pojmenujte složku *Modely*.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-254">Name the folder *Models*.</span></span>

<span data-ttu-id="7f0bb-255">Klikněte pravým tlačítkem myši na složku *Modely.*</span><span class="sxs-lookup"><span data-stu-id="7f0bb-255">Right click the *Models* folder.</span></span> <span data-ttu-id="7f0bb-256">Vyberte **Přidat** > **třídu**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-256">Select **Add** > **Class**.</span></span> <span data-ttu-id="7f0bb-257">Pojmenujte třídu **Film**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-257">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7f0bb-258">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f0bb-258">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7f0bb-259">Přidejte složku s názvem *Modely*.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-259">Add a folder named *Models*.</span></span>
* <span data-ttu-id="7f0bb-260">Přidejte třídu do složky *Models* s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-260">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7f0bb-261">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7f0bb-261">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7f0bb-262">V Průzkumníku řešení klepněte pravým tlačítkem myši na projekt **RazorPagesMovie** a pak vyberte **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-262">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="7f0bb-263">Pojmenujte složku *Modely*.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-263">Name the folder *Models*.</span></span>
* <span data-ttu-id="7f0bb-264">Klikněte pravým tlačítkem myši na složku *Modely* a potom vyberte **Přidat** > **nový soubor**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-264">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="7f0bb-265">V dialogovém okně **Nový soubor:**</span><span class="sxs-lookup"><span data-stu-id="7f0bb-265">In the **New File** dialog:</span></span>

  * <span data-ttu-id="7f0bb-266">V levém podokně vyberte **Obecné.**</span><span class="sxs-lookup"><span data-stu-id="7f0bb-266">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="7f0bb-267">V prostředním podokně vyberte **Vyprázdnit třídu.**</span><span class="sxs-lookup"><span data-stu-id="7f0bb-267">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="7f0bb-268">Pojmenujte třídu **Film** a vyberte **Nový**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-268">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="7f0bb-269">Sestavení projektu ověřte, že neexistují žádné chyby kompilace.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-269">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="7f0bb-270">Lešení filmového modelu</span><span class="sxs-lookup"><span data-stu-id="7f0bb-270">Scaffold the movie model</span></span>

<span data-ttu-id="7f0bb-271">V této části je filmový model scaffolded.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-271">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="7f0bb-272">To znamená, že nástroj lešení vytváří stránky pro operace Vytvořit, Číst, Aktualizovat a Odstranit (CRUD) pro filmový model.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-272">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7f0bb-273">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f0bb-273">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7f0bb-274">Vytvoření složky *Stránky a filmy:*</span><span class="sxs-lookup"><span data-stu-id="7f0bb-274">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="7f0bb-275">Klikněte pravým tlačítkem myši na složku *Stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-275">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="7f0bb-276">Pojmenujte složku *Filmy*</span><span class="sxs-lookup"><span data-stu-id="7f0bb-276">Name the folder *Movies*</span></span>

<span data-ttu-id="7f0bb-277">Klikněte pravým tlačítkem myši na složku *Stránky/filmy* > **Přidat** > **novou scaffolded položku**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-277">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obrázek z předchozích pokynů.](model/_static/sca.png)

<span data-ttu-id="7f0bb-279">V dialogovém okně **Přidat pomocí okna Písmo šaten** vyberte **Razor Pages pomocí entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-279">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obrázek z předchozích pokynů.](model/_static/add_scaffold.png)

<span data-ttu-id="7f0bb-281">Dokončete dialogové okno **Přidat žiletky pomocí entity frameworku (CRUD):**</span><span class="sxs-lookup"><span data-stu-id="7f0bb-281">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="7f0bb-282">V rozevíracím seznamu **Třída Model** vyberte **Film (RazorPagesMovie.Models)**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-282">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="7f0bb-283">V řádku **třídy kontext** **+** dat vyberte znaménko (plus) a přijměte generovaný název **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-283">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="7f0bb-284">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-284">Select **Add**.</span></span>

![Obrázek z předchozích pokynů.](model/_static/arp.png)

<span data-ttu-id="7f0bb-286">Soubor *appsettings.json* je aktualizován pomocí připojovacího řetězce používaného k připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-286">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7f0bb-287">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f0bb-287">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="7f0bb-288">Otevřete příkazové okno v adresáři projektu (Adresář obsahující soubory *Program.cs*, *Startup.cs*a *.csproj).*</span><span class="sxs-lookup"><span data-stu-id="7f0bb-288">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="7f0bb-289">**Pro Windows**: Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="7f0bb-289">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="7f0bb-290">**Pro macOS a Linux**: Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="7f0bb-290">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7f0bb-291">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7f0bb-291">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7f0bb-292">Vytvoření složky *Stránky a filmy:*</span><span class="sxs-lookup"><span data-stu-id="7f0bb-292">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="7f0bb-293">Klikněte pravým tlačítkem myši na složku *Stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-293">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="7f0bb-294">Pojmenujte složku *Filmy*</span><span class="sxs-lookup"><span data-stu-id="7f0bb-294">Name the folder *Movies*</span></span>

<span data-ttu-id="7f0bb-295">Klikněte pravým tlačítkem myši na složku *Stránky/filmy* > **Přidat** > **novou scaffolded položku**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-295">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obrázek z předchozích pokynů.](model/_static/scaMac.png)

<span data-ttu-id="7f0bb-297">V dialogovém okně **Přidat nové lešení** vyberte **Razor Pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-297">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obrázek z předchozích pokynů.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="7f0bb-299">Dokončete dialogové okno **Přidat žiletky pomocí entity frameworku (CRUD):**</span><span class="sxs-lookup"><span data-stu-id="7f0bb-299">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="7f0bb-300">V rozevíracím seznamu **Třída Model** vyberte nebo zadejte **film**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-300">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="7f0bb-301">Do řádku **třídy kontextu Data** zadejte **RazorPagesMovieContext,** který vytvoří novou třídu kontextu db se správným oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-301">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="7f0bb-302">V tomto případě to bude **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-302">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="7f0bb-303">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-303">Select **Add**.</span></span>

![Obrázek z předchozích pokynů.](model/_static/arpMac.png)

<span data-ttu-id="7f0bb-305">Soubor *appsettings.json* je aktualizován pomocí připojovacího řetězce používaného k připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-305">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="7f0bb-306">Proces vytváření uživatelského příkazu vytvoří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="7f0bb-306">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="7f0bb-307">Vytvořené soubory</span><span class="sxs-lookup"><span data-stu-id="7f0bb-307">Files created</span></span>

* <span data-ttu-id="7f0bb-308">*Stránky/filmy:* Vytvoření, odstranění, podrobnosti, úpravy a rejstřík.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-308">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="7f0bb-309">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="7f0bb-309">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="7f0bb-310">Soubor byl aktualizován.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-310">File updated</span></span>

* <span data-ttu-id="7f0bb-311">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="7f0bb-311">*Startup.cs*</span></span>

<span data-ttu-id="7f0bb-312">Vytvořené a aktualizované soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-312">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="7f0bb-313">Počáteční migrace</span><span class="sxs-lookup"><span data-stu-id="7f0bb-313">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7f0bb-314">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f0bb-314">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7f0bb-315">V této části se konzola Správce balíčků (PMC) používá k:</span><span class="sxs-lookup"><span data-stu-id="7f0bb-315">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="7f0bb-316">Přidejte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-316">Add an initial migration.</span></span>
* <span data-ttu-id="7f0bb-317">Aktualizujte databázi počáteční migrací.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-317">Update the database with the initial migration.</span></span>

<span data-ttu-id="7f0bb-318">V nabídce **Nástroje** vyberte konzolu **Správce balíčků** **Aplikace NuGet .** ></span><span class="sxs-lookup"><span data-stu-id="7f0bb-318">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Nabídka PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="7f0bb-320">Do pmc zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="7f0bb-320">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="7f0bb-321">Příkaz `Add-Migration` generuje kód k vytvoření počátečního schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-321">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="7f0bb-322">Schéma je založeno na modelu zadaném `DbContext` v souboru (V *souboru RazorPagesMovieContext.cs).*</span><span class="sxs-lookup"><span data-stu-id="7f0bb-322">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="7f0bb-323">Argument `InitialCreate` se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-323">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="7f0bb-324">Lze použít libovolný název, ale podle konvence se používá název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-324">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="7f0bb-325">Další informace naleznete v tématu <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-325">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="7f0bb-326">Příkaz `Update-Database` spustí `Up` metodu v souboru *Migrace/\<časové razítko>_InitialCreate.cs.*</span><span class="sxs-lookup"><span data-stu-id="7f0bb-326">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="7f0bb-327">Metoda `Up` vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-327">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7f0bb-328">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f0bb-328">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7f0bb-329">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7f0bb-329">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="7f0bb-330">Předchozí příkazy generují následující upozornění: "*Pro desítkový sloupec Cena nebyl zadán žádný typ. To způsobí, že hodnoty, které mají být tiše zkrácena, pokud se nevejdou do výchozí přesnosti a měřítka. Explicitně určete typ sloupce serveru SQL, který může pojmout všechny hodnoty pomocí 'HasColumnType()'.*" Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-330">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7f0bb-331">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f0bb-331">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="7f0bb-332">Prozkoumejte kontext registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="7f0bb-332">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="7f0bb-333">ASP.NET Core je postaven s [vstřikování závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7f0bb-333">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7f0bb-334">Služby (například kontext EF Core DB) jsou registrovány s vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-334">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="7f0bb-335">Součásti, které vyžadují tyto služby (například Razor Pages) jsou poskytovány tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-335">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="7f0bb-336">Kód konstruktoru, který získá instanci kontextu DB je zobrazen dále v kurzu.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-336">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="7f0bb-337">Nástroj pro vytváření uživatelského funkce automaticky vytvořil kontext DB a zaregistroval jej do kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-337">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="7f0bb-338">Zkontrolujte `Startup.ConfigureServices` metodu.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-338">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="7f0bb-339">Zvýrazněný řádek byl přidán lešení:</span><span class="sxs-lookup"><span data-stu-id="7f0bb-339">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="7f0bb-340">Souřadnice `RazorPagesMovieContext` EF core funkce (vytvořit, číst, aktualizovat, `Movie` odstranit, atd.) pro model.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-340">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="7f0bb-341">Kontext dat`RazorPagesMovieContext`( ) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="7f0bb-341">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="7f0bb-342">Kontext dat určuje, které entity jsou zahrnuty do datového modelu.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-342">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="7f0bb-343">Předchozí kód vytvoří [vlastnost\<DbSet Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-343">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="7f0bb-344">V terminologii entity framework sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-344">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="7f0bb-345">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-345">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="7f0bb-346">Název připojovacího řetězce je předán do kontextu voláním metody na [dbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-346">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="7f0bb-347">Pro místní vývoj [ASP.NET konfiguračnísystém Core](xref:fundamentals/configuration/index) přečte připojovací řetězec ze souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="7f0bb-347">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7f0bb-348">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f0bb-348">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7f0bb-349">Zkontrolujte `Up` metodu.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-349">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7f0bb-350">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7f0bb-350">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7f0bb-351">Zkontrolujte `Up` metodu.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-351">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="7f0bb-352">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="7f0bb-352">Test the app</span></span>

* <span data-ttu-id="7f0bb-353">Spusťte aplikaci `/Movies` a připojte`http://localhost:port/movies`adresu URL v prohlížeči ( ).</span><span class="sxs-lookup"><span data-stu-id="7f0bb-353">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="7f0bb-354">Pokud se zobrazí chyba:</span><span class="sxs-lookup"><span data-stu-id="7f0bb-354">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="7f0bb-355">Jste vynechal [krok migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="7f0bb-355">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="7f0bb-356">Otestujte odkaz **Vytvořit.**</span><span class="sxs-lookup"><span data-stu-id="7f0bb-356">Test the **Create** link.</span></span>

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="7f0bb-358">Je možné, že do pole nebudete moci `Price` zadat desetinná čárka.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-358">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="7f0bb-359">Chcete-li podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, která používají čárku (",") pro desetinnou čárku a pro formáty kalendářních dat mimo americkou angličtinu, musí být aplikace globalizovaná.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-359">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="7f0bb-360">Pokyny pro globalizaci najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="7f0bb-360">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="7f0bb-361">Otestujte odkazy **Upravit**, **Podrobnosti** a **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-361">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="7f0bb-362">Další kurz vysvětluje soubory vytvořené pomocí lešení.</span><span class="sxs-lookup"><span data-stu-id="7f0bb-362">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7f0bb-363">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="7f0bb-363">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="7f0bb-364">[Předchozí: Začínáme](xref:tutorials/razor-pages/razor-pages-start)
> [Další: Scaffolded Razor Stránky](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="7f0bb-364">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
