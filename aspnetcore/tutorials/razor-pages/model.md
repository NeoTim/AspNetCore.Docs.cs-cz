---
title: Přidání modelu do aplikace Razor Pages v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak přidat třídy pro správu filmů v databázi pomocí entity framework core (EF Core).
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: 7f7c2a09b74e6007ee3ea9c038398bac54988186
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488868"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="5c7cc-103">Přidání modelu do aplikace Razor Pages v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5c7cc-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="5c7cc-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5c7cc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="5c7cc-105">V této části jsou přidány třídy pro správu filmů.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-105">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="5c7cc-106">Třídy modelu aplikace používají [entity framework core (EF Core)](/ef/core) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-106">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="5c7cc-107">EF Core je objekt-relační mapovač (O/RM), který zjednodušuje přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-107">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="5c7cc-108">Třídy modelu jsou označovány jako třídy POCO (z "prostý staré objekty CLR"), protože nemají žádnou závislost na EF Core.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="5c7cc-109">Definují vlastnosti dat, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="5c7cc-110">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="5c7cc-110">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5c7cc-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c7cc-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5c7cc-112">Klepněte pravým tlačítkem myši na projekt **RazorPagesMovie** > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="5c7cc-113">Pojmenujte složku *Modely*.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-113">Name the folder *Models*.</span></span>

<span data-ttu-id="5c7cc-114">Klikněte pravým tlačítkem myši na složku *Modely.*</span><span class="sxs-lookup"><span data-stu-id="5c7cc-114">Right click the *Models* folder.</span></span> <span data-ttu-id="5c7cc-115">Vyberte **Přidat** > **třídu**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="5c7cc-116">Pojmenujte třídu **Film**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5c7cc-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c7cc-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5c7cc-118">Přidejte složku s názvem *Modely*.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="5c7cc-119">Přidejte třídu do složky *Models* s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5c7cc-120">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5c7cc-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5c7cc-121">V panelu řešení klepněte pravým tlačítkem myši na projekt **RazorPagesMovie** a pak vyberte **Přidat** > **novou složku...**. Pojmenujte složku *Modely*.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-121">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="5c7cc-122">Klikněte pravým tlačítkem myši na složku *Modely* a potom vyberte **Přidat** > **nový soubor...**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-122">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="5c7cc-123">V dialogovém okně **Nový soubor:**</span><span class="sxs-lookup"><span data-stu-id="5c7cc-123">In the **New File** dialog:</span></span>

  * <span data-ttu-id="5c7cc-124">V levém podokně vyberte **Obecné.**</span><span class="sxs-lookup"><span data-stu-id="5c7cc-124">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="5c7cc-125">V prostředním podokně vyberte **Vyprázdnit třídu.**</span><span class="sxs-lookup"><span data-stu-id="5c7cc-125">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="5c7cc-126">Pojmenujte třídu **Film** a vyberte **Nový**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-126">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="5c7cc-127">Sestavení projektu ověřte, že neexistují žádné chyby kompilace.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-127">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="5c7cc-128">Lešení filmového modelu</span><span class="sxs-lookup"><span data-stu-id="5c7cc-128">Scaffold the movie model</span></span>

<span data-ttu-id="5c7cc-129">V této části je filmový model scaffolded.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-129">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="5c7cc-130">To znamená, že nástroj lešení vytváří stránky pro operace Vytvořit, Číst, Aktualizovat a Odstranit (CRUD) pro filmový model.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-130">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5c7cc-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c7cc-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5c7cc-132">Vytvoření složky *Stránky a filmy:*</span><span class="sxs-lookup"><span data-stu-id="5c7cc-132">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="5c7cc-133">Klikněte pravým tlačítkem myši na složku *Stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-133">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="5c7cc-134">Pojmenujte složku *Filmy*</span><span class="sxs-lookup"><span data-stu-id="5c7cc-134">Name the folder *Movies*</span></span>

<span data-ttu-id="5c7cc-135">Klikněte pravým tlačítkem myši na složku *Stránky/filmy* > **Přidat** > **novou scaffolded položku**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-135">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obrázek z předchozích pokynů.](model/_static/sca.png)

<span data-ttu-id="5c7cc-137">V dialogovém okně **Přidat pomocí okna Písmo šaten** vyberte **Razor Pages pomocí entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-137">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obrázek z předchozích pokynů.](model/_static/add_scaffold.png)

