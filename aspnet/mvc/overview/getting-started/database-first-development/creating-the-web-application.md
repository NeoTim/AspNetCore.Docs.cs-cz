---
uid: mvc/overview/getting-started/database-first-development/creating-the-web-application
title: 'EF Database First s ASP.NET MVC: vytvoření webové aplikace a datových modelů | Dokumentace Microsoftu'
author: Rick-Anderson
description: Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi. Tento kurz seri...
ms.author: riande
ms.date: 10/01/2014
ms.assetid: bc8f2bd5-ff57-4dcd-8418-a5bd517d8953
msc.legacyurl: /mvc/overview/getting-started/database-first-development/creating-the-web-application
msc.type: authoredcontent
ms.openlocfilehash: 6679b61326bd016481d96a4b5d58ec006f86b633
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51020794"
---
<a name="ef-database-first-with-aspnet-mvc-creating-the-web-application-and-data-models"></a><span data-ttu-id="3d25d-104">EF Database First s ASP.NET MVC: vytvoření webové aplikace a datových modelů</span><span class="sxs-lookup"><span data-stu-id="3d25d-104">EF Database First with ASP.NET MVC: Creating the Web Application and Data Models</span></span>
====================
<span data-ttu-id="3d25d-105">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="3d25d-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="3d25d-106">Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi.</span><span class="sxs-lookup"><span data-stu-id="3d25d-106">Using MVC, Entity Framework, and ASP.NET Scaffolding, you can create a web application that provides an interface to an existing database.</span></span> <span data-ttu-id="3d25d-107">V této sérii kurzů se dozvíte, jak automaticky vygenerovat kód, který umožňuje uživatelům zobrazit, upravit, vytvořit a odstranit data, která se nachází v databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="3d25d-107">This tutorial series shows you how to automatically generate code that enables users to display, edit, create, and delete data that resides in a database table.</span></span> <span data-ttu-id="3d25d-108">Generovaný kód odpovídá sloupců v tabulce databáze.</span><span class="sxs-lookup"><span data-stu-id="3d25d-108">The generated code corresponds to the columns in the database table.</span></span>
> 
> <span data-ttu-id="3d25d-109">Tato části této série se zaměřuje na vytvoření webové aplikace a generování datové modely založené na databázových tabulek.</span><span class="sxs-lookup"><span data-stu-id="3d25d-109">This part of the series focuses on creating the web application, and generating the data models based on your database tables.</span></span>


## <a name="create-a-new-aspnet-web-application"></a><span data-ttu-id="3d25d-110">Vytvořit novou webovou aplikaci ASP.NET</span><span class="sxs-lookup"><span data-stu-id="3d25d-110">Create a new ASP.NET Web Application</span></span>

<span data-ttu-id="3d25d-111">V nové řešení nebo do stejného řešení jako databázového projektu vytvořte nový projekt v sadě Visual Studio a vyberte **webová aplikace ASP.NET** šablony.</span><span class="sxs-lookup"><span data-stu-id="3d25d-111">In either a new solution or the same solution as the database project, create a new project in Visual Studio and select the **ASP.NET Web Application** template.</span></span> <span data-ttu-id="3d25d-112">Pojmenujte projekt **ContosoSite**.</span><span class="sxs-lookup"><span data-stu-id="3d25d-112">Name the project **ContosoSite**.</span></span>

![Vytvoření projektu](creating-the-web-application/_static/image1.png)

<span data-ttu-id="3d25d-114">Klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="3d25d-114">Click **OK**.</span></span>

<span data-ttu-id="3d25d-115">V okně Nový projekt ASP.NET, vyberte **MVC** šablony.</span><span class="sxs-lookup"><span data-stu-id="3d25d-115">In the New ASP.NET Project window, select the **MVC** template.</span></span> <span data-ttu-id="3d25d-116">Můžete vymazat **hostovat v cloudu** možnost prozatím, protože budete nasazovat aplikaci do cloudu později.</span><span class="sxs-lookup"><span data-stu-id="3d25d-116">You can clear the **Host in the cloud** option for now because you will deploy the application to the cloud later.</span></span> <span data-ttu-id="3d25d-117">Klikněte na tlačítko **OK** k vytvoření aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d25d-117">Click **OK** to create the application.</span></span>

![Vyberte šablonu mvc](creating-the-web-application/_static/image2.png)

<span data-ttu-id="3d25d-119">Projekt je vytvořen s výchozí soubory a složky.</span><span class="sxs-lookup"><span data-stu-id="3d25d-119">The project is created with the default files and folders.</span></span>

