---
uid: mvc/overview/getting-started/database-first-development/changing-the-database
title: 'EF Database First s ASP.NET MVC: Změna databáze | Dokumentace Microsoftu'
author: Rick-Anderson
description: Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi. Tento kurz seri...
ms.author: riande
ms.date: 10/01/2014
ms.assetid: cfd5c083-a319-482e-8f25-5b38caa93954
msc.legacyurl: /mvc/overview/getting-started/database-first-development/changing-the-database
msc.type: authoredcontent
ms.openlocfilehash: 8d0eff37ced757cd8be74f8171c9aaa430940010
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021271"
---
<a name="ef-database-first-with-aspnet-mvc-changing-the-database"></a><span data-ttu-id="5974c-104">EF Database First s ASP.NET MVC: Změna databáze</span><span class="sxs-lookup"><span data-stu-id="5974c-104">EF Database First with ASP.NET MVC: Changing the Database</span></span>
====================
<span data-ttu-id="5974c-105">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="5974c-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="5974c-106">Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi.</span><span class="sxs-lookup"><span data-stu-id="5974c-106">Using MVC, Entity Framework, and ASP.NET Scaffolding, you can create a web application that provides an interface to an existing database.</span></span> <span data-ttu-id="5974c-107">V této sérii kurzů se dozvíte, jak automaticky vygenerovat kód, který umožňuje uživatelům zobrazit, upravit, vytvořit a odstranit data, která se nachází v databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="5974c-107">This tutorial series shows you how to automatically generate code that enables users to display, edit, create, and delete data that resides in a database table.</span></span> <span data-ttu-id="5974c-108">Generovaný kód odpovídá sloupců v tabulce databáze.</span><span class="sxs-lookup"><span data-stu-id="5974c-108">The generated code corresponds to the columns in the database table.</span></span>
> 
> <span data-ttu-id="5974c-109">Tato části této série se zaměřuje na provádět aktualizaci struktura databáze změny a šíření tuto změnu v rámci webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="5974c-109">This part of the series focuses on making an update to the database structure and propagating that change throughout the web application.</span></span>


## <a name="add-a-column"></a><span data-ttu-id="5974c-110">Přidat sloupec</span><span class="sxs-lookup"><span data-stu-id="5974c-110">Add a column</span></span>

<span data-ttu-id="5974c-111">Při aktualizaci struktury tabulky v databázi, je potřeba zajistit, že vaše změna rozšířena na datový model, zobrazení a kontroler.</span><span class="sxs-lookup"><span data-stu-id="5974c-111">If you update the structure of a table in your database, you need to ensure that your change is propagated to the data model, views, and controller.</span></span>

<span data-ttu-id="5974c-112">Pro účely tohoto kurzu přidáte nový sloupec do tabulky Student k zaznamenání prostřední jméno studenta.</span><span class="sxs-lookup"><span data-stu-id="5974c-112">For this tutorial, you will add a new column to the Student table to record the middle name of the student.</span></span> <span data-ttu-id="5974c-113">Chcete-li přidat tento sloupec, otevřete projekt databáze a otevřete soubor Student.sql.</span><span class="sxs-lookup"><span data-stu-id="5974c-113">To add this column, open the database project, and open the Student.sql file.</span></span> <span data-ttu-id="5974c-114">Pomocí návrháře nebo kód T-SQL, přidejte sloupec s názvem **MiddleName** , který je NVARCHAR(50) a povoluje hodnoty NULL.</span><span class="sxs-lookup"><span data-stu-id="5974c-114">Through either the designer or the T-SQL code, add a column named **MiddleName** that is an NVARCHAR(50) and allows NULL values.</span></span>

![Přidejte druhé křestní jméno](changing-the-database/_static/image1.png)

<span data-ttu-id="5974c-116">Nasaďte tuto změnu do místní databáze spuštěním projektu databáze (nebo F5).</span><span class="sxs-lookup"><span data-stu-id="5974c-116">Deploy this change to your local database by starting your database project (or F5).</span></span> <span data-ttu-id="5974c-117">Nové pole se přidá do tabulky.</span><span class="sxs-lookup"><span data-stu-id="5974c-117">The new field is added to the table.</span></span> <span data-ttu-id="5974c-118">Pokud v Průzkumníku objektů SQL serveru ji nevidíte, klikněte na tlačítko Aktualizovat v podokně.</span><span class="sxs-lookup"><span data-stu-id="5974c-118">If you do not see it in the SQL Server Object Explorer, click the Refresh button in the pane.</span></span>

![Zobrazit nový sloupec](changing-the-database/_static/image2.png)

<span data-ttu-id="5974c-120">Nový sloupec existuje v tabulce databáze, ale neexistuje momentálně ve třídě datového modelu.</span><span class="sxs-lookup"><span data-stu-id="5974c-120">The new column exists in the database table, but it does not currently exist in the data model class.</span></span> <span data-ttu-id="5974c-121">Je nutné aktualizovat modelu, který má obsahovat nový sloupec.</span><span class="sxs-lookup"><span data-stu-id="5974c-121">You must update the model to include your new column.</span></span> <span data-ttu-id="5974c-122">V **modely** složku, otevřete **ContosoModel.edmx** soubor k zobrazení diagramu modelu.</span><span class="sxs-lookup"><span data-stu-id="5974c-122">In the **Models** folder, open the **ContosoModel.edmx** file to display the model diagram.</span></span> <span data-ttu-id="5974c-123">Všimněte si, že Student model neobsahuje vlastnost MiddleName.</span><span class="sxs-lookup"><span data-stu-id="5974c-123">Notice that the Student model does not contain the MiddleName property.</span></span> <span data-ttu-id="5974c-124">Klikněte pravým tlačítkem kamkoli na návrhové ploše a vyberte **aktualizace modelů z databáze**.</span><span class="sxs-lookup"><span data-stu-id="5974c-124">Right-click anywhere on the design surface, and select **Update Model from Database**.</span></span>