<span data-ttu-id="5c7cc-139">Dokončete dialogové okno **Přidat žiletky pomocí entity frameworku (CRUD):**</span><span class="sxs-lookup"><span data-stu-id="5c7cc-139">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="5c7cc-140">V rozevíracím seznamu **Třída Model** vyberte **Film (RazorPagesMovie.Models)**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-140">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="5c7cc-141">V řádku **třídy kontext** **+** dat vyberte znaménko (plus) a změňte generovaný název z RazorPagesMovie. **Modely**. RazorPagesMovieContext na RazorPagesMovie. **Data**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-141">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="5c7cc-142">[Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) není nutná.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-142">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="5c7cc-143">Vytvoří databázovou třídu kontextu se správným oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-143">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="5c7cc-144">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-144">Select **Add**.</span></span>

![Obrázek z předchozích pokynů.](model/_static/3/arp.png)

<span data-ttu-id="5c7cc-146">Soubor *appsettings.json* je aktualizován pomocí připojovacího řetězce používaného k připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5c7cc-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c7cc-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="5c7cc-148">Otevřete příkazové okno v adresáři projektu (Adresář obsahující soubory *Program.cs*, *Startup.cs*a *.csproj).*</span><span class="sxs-lookup"><span data-stu-id="5c7cc-148">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="5c7cc-149">Nainstalujte nástroj lešení:</span><span class="sxs-lookup"><span data-stu-id="5c7cc-149">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="5c7cc-150">**Pro Windows**: Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5c7cc-150">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="5c7cc-151">**Pro macOS a Linux**: Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5c7cc-151">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5c7cc-152">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5c7cc-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5c7cc-153">Vytvoření složky *Stránky a filmy:*</span><span class="sxs-lookup"><span data-stu-id="5c7cc-153">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="5c7cc-154">Klikněte pravým tlačítkem myši na složku *Stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-154">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="5c7cc-155">Pojmenujte složku *Filmy*</span><span class="sxs-lookup"><span data-stu-id="5c7cc-155">Name the folder *Movies*</span></span>

<span data-ttu-id="5c7cc-156">Klikněte pravým tlačítkem myši na složku *Stránky/Filmy* > **Přidat** > **nové lešení...**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-156">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Obrázek z předchozích pokynů.](model/_static/scaMac.png)

<span data-ttu-id="5c7cc-158">V dialogovém okně **Nové lešení** vyberte **Razor Pages using Entity Framework (CRUD)** > **Next**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-158">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Obrázek z předchozích pokynů.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="5c7cc-160">Dokončete dialogové okno **Přidat žiletky pomocí entity frameworku (CRUD):**</span><span class="sxs-lookup"><span data-stu-id="5c7cc-160">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="5c7cc-161">V rozevíracím seznamu **Třídy Model** vyberte nebo zadejte **film (RazorPagesMovie.Models)**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-161">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="5c7cc-162">Do řádku **třídy Kontext dat** zadejte název nové třídy RazorPagesMovie. **Data**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-162">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="5c7cc-163">[Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) není nutná.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-163">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="5c7cc-164">Vytvoří databázovou třídu kontextu se správným oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-164">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="5c7cc-165">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-165">Select **Add**.</span></span>

![Obrázek z předchozích pokynů.](model/_static/arpMac.png)

<span data-ttu-id="5c7cc-167">Soubor *appsettings.json* je aktualizován pomocí připojovacího řetězce používaného k připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-167">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="5c7cc-168">Přidat nástroje EF</span><span class="sxs-lookup"><span data-stu-id="5c7cc-168">Add EF tools</span></span>

<span data-ttu-id="5c7cc-169">Spusťte následující příkaz CLI jádra .NET:</span><span class="sxs-lookup"><span data-stu-id="5c7cc-169">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="5c7cc-170">Předchozí příkaz přidá základní nástroje entity frameworku pro rozhraní příkazového příkazu .NET Core.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-170">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="5c7cc-171">Vytvořené soubory</span><span class="sxs-lookup"><span data-stu-id="5c7cc-171">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5c7cc-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c7cc-172">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5c7cc-173">Proces vytváření uživatelského příkazu vytvoří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="5c7cc-173">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="5c7cc-174">*Stránky/filmy:* Vytvoření, odstranění, podrobnosti, úpravy a rejstřík.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-174">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="5c7cc-175">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="5c7cc-175">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="5c7cc-176">Aktualizováno</span><span class="sxs-lookup"><span data-stu-id="5c7cc-176">Updated</span></span>

