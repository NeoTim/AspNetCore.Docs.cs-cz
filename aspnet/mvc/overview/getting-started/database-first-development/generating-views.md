---
uid: mvc/overview/getting-started/database-first-development/generating-views
title: 'EF Database First s ASP.NET MVC: generování zobrazení | Dokumentace Microsoftu'
author: Rick-Anderson
description: Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi. Tento kurz seri...
ms.author: riande
ms.date: 12/29/2014
ms.assetid: 669367cf-8e30-4eb6-821d-10a7d9bb906c
msc.legacyurl: /mvc/overview/getting-started/database-first-development/generating-views
msc.type: authoredcontent
ms.openlocfilehash: 7d925573dd4cdf5c1a36e51f312e18093bd35043
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021085"
---
<a name="ef-database-first-with-aspnet-mvc-generating-views"></a><span data-ttu-id="e32b5-104">EF Database First s ASP.NET MVC: generování zobrazení</span><span class="sxs-lookup"><span data-stu-id="e32b5-104">EF Database First with ASP.NET MVC: Generating Views</span></span>
====================
<span data-ttu-id="e32b5-105">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="e32b5-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="e32b5-106">Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi.</span><span class="sxs-lookup"><span data-stu-id="e32b5-106">Using MVC, Entity Framework, and ASP.NET Scaffolding, you can create a web application that provides an interface to an existing database.</span></span> <span data-ttu-id="e32b5-107">V této sérii kurzů se dozvíte, jak automaticky vygenerovat kód, který umožňuje uživatelům zobrazit, upravit, vytvořit a odstranit data, která se nachází v databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="e32b5-107">This tutorial series shows you how to automatically generate code that enables users to display, edit, create, and delete data that resides in a database table.</span></span> <span data-ttu-id="e32b5-108">Generovaný kód odpovídá sloupců v tabulce databáze.</span><span class="sxs-lookup"><span data-stu-id="e32b5-108">The generated code corresponds to the columns in the database table.</span></span>
> 
> <span data-ttu-id="e32b5-109">Tato části této série se soustředí na používání ASP.NET generování uživatelského rozhraní pro generování kontrolerů a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="e32b5-109">This part of the series focuses on using ASP.NET Scaffolding to generate the controllers and views.</span></span>


## <a name="add-scaffold"></a><span data-ttu-id="e32b5-110">Přidat vygenerované uživatelské rozhraní</span><span class="sxs-lookup"><span data-stu-id="e32b5-110">Add scaffold</span></span>

<span data-ttu-id="e32b5-111">Jste připraveni vygenerovat kód, který bude poskytovat operace standardních datových tříd modelu.</span><span class="sxs-lookup"><span data-stu-id="e32b5-111">You are ready to generate code that will provide standard data operations for the model classes.</span></span> <span data-ttu-id="e32b5-112">Přidat kód tak, že přidáte položku vygenerované uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e32b5-112">You add the code by adding a scaffold item.</span></span> <span data-ttu-id="e32b5-113">Existuje mnoho možností pro typ generování uživatelského rozhraní, které můžete přidat; v tomto kurzu se zahrne vygenerované uživatelské rozhraní kontroler a zobrazení, které odpovídají vzorům studenty a registrace, který jste vytvořili v předchozí části.</span><span class="sxs-lookup"><span data-stu-id="e32b5-113">There are many options for the type of scaffolding you can add; in this tutorial, the scaffold will include a controller and views that correspond to the Student and Enrollment models you created in the previous section.</span></span>

<span data-ttu-id="e32b5-114">Můžete zachovat konzistenci ve vašem projektu, přidáte nový kontroler ke stávající **řadiče** složky.</span><span class="sxs-lookup"><span data-stu-id="e32b5-114">To maintain consistency in your project, you will add the new controller to the existing **Controllers** folder.</span></span> <span data-ttu-id="e32b5-115">Klikněte pravým tlačítkem myši **řadiče** a pak zvolte položku **přidat** – **novou vygenerovanou položku**.</span><span class="sxs-lookup"><span data-stu-id="e32b5-115">Right-click the **Controllers** folder, and select **Add** – **New Scaffolded Item**.</span></span>

![Přidat vygenerované uživatelské rozhraní](generating-views/_static/image1.png)

