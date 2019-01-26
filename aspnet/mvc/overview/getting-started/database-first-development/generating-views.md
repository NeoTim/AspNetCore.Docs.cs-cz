---
uid: mvc/overview/getting-started/database-first-development/generating-views
title: 'Kurz: Generovat zobrazení pro EF Database First s ASP.NET MVC aplikace'
description: Tento článek se soustředí na používání ASP.NET generování uživatelského rozhraní pro generování kontrolerů a zobrazení.
author: Rick-Anderson
ms.author: riande
ms.date: 01/23/2019
ms.topic: tutorial
ms.assetid: 669367cf-8e30-4eb6-821d-10a7d9bb906c
msc.legacyurl: /mvc/overview/getting-started/database-first-development/generating-views
msc.type: authoredcontent
ms.openlocfilehash: e1f6646cdf10d293268b92f44b018709e70c0f86
ms.sourcegitcommit: d5223cf6a2cf80b4f5dc54169b0e376d493d2d3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54889779"
---
# <a name="tutorial-generate-views-for-ef-database-first-with-aspnet-mvc-app"></a><span data-ttu-id="519fa-103">Kurz: Generovat zobrazení pro EF Database First s ASP.NET MVC aplikace</span><span class="sxs-lookup"><span data-stu-id="519fa-103">Tutorial: Generate views for EF Database First with ASP.NET MVC app</span></span>

<span data-ttu-id="519fa-104">Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi.</span><span class="sxs-lookup"><span data-stu-id="519fa-104">Using MVC, Entity Framework, and ASP.NET Scaffolding, you can create a web application that provides an interface to an existing database.</span></span> <span data-ttu-id="519fa-105">V této sérii kurzů se dozvíte, jak automaticky vygenerovat kód, který umožňuje uživatelům zobrazit, upravit, vytvořit a odstranit data, která se nachází v databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="519fa-105">This tutorial series shows you how to automatically generate code that enables users to display, edit, create, and delete data that resides in a database table.</span></span> <span data-ttu-id="519fa-106">Generovaný kód odpovídá sloupců v tabulce databáze.</span><span class="sxs-lookup"><span data-stu-id="519fa-106">The generated code corresponds to the columns in the database table.</span></span>

<span data-ttu-id="519fa-107">Tento článek se soustředí na používání ASP.NET generování uživatelského rozhraní pro generování kontrolerů a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="519fa-107">This article focuses on using ASP.NET Scaffolding to generate the controllers and views.</span></span>

<span data-ttu-id="519fa-108">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="519fa-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="519fa-109">Přidat vygenerované uživatelské rozhraní</span><span class="sxs-lookup"><span data-stu-id="519fa-109">Add scaffold</span></span>
> * <span data-ttu-id="519fa-110">Přidat odkazy pro nové zobrazení</span><span class="sxs-lookup"><span data-stu-id="519fa-110">Add links to new views</span></span>
> * <span data-ttu-id="519fa-111">Zobrazení studenta</span><span class="sxs-lookup"><span data-stu-id="519fa-111">Display student views</span></span>
> * <span data-ttu-id="519fa-112">Zobrazení registrace</span><span class="sxs-lookup"><span data-stu-id="519fa-112">Display enrollment views</span></span>

## <a name="prerequisite"></a><span data-ttu-id="519fa-113">Předpoklad</span><span class="sxs-lookup"><span data-stu-id="519fa-113">Prerequisite</span></span>

* [<span data-ttu-id="519fa-114">Vytvoření webové aplikace a datových modelů</span><span class="sxs-lookup"><span data-stu-id="519fa-114">Create the web application and data models</span></span>](creating-the-web-application.md)

## <a name="add-scaffold"></a><span data-ttu-id="519fa-115">Přidat vygenerované uživatelské rozhraní</span><span class="sxs-lookup"><span data-stu-id="519fa-115">Add scaffold</span></span>