<span data-ttu-id="3d25d-120">V tomto kurzu budete používat Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="3d25d-120">In this tutorial, you will use Entity Framework 6.</span></span> <span data-ttu-id="3d25d-121">Verze rozhraní Entity Framework můžete zkontrolovat ve vašem projektu prostřednictvím okna spravovat balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="3d25d-121">You can double-check the version of Entity Framework in your project through the Manage NuGet Packages window.</span></span> <span data-ttu-id="3d25d-122">V případě potřeby aktualizujte na verzi rozhraní Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="3d25d-122">If necessary, update your version of Entity Framework.</span></span>

![Zobrazit verze](creating-the-web-application/_static/image3.png)

## <a name="generate-the-models"></a><span data-ttu-id="3d25d-124">Generovat modelů</span><span class="sxs-lookup"><span data-stu-id="3d25d-124">Generate the models</span></span>

<span data-ttu-id="3d25d-125">Modely Entity Framework teď vytvoříte z databázových tabulek.</span><span class="sxs-lookup"><span data-stu-id="3d25d-125">You will now create Entity Framework models from the database tables.</span></span> <span data-ttu-id="3d25d-126">Tyto modely jsou třídy, které budete používat pro práci s daty.</span><span class="sxs-lookup"><span data-stu-id="3d25d-126">These models are classes that you will use to work with the data.</span></span> <span data-ttu-id="3d25d-127">Každý model zrcadlí tabulky v databázi a obsahuje vlastnosti, které odpovídají sloupcům v tabulce.</span><span class="sxs-lookup"><span data-stu-id="3d25d-127">Each model mirrors a table in the database and contains properties that correspond to the columns in the table.</span></span>

<span data-ttu-id="3d25d-128">Klikněte pravým tlačítkem myši **modely** a pak zvolte položku **přidat** a **nová položka**.</span><span class="sxs-lookup"><span data-stu-id="3d25d-128">Right-click the **Models** folder, and select **Add** and **New Item**.</span></span>

![Přidat novou položku](creating-the-web-application/_static/image4.png)

<span data-ttu-id="3d25d-130">V okně Přidat novou položku vyberte **Data** v levém podokně a **datový Model Entity ADO.NET** z možností v prostředním podokně.</span><span class="sxs-lookup"><span data-stu-id="3d25d-130">In the Add New Item window, select **Data** in the left pane and **ADO.NET Entity Data Model** from the options in the center pane.</span></span> <span data-ttu-id="3d25d-131">Pojmenujte nový soubor modelu **ContosoModel**.</span><span class="sxs-lookup"><span data-stu-id="3d25d-131">Name the new model file **ContosoModel**.</span></span>

![Vytvoření modelu](creating-the-web-application/_static/image5.png)

<span data-ttu-id="3d25d-133">Klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="3d25d-133">Click **Add**.</span></span>

<span data-ttu-id="3d25d-134">V Průvodci modelem Entity Data vyberte **EF designeru z databáze**.</span><span class="sxs-lookup"><span data-stu-id="3d25d-134">In the Entity Data Model Wizard, select **EF Designer from database**.</span></span>

![Generovat z databáze](creating-the-web-application/_static/image6.png)

<span data-ttu-id="3d25d-136">Klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="3d25d-136">Click **Next**.</span></span>

<span data-ttu-id="3d25d-137">Pokud máte připojení k databázi definována v rámci vývojového prostředí, může se zobrazit jeden z těchto připojení předem vybraná.</span><span class="sxs-lookup"><span data-stu-id="3d25d-137">If you have database connections defined within your development environment, you may see one of these connections pre-selected.</span></span> <span data-ttu-id="3d25d-138">Chcete vytvořit nové připojení k databázi, kterou jste vytvořili v první části tohoto kurzu.</span><span class="sxs-lookup"><span data-stu-id="3d25d-138">However, you want to create a new connection to the database you created in the first part of this tutorial.</span></span> <span data-ttu-id="3d25d-139">Klikněte na tlačítko **nové připojení** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="3d25d-139">Click the **New Connection** button.</span></span>

![připojení k databázi](creating-the-web-application/_static/image7.png)

