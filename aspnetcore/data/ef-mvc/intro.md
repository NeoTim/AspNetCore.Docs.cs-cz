---
title: ASP.NET Core MVC s Entity Framework Core – kurz 1 10
author: rick-anderson
description: ''
ms.author: tdykstra
ms.date: 03/15/2017
uid: data/ef-mvc/intro
ms.openlocfilehash: 46cc5144a289637a0fb1c1c2ac22aba2d881af52
ms.sourcegitcommit: a669c4e3f42e387e214a354ac4143555602e6f66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43336104"
---
# <a name="aspnet-core-mvc-with-entity-framework-core---tutorial-1-of-10"></a><span data-ttu-id="62462-102">ASP.NET Core MVC s Entity Framework Core – kurz 1 10</span><span class="sxs-lookup"><span data-stu-id="62462-102">ASP.NET Core MVC with Entity Framework Core - Tutorial 1 of 10</span></span>

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc-21.md)]

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="62462-103">Podle [Petr Dykstra](https://github.com/tdykstra) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="62462-103">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="62462-104">Contoso University ukázkovou webovou aplikaci ukazuje, jak vytvářet webové aplikace ASP.NET Core 2.0 MVC pomocí Entity Framework (EF) Core 2.0 a Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="62462-104">The Contoso University sample web application demonstrates how to create ASP.NET Core 2.0 MVC web applications using Entity Framework (EF) Core 2.0 and Visual Studio 2017.</span></span>

<span data-ttu-id="62462-105">Ukázková aplikace je webovou stránku pro fiktivní společnosti Contoso University.</span><span class="sxs-lookup"><span data-stu-id="62462-105">The sample application is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="62462-106">Zahrnuje funkce, jako student přijetí, kurz vytvoření a přiřazení instruktorem.</span><span class="sxs-lookup"><span data-stu-id="62462-106">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="62462-107">Toto je první ze série kurzů, které vysvětlují, jak vytvořit ukázková aplikace Contoso University úplně od začátku.</span><span class="sxs-lookup"><span data-stu-id="62462-107">This is the first in a series of tutorials that explain how to build the Contoso University sample application from scratch.</span></span>

[<span data-ttu-id="62462-108">Stažení nebo zobrazení dokončené aplikace.</span><span class="sxs-lookup"><span data-stu-id="62462-108">Download or view the completed application.</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

<span data-ttu-id="62462-109">EF Core 2.0 je nejnovější verze EF, ale ještě nemá všechny funkce verze EF 6.x.</span><span class="sxs-lookup"><span data-stu-id="62462-109">EF Core 2.0 is the latest version of EF but doesn't yet have all the features of EF 6.x.</span></span> <span data-ttu-id="62462-110">Informace o tom, jak si vybrat mezi EF 6.x a EF Core, najdete v části [EF Core vs. EF6.x](https://docs.microsoft.com/ef/efcore-and-ef6/).</span><span class="sxs-lookup"><span data-stu-id="62462-110">For information about how to choose between EF 6.x and EF Core, see [EF Core vs. EF6.x](https://docs.microsoft.com/ef/efcore-and-ef6/).</span></span> <span data-ttu-id="62462-111">Pokud se rozhodnete EF 6.x, naleznete v tématu [předchozí verze v této sérii kurzů](https://docs.microsoft.com/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).</span><span class="sxs-lookup"><span data-stu-id="62462-111">If you choose EF 6.x, see [the previous version of this tutorial series](https://docs.microsoft.com/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).</span></span>

> [!NOTE]
> <span data-ttu-id="62462-112">ASP.NET Core 1.1 tohoto kurzu, najdete v článku [verze VS 2017 Update 2 v tomto kurzu ve formátu PDF](https://github.com/aspnet/Docs/blob/master/aspnetcore/data/ef-mvc/intro/_static/efmvc1.1.pdf).</span><span class="sxs-lookup"><span data-stu-id="62462-112">For the ASP.NET Core 1.1 version of this tutorial, see the [VS 2017 Update 2 version of this tutorial in PDF format](https://github.com/aspnet/Docs/blob/master/aspnetcore/data/ef-mvc/intro/_static/efmvc1.1.pdf).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="62462-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="62462-113">Prerequisites</span></span>

[!INCLUDE [](~/includes/net-core-prereqs.md)]

## <a name="troubleshooting"></a><span data-ttu-id="62462-114">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="62462-114">Troubleshooting</span></span>

<span data-ttu-id="62462-115">Pokud narazíte na problém nevyřešíte sami, můžete najít řešení obvykle porovnáním kódu [dokončený projekt](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final).</span><span class="sxs-lookup"><span data-stu-id="62462-115">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final).</span></span> <span data-ttu-id="62462-116">Seznam běžných chyb a jak je vyřešit, najdete v části [části Poradce při potížích s posledním dílem série](advanced.md#common-errors).</span><span class="sxs-lookup"><span data-stu-id="62462-116">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="62462-117">Pokud jste nenašli, co potřebujete existuje, můžete odeslat dotaz do StackOverflow.com pro [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="62462-117">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="62462-118">Toto je série 10 kurzů, z nichž každý je založena na co se provádí v předchozích kurzech.</span><span class="sxs-lookup"><span data-stu-id="62462-118">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="62462-119">Zvažte možnost uložení kopie projektu po každé úspěšné dokončení tohoto kurzu.</span><span class="sxs-lookup"><span data-stu-id="62462-119">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="62462-120">A pokud narazíte na problémy, můžete začít z předchozí kurz o službě místo přechodu zpět na začátek celou řadu.</span><span class="sxs-lookup"><span data-stu-id="62462-120">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="the-contoso-university-web-application"></a><span data-ttu-id="62462-121">Webová aplikace Contoso University</span><span class="sxs-lookup"><span data-stu-id="62462-121">The Contoso University web application</span></span>

<span data-ttu-id="62462-122">Aplikace, kterou je budete vytvářet v těchto kurzech je webová stránka jednoduché university.</span><span class="sxs-lookup"><span data-stu-id="62462-122">The application you'll be building in these tutorials is a simple university web site.</span></span>

<span data-ttu-id="62462-123">Uživatelé mohou zobrazit a aktualizovat Všichni studenti, kurz a informace instruktorem.</span><span class="sxs-lookup"><span data-stu-id="62462-123">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="62462-124">Tady je několik obrazovek, které vytvoříte.</span><span class="sxs-lookup"><span data-stu-id="62462-124">Here are a few of the screens you'll create.</span></span>

![Studenti indexová stránka](intro/_static/students-index.png)

![Stránky pro úpravu studentů](intro/_static/student-edit.png)

<span data-ttu-id="62462-127">Styl uživatelského rozhraní tohoto webu má blízko co je generována pomocí integrovaných šablon, ukládají, abyste se mohli zaměřit kurz hlavně na tom, jak používat rozhraní Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="62462-127">The UI style of this site has been kept close to what's generated by the built-in templates, so that the tutorial can focus mainly on how to use the Entity Framework.</span></span>

## <a name="create-an-aspnet-core-mvc-web-application"></a><span data-ttu-id="62462-128">Vytvořit webovou aplikaci ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="62462-128">Create an ASP.NET Core MVC web application</span></span>

<span data-ttu-id="62462-129">Otevřít Visual Studio a vytvořte nové technologie ASP.NET Core C# projekt webu s názvem "ContosoUniversity".</span><span class="sxs-lookup"><span data-stu-id="62462-129">Open Visual Studio and create a new ASP.NET Core C# web project named "ContosoUniversity".</span></span>

* <span data-ttu-id="62462-130">Z **souboru** nabídce vyberte možnost **nový > projekt**.</span><span class="sxs-lookup"><span data-stu-id="62462-130">From the **File** menu, select **New > Project**.</span></span>

* <span data-ttu-id="62462-131">V levém podokně vyberte **nainstalováno > Visual C# > Web**.</span><span class="sxs-lookup"><span data-stu-id="62462-131">From the left pane, select **Installed > Visual C# > Web**.</span></span>

* <span data-ttu-id="62462-132">Vyberte **webové aplikace ASP.NET Core** šablony projektu.</span><span class="sxs-lookup"><span data-stu-id="62462-132">Select the **ASP.NET Core Web Application** project template.</span></span>

* <span data-ttu-id="62462-133">Zadejte **ContosoUniversity** jako název a klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="62462-133">Enter **ContosoUniversity** as the name and click **OK**.</span></span>

  ![Dialogové okno nového projektu](intro/_static/new-project.png)

* <span data-ttu-id="62462-135">Počkejte **nové technologie ASP.NET Core webová aplikace (.NET Core)** zobrazit dialogové okno</span><span class="sxs-lookup"><span data-stu-id="62462-135">Wait for the **New ASP.NET Core Web Application (.NET Core)** dialog to appear</span></span>

* <span data-ttu-id="62462-136">Vyberte **ASP.NET Core 2.0** a **webové aplikace (Model-View-Controller)** šablony.</span><span class="sxs-lookup"><span data-stu-id="62462-136">Select **ASP.NET Core 2.0** and the **Web Application (Model-View-Controller)** template.</span></span>

  <span data-ttu-id="62462-137">**Poznámka:** ASP.NET Core 2.0 a EF Core 2.0 nebo novější – Ujistěte se, že v tomto kurzu potřebovat **ASP.NET Core 1.1** není vybraná.</span><span class="sxs-lookup"><span data-stu-id="62462-137">**Note:** This tutorial requires ASP.NET Core 2.0 and EF Core 2.0 or later -- make sure that **ASP.NET Core 1.1** isn't selected.</span></span>

* <span data-ttu-id="62462-138">Ujistěte se, že **ověřování** je nastavena na **bez ověřování**.</span><span class="sxs-lookup"><span data-stu-id="62462-138">Make sure **Authentication** is set to **No Authentication**.</span></span>

* <span data-ttu-id="62462-139">Klikněte na tlačítko **OK**</span><span class="sxs-lookup"><span data-stu-id="62462-139">Click **OK**</span></span>

  ![Dialogové okno Nový projekt ASP.NET Core](intro/_static/new-aspnet.png)

## <a name="set-up-the-site-style"></a><span data-ttu-id="62462-141">Nastavit styl lokality</span><span class="sxs-lookup"><span data-stu-id="62462-141">Set up the site style</span></span>

<span data-ttu-id="62462-142">Několik jednoduchých změn se nastavit v nabídce webu, rozložení a domovské stránky.</span><span class="sxs-lookup"><span data-stu-id="62462-142">A few simple changes will set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="62462-143">Otevřít *Views/Shared/_Layout.cshtml* a proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="62462-143">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="62462-144">Změňte všechny výskyty "ContosoUniversity" na "University společnosti Contoso".</span><span class="sxs-lookup"><span data-stu-id="62462-144">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="62462-145">Existují tři výskyty.</span><span class="sxs-lookup"><span data-stu-id="62462-145">There are three occurrences.</span></span>

* <span data-ttu-id="62462-146">Přidání položek nabídky **studenty**, **kurzy**, **Instruktoři**, a **oddělení**a odstranit **kontakt** položku nabídky.</span><span class="sxs-lookup"><span data-stu-id="62462-146">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="62462-147">Změny jsou zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="62462-147">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,30,36-39,48)]

<span data-ttu-id="62462-148">V *Views/Home/Index.cshtml*, nahraďte obsah souboru následující kód, který nahradí text o ASP.NET a MVC o této aplikaci:</span><span class="sxs-lookup"><span data-stu-id="62462-148">In *Views/Home/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

<span data-ttu-id="62462-149">Stisknutím kláves CTRL + F5 ke spuštění projektu nebo zvolte **ladit > Spustit bez ladění** z nabídky.</span><span class="sxs-lookup"><span data-stu-id="62462-149">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="62462-150">Zobrazí domovská stránka s kartami pro stránky, které vytvoříte v těchto kurzech.</span><span class="sxs-lookup"><span data-stu-id="62462-150">You see the home page with tabs for the pages you'll create in these tutorials.</span></span>

![Domovská stránka vysoké školy contoso](intro/_static/home-page.png)

## <a name="entity-framework-core-nuget-packages"></a><span data-ttu-id="62462-152">Entity Framework Core NuGet balíčky</span><span class="sxs-lookup"><span data-stu-id="62462-152">Entity Framework Core NuGet packages</span></span>

<span data-ttu-id="62462-153">Do projektu přidat podporu EF Core, nainstalujte poskytovatele databáze, kterou chcete cílit.</span><span class="sxs-lookup"><span data-stu-id="62462-153">To add EF Core support to a project, install the database provider that you want to target.</span></span> <span data-ttu-id="62462-154">Tento kurz používá systém SQL Server a je balíček zprostředkovatele [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="62462-154">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span> <span data-ttu-id="62462-155">Tento balíček je součástí [metabalíček](xref:fundamentals/metapackage) Microsoft.aspnetcore.all, takže není nutné k jeho instalaci.</span><span class="sxs-lookup"><span data-stu-id="62462-155">This package is included in the [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage, so you don't have to install it.</span></span>

<span data-ttu-id="62462-156">Tento balíček a jeho závislosti (`Microsoft.EntityFrameworkCore` a `Microsoft.EntityFrameworkCore.Relational`) poskytují podporu runtime pro EF.</span><span class="sxs-lookup"><span data-stu-id="62462-156">This package and its dependencies (`Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`) provide runtime support for EF.</span></span> <span data-ttu-id="62462-157">Přidejte balíček nástroje v pozdější [migrace](migrations.md) kurzu.</span><span class="sxs-lookup"><span data-stu-id="62462-157">You'll add a tooling package later, in the [Migrations](migrations.md) tutorial.</span></span>

<span data-ttu-id="62462-158">Informace o dalších poskytovatelů databáze, které jsou dostupné pro Entity Framework Core najdete v tématu [databáze poskytovatelé](https://docs.microsoft.com/ef/core/providers/).</span><span class="sxs-lookup"><span data-stu-id="62462-158">For information about other database providers that are available for Entity Framework Core, see [Database providers](https://docs.microsoft.com/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="62462-159">Vytvoření datového modelu</span><span class="sxs-lookup"><span data-stu-id="62462-159">Create the data model</span></span>

<span data-ttu-id="62462-160">Dále vytvoříte tříd entit pro aplikaci Contoso University.</span><span class="sxs-lookup"><span data-stu-id="62462-160">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="62462-161">Začnete s následující tři entity.</span><span class="sxs-lookup"><span data-stu-id="62462-161">You'll start with the following three entities.</span></span>

![Kurz – registrace – studentech modelového diagramu](intro/_static/data-model-diagram.png)

<span data-ttu-id="62462-163">Existuje vztah jeden mnoho mezi `Student` a `Enrollment` entity, a existuje vztah jeden mnoho mezi `Course` a `Enrollment` entity.</span><span class="sxs-lookup"><span data-stu-id="62462-163">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="62462-164">Jinými slovy student možné zaregistrovat libovolný počet kurzy a kurzu může mít libovolný počet studentů zaregistrovaná do něj.</span><span class="sxs-lookup"><span data-stu-id="62462-164">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="62462-165">V následujících částech vytvoříte třídu pro každou z těchto entit.</span><span class="sxs-lookup"><span data-stu-id="62462-165">In the following sections you'll create a class for each one of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="62462-166">Entita studenta</span><span class="sxs-lookup"><span data-stu-id="62462-166">The Student entity</span></span>

![Diagram entity studenta](intro/_static/student-entity.png)

<span data-ttu-id="62462-168">V *modely* složce vytvořte soubor třídy *Student.cs* a nahraďte kód šablony následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="62462-168">In the *Models* folder, create a class file named *Student.cs* and replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="62462-169">`ID` Vlastnost se stane sloupec primárního klíče tabulky databáze, která odpovídá této třídy.</span><span class="sxs-lookup"><span data-stu-id="62462-169">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="62462-170">Ve výchozím nastavení interpretuje Entity Framework vlastnost s názvem `ID` nebo `classnameID` jako primární klíč.</span><span class="sxs-lookup"><span data-stu-id="62462-170">By default, the Entity Framework interprets a property that's named `ID` or `classnameID` as the primary key.</span></span>

<span data-ttu-id="62462-171">`Enrollments` Je vlastnost [navigační vlastnost](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="62462-171">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="62462-172">Vlastnosti navigace podržte dalšími subjekty, které se vztahují k této entity.</span><span class="sxs-lookup"><span data-stu-id="62462-172">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="62462-173">V takovém případě `Enrollments` vlastnost `Student entity` bude obsahovat všechny `Enrollment` entity, které se vztahují k, které `Student` entity.</span><span class="sxs-lookup"><span data-stu-id="62462-173">In this case, the `Enrollments` property of a `Student entity` will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="62462-174">Jinými slovy, pokud daný řádek studentů v databázi má dva související řádky registrace (řádky, které obsahují hodnotu primárního klíče student získal v jejich StudentID sloupec cizího klíče), který `Student` entity `Enrollments` navigační vlastnost bude obsahovat tyto dvě `Enrollment` entity.</span><span class="sxs-lookup"><span data-stu-id="62462-174">In other words, if a given Student row in the database has two related Enrollment rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="62462-175">Pokud vlastnost navigace může obsahovat více entit (jako v relace m: n nebo 1 n), jeho typ musí být seznam, ve kterém položky lze přidávat, odstranit a aktualizovat, například `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="62462-175">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection<T>`.</span></span> <span data-ttu-id="62462-176">Můžete zadat `ICollection<T>` nebo typu jako `List<T>` nebo `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="62462-176">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="62462-177">Pokud zadáte `ICollection<T>`, vytvoří EF `HashSet<T>` kolekcí ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="62462-177">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="62462-178">Registrace entity</span><span class="sxs-lookup"><span data-stu-id="62462-178">The Enrollment entity</span></span>

![Diagram entity registrace](intro/_static/enrollment-entity.png)

<span data-ttu-id="62462-180">V *modely* složku, vytvořte *Enrollment.cs* a nahraďte existující kód následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="62462-180">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="62462-181">`EnrollmentID` Bude mít vlastnost primárního klíče; tato entita používá `classnameID` vzorku místo `ID` samostatně jako jste viděli v `Student` entity.</span><span class="sxs-lookup"><span data-stu-id="62462-181">The `EnrollmentID` property will be the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="62462-182">Obvykle by zvolte jeden model a použít ho v rámci datového modelu.</span><span class="sxs-lookup"><span data-stu-id="62462-182">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="62462-183">Tady variantu ukazuje, které můžete použít buď vzor.</span><span class="sxs-lookup"><span data-stu-id="62462-183">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="62462-184">V [pozdějších kurzech](inheritance.md), uvidíte, jak pomocí ID bez classname usnadňuje implementaci dědičnosti v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="62462-184">In a [later tutorial](inheritance.md), you'll see how using ID without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="62462-185">`Grade` Vlastnost je `enum`.</span><span class="sxs-lookup"><span data-stu-id="62462-185">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="62462-186">Otazník po `Grade` deklarace typu znamená, že `Grade` vlastnost může mít hodnotu Null.</span><span class="sxs-lookup"><span data-stu-id="62462-186">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="62462-187">Na podnikové úrovni, který má hodnotu null se liší od nulové třída – null znamená, že známku vyjádřenou není znám nebo ještě nebyly přiřazeny.</span><span class="sxs-lookup"><span data-stu-id="62462-187">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="62462-188">`StudentID` Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Student`.</span><span class="sxs-lookup"><span data-stu-id="62462-188">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="62462-189">`Enrollment` Entita je přidružený nejméně k jednomu `Student` entity, tak vlastnost může obsahovat pouze jeden `Student` entity (na rozdíl od `Student.Enrollments` navigační vlastnost předchozímu příkladu, který může obsahovat více `Enrollment` entity).</span><span class="sxs-lookup"><span data-stu-id="62462-189">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="62462-190">`CourseID` Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Course`.</span><span class="sxs-lookup"><span data-stu-id="62462-190">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="62462-191">`Enrollment` Entita je přidružený nejméně k jednomu `Course` entity.</span><span class="sxs-lookup"><span data-stu-id="62462-191">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="62462-192">Nastavení interpretuje Entity Framework vlastnost jako vlastnost cizího klíče Pokud je název `<navigation property name><primary key property name>` (například `StudentID` pro `Student` navigační vlastnost, protože `Student` je primární klíč entity `ID`).</span><span class="sxs-lookup"><span data-stu-id="62462-192">Entity Framework interprets a property as a foreign key property if it's named `<navigation property name><primary key property name>` (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="62462-193">Vlastnosti cizího klíče může nazývat také jednoduše `<primary key property name>` (například `CourseID` od `Course` je primární klíč entity `CourseID`).</span><span class="sxs-lookup"><span data-stu-id="62462-193">Foreign key properties can also be named simply `<primary key property name>` (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="62462-194">Kurz entity</span><span class="sxs-lookup"><span data-stu-id="62462-194">The Course entity</span></span>

![Diagram kurzu entity](intro/_static/course-entity.png)

<span data-ttu-id="62462-196">V *modely* složku, vytvořte *Course.cs* a nahraďte existující kód následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="62462-196">In the *Models* folder, create *Course.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="62462-197">`Enrollments` Je navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="62462-197">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="62462-198">A `Course` entit může souviset s libovolným počtem `Enrollment` entity.</span><span class="sxs-lookup"><span data-stu-id="62462-198">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="62462-199">Informace o kliknu `DatabaseGenerated` atribut v [pozdějších kurzech](complex-data-model.md) v této sérii.</span><span class="sxs-lookup"><span data-stu-id="62462-199">We'll say more about the `DatabaseGenerated` attribute in a [later tutorial](complex-data-model.md) in this series.</span></span> <span data-ttu-id="62462-200">V podstatě tento atribut umožňuje zadat primární klíč pro kurz namísto nutnosti databáze jeho vygenerování.</span><span class="sxs-lookup"><span data-stu-id="62462-200">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="62462-201">Vytvořte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="62462-201">Create the Database Context</span></span>

<span data-ttu-id="62462-202">Hlavní třída, která koordinuje funkce Entity Framework pro daný datový model je třídy kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="62462-202">The main class that coordinates Entity Framework functionality for a given data model is the database context class.</span></span> <span data-ttu-id="62462-203">Vytvoření této třídy odvozené z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="62462-203">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="62462-204">V kódu určíte entit, které jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="62462-204">In your code you specify which entities are included in the data model.</span></span> <span data-ttu-id="62462-205">Můžete také přizpůsobit chování určité Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="62462-205">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="62462-206">V tomto projektu je s názvem třídy `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="62462-206">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="62462-207">Ve složce projektu vytvořte složku s názvem *Data*.</span><span class="sxs-lookup"><span data-stu-id="62462-207">In the project folder, create a folder named *Data*.</span></span>

<span data-ttu-id="62462-208">V *Data* složku vytvořte nový soubor třídy *SchoolContext.cs*a nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="62462-208">In the *Data* folder create a new class file named *SchoolContext.cs*, and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="62462-209">Tento kód vytvoří `DbSet` vlastností pro každou sadu entit.</span><span class="sxs-lookup"><span data-stu-id="62462-209">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="62462-210">V terminologii Entity Framework obvykle sadu entit odpovídá databázové tabulky a entity odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="62462-210">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<span data-ttu-id="62462-211">Mohli jste byl vynechán `DbSet<Enrollment>` a `DbSet<Course>` příkazy a bude fungovat stejně.</span><span class="sxs-lookup"><span data-stu-id="62462-211">You could've omitted the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="62462-212">Entity Framework bude zahrnovat je implicitně protože `Student` odkazy na entity `Enrollment` entity a `Enrollment` odkazy na entity `Course` entity.</span><span class="sxs-lookup"><span data-stu-id="62462-212">The Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="62462-213">Když se vytvoří databáze, EF vytvoří tabulky, které mají názvy, stejně jako `DbSet` názvy vlastností.</span><span class="sxs-lookup"><span data-stu-id="62462-213">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="62462-214">Pro kolekce v názvech vlastností se obvykle množném čísle (studenti spíše než Student), ale vývojáři Nesouhlasím o tom, jestli by měl názvy tabulek pluralized nebo ne.</span><span class="sxs-lookup"><span data-stu-id="62462-214">Property names for collections are typically plural (Students rather than Student), but developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="62462-215">Pro tyto kurzy přepíšete výchozí chování tak, že zadáte názvy singulární tabulek v uvolněn objekt DbContext.</span><span class="sxs-lookup"><span data-stu-id="62462-215">For these tutorials you'll override the default behavior by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="62462-216">K tomu, přidejte následující zvýrazněný kód za poslední DbSet vlastnost.</span><span class="sxs-lookup"><span data-stu-id="62462-216">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-context-with-dependency-injection"></a><span data-ttu-id="62462-217">Zaregistrovat kontext pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="62462-217">Register the context with dependency injection</span></span>

<span data-ttu-id="62462-218">ASP.NET Core implementuje [injektáž závislostí](../../fundamentals/dependency-injection.md) ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="62462-218">ASP.NET Core implements [dependency injection](../../fundamentals/dependency-injection.md) by default.</span></span> <span data-ttu-id="62462-219">Služby (například v kontextu databáze EF) jsou registrované pomocí vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="62462-219">Services (such as the EF database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="62462-220">Komponenty, které vyžadují tyto služby (jako jsou řadiče MVC) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="62462-220">Components that require these services (such as MVC controllers) are provided these services via constructor parameters.</span></span> <span data-ttu-id="62462-221">Zobrazí se vám kód konstruktoru kontroler, který získá instance kontextu později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="62462-221">You'll see the controller constructor code that gets a context instance later in this tutorial.</span></span>

<span data-ttu-id="62462-222">K registraci `SchoolContext` jako služby, otevřete *Startup.cs*a přidejte zvýrazněné řádky a `ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="62462-222">To register `SchoolContext` as a service, open *Startup.cs*, and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=3-4)]

<span data-ttu-id="62462-223">Název připojovacího řetězce je předán v rámci voláním metody na `DbContextOptionsBuilder` objektu.</span><span class="sxs-lookup"><span data-stu-id="62462-223">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="62462-224">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="62462-224">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="62462-225">Přidat `using` příkazy pro `ContosoUniversity.Data` a `Microsoft.EntityFrameworkCore` obory názvů a pak sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="62462-225">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces, and then build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="62462-226">Otevřít *appsettings.json* a přidejte připojovací řetězec, jak je znázorněno v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="62462-226">Open the *appsettings.json* file and add a connection string as shown in the following example.</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a><span data-ttu-id="62462-227">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="62462-227">SQL Server Express LocalDB</span></span>

<span data-ttu-id="62462-228">Připojovací řetězec Určuje databázi SQL Server LocalDB.</span><span class="sxs-lookup"><span data-stu-id="62462-228">The connection string specifies a SQL Server LocalDB database.</span></span> <span data-ttu-id="62462-229">LocalDB je Odlehčená verze SQL serveru Express Database Engine a je určená pro vývoj aplikací, není použití v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="62462-229">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for application development, not production use.</span></span> <span data-ttu-id="62462-230">LocalDB spustí na vyžádání a běží v uživatelském režimu, takže není bez složité konfigurace.</span><span class="sxs-lookup"><span data-stu-id="62462-230">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="62462-231">Ve výchozím nastavení LocalDB vytvoří *.mdf* databázové soubory v `C:/Users/<user>` adresáře.</span><span class="sxs-lookup"><span data-stu-id="62462-231">By default, LocalDB creates *.mdf* database files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-database-with-test-data"></a><span data-ttu-id="62462-232">Přidejte kód pro inicializaci databáze s testovací data</span><span class="sxs-lookup"><span data-stu-id="62462-232">Add code to initialize the database with test data</span></span>

<span data-ttu-id="62462-233">Entity Framework pro vytvoření prázdné databáze.</span><span class="sxs-lookup"><span data-stu-id="62462-233">The Entity Framework will create an empty database for you.</span></span> <span data-ttu-id="62462-234">V této části napíšete metodu, která je volána po vytvoření databáze, aby bylo možné naplnit ho daty testu.</span><span class="sxs-lookup"><span data-stu-id="62462-234">In this section, you write a method that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="62462-235">Zde použijete `EnsureCreated` metoda automaticky vytvořit databázi.</span><span class="sxs-lookup"><span data-stu-id="62462-235">Here you'll use the `EnsureCreated` method to automatically create the database.</span></span> <span data-ttu-id="62462-236">V [pozdějších kurzech](migrations.md) uvidíte, jak zpracovat změny modelu pomocí migrace Code First pro změnu schématu databáze místo vyřadit a znovu vytvořit databázi.</span><span class="sxs-lookup"><span data-stu-id="62462-236">In a [later tutorial](migrations.md) you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="62462-237">V *Data* složku, vytvořte nový soubor třídy *DbInitializer.cs* a nahraďte kód šablony následujícím kódem, což způsobí, že databáze má být vytvořen v případě potřeby a načte testovací data do nové databáze.</span><span class="sxs-lookup"><span data-stu-id="62462-237">In the *Data* folder, create a new class file named *DbInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="62462-238">Kód kontroluje, jestli jsou všechny studenty v databázi a pokud ne, předpokládá se nové databáze a musí být nasazený s testovací data.</span><span class="sxs-lookup"><span data-stu-id="62462-238">The code checks if there are any students in the database, and if not, it assumes the database is new and needs to be seeded with test data.</span></span> <span data-ttu-id="62462-239">Načte testovací data do pole spíše než `List<T>` kolekce za účelem optimalizace výkonu.</span><span class="sxs-lookup"><span data-stu-id="62462-239">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="62462-240">V *Program.cs*, změnit `Main` metoda na spuštění aplikace, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="62462-240">In *Program.cs*, modify the `Main` method to do the following on application startup:</span></span>

* <span data-ttu-id="62462-241">Instance kontextu databáze získáte z kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="62462-241">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="62462-242">Volejte metodu počáteční hodnoty předání kontextu.</span><span class="sxs-lookup"><span data-stu-id="62462-242">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="62462-243">Kontext Dispose po dokončení počáteční hodnoty metody.</span><span class="sxs-lookup"><span data-stu-id="62462-243">Dispose the context when the seed method is done.</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

<span data-ttu-id="62462-244">Přidat `using` příkazy:</span><span class="sxs-lookup"><span data-stu-id="62462-244">Add `using` statements:</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Usings)]

<span data-ttu-id="62462-245">V kurzech starší se může zobrazit podobný kód v `Configure` metoda *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="62462-245">In older tutorials, you may see similar code in the `Configure` method in *Startup.cs*.</span></span> <span data-ttu-id="62462-246">Doporučujeme použít `Configure` metodu pouze k vytvoření kanálu požadavku.</span><span class="sxs-lookup"><span data-stu-id="62462-246">We recommend that you use the `Configure` method only to set up the request pipeline.</span></span> <span data-ttu-id="62462-247">Spouštěcímu kódu aplikace, do kterých patří `Main` metody.</span><span class="sxs-lookup"><span data-stu-id="62462-247">Application startup code belongs in the `Main` method.</span></span>

<span data-ttu-id="62462-248">Nyní při prvním spuštění aplikace, databáze bude vytvoří a nasadí se testovací data.</span><span class="sxs-lookup"><span data-stu-id="62462-248">Now the first time you run the application, the database will be created and seeded with test data.</span></span> <span data-ttu-id="62462-249">Pokaždé, když změníte datový model, můžete odstranit databázi, aktualizovat vaše seed – metoda a začít znovu s novou databázi stejným způsobem.</span><span class="sxs-lookup"><span data-stu-id="62462-249">Whenever you change your data model, you can delete the database, update your seed method, and start afresh with a new database the same way.</span></span> <span data-ttu-id="62462-250">V budoucích kurzech uvidíte, jak upravit databázi, když datového modelu změny, bez odstranění a vytvoříte ho znovu.</span><span class="sxs-lookup"><span data-stu-id="62462-250">In later tutorials, you'll see how to modify the database when the data model changes, without deleting and re-creating it.</span></span>

## <a name="create-a-controller-and-views"></a><span data-ttu-id="62462-251">Vytvoření kontroleru a zobrazení</span><span class="sxs-lookup"><span data-stu-id="62462-251">Create a controller and views</span></span>

<span data-ttu-id="62462-252">V dalším kroku použijete modul generování uživatelského rozhraní v sadě Visual Studio přidat kontroler MVC a zobrazení, která bude používat EF pro dotazování a uložit data.</span><span class="sxs-lookup"><span data-stu-id="62462-252">Next, you'll use the scaffolding engine in Visual Studio to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="62462-253">Automatické vytváření metody akcí CRUD a zobrazení se označuje jako generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="62462-253">The automatic creation of CRUD action methods and views is known as scaffolding.</span></span> <span data-ttu-id="62462-254">Generování uživatelského rozhraní se liší od vytváření kódu v tom, že automaticky generovaný kód je výchozím bodem, který můžete upravit tak, aby vyhovoval vašim požadavkům, zatímco obvykle neupravujte generovaného kódu.</span><span class="sxs-lookup"><span data-stu-id="62462-254">Scaffolding differs from code generation in that the scaffolded code is a starting point that you can modify to suit your own requirements, whereas you typically don't modify generated code.</span></span> <span data-ttu-id="62462-255">Když budete potřebovat pro vygenerovaný kód upravit, použijte částečné třídy nebo při změně věci se znovu vygenerovat kód.</span><span class="sxs-lookup"><span data-stu-id="62462-255">When you need to customize generated code, you use partial classes or you regenerate the code when things change.</span></span>

* <span data-ttu-id="62462-256">Klikněte pravým tlačítkem myši **řadiče** složky v **Průzkumníka řešení** a vyberte **Přidat > novou vygenerovanou položku**.</span><span class="sxs-lookup"><span data-stu-id="62462-256">Right-click the **Controllers** folder in **Solution Explorer** and select **Add > New Scaffolded Item**.</span></span>

<span data-ttu-id="62462-257">Pokud **přidat závislosti MVC** se zobrazí dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="62462-257">If the **Add MVC Dependencies** dialog appears:</span></span>

* <span data-ttu-id="62462-258">[Aktualizace na nejnovější verzi sady Visual Studio](https://www.visualstudio.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="62462-258">[Update Visual Studio to the latest version](https://www.visualstudio.com/downloads/).</span></span> <span data-ttu-id="62462-259">Visual Studio verze starší než 15.5 zobrazí tento dialog.</span><span class="sxs-lookup"><span data-stu-id="62462-259">Visual Studio versions prior to 15.5 show this dialog.</span></span>
* <span data-ttu-id="62462-260">Pokud nelze aktualizovat, vyberte **přidat**a pak postupujte podle kroků přidat kontroler.</span><span class="sxs-lookup"><span data-stu-id="62462-260">If you can't update, select **ADD**, and then follow the add controller steps again.</span></span>

* <span data-ttu-id="62462-261">V **přidat vygenerované uživatelské rozhraní** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="62462-261">In the **Add Scaffold** dialog box:</span></span>

  * <span data-ttu-id="62462-262">Vyberte **kontroler MVC se zobrazeními pomocí Entity Frameworku**.</span><span class="sxs-lookup"><span data-stu-id="62462-262">Select **MVC controller with views, using Entity Framework**.</span></span>

  * <span data-ttu-id="62462-263">Klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="62462-263">Click **Add**.</span></span>

* <span data-ttu-id="62462-264">V **přidat kontroler** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="62462-264">In the **Add Controller** dialog box:</span></span>

  * <span data-ttu-id="62462-265">V **třída modelu** vyberte **Student**.</span><span class="sxs-lookup"><span data-stu-id="62462-265">In **Model class** select **Student**.</span></span>

  * <span data-ttu-id="62462-266">V **třída kontextu dat** vyberte **SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="62462-266">In **Data context class** select **SchoolContext**.</span></span>

  * <span data-ttu-id="62462-267">Přijměte výchozí nastavení **StudentsController** jako název.</span><span class="sxs-lookup"><span data-stu-id="62462-267">Accept the default **StudentsController** as the name.</span></span>

  * <span data-ttu-id="62462-268">Klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="62462-268">Click **Add**.</span></span>

  ![Student vygenerované uživatelské rozhraní](intro/_static/scaffold-student.png)

  <span data-ttu-id="62462-270">Po kliknutí na **přidat**, vytvoří modul generování uživatelského rozhraní sady Visual Studio *StudentsController.cs* souboru a nastavte zobrazení (*.cshtml* soubory), které fungují s kontrolerem.</span><span class="sxs-lookup"><span data-stu-id="62462-270">When you click **Add**, the Visual Studio scaffolding engine creates a *StudentsController.cs* file and a set of views (*.cshtml* files) that work with the controller.</span></span>

<span data-ttu-id="62462-271">(Modul generování uživatelského rozhraní můžete také vytvořit kontext databáze za vás Pokud nechcete vytvořit ručně nejprve, jako jste to udělali dříve v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="62462-271">(The scaffolding engine can also create the database context for you if you don't create it manually first as you did earlier for this tutorial.</span></span> <span data-ttu-id="62462-272">Můžete zadat novou třídu v kontextu **přidat kontroler** otevřete ho kliknutím na znaménko plus vedle **třída kontextu dat**.</span><span class="sxs-lookup"><span data-stu-id="62462-272">You can specify a new context class in the **Add Controller** box by clicking the plus sign to the right of **Data context class**.</span></span>  <span data-ttu-id="62462-273">Visual Studio vytvoří vaše `DbContext` třídy a také kontroler a zobrazení.)</span><span class="sxs-lookup"><span data-stu-id="62462-273">Visual Studio will then create your `DbContext` class as well as the controller and views.)</span></span>

<span data-ttu-id="62462-274">Všimněte si, že trvá kontroleru `SchoolContext` jako parametr konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="62462-274">You'll notice that the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="62462-275">Injektáž závislostí ASP.NET Core se postará o předáním instance `SchoolContext` do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="62462-275">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="62462-276">Jste nakonfigurovali, že *Startup.cs* soubor výše.</span><span class="sxs-lookup"><span data-stu-id="62462-276">You configured that in the *Startup.cs* file earlier.</span></span>

<span data-ttu-id="62462-277">Obsahuje kontroler `Index` metodě akce, která zobrazuje všechny studenty v databázi.</span><span class="sxs-lookup"><span data-stu-id="62462-277">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="62462-278">Metoda získá seznam studentů z entity studenty ve čtení nastavení `Students` vlastnost instance kontextu databáze:</span><span class="sxs-lookup"><span data-stu-id="62462-278">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="62462-279">Později v tomto kurzu získáte informace o asynchronní programovací prvky v tomto kódu.</span><span class="sxs-lookup"><span data-stu-id="62462-279">You'll learn about the asynchronous programming elements in this code later in the tutorial.</span></span>

<span data-ttu-id="62462-280">*Views/Students/Index.cshtml* zobrazení seznamu v tabulce:</span><span class="sxs-lookup"><span data-stu-id="62462-280">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="62462-281">Stisknutím kláves CTRL + F5 ke spuštění projektu nebo zvolte **ladit > Spustit bez ladění** z nabídky.</span><span class="sxs-lookup"><span data-stu-id="62462-281">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="62462-282">Klikněte na kartu studenty a zobrazit testovací data, která `DbInitializer.Initialize` metoda vložen.</span><span class="sxs-lookup"><span data-stu-id="62462-282">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="62462-283">V závislosti na tom, jak úzké okno prohlížeče, je, zobrazí se vám `Student` odkaz kartě v horní části stránky nebo je budete muset kliknout na navigační ikonu v pravém horním rohu na odkaz zobrazíte.</span><span class="sxs-lookup"><span data-stu-id="62462-283">Depending on how narrow your browser window is, you'll see the `Student` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Domovská stránka Contoso University úzký](intro/_static/home-page-narrow.png)

![Studenti indexová stránka](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="62462-286">Zobrazení databáze</span><span class="sxs-lookup"><span data-stu-id="62462-286">View the Database</span></span>

<span data-ttu-id="62462-287">Při spuštění aplikace, `DbInitializer.Initialize` volání metody `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="62462-287">When you started the application, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="62462-288">EF viděli, že se žádná databáze a proto vytvoří jeden a zbytek `Initialize` kódu metody naplnit databázi daty.</span><span class="sxs-lookup"><span data-stu-id="62462-288">EF saw that there was no database and so it created one, then the remainder of the `Initialize` method code populated the database with data.</span></span> <span data-ttu-id="62462-289">Můžete použít **Průzkumník objektů systému SQL Server** (SSOX) Chcete-li zobrazit databáze v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="62462-289">You can use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span>

<span data-ttu-id="62462-290">Zavřete prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="62462-290">Close the browser.</span></span>

<span data-ttu-id="62462-291">Pokud okno SSOX ještě není otevřeno, vyberte ho z **zobrazení** nabídky v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="62462-291">If the SSOX window isn't already open, select it from the **View** menu in Visual Studio.</span></span>

<span data-ttu-id="62462-292">V SSOX, klikněte na tlačítko **\MSSQLLocalDB (localdb) > databáze**a potom klikněte na položku pro název databáze, která je v připojovacím řetězci v vaše *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="62462-292">In SSOX, click **(localdb)\MSSQLLocalDB > Databases**, and then click the entry for the database name that's in the connection string in your *appsettings.json* file.</span></span>

<span data-ttu-id="62462-293">Rozbalte **tabulky** uzel zobrazíte tabulky v databázi.</span><span class="sxs-lookup"><span data-stu-id="62462-293">Expand the **Tables** node to see the tables in your database.</span></span>

![Tabulky v SSOX](intro/_static/ssox-tables.png)

<span data-ttu-id="62462-295">Klikněte pravým tlačítkem na **Student** tabulky a klikněte na tlačítko **Data zobrazení** zobrazit sloupce, které byly vytvořeny a řádky, které byly vloženy do tabulky.</span><span class="sxs-lookup"><span data-stu-id="62462-295">Right-click the **Student** table and click **View Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

![Tabulka Student v SSOX](intro/_static/ssox-student-table.png)

<span data-ttu-id="62462-297"><em>.Mdf</em> a <em>.ldf</em> databázové soubory jsou v <em>C:\Users\\ <yourusername> </em> složky.</span><span class="sxs-lookup"><span data-stu-id="62462-297">The <em>.mdf</em> and <em>.ldf</em> database files are in the <em>C:\Users\\<yourusername></em> folder.</span></span>

<span data-ttu-id="62462-298">Protože voláte `EnsureCreated` v inicializační metoda, která se spustí při spuštění aplikace, může teď provedete změnu `Student` třídy, odstraňte databázi, spusťte aplikaci znovu spustit a databáze bude automaticky znovu vytvořit tak, aby odpovídaly změny.</span><span class="sxs-lookup"><span data-stu-id="62462-298">Because you're calling `EnsureCreated` in the initializer method that runs on app start, you could now make a change to the `Student` class, delete the database, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="62462-299">Například, pokud chcete přidat `EmailAddress` vlastnost `Student` třídy, zobrazí se vám nový `EmailAddress` sloupec v tabulce znovu vytvořit.</span><span class="sxs-lookup"><span data-stu-id="62462-299">For example, if you add an `EmailAddress` property to the `Student` class, you'll see a new `EmailAddress` column in the re-created table.</span></span>

## <a name="conventions"></a><span data-ttu-id="62462-300">Konvence</span><span class="sxs-lookup"><span data-stu-id="62462-300">Conventions</span></span>

<span data-ttu-id="62462-301">Protože se používá konvence nebo předpokladů, které díky rozhraní Entity Framework je minimální množství kódu, kterou jste používali pro zápis v pořadí pro Entity Framework, abyste mohli vytvořit kompletní databáze za vás.</span><span class="sxs-lookup"><span data-stu-id="62462-301">The amount of code you had to write in order for the Entity Framework to be able to create a complete database for you is minimal because of the use of conventions, or assumptions that the Entity Framework makes.</span></span>

* <span data-ttu-id="62462-302">Názvy `DbSet` vlastnosti slouží jako názvy tabulek.</span><span class="sxs-lookup"><span data-stu-id="62462-302">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="62462-303">Pro entity není odkazuje `DbSet` vlastnost entity třída názvy jsou použity jako názvy tabulek.</span><span class="sxs-lookup"><span data-stu-id="62462-303">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>

* <span data-ttu-id="62462-304">Názvy vlastností entity se používají pro názvy sloupců.</span><span class="sxs-lookup"><span data-stu-id="62462-304">Entity property names are used for column names.</span></span>

* <span data-ttu-id="62462-305">Vlastnosti entity, které jsou pojmenovány ID nebo classnameID jsou rozpoznány jako vlastnosti primárního klíče.</span><span class="sxs-lookup"><span data-stu-id="62462-305">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>

* <span data-ttu-id="62462-306">Vlastnost je interpretován jako vlastnost cizího klíče, pokud je název *<navigation property name> <primary key property name>* (například `StudentID` pro `Student` navigační vlastnost, protože `Student` je primární klíč entity `ID`).</span><span class="sxs-lookup"><span data-stu-id="62462-306">A property is interpreted as a foreign key property if it's named *<navigation property name><primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="62462-307">Vlastnosti cizího klíče může nazývat také jednoduše *<primary key property name>* (například `EnrollmentID` od `Enrollment` je primární klíč entity `EnrollmentID`).</span><span class="sxs-lookup"><span data-stu-id="62462-307">Foreign key properties can also be named simply *<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="62462-308">Konvenční chování můžete přepsat.</span><span class="sxs-lookup"><span data-stu-id="62462-308">Conventional behavior can be overridden.</span></span> <span data-ttu-id="62462-309">Například můžete explicitně určit názvy tabulek, protože jste viděli dříve v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="62462-309">For example, you can explicitly specify table names, as you saw earlier in this tutorial.</span></span> <span data-ttu-id="62462-310">A můžete nastavit názvy sloupců a nastavte libovolnou vlastnost jako primární klíč, cizí klíč, nebo jak uvidíte v [pozdějších kurzech](complex-data-model.md) této série.</span><span class="sxs-lookup"><span data-stu-id="62462-310">And you can set column names and set any property as primary key or foreign key, as you'll see in a [later tutorial](complex-data-model.md) in this series.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="62462-311">Asynchronní kód</span><span class="sxs-lookup"><span data-stu-id="62462-311">Asynchronous code</span></span>

<span data-ttu-id="62462-312">Asynchronní programování je výchozím režimem pro ASP.NET Core a EF Core.</span><span class="sxs-lookup"><span data-stu-id="62462-312">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="62462-313">Webový server má omezený počet vláken, které jsou k dispozici, a v situacích, vysokého zatížení všech dostupných vláken může být používán.</span><span class="sxs-lookup"><span data-stu-id="62462-313">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="62462-314">Pokud k tomu dojde, server nemůže zpracovat nové žádosti, dokud se uvolnit vlákna.</span><span class="sxs-lookup"><span data-stu-id="62462-314">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="62462-315">Přestože se nejedná skutečně každé dílo vzhledem k tomu, že čekání na vstupně-výstupních operací na dokončení, může kódem synchronní svázané několika vlákny.</span><span class="sxs-lookup"><span data-stu-id="62462-315">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="62462-316">Asynchronní kód když proces čeká na vstupně-výstupních operací na dokončení, je jeho vlákno uvolněn pro server určený pro zpracováním jiných požadavků.</span><span class="sxs-lookup"><span data-stu-id="62462-316">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="62462-317">V důsledku toho asynchronního kódu umožňuje prostředky serveru použije efektivněji a aby zvládla větší provoz bez zpoždění je povoleno na serveru.</span><span class="sxs-lookup"><span data-stu-id="62462-317">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="62462-318">Asynchronní kód v době běhu zavést malé množství režie, ale v situacích s nízkým provozem výkonu přístupů je zanedbatelný, při vysoké návštěvnosti situacích, je možné zlepšení výkonu podstatné.</span><span class="sxs-lookup"><span data-stu-id="62462-318">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="62462-319">V následujícím kódu `async` – klíčové slovo, `Task<T>` návratovou hodnotu, `await` – klíčové slovo, a `ToListAsync` metoda změňte kód spustit asynchronně.</span><span class="sxs-lookup"><span data-stu-id="62462-319">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="62462-320">`async` – Klíčové slovo instruuje kompilátor generovat zpětná volání pro části tělo metody a automaticky vytvářet `Task<IActionResult>` vráceného objektu.</span><span class="sxs-lookup"><span data-stu-id="62462-320">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>

* <span data-ttu-id="62462-321">Návratový typ `Task<IActionResult>` představuje probíhající práci s výsledkem typu `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="62462-321">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>

* <span data-ttu-id="62462-322">`await` – Klíčové slovo způsobí, že kompilátor metodu rozdělit do dvou částí.</span><span class="sxs-lookup"><span data-stu-id="62462-322">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="62462-323">První část končí operace, která se spustí asynchronně.</span><span class="sxs-lookup"><span data-stu-id="62462-323">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="62462-324">Druhá část je nepoužili metodu zpětného volání, která je volána po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="62462-324">The second part is put into a callback method that's called when the operation completes.</span></span>

* <span data-ttu-id="62462-325">`ToListAsync` je asynchronní verze `ToList` – metoda rozšíření.</span><span class="sxs-lookup"><span data-stu-id="62462-325">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="62462-326">Je potřeba vědět při psaní asynchronního kódu, který používá Entity Framework pár věcí:</span><span class="sxs-lookup"><span data-stu-id="62462-326">Some things to be aware of when you are writing asynchronous code that uses the Entity Framework:</span></span>

* <span data-ttu-id="62462-327">Jenom příkazy, které způsobují dotazy nebo příkazy, které se odesílají do databáze se provedl asynchronně.</span><span class="sxs-lookup"><span data-stu-id="62462-327">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="62462-328">Který obsahuje, například `ToListAsync`, `SingleOrDefaultAsync`, a `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="62462-328">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="62462-329">Neměl by zahrnovat, například příkazy, které stačí změnit `IQueryable`, jako například `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="62462-329">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>

* <span data-ttu-id="62462-330">Objekt context EF není bezpečné pro vlákna: nedoporučujeme provádět více operací paralelně.</span><span class="sxs-lookup"><span data-stu-id="62462-330">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="62462-331">Při volání asynchronní metody EF, vždy používejte `await` – klíčové slovo.</span><span class="sxs-lookup"><span data-stu-id="62462-331">When you call any async EF method, always use the `await` keyword.</span></span>

* <span data-ttu-id="62462-332">Pokud chcete využít výhod výkony těží z asynchronní kód, ujistěte se, že všechny knihovny balíčky, které používáte (například stránkování), asynchronní použijte i v případě volají všechny Entity Framework metody, které způsobují dotazů k odeslání do databáze.</span><span class="sxs-lookup"><span data-stu-id="62462-332">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="62462-333">Další informace o asynchronním programování v rozhraní .NET najdete v tématu [asynchronní přehled](https://docs.microsoft.com/dotnet/articles/standard/async).</span><span class="sxs-lookup"><span data-stu-id="62462-333">For more information about asynchronous programming in .NET, see [Async Overview](https://docs.microsoft.com/dotnet/articles/standard/async).</span></span>

## <a name="summary"></a><span data-ttu-id="62462-334">Souhrn</span><span class="sxs-lookup"><span data-stu-id="62462-334">Summary</span></span>

<span data-ttu-id="62462-335">Právě jste vytvořili jednoduchou aplikaci, která se používá k ukládání a zobrazení dat Entity Framework Core a SQL Server Express LocalDB.</span><span class="sxs-lookup"><span data-stu-id="62462-335">You've now created a simple application that uses the Entity Framework Core and SQL Server Express LocalDB to store and display data.</span></span> <span data-ttu-id="62462-336">V následujícím kurzu se dozvíte jak provést základní CRUD (vytváření, čtení, aktualizace nebo odstranění) operace.</span><span class="sxs-lookup"><span data-stu-id="62462-336">In the following tutorial, you'll learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

::: moniker-end

> [!div class="step-by-step"]
> [<span data-ttu-id="62462-337">Next</span><span class="sxs-lookup"><span data-stu-id="62462-337">Next</span></span>](crud.md)