<span data-ttu-id="519fa-116">Jste připraveni vygenerovat kód, který bude poskytovat operace standardních datových tříd modelu.</span><span class="sxs-lookup"><span data-stu-id="519fa-116">You are ready to generate code that will provide standard data operations for the model classes.</span></span> <span data-ttu-id="519fa-117">Přidat kód tak, že přidáte položku vygenerované uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="519fa-117">You add the code by adding a scaffold item.</span></span> <span data-ttu-id="519fa-118">Existuje mnoho možností pro typ generování uživatelského rozhraní, které můžete přidat; v tomto kurzu se zahrne vygenerované uživatelské rozhraní kontroler a zobrazení, které odpovídají vzorům studenty a registrace, který jste vytvořili v předchozí části.</span><span class="sxs-lookup"><span data-stu-id="519fa-118">There are many options for the type of scaffolding you can add; in this tutorial, the scaffold will include a controller and views that correspond to the Student and Enrollment models you created in the previous section.</span></span>

<span data-ttu-id="519fa-119">Můžete zachovat konzistenci ve vašem projektu, přidáte nový kontroler ke stávající **řadiče** složky.</span><span class="sxs-lookup"><span data-stu-id="519fa-119">To maintain consistency in your project, you will add the new controller to the existing **Controllers** folder.</span></span> <span data-ttu-id="519fa-120">Klikněte pravým tlačítkem myši **řadiče** a pak zvolte položku **přidat** > **novou vygenerovanou položku**.</span><span class="sxs-lookup"><span data-stu-id="519fa-120">Right-click the **Controllers** folder, and select **Add** > **New Scaffolded Item**.</span></span>

<span data-ttu-id="519fa-121">Vyberte **kontroler MVC 5 se zobrazeními, používá nástroj Entity Framework** možnost.</span><span class="sxs-lookup"><span data-stu-id="519fa-121">Select the **MVC 5 Controller with views, using Entity Framework** option.</span></span> <span data-ttu-id="519fa-122">Tato možnost bude generovat kontroler a zobrazení pro aktualizaci, odstranění, vytváření a zobrazování dat v modelu.</span><span class="sxs-lookup"><span data-stu-id="519fa-122">This option will generate the controller and views for updating, deleting, creating and displaying the data in your model.</span></span>

![Přidat kontroler mvc](generating-views/_static/image2.png)

<span data-ttu-id="519fa-124">Vyberte **Student (ContosoSite.Models)** pro třídu modelu a vyberte **ContosoUniversityDataEntities (ContosoSite.Models)** pro třídy kontextu.</span><span class="sxs-lookup"><span data-stu-id="519fa-124">Select **Student (ContosoSite.Models)** for the model class and select the **ContosoUniversityDataEntities (ContosoSite.Models)** for the context class.</span></span> <span data-ttu-id="519fa-125">Zachovat název kontroleru jako **StudentsController**.</span><span class="sxs-lookup"><span data-stu-id="519fa-125">Keep the controller name as **StudentsController**.</span></span>

<span data-ttu-id="519fa-126">Klikněte na **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="519fa-126">Click **Add**.</span></span>

<span data-ttu-id="519fa-127">Pokud se zobrazí chyba, pravděpodobně jste nesestavili projektu v předchozí části.</span><span class="sxs-lookup"><span data-stu-id="519fa-127">If you receive an error, it may be because you did not build the project in the previous section.</span></span> <span data-ttu-id="519fa-128">Pokud ano, zkuste sestavit projekt a pak znovu přidání vygenerované položky.</span><span class="sxs-lookup"><span data-stu-id="519fa-128">If so, try building the project, and then add the scaffolded item again.</span></span>

<span data-ttu-id="519fa-129">Po dokončení procesu generování kódu se zobrazí nový kontroler a zobrazení ve vašem projektu **řadiče** a **zobrazení** > **studenty** složek .</span><span class="sxs-lookup"><span data-stu-id="519fa-129">After the code generation process is complete, you will see a new controller and views in your project's **Controllers** and **Views** > **Students** folders.</span></span>