<span data-ttu-id="e32b5-117">Vyberte **kontroler MVC 5 se zobrazeními, používá nástroj Entity Framework** možnost.</span><span class="sxs-lookup"><span data-stu-id="e32b5-117">Select the **MVC 5 Controller with views, using Entity Framework** option.</span></span> <span data-ttu-id="e32b5-118">Tato možnost bude generovat kontroler a zobrazení pro aktualizaci, odstranění, vytváření a zobrazování dat v modelu.</span><span class="sxs-lookup"><span data-stu-id="e32b5-118">This option will generate the controller and views for updating, deleting, creating and displaying the data in your model.</span></span>

![Přidat kontroler mvc](generating-views/_static/image2.png)

<span data-ttu-id="e32b5-120">Vyberte **Student** pro třídu modelu, vyberte **ContosoUniversityEntities** pro třídy kontextu.</span><span class="sxs-lookup"><span data-stu-id="e32b5-120">Select **Student** for the model class, and select the **ContosoUniversityEntities** for the context class.</span></span> <span data-ttu-id="e32b5-121">Zachovat název kontroleru jako **StudentsController**,</span><span class="sxs-lookup"><span data-stu-id="e32b5-121">Keep the controller name as **StudentsController**,</span></span>

![Zadejte kontroler](generating-views/_static/image3.png)

<span data-ttu-id="e32b5-123">Klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="e32b5-123">Click **Add**.</span></span>

<span data-ttu-id="e32b5-124">Pokud se zobrazí chyba, pravděpodobně jste nesestavili projektu v předchozí části.</span><span class="sxs-lookup"><span data-stu-id="e32b5-124">If you receive an error, it may be because you did not build the project in the previous section.</span></span> <span data-ttu-id="e32b5-125">Pokud ano, zkuste sestavit projekt a pak znovu přidání vygenerované položky.</span><span class="sxs-lookup"><span data-stu-id="e32b5-125">If so, try building the project, and then add the scaffolded item again.</span></span>

<span data-ttu-id="e32b5-126">Po dokončení procesu generování kódu se zobrazí nový kontroler a zobrazení ve vašem projektu.</span><span class="sxs-lookup"><span data-stu-id="e32b5-126">After the code generation process is complete, you will see a new controller and views in your project.</span></span>

![Vyberte zobrazení](generating-views/_static/image4.png)

<span data-ttu-id="e32b5-128">Znovu proveďte stejné kroky, ale přidat vygenerované uživatelské rozhraní pro třídu pro zápis.</span><span class="sxs-lookup"><span data-stu-id="e32b5-128">Perform the same steps again, but add a scaffold for the Enrollment class.</span></span> <span data-ttu-id="e32b5-129">Po dokončení byste měli mít **EnrollmentsController.cs** soubor a složku ve složce **zobrazení** s názvem **registrace** s Create, odstranění, podrobností, úpravy a indexu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="e32b5-129">When finished, you should have an **EnrollmentsController.cs** file, and a folder under **Views** named **Enrollments** with the Create, Delete, Details, Edit and Index views.</span></span>

![Vyberte zobrazení](generating-views/_static/image5.png)

## <a name="add-links-to-new-views"></a><span data-ttu-id="e32b5-131">Přidat odkazy pro nové zobrazení</span><span class="sxs-lookup"><span data-stu-id="e32b5-131">Add links to new views</span></span>

<span data-ttu-id="e32b5-132">Zjednodušit přechod na nové zobrazení, můžete přidat několik hypertextové odkazy k zobrazení indexu pro studenty a registrací.</span><span class="sxs-lookup"><span data-stu-id="e32b5-132">To make it easier for you to navigate to your new views, you can add a couple of hyperlinks to the Index views for students and enrollments.</span></span> <span data-ttu-id="e32b5-133">Otevřete soubor v **Views/Home/Index.cshtml**, což je domovská stránka pro váš web.</span><span class="sxs-lookup"><span data-stu-id="e32b5-133">Open the file at **Views/Home/Index.cshtml**, which is the home page for your site.</span></span> <span data-ttu-id="e32b5-134">Přidejte následující kód níže jumbotron.</span><span class="sxs-lookup"><span data-stu-id="e32b5-134">Add the following code below the jumbotron.</span></span>

[!code-cshtml[Main](generating-views/samples/sample1.cshtml)]