<span data-ttu-id="3d25d-141">V okně Vlastnosti připojení zadat název místního serveru, kde byla vytvořena databáze (v tomto případě **(localdb) \ProjectsV12**).</span><span class="sxs-lookup"><span data-stu-id="3d25d-141">In the Connection Properties window, provide the name of the local server where your database was created (in this case **(localdb)\ProjectsV12**).</span></span> <span data-ttu-id="3d25d-142">Po zadání názvu serveru, vyberte z dostupných databází ContosoUniversityData.</span><span class="sxs-lookup"><span data-stu-id="3d25d-142">After providing the server name, select the ContosoUniversityData from the available databases.</span></span>

![nastavit vlastnosti připojení](creating-the-web-application/_static/image8.png)

<span data-ttu-id="3d25d-144">Klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="3d25d-144">Click **OK**.</span></span>

<span data-ttu-id="3d25d-145">Vlastnosti správné připojení se teď zobrazují.</span><span class="sxs-lookup"><span data-stu-id="3d25d-145">The correct connection properties are now displayed.</span></span> <span data-ttu-id="3d25d-146">Můžete použít výchozí název připojení v souboru Web.Config</span><span class="sxs-lookup"><span data-stu-id="3d25d-146">You can use the default name for connection in the Web.Config file</span></span>

![nastavení připojení](creating-the-web-application/_static/image9.png)

<span data-ttu-id="3d25d-148">Klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="3d25d-148">Click **Next**.</span></span>

<span data-ttu-id="3d25d-149">Vyberte **tabulky** ke generování modely pro všechny tři tabulky.</span><span class="sxs-lookup"><span data-stu-id="3d25d-149">Select **Tables** to generate models for all three tables.</span></span>

![Vybrat tabulky](creating-the-web-application/_static/image10.png)

<span data-ttu-id="3d25d-151">Klikněte na tlačítko **Dokončit**.</span><span class="sxs-lookup"><span data-stu-id="3d25d-151">Click **Finish**.</span></span>

<span data-ttu-id="3d25d-152">Pokud se zobrazí upozornění zabezpečení, vyberte **OK** pokračoval šablony.</span><span class="sxs-lookup"><span data-stu-id="3d25d-152">If you receive a security warning, select **OK** to continue running the template.</span></span>

<span data-ttu-id="3d25d-153">Modely se generují z databázové tabulky a diagramu se zobrazí, který ukazuje vlastnosti a vztahy mezi tabulkami.</span><span class="sxs-lookup"><span data-stu-id="3d25d-153">The models are generated from the database tables, and a diagram is displayed that shows the properties and relationships between the tables.</span></span>

![Diagram modelu](creating-the-web-application/_static/image11.png)

<span data-ttu-id="3d25d-155">Složku modely nyní obsahuje mnoho nových souborů související s modely, které byly generovány z databáze.</span><span class="sxs-lookup"><span data-stu-id="3d25d-155">The Models folder now includes many new files related to the models that were generated from the database.</span></span>

![Zobrazit nové soubory modelu](creating-the-web-application/_static/image12.png)

<span data-ttu-id="3d25d-157">**ContosoModel.Context.cs** soubor obsahuje třídu, která je odvozena z **DbContext** třídy a poskytuje vlastnosti pro každou třídu modelu, odpovídající do databázové tabulky.</span><span class="sxs-lookup"><span data-stu-id="3d25d-157">The **ContosoModel.Context.cs** file contains a class that derives from the **DbContext** class, and provides a property for each model class that corresponds to a database table.</span></span> <span data-ttu-id="3d25d-158">**Course.cs**, **Enrollment.cs**, a **Student.cs** soubory obsahují, které představují tabulky databáze třídy modelu.</span><span class="sxs-lookup"><span data-stu-id="3d25d-158">The **Course.cs**, **Enrollment.cs**, and **Student.cs** files contain the model classes that represent the databases tables.</span></span> <span data-ttu-id="3d25d-159">Třídy kontextu a tříd modelu bude používat při práci s generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="3d25d-159">You will use both the context class and the model classes when working with scaffolding.</span></span>

<span data-ttu-id="3d25d-160">Než budete pokračovat s tímto kurzem, sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="3d25d-160">Before proceeding with this tutorial, build the project.</span></span> <span data-ttu-id="3d25d-161">V další části bude generovat kód založený na datové modely, ale, že část nebude fungovat, pokud projekt nebyl sestaven.</span><span class="sxs-lookup"><span data-stu-id="3d25d-161">In the next section, you will generate code based on the data models, but that section will not work if the project has not been built.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="3d25d-162">[Předchozí](setting-up-database.md)
> [další](generating-views.md)</span><span class="sxs-lookup"><span data-stu-id="3d25d-162">[Previous](setting-up-database.md)
[Next](generating-views.md)</span></span>