<span data-ttu-id="519fa-130">Znovu proveďte stejné kroky, ale přidat vygenerované uživatelské rozhraní pro **registrace** třídy.</span><span class="sxs-lookup"><span data-stu-id="519fa-130">Perform the same steps again, but add a scaffold for the **Enrollment** class.</span></span> <span data-ttu-id="519fa-131">Až budete hotovi, budete mít **EnrollmentsController.cs** soubor a složku ve složce **zobrazení** s názvem **registrace** Create, odstranění, podrobností, úpravy a indexu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="519fa-131">When finished, you have an **EnrollmentsController.cs** file, and a folder under **Views** named **Enrollments** with the Create, Delete, Details, Edit and Index views.</span></span>

## <a name="add-links-to-new-views"></a><span data-ttu-id="519fa-132">Přidat odkazy pro nové zobrazení</span><span class="sxs-lookup"><span data-stu-id="519fa-132">Add links to new views</span></span>

<span data-ttu-id="519fa-133">Zjednodušit přechod na nové zobrazení, můžete přidat několik hypertextové odkazy k zobrazení indexu pro studenty a registrací.</span><span class="sxs-lookup"><span data-stu-id="519fa-133">To make it easier for you to navigate to your new views, you can add a couple of hyperlinks to the Index views for students and enrollments.</span></span> <span data-ttu-id="519fa-134">Otevřete soubor v **zobrazení** > **domácí** > *Index.cshtml*, což je domovská stránka pro váš web.</span><span class="sxs-lookup"><span data-stu-id="519fa-134">Open the file at **Views** > **Home** > *Index.cshtml*, which is the home page for your site.</span></span> <span data-ttu-id="519fa-135">Přidejte následující kód níže jumbotron.</span><span class="sxs-lookup"><span data-stu-id="519fa-135">Add the following code below the jumbotron.</span></span>

[!code-cshtml[Main](generating-views/samples/sample1.cshtml)]

<span data-ttu-id="519fa-136">První parametr metody ActionLink je text, který se zobrazí v odkazu.</span><span class="sxs-lookup"><span data-stu-id="519fa-136">For the ActionLink method, the first parameter is the text to display in the link.</span></span> <span data-ttu-id="519fa-137">Druhý parametr je akce a třetí parametr je název kontroleru.</span><span class="sxs-lookup"><span data-stu-id="519fa-137">The second parameter is the action and the third parameter is the name of the controller.</span></span> <span data-ttu-id="519fa-138">Například na první odkaz odkazuje na akci indexu v StudentsController.</span><span class="sxs-lookup"><span data-stu-id="519fa-138">For example, the first link points to the Index action in StudentsController.</span></span> <span data-ttu-id="519fa-139">Skutečné hypertextový odkaz je vytvořený z těchto hodnot.</span><span class="sxs-lookup"><span data-stu-id="519fa-139">The actual hyperlink is constructed from these values.</span></span> <span data-ttu-id="519fa-140">Na první odkaz, takže v konečném důsledku přejdou uživatelé k **Index.cshtml** soubor **zobrazení/studenty** složky.</span><span class="sxs-lookup"><span data-stu-id="519fa-140">The first link ultimately takes users to the **Index.cshtml** file within the **Views/Students** folder.</span></span>

## <a name="display-student-views"></a><span data-ttu-id="519fa-141">Zobrazení studenta</span><span class="sxs-lookup"><span data-stu-id="519fa-141">Display student views</span></span>

<span data-ttu-id="519fa-142">Ověříte, že kód přidaný do projektu správně zobrazí seznam studenty a umožňuje uživatelům upravovat, vytvořit nebo odstranit záznamech studentů v databázi.</span><span class="sxs-lookup"><span data-stu-id="519fa-142">You will verify that the code added to your project correctly displays a list of the students, and enables users to edit, create, or delete the student records in the database.</span></span>