* <span data-ttu-id="5c7cc-177">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="5c7cc-177">*Startup.cs*</span></span>

<span data-ttu-id="5c7cc-178">Vytvořené a aktualizované soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-178">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5c7cc-179">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5c7cc-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5c7cc-180">Proces vytváření uživatelského příkazu vytvoří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="5c7cc-180">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="5c7cc-181">*Stránky/filmy:* Vytvoření, odstranění, podrobnosti, úpravy a rejstřík.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-181">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="5c7cc-182">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="5c7cc-182">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="5c7cc-183">Aktualizováno</span><span class="sxs-lookup"><span data-stu-id="5c7cc-183">Updated</span></span>

* <span data-ttu-id="5c7cc-184">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="5c7cc-184">*Startup.cs*</span></span>

<span data-ttu-id="5c7cc-185">Vytvořené a aktualizované soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-185">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5c7cc-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c7cc-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5c7cc-187">Proces vytváření uživatelského příkazu vytvoří následující soubory:</span><span class="sxs-lookup"><span data-stu-id="5c7cc-187">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="5c7cc-188">*Stránky/filmy:* Vytvoření, odstranění, podrobnosti, úpravy a rejstřík.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-188">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="5c7cc-189">Vytvořené soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-189">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="5c7cc-190">Počáteční migrace</span><span class="sxs-lookup"><span data-stu-id="5c7cc-190">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5c7cc-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c7cc-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5c7cc-192">V této části se konzola Správce balíčků (PMC) používá k:</span><span class="sxs-lookup"><span data-stu-id="5c7cc-192">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="5c7cc-193">Přidejte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-193">Add an initial migration.</span></span>
* <span data-ttu-id="5c7cc-194">Aktualizujte databázi počáteční migrací.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-194">Update the database with the initial migration.</span></span>