<span data-ttu-id="e32b5-135">První parametr metody ActionLink je text, který se zobrazí v odkazu.</span><span class="sxs-lookup"><span data-stu-id="e32b5-135">For the ActionLink method, the first parameter is the text to display in the link.</span></span> <span data-ttu-id="e32b5-136">Druhý parametr je akce a třetí parametr je název kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e32b5-136">The second parameter is the action and the third parameter is the name of the controller.</span></span> <span data-ttu-id="e32b5-137">Například na první odkaz odkazuje na akci indexu v StudentsController.</span><span class="sxs-lookup"><span data-stu-id="e32b5-137">For example, the first link points to the Index action in StudentsController.</span></span> <span data-ttu-id="e32b5-138">Skutečné hypertextový odkaz je vytvořený z těchto hodnot.</span><span class="sxs-lookup"><span data-stu-id="e32b5-138">The actual hyperlink is constructed from these values.</span></span> <span data-ttu-id="e32b5-139">Na první odkaz, takže v konečném důsledku přejdou uživatelé k **Index.cshtml** soubor **zobrazení/studenty** složky.</span><span class="sxs-lookup"><span data-stu-id="e32b5-139">The first link ultimately takes users to the **Index.cshtml** file within the **Views/Students** folder.</span></span>

## <a name="display-student-views"></a><span data-ttu-id="e32b5-140">Zobrazení studenta</span><span class="sxs-lookup"><span data-stu-id="e32b5-140">Display student views</span></span>

<span data-ttu-id="e32b5-141">Ověříte, že kód přidaný do projektu správně zobrazí seznam studenty a umožňuje uživatelům upravovat, vytvořit nebo odstranit záznamech studentů v databázi.</span><span class="sxs-lookup"><span data-stu-id="e32b5-141">You will verify that the code added to your project correctly displays a list of the students, and enables users to edit, create, or delete the student records in the database.</span></span>

<span data-ttu-id="e32b5-142">Klikněte pravým tlačítkem myši **Views/Home/Index.cshtml** a vyberte možnost **zobrazit v prohlížeči**.</span><span class="sxs-lookup"><span data-stu-id="e32b5-142">Right-click the **Views/Home/Index.cshtml** file, and select **View in Browser**.</span></span> <span data-ttu-id="e32b5-143">Na této stránce klikněte na odkaz pro seznam studentů.</span><span class="sxs-lookup"><span data-stu-id="e32b5-143">On this page, click the link for the list of students.</span></span>

![](generating-views/_static/image6.png)

<span data-ttu-id="e32b5-144">Na této stránce Všimněte si, že seznam studentů a odkazy, chcete-li změnit tato data.</span><span class="sxs-lookup"><span data-stu-id="e32b5-144">On this page, notice the list of the students and links to modify this data.</span></span>

![seznam studentů](generating-views/_static/image7.png)

<span data-ttu-id="e32b5-146">Klikněte na tlačítko **vytvořit nový** propojit a zadání několika hodnot pro nové studenty.</span><span class="sxs-lookup"><span data-stu-id="e32b5-146">Click the **Create New** link and provide some values for a new student.</span></span>

![Vytvoření nového objektu student](generating-views/_static/image8.png)

<span data-ttu-id="e32b5-148">Klikněte na tlačítko **vytvořit**a Všimněte si, že přidání nového objektu student do seznamu.</span><span class="sxs-lookup"><span data-stu-id="e32b5-148">Click **Create**, and notice the new student is added to your list.</span></span>

![seznam s nového objektu student](generating-views/_static/image9.png)

<span data-ttu-id="e32b5-150">Vyberte **upravit** propojit a změnit některé z hodnot pro student.</span><span class="sxs-lookup"><span data-stu-id="e32b5-150">Select the **Edit** link, and change some of the values for a student.</span></span>

![Upravit studenta](generating-views/_static/image10.png)

<span data-ttu-id="e32b5-152">Klikněte na tlačítko **Uložit**a Všimněte si, že se změnil záznam studentů.</span><span class="sxs-lookup"><span data-stu-id="e32b5-152">Click **Save**, and notice the student record has been changed.</span></span>

<span data-ttu-id="e32b5-153">Nakonec vyberte **odstranit** propojit a potvrďte, že chcete odstranit záznam kliknutím **odstranit** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="e32b5-153">Finally, select the **Delete** link and confirm that you want to delete the record by clicking the **Delete** button.</span></span>

![Odstranit studenta](generating-views/_static/image11.png)