![aktualizace modelu](changing-the-database/_static/image3.png)

<span data-ttu-id="5974c-126">V Průvodci aktualizacemi, vyberte **aktualizovat** kartu a **Student** tabulky.</span><span class="sxs-lookup"><span data-stu-id="5974c-126">In the Update Wizard, select the **Refresh** tab and the **Student** table.</span></span>

![Průvodce aktualizací](changing-the-database/_static/image4.png)

<span data-ttu-id="5974c-128">Klikněte na tlačítko **Dokončit**.</span><span class="sxs-lookup"><span data-stu-id="5974c-128">Click **Finish**.</span></span>

<span data-ttu-id="5974c-129">Po dokončení procesu aktualizace zahrnuje nový databázový diagram **MiddleName** vlastnost.</span><span class="sxs-lookup"><span data-stu-id="5974c-129">After the update process is finished, the database diagram includes the new **MiddleName** property.</span></span> <span data-ttu-id="5974c-130">Uložit **ContosoModel.edmx** souboru.</span><span class="sxs-lookup"><span data-stu-id="5974c-130">Save the **ContosoModel.edmx** file.</span></span> <span data-ttu-id="5974c-131">Je nutné uložit tento soubor pro novou vlastnost mají být předány **Student.cs** třídy.</span><span class="sxs-lookup"><span data-stu-id="5974c-131">You must save this file for the new property to be propagated to the **Student.cs** class.</span></span> <span data-ttu-id="5974c-132">Teď jste aktualizovali databáze a modelu.</span><span class="sxs-lookup"><span data-stu-id="5974c-132">You have now updated the database and the model.</span></span>

<span data-ttu-id="5974c-133">Sestavte řešení.</span><span class="sxs-lookup"><span data-stu-id="5974c-133">Build the solution.</span></span>

<span data-ttu-id="5974c-134">Bohužel zobrazení stále neobsahují nové vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="5974c-134">Unfortunately, the views still do not contain the new property.</span></span> <span data-ttu-id="5974c-135">Chcete-li aktualizovat zobrazení máte dvě možnosti – můžete znovu vygenerovat zobrazení tak, že znovu přidáte generování uživatelského rozhraní pro třídu Student nebo můžete ručně přidat nové vlastnosti pro stávající zobrazení.</span><span class="sxs-lookup"><span data-stu-id="5974c-135">To update the views you have two options - you can either re-generate the views by once again adding scaffolding for the Student class, or you can manually add the new property to your existing views.</span></span> <span data-ttu-id="5974c-136">V tomto kurzu přidáte základní kostry aplikace znovu vzhledem k tomu, že všechny vlastní změny nebyla provedena pro automaticky generované zobrazení.</span><span class="sxs-lookup"><span data-stu-id="5974c-136">In this tutorial, you will add the scaffolding again because you have not made any customized changes to the automatically-generated views.</span></span> <span data-ttu-id="5974c-137">Můžete zvážit, když změny provedené zobrazení a nechcete, aby tyto změny přijít o ručním přidání vlastnost.</span><span class="sxs-lookup"><span data-stu-id="5974c-137">You might consider manually adding the property when you have made changes to the views and do not want to lose those changes.</span></span>

<span data-ttu-id="5974c-138">Aby se zajistilo zobrazení jsou znovu vytvořena, odstraňte **studenty** ve složce **zobrazení**a odstranit **StudentsController**.</span><span class="sxs-lookup"><span data-stu-id="5974c-138">To ensure the views are re-created, delete the **Students** folder under **Views**, and delete the **StudentsController**.</span></span> <span data-ttu-id="5974c-139">Klikněte pravým tlačítkem na **řadiče** složky a přidat generování uživatelského rozhraní pro **Student** modelu.</span><span class="sxs-lookup"><span data-stu-id="5974c-139">Then, right-click the **Controllers** folder and add scaffolding for the **Student** model.</span></span> <span data-ttu-id="5974c-140">Znovu, název kontroleru **StudentsController**.</span><span class="sxs-lookup"><span data-stu-id="5974c-140">Again, name the controller **StudentsController**.</span></span> <span data-ttu-id="5974c-141">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="5974c-141">Select **OK**.</span></span>

<span data-ttu-id="5974c-142">Zobrazení nyní obsahovat vlastnost MiddleName.</span><span class="sxs-lookup"><span data-stu-id="5974c-142">The views now contain the MiddleName property.</span></span>

![Zobrazit křestní jméno](changing-the-database/_static/image5.png)

<span data-ttu-id="5974c-144">V další části přidáte kód pro přizpůsobení zobrazení k zobrazení podrobností o záznamu studentů.</span><span class="sxs-lookup"><span data-stu-id="5974c-144">In the next section, you will add code to customize the view for showing details about a student record.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="5974c-145">[Předchozí](generating-views.md)
> [další](customizing-a-view.md)</span><span class="sxs-lookup"><span data-stu-id="5974c-145">[Previous](generating-views.md)
[Next](customizing-a-view.md)</span></span>