<span data-ttu-id="5c7cc-195">V nabídce **Nástroje** vyberte konzolu **Správce balíčků** **Aplikace NuGet .** ></span><span class="sxs-lookup"><span data-stu-id="5c7cc-195">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Nabídka PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="5c7cc-197">Do pmc zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="5c7cc-197">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="5c7cc-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c7cc-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5c7cc-199">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5c7cc-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="5c7cc-200">Předchozí příkazy generují následující upozornění: "Pro desítkový sloupec Cena nebyl zadán žádný typ.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-200">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="5c7cc-201">To způsobí, že hodnoty, které mají být tiše zkrácena, pokud se nevejdou do výchozí přesnosti a měřítka.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="5c7cc-202">Explicitně určete typ sloupce serveru SQL, který může pojmout všechny hodnoty pomocí 'HasColumnType()'."</span><span class="sxs-lookup"><span data-stu-id="5c7cc-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="5c7cc-203">Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-203">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="5c7cc-204">Příkaz Migrace generuje kód pro vytvoření počátečního schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-204">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="5c7cc-205">Schéma je založeno na modelu zadaném `DbContext`v .</span><span class="sxs-lookup"><span data-stu-id="5c7cc-205">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="5c7cc-206">Argument `InitialCreate` se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-206">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="5c7cc-207">Lze použít libovolný název, ale podle konvence je vybrán název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-207">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="5c7cc-208">Příkaz `update` spustí `Up` metodu v migracích, které nebyly použity.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-208">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="5c7cc-209">V tomto `update` případě `Up` spustí metodu v *migraci /\<časové razítko>_InitialCreate.cs* souboru, který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-209">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5c7cc-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c7cc-210">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="5c7cc-211">Prozkoumejte kontext registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="5c7cc-211">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="5c7cc-212">ASP.NET Core je postaven s [vstřikování závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5c7cc-212">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5c7cc-213">Služby (například kontext EF Core DB) jsou registrovány s vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-213">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="5c7cc-214">Součásti, které vyžadují tyto služby (například Razor Pages) jsou poskytovány tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-214">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="5c7cc-215">Kód konstruktoru, který získá instanci kontextu DB je zobrazen dále v kurzu.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-215">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="5c7cc-216">Nástroj pro vytváření uživatelského funkce automaticky vytvořil kontext DB a zaregistroval jej do kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-216">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="5c7cc-217">Zkontrolujte `Startup.ConfigureServices` metodu.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-217">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5c7cc-218">Zvýrazněný řádek byl přidán lešení:</span><span class="sxs-lookup"><span data-stu-id="5c7cc-218">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="5c7cc-219">Souřadnice `RazorPagesMovieContext` EF core funkce (vytvořit, číst, aktualizovat, `Movie` odstranit, atd.) pro model.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-219">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="5c7cc-220">Kontext dat`RazorPagesMovieContext`( ) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="5c7cc-220">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="5c7cc-221">Kontext dat určuje, které entity jsou zahrnuty do datového modelu.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-221">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="5c7cc-222">Předchozí kód vytvoří [vlastnost\<DbSet Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-222">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="5c7cc-223">V terminologii entity framework sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-223">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="5c7cc-224">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-224">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="5c7cc-225">Název připojovacího řetězce je předán do kontextu voláním metody na [dbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-225">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="5c7cc-226">Pro místní vývoj [ASP.NET konfiguračnísystém Core](xref:fundamentals/configuration/index) přečte připojovací řetězec ze souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="5c7cc-226">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5c7cc-227">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c7cc-227">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5c7cc-228">Zkontrolujte `Up` metodu.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-228">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5c7cc-229">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5c7cc-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5c7cc-230">Zkontrolujte `Up` metodu.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-230">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="5c7cc-231">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="5c7cc-231">Test the app</span></span>

* <span data-ttu-id="5c7cc-232">Spusťte aplikaci `/Movies` a připojte`http://localhost:port/movies`adresu URL v prohlížeči ( ).</span><span class="sxs-lookup"><span data-stu-id="5c7cc-232">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="5c7cc-233">Pokud se zobrazí chyba:</span><span class="sxs-lookup"><span data-stu-id="5c7cc-233">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="5c7cc-234">Jste vynechal [krok migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="5c7cc-234">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="5c7cc-235">Otestujte odkaz **Vytvořit.**</span><span class="sxs-lookup"><span data-stu-id="5c7cc-235">Test the **Create** link.</span></span>

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="5c7cc-237">Je možné, že do pole nebudete moci `Price` zadat desetinná čárka.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-237">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="5c7cc-238">Chcete-li podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, která používají čárku (",") pro desetinnou čárku a pro formáty kalendářních dat mimo americkou angličtinu, musí být aplikace globalizovaná.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-238">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="5c7cc-239">Pokyny pro globalizaci najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="5c7cc-239">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="5c7cc-240">Otestujte odkazy **Upravit**, **Podrobnosti** a **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-240">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="5c7cc-241">Další kurz vysvětluje soubory vytvořené pomocí lešení.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-241">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5c7cc-242">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5c7cc-242">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="5c7cc-243">[Předchozí: Začínáme](xref:tutorials/razor-pages/razor-pages-start)
> [Další: Scaffolded Razor Stránky](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="5c7cc-243">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5c7cc-244">V této části jsou přidány třídy pro správu filmů v [databázi SQLite](https://www.sqlite.org/index.html)napříč platformami .</span><span class="sxs-lookup"><span data-stu-id="5c7cc-244">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="5c7cc-245">Aplikace vytvořené ze ASP.NET šablony Core používají databázi SQLite.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-245">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="5c7cc-246">Třídy modelu aplikace se používají s [entity Framework Core (EF Core)](/ef/core) [(SQLite EF Základní databáze zprostředkovatele)](/ef/core/providers/sqlite)pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-246">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="5c7cc-247">EF Core je objekt-relační mapování (ORM) framework, který zjednodušuje přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-247">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="5c7cc-248">Třídy modelu jsou označovány jako třídy POCO (z "prostý staré objekty CLR"), protože nemají žádnou závislost na EF Core.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-248">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="5c7cc-249">Definují vlastnosti dat, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-249">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="5c7cc-250">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="5c7cc-250">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5c7cc-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c7cc-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5c7cc-252">Klepněte pravým tlačítkem myši na projekt **RazorPagesMovie** > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-252">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="5c7cc-253">Pojmenujte složku *Modely*.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-253">Name the folder *Models*.</span></span>

<span data-ttu-id="5c7cc-254">Klikněte pravým tlačítkem myši na složku *Modely.*</span><span class="sxs-lookup"><span data-stu-id="5c7cc-254">Right click the *Models* folder.</span></span> <span data-ttu-id="5c7cc-255">Vyberte **Přidat** > **třídu**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-255">Select **Add** > **Class**.</span></span> <span data-ttu-id="5c7cc-256">Pojmenujte třídu **Film**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-256">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5c7cc-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c7cc-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5c7cc-258">Přidejte složku s názvem *Modely*.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-258">Add a folder named *Models*.</span></span>
* <span data-ttu-id="5c7cc-259">Přidejte třídu do složky *Models* s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-259">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5c7cc-260">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5c7cc-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5c7cc-261">V Průzkumníku řešení klepněte pravým tlačítkem myši na projekt **RazorPagesMovie** a pak vyberte **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-261">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="5c7cc-262">Pojmenujte složku *Modely*.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-262">Name the folder *Models*.</span></span>
* <span data-ttu-id="5c7cc-263">Klikněte pravým tlačítkem myši na složku *Modely* a potom vyberte **Přidat** > **nový soubor**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-263">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="5c7cc-264">V dialogovém okně **Nový soubor:**</span><span class="sxs-lookup"><span data-stu-id="5c7cc-264">In the **New File** dialog:</span></span>

  * <span data-ttu-id="5c7cc-265">V levém podokně vyberte **Obecné.**</span><span class="sxs-lookup"><span data-stu-id="5c7cc-265">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="5c7cc-266">V prostředním podokně vyberte **Vyprázdnit třídu.**</span><span class="sxs-lookup"><span data-stu-id="5c7cc-266">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="5c7cc-267">Pojmenujte třídu **Film** a vyberte **Nový**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-267">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="5c7cc-268">Sestavení projektu ověřte, že neexistují žádné chyby kompilace.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-268">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="5c7cc-269">Lešení filmového modelu</span><span class="sxs-lookup"><span data-stu-id="5c7cc-269">Scaffold the movie model</span></span>

<span data-ttu-id="5c7cc-270">V této části je filmový model scaffolded.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-270">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="5c7cc-271">To znamená, že nástroj lešení vytváří stránky pro operace Vytvořit, Číst, Aktualizovat a Odstranit (CRUD) pro filmový model.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-271">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5c7cc-272">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c7cc-272">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5c7cc-273">Vytvoření složky *Stránky a filmy:*</span><span class="sxs-lookup"><span data-stu-id="5c7cc-273">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="5c7cc-274">Klikněte pravým tlačítkem myši na složku *Stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-274">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="5c7cc-275">Pojmenujte složku *Filmy*</span><span class="sxs-lookup"><span data-stu-id="5c7cc-275">Name the folder *Movies*</span></span>

<span data-ttu-id="5c7cc-276">Klikněte pravým tlačítkem myši na složku *Stránky/filmy* > **Přidat** > **novou scaffolded položku**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-276">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obrázek z předchozích pokynů.](model/_static/sca.png)

<span data-ttu-id="5c7cc-278">V dialogovém okně **Přidat pomocí okna Písmo šaten** vyberte **Razor Pages pomocí entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-278">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obrázek z předchozích pokynů.](model/_static/add_scaffold.png)

<span data-ttu-id="5c7cc-280">Dokončete dialogové okno **Přidat žiletky pomocí entity frameworku (CRUD):**</span><span class="sxs-lookup"><span data-stu-id="5c7cc-280">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="5c7cc-281">V rozevíracím seznamu **Třída Model** vyberte **Film (RazorPagesMovie.Models)**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-281">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="5c7cc-282">V řádku **třídy kontext** **+** dat vyberte znaménko (plus) a přijměte generovaný název **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-282">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="5c7cc-283">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-283">Select **Add**.</span></span>

![Obrázek z předchozích pokynů.](model/_static/arp.png)

<span data-ttu-id="5c7cc-285">Soubor *appsettings.json* je aktualizován pomocí připojovacího řetězce používaného k připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-285">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5c7cc-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c7cc-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="5c7cc-287">Otevřete příkazové okno v adresáři projektu (Adresář obsahující soubory *Program.cs*, *Startup.cs*a *.csproj).*</span><span class="sxs-lookup"><span data-stu-id="5c7cc-287">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="5c7cc-288">**Pro Windows**: Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5c7cc-288">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="5c7cc-289">**Pro macOS a Linux**: Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5c7cc-289">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5c7cc-290">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5c7cc-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5c7cc-291">Vytvoření složky *Stránky a filmy:*</span><span class="sxs-lookup"><span data-stu-id="5c7cc-291">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="5c7cc-292">Klikněte pravým tlačítkem myši na složku *Stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-292">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="5c7cc-293">Pojmenujte složku *Filmy*</span><span class="sxs-lookup"><span data-stu-id="5c7cc-293">Name the folder *Movies*</span></span>

<span data-ttu-id="5c7cc-294">Klikněte pravým tlačítkem myši na složku *Stránky/filmy* > **Přidat** > **novou scaffolded položku**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-294">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obrázek z předchozích pokynů.](model/_static/scaMac.png)

<span data-ttu-id="5c7cc-296">V dialogovém okně **Přidat nové lešení** vyberte **Razor Pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-296">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obrázek z předchozích pokynů.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="5c7cc-298">Dokončete dialogové okno **Přidat žiletky pomocí entity frameworku (CRUD):**</span><span class="sxs-lookup"><span data-stu-id="5c7cc-298">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="5c7cc-299">V rozevíracím seznamu **Třída Model** vyberte nebo zadejte **film**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-299">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="5c7cc-300">Do řádku **třídy kontextu Data** zadejte **RazorPagesMovieContext,** který vytvoří novou třídu kontextu db se správným oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-300">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="5c7cc-301">V tomto případě to bude **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-301">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="5c7cc-302">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-302">Select **Add**.</span></span>

![Obrázek z předchozích pokynů.](model/_static/arpMac.png)

<span data-ttu-id="5c7cc-304">Soubor *appsettings.json* je aktualizován pomocí připojovacího řetězce používaného k připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-304">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="5c7cc-305">Proces vytváření uživatelského příkazu vytvoří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="5c7cc-305">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="5c7cc-306">Vytvořené soubory</span><span class="sxs-lookup"><span data-stu-id="5c7cc-306">Files created</span></span>

* <span data-ttu-id="5c7cc-307">*Stránky/filmy:* Vytvoření, odstranění, podrobnosti, úpravy a rejstřík.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-307">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="5c7cc-308">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="5c7cc-308">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="5c7cc-309">Soubor byl aktualizován.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-309">File updated</span></span>

* <span data-ttu-id="5c7cc-310">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="5c7cc-310">*Startup.cs*</span></span>

<span data-ttu-id="5c7cc-311">Vytvořené a aktualizované soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-311">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="5c7cc-312">Počáteční migrace</span><span class="sxs-lookup"><span data-stu-id="5c7cc-312">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5c7cc-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c7cc-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5c7cc-314">V této části se konzola Správce balíčků (PMC) používá k:</span><span class="sxs-lookup"><span data-stu-id="5c7cc-314">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="5c7cc-315">Přidejte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-315">Add an initial migration.</span></span>
* <span data-ttu-id="5c7cc-316">Aktualizujte databázi počáteční migrací.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-316">Update the database with the initial migration.</span></span>

<span data-ttu-id="5c7cc-317">V nabídce **Nástroje** vyberte konzolu **Správce balíčků** **Aplikace NuGet .** ></span><span class="sxs-lookup"><span data-stu-id="5c7cc-317">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Nabídka PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="5c7cc-319">Do pmc zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="5c7cc-319">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="5c7cc-320">Příkaz `Add-Migration` generuje kód k vytvoření počátečního schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-320">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="5c7cc-321">Schéma je založeno na modelu zadaném `DbContext` v souboru (V *souboru RazorPagesMovieContext.cs).*</span><span class="sxs-lookup"><span data-stu-id="5c7cc-321">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="5c7cc-322">Argument `InitialCreate` se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-322">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="5c7cc-323">Lze použít libovolný název, ale podle konvence se používá název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-323">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="5c7cc-324">Další informace naleznete v tématu <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-324">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="5c7cc-325">Příkaz `Update-Database` spustí `Up` metodu v souboru *Migrace/\<časové razítko>_InitialCreate.cs.*</span><span class="sxs-lookup"><span data-stu-id="5c7cc-325">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="5c7cc-326">Metoda `Up` vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-326">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5c7cc-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c7cc-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5c7cc-328">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5c7cc-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="5c7cc-329">Předchozí příkazy generují následující upozornění: "*Pro desítkový sloupec Cena nebyl zadán žádný typ. To způsobí, že hodnoty, které mají být tiše zkrácena, pokud se nevejdou do výchozí přesnosti a měřítka. Explicitně určete typ sloupce serveru SQL, který může pojmout všechny hodnoty pomocí 'HasColumnType()'.*" Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-329">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5c7cc-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c7cc-330">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="5c7cc-331">Prozkoumejte kontext registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="5c7cc-331">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="5c7cc-332">ASP.NET Core je postaven s [vstřikování závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5c7cc-332">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5c7cc-333">Služby (například kontext EF Core DB) jsou registrovány s vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-333">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="5c7cc-334">Součásti, které vyžadují tyto služby (například Razor Pages) jsou poskytovány tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-334">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="5c7cc-335">Kód konstruktoru, který získá instanci kontextu DB je zobrazen dále v kurzu.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-335">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="5c7cc-336">Nástroj pro vytváření uživatelského funkce automaticky vytvořil kontext DB a zaregistroval jej do kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-336">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="5c7cc-337">Zkontrolujte `Startup.ConfigureServices` metodu.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-337">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5c7cc-338">Zvýrazněný řádek byl přidán lešení:</span><span class="sxs-lookup"><span data-stu-id="5c7cc-338">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="5c7cc-339">Souřadnice `RazorPagesMovieContext` EF core funkce (vytvořit, číst, aktualizovat, `Movie` odstranit, atd.) pro model.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-339">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="5c7cc-340">Kontext dat`RazorPagesMovieContext`( ) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="5c7cc-340">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="5c7cc-341">Kontext dat určuje, které entity jsou zahrnuty do datového modelu.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-341">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="5c7cc-342">Předchozí kód vytvoří [vlastnost\<DbSet Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-342">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="5c7cc-343">V terminologii entity framework sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-343">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="5c7cc-344">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-344">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="5c7cc-345">Název připojovacího řetězce je předán do kontextu voláním metody na [dbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="5c7cc-346">Pro místní vývoj [ASP.NET konfiguračnísystém Core](xref:fundamentals/configuration/index) přečte připojovací řetězec ze souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="5c7cc-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5c7cc-347">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c7cc-347">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5c7cc-348">Zkontrolujte `Up` metodu.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-348">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5c7cc-349">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5c7cc-349">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5c7cc-350">Zkontrolujte `Up` metodu.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-350">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="5c7cc-351">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="5c7cc-351">Test the app</span></span>

* <span data-ttu-id="5c7cc-352">Spusťte aplikaci `/Movies` a připojte`http://localhost:port/movies`adresu URL v prohlížeči ( ).</span><span class="sxs-lookup"><span data-stu-id="5c7cc-352">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="5c7cc-353">Pokud se zobrazí chyba:</span><span class="sxs-lookup"><span data-stu-id="5c7cc-353">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="5c7cc-354">Jste vynechal [krok migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="5c7cc-354">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="5c7cc-355">Otestujte odkaz **Vytvořit.**</span><span class="sxs-lookup"><span data-stu-id="5c7cc-355">Test the **Create** link.</span></span>

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="5c7cc-357">Je možné, že do pole nebudete moci `Price` zadat desetinná čárka.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-357">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="5c7cc-358">Chcete-li podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, která používají čárku (",") pro desetinnou čárku a pro formáty kalendářních dat mimo americkou angličtinu, musí být aplikace globalizovaná.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-358">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="5c7cc-359">Pokyny pro globalizaci najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="5c7cc-359">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="5c7cc-360">Otestujte odkazy **Upravit**, **Podrobnosti** a **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-360">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="5c7cc-361">Další kurz vysvětluje soubory vytvořené pomocí lešení.</span><span class="sxs-lookup"><span data-stu-id="5c7cc-361">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5c7cc-362">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5c7cc-362">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="5c7cc-363">[Předchozí: Začínáme](xref:tutorials/razor-pages/razor-pages-start)
> [Další: Scaffolded Razor Stránky](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="5c7cc-363">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