<span data-ttu-id="e32b5-155">Bez psaní kódu, přidané zobrazení, které provádějí běžné operace s daty v tabulce studentů.</span><span class="sxs-lookup"><span data-stu-id="e32b5-155">Without writing any code, you have added views that perform common operations on the data in the Student table.</span></span>

<span data-ttu-id="e32b5-156">Mohli jste si všimnout, že je textový popisek pro pole založeného na vlastnosti databáze (například **LastName**) což není nutně co byste chtěli zobrazit na webové stránce.</span><span class="sxs-lookup"><span data-stu-id="e32b5-156">You may have noticed that the text label for a field is based on the database property (such as **LastName**) which is not necessarily what you want to display on the web page.</span></span> <span data-ttu-id="e32b5-157">Například můžete chtít raději popisek bude **příjmení**.</span><span class="sxs-lookup"><span data-stu-id="e32b5-157">For example, you may prefer the label to be **Last Name**.</span></span> <span data-ttu-id="e32b5-158">Později v tomto kurzu se opravit tento problém se zobrazením.</span><span class="sxs-lookup"><span data-stu-id="e32b5-158">You will fix this display issue later in the tutorial.</span></span>

## <a name="display-enrollment-views"></a><span data-ttu-id="e32b5-159">Zobrazení registrace</span><span class="sxs-lookup"><span data-stu-id="e32b5-159">Display enrollment views</span></span>

<span data-ttu-id="e32b5-160">Vaše databáze obsahuje vztah jeden mnoho mezi tabulkami, studenty a registrace a vztah jeden mnoho mezi tabulkami kurzu a registrace.</span><span class="sxs-lookup"><span data-stu-id="e32b5-160">Your database includes a one-to-many relationship between the Student and Enrollment tables, and a one-to-many relationship between the Course and Enrollment tables.</span></span> <span data-ttu-id="e32b5-161">Zobrazení pro registraci správně zpracovat tyto vztahy.</span><span class="sxs-lookup"><span data-stu-id="e32b5-161">The views for Enrollment correctly handle these relationships.</span></span> <span data-ttu-id="e32b5-162">Přejděte na domovskou stránku pro web a vyberte **seznamu registrací** odkaz a pak **vytvořit nový** odkaz.</span><span class="sxs-lookup"><span data-stu-id="e32b5-162">Navigate to the home page for your site and select the **List of enrollments** link and then the **Create New** link.</span></span> <span data-ttu-id="e32b5-163">Zobrazení zobrazí formulář pro vytvoření nového záznamu registrace.</span><span class="sxs-lookup"><span data-stu-id="e32b5-163">The view displays a form for creating a new enrollment record.</span></span> <span data-ttu-id="e32b5-164">Zejména Všimněte si, že formulář obsahuje dva rozevírací seznamy, které se vyplní s hodnotami ze souvisejících tabulek.</span><span class="sxs-lookup"><span data-stu-id="e32b5-164">In particular, notice that the form contains two drop-down lists that are populated with values from the related tables.</span></span>

![Vytvoření registrace](generating-views/_static/image12.png)

<span data-ttu-id="e32b5-166">Kromě toho ověření zadané hodnoty je automaticky použity na základě datového typu pole.</span><span class="sxs-lookup"><span data-stu-id="e32b5-166">Furthermore, validation of the provided values is automatically applied based on the data type of the field.</span></span> <span data-ttu-id="e32b5-167">Na podnikové úrovni vyžaduje číslo, takže se zobrazí chybová zpráva, pokud se pokusíte zadejte na nekompatibilní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="e32b5-167">Grade requires a number, so an error message is displayed if you try to provide an incompatible value.</span></span>

![Ověřovací zpráva](generating-views/_static/image13.png)

<span data-ttu-id="e32b5-169">Ověření, že automaticky generované zobrazení povolení uživatelé pracovat s daty v databázi.</span><span class="sxs-lookup"><span data-stu-id="e32b5-169">You have verified that the automatically-generated views enable users to work with the data in the database.</span></span> <span data-ttu-id="e32b5-170">V dalším kurzu této série se aktualizovat databázi a provádět odpovídající změny ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e32b5-170">In the next tutorial in this series, you will update the database and make the corresponding changes in the web application.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e32b5-171">[Předchozí](creating-the-web-application.md)
> [další](changing-the-database.md)</span><span class="sxs-lookup"><span data-stu-id="e32b5-171">[Previous](creating-the-web-application.md)
[Next](changing-the-database.md)</span></span>