<span data-ttu-id="519fa-143">Klikněte pravým tlačítkem myši **zobrazení** > **Domů** > *Index.cshtml* a vyberte možnost **zobrazit v prohlížeči**.</span><span class="sxs-lookup"><span data-stu-id="519fa-143">Right-click the **Views** > **Home** > *Index.cshtml* file, and select **View in Browser**.</span></span> <span data-ttu-id="519fa-144">Na domovské stránce aplikace vyberte **seznamu studentů**.</span><span class="sxs-lookup"><span data-stu-id="519fa-144">On the application home page, select **List of students**.</span></span>

![](generating-views/_static/image6.png)

<span data-ttu-id="519fa-145">Na **Index** stránky si všimněte seznamu studentů a odkazy, chcete-li změnit tato data.</span><span class="sxs-lookup"><span data-stu-id="519fa-145">On the **Index** page, notice the list of the students and links to modify this data.</span></span> <span data-ttu-id="519fa-146">Vyberte **vytvořit nový** propojit a zadání několika hodnot pro nové studenty.</span><span class="sxs-lookup"><span data-stu-id="519fa-146">Select the **Create New** link and provide some values for a new student.</span></span> <span data-ttu-id="519fa-147">Klikněte na tlačítko **vytvořit**a Všimněte si, že přidání nového objektu student do seznamu.</span><span class="sxs-lookup"><span data-stu-id="519fa-147">Click **Create**, and notice the new student is added to your list.</span></span>

<span data-ttu-id="519fa-148">Zpět na **Index** stránky, vyberte **upravit** propojit a změnit některé z hodnot pro student.</span><span class="sxs-lookup"><span data-stu-id="519fa-148">Back on the **Index** page, select the **Edit** link, and change some of the values for a student.</span></span> <span data-ttu-id="519fa-149">Klikněte na tlačítko **Uložit**a Všimněte si, že se změnil záznam studentů.</span><span class="sxs-lookup"><span data-stu-id="519fa-149">Click **Save**, and notice the student record has been changed.</span></span>

<span data-ttu-id="519fa-150">Nakonec vyberte **odstranit** propojit a potvrďte, že chcete odstranit záznam kliknutím **odstranit** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="519fa-150">Finally, select the **Delete** link and confirm that you want to delete the record by clicking the **Delete** button.</span></span>

<span data-ttu-id="519fa-151">Bez psaní kódu, přidané zobrazení, které provádějí běžné operace s daty v tabulce studentů.</span><span class="sxs-lookup"><span data-stu-id="519fa-151">Without writing any code, you have added views that perform common operations on the data in the Student table.</span></span>

<span data-ttu-id="519fa-152">Mohli jste si všimnout, že je textový popisek pro pole založeného na vlastnosti databáze (například **LastName**) což není nutně co byste chtěli zobrazit na webové stránce.</span><span class="sxs-lookup"><span data-stu-id="519fa-152">You may have noticed that the text label for a field is based on the database property (such as **LastName**) which is not necessarily what you want to display on the web page.</span></span> <span data-ttu-id="519fa-153">Například můžete chtít raději popisek bude **příjmení**.</span><span class="sxs-lookup"><span data-stu-id="519fa-153">For example, you may prefer the label to be **Last Name**.</span></span> <span data-ttu-id="519fa-154">Později v tomto kurzu se opravit tento problém se zobrazením.</span><span class="sxs-lookup"><span data-stu-id="519fa-154">You will fix this display issue later in the tutorial.</span></span>

## <a name="display-enrollment-views"></a><span data-ttu-id="519fa-155">Zobrazení registrace</span><span class="sxs-lookup"><span data-stu-id="519fa-155">Display enrollment views</span></span>

<span data-ttu-id="519fa-156">Vaše databáze obsahuje vztah jeden mnoho mezi tabulkami, studenty a registrace a vztah jeden mnoho mezi tabulkami kurzu a registrace.</span><span class="sxs-lookup"><span data-stu-id="519fa-156">Your database includes a one-to-many relationship between the Student and Enrollment tables, and a one-to-many relationship between the Course and Enrollment tables.</span></span> <span data-ttu-id="519fa-157">Zobrazení pro registraci správně zpracovat tyto vztahy.</span><span class="sxs-lookup"><span data-stu-id="519fa-157">The views for Enrollment correctly handle these relationships.</span></span> <span data-ttu-id="519fa-158">Přejděte na domovskou stránku pro web a vyberte **seznamu registrací** odkaz a pak **vytvořit nový** odkaz.</span><span class="sxs-lookup"><span data-stu-id="519fa-158">Navigate to the home page for your site and select the **List of enrollments** link and then the **Create New** link.</span></span>

<span data-ttu-id="519fa-159">Zobrazení zobrazí formulář pro vytvoření nového záznamu registrace.</span><span class="sxs-lookup"><span data-stu-id="519fa-159">The view displays a form for creating a new enrollment record.</span></span> <span data-ttu-id="519fa-160">Zejména, Všimněte si, že formulář obsahuje **CourseID** rozevíracího seznamu a **StudentID** rozevíracího seznamu.</span><span class="sxs-lookup"><span data-stu-id="519fa-160">In particular, notice that the form contains a **CourseID** drop-down list and a **StudentID** drop-down list.</span></span> <span data-ttu-id="519fa-161">Obě jsou naplněnou hodnotami ze souvisejících tabulek.</span><span class="sxs-lookup"><span data-stu-id="519fa-161">Both are populated with values from the related tables.</span></span>

<span data-ttu-id="519fa-162">Kromě toho ověření zadané hodnoty je automaticky použity na základě datového typu pole.</span><span class="sxs-lookup"><span data-stu-id="519fa-162">Furthermore, validation of the provided values is automatically applied based on the data type of the field.</span></span> <span data-ttu-id="519fa-163">**Na podnikové úrovni** vyžaduje číslo, takže pokud se pokusíte zadejte na nekompatibilní hodnotu, zobrazí se chybová zpráva: *Pole na podnikové úrovni, musí být číslo.*</span><span class="sxs-lookup"><span data-stu-id="519fa-163">**Grade** requires a number, so an error message is displayed if you try to provide an incompatible value: *The field Grade must be a number.*</span></span>

<span data-ttu-id="519fa-164">Ověření, že automaticky generované zobrazení povolení uživatelé pracovat s daty v databázi.</span><span class="sxs-lookup"><span data-stu-id="519fa-164">You have verified that the automatically-generated views enable users to work with the data in the database.</span></span> <span data-ttu-id="519fa-165">V dalším kurzu této série se aktualizovat databázi a provádět odpovídající změny ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="519fa-165">In the next tutorial in this series, you will update the database and make the corresponding changes in the web application.</span></span>

## <a name="next-steps"></a><span data-ttu-id="519fa-166">Další kroky</span><span class="sxs-lookup"><span data-stu-id="519fa-166">Next steps</span></span>

<span data-ttu-id="519fa-167">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="519fa-167">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="519fa-168">Přidání vygenerované uživatelské rozhraní</span><span class="sxs-lookup"><span data-stu-id="519fa-168">Added scaffold</span></span>
> * <span data-ttu-id="519fa-169">Přidání odkazů na nové zobrazení</span><span class="sxs-lookup"><span data-stu-id="519fa-169">Added links to new views</span></span>
> * <span data-ttu-id="519fa-170">Zobrazení zobrazené studenta</span><span class="sxs-lookup"><span data-stu-id="519fa-170">Displayed student views</span></span>
> * <span data-ttu-id="519fa-171">Zobrazení zobrazené registrace</span><span class="sxs-lookup"><span data-stu-id="519fa-171">Displayed enrollment views</span></span>

<span data-ttu-id="519fa-172">Přejděte k dalším článku se naučíte, jak změnit databázi.</span><span class="sxs-lookup"><span data-stu-id="519fa-172">Advance to the next article to learn how to change the database.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="519fa-173">Změna databáze</span><span class="sxs-lookup"><span data-stu-id="519fa-173">Change the database</span></span>](changing-the-database.md)