---
title: 'Kurz: Začínáme s EF Core ve webové aplikaci ASP.NET MVC'
description: Toto je první ze série kurzů, které vysvětlují, jak vytvořit ukázková aplikace Contoso University úplně od začátku.
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/06/2019
ms.topic: tutorial
uid: data/ef-mvc/intro
ms.openlocfilehash: f7b557c8e560393ae886c46fad95c48ccbcc65b4
ms.sourcegitcommit: 5e3797a02ff3c48bb8cb9ad4320bfd169ebe8aba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56102965"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a><span data-ttu-id="01b0c-103">Kurz: Začínáme s EF Core ve webové aplikaci ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="01b0c-103">Tutorial: Get started with EF Core in an ASP.NET MVC web app</span></span>

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="01b0c-104">Contoso University ukázkovou webovou aplikaci ukazuje, jak vytvářet webové aplikace ASP.NET Core 2.2 MVC pomocí Entity Framework (EF) Core 2.0 a Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="01b0c-104">The Contoso University sample web application demonstrates how to create ASP.NET Core 2.2 MVC web applications using Entity Framework (EF) Core 2.0 and Visual Studio 2017.</span></span>

<span data-ttu-id="01b0c-105">Ukázková aplikace je webovou stránku pro fiktivní společnosti Contoso University.</span><span class="sxs-lookup"><span data-stu-id="01b0c-105">The sample application is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="01b0c-106">Zahrnuje funkce, jako student přijetí, kurz vytvoření a přiřazení instruktorem.</span><span class="sxs-lookup"><span data-stu-id="01b0c-106">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="01b0c-107">Toto je první ze série kurzů, které vysvětlují, jak vytvořit ukázková aplikace Contoso University úplně od začátku.</span><span class="sxs-lookup"><span data-stu-id="01b0c-107">This is the first in a series of tutorials that explain how to build the Contoso University sample application from scratch.</span></span>

<span data-ttu-id="01b0c-108">EF Core 2.0 je nejnovější verze EF, ale ještě nemá všechny funkce verze EF 6.x.</span><span class="sxs-lookup"><span data-stu-id="01b0c-108">EF Core 2.0 is the latest version of EF but doesn't yet have all the features of EF 6.x.</span></span> <span data-ttu-id="01b0c-109">Informace o tom, jak si vybrat mezi EF 6.x a EF Core, najdete v části [EF Core vs. EF6.x](/ef/efcore-and-ef6/).</span><span class="sxs-lookup"><span data-stu-id="01b0c-109">For information about how to choose between EF 6.x and EF Core, see [EF Core vs. EF6.x](/ef/efcore-and-ef6/).</span></span> <span data-ttu-id="01b0c-110">Pokud se rozhodnete EF 6.x, naleznete v tématu [předchozí verze v této sérii kurzů](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).</span><span class="sxs-lookup"><span data-stu-id="01b0c-110">If you choose EF 6.x, see [the previous version of this tutorial series](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).</span></span>

> [!NOTE]
> <span data-ttu-id="01b0c-111">ASP.NET Core 1.1 tohoto kurzu, najdete v článku [verze VS 2017 Update 2 v tomto kurzu ve formátu PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/efmvc1.1.pdf).</span><span class="sxs-lookup"><span data-stu-id="01b0c-111">For the ASP.NET Core 1.1 version of this tutorial, see the [VS 2017 Update 2 version of this tutorial in PDF format](https://webpifeed.blob.core.windows.net/webpifeed/Partners/efmvc1.1.pdf).</span></span>

<span data-ttu-id="01b0c-112">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="01b0c-112">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="01b0c-113">Vytvoření webové aplikace ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="01b0c-113">Create ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="01b0c-114">Nastavit styl lokality</span><span class="sxs-lookup"><span data-stu-id="01b0c-114">Set up the site style</span></span>
> * <span data-ttu-id="01b0c-115">Další informace o balíčcích EF Core NuGet</span><span class="sxs-lookup"><span data-stu-id="01b0c-115">Learn about EF Core NuGet packages</span></span>
> * <span data-ttu-id="01b0c-116">Vytvoření datového modelu</span><span class="sxs-lookup"><span data-stu-id="01b0c-116">Create the data model</span></span>
> * <span data-ttu-id="01b0c-117">Vytvořte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="01b0c-117">Create the database context</span></span>
> * <span data-ttu-id="01b0c-118">Zaregistrujte SchoolContext</span><span class="sxs-lookup"><span data-stu-id="01b0c-118">Register the SchoolContext</span></span>
> * <span data-ttu-id="01b0c-119">Inicializace databáze s testovací data</span><span class="sxs-lookup"><span data-stu-id="01b0c-119">Initialize DB with test data</span></span>
> * <span data-ttu-id="01b0c-120">Vytvoření kontroleru a zobrazení</span><span class="sxs-lookup"><span data-stu-id="01b0c-120">Create controller and views</span></span>
> * <span data-ttu-id="01b0c-121">Zobrazení databáze</span><span class="sxs-lookup"><span data-stu-id="01b0c-121">View the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="01b0c-122">Požadavky</span><span class="sxs-lookup"><span data-stu-id="01b0c-122">Prerequisites</span></span>

[!INCLUDE [](~/includes/net-core-prereqs.md)]

## <a name="troubleshooting"></a><span data-ttu-id="01b0c-123">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="01b0c-123">Troubleshooting</span></span>

<span data-ttu-id="01b0c-124">Pokud narazíte na problém nevyřešíte sami, můžete najít řešení obvykle porovnáním kódu [dokončený projekt](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final).</span><span class="sxs-lookup"><span data-stu-id="01b0c-124">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final).</span></span> <span data-ttu-id="01b0c-125">Seznam běžných chyb a jak je vyřešit, najdete v části [části Poradce při potížích s posledním dílem série](advanced.md#common-errors).</span><span class="sxs-lookup"><span data-stu-id="01b0c-125">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="01b0c-126">Pokud jste nenašli, co potřebujete existuje, můžete odeslat dotaz do StackOverflow.com pro [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="01b0c-126">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="01b0c-127">Toto je série 10 kurzů, z nichž každý je založena na co se provádí v předchozích kurzech.</span><span class="sxs-lookup"><span data-stu-id="01b0c-127">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="01b0c-128">Zvažte možnost uložení kopie projektu po každé úspěšné dokončení tohoto kurzu.</span><span class="sxs-lookup"><span data-stu-id="01b0c-128">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="01b0c-129">A pokud narazíte na problémy, můžete začít z předchozí kurz o službě místo přechodu zpět na začátek celou řadu.</span><span class="sxs-lookup"><span data-stu-id="01b0c-129">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="01b0c-130">Contoso University webové aplikace</span><span class="sxs-lookup"><span data-stu-id="01b0c-130">Contoso University web app</span></span>

<span data-ttu-id="01b0c-131">Aplikace, kterou je budete vytvářet v těchto kurzech je webová stránka jednoduché university.</span><span class="sxs-lookup"><span data-stu-id="01b0c-131">The application you'll be building in these tutorials is a simple university web site.</span></span>

<span data-ttu-id="01b0c-132">Uživatelé mohou zobrazit a aktualizovat Všichni studenti, kurz a informace instruktorem.</span><span class="sxs-lookup"><span data-stu-id="01b0c-132">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="01b0c-133">Tady je několik obrazovek, které vytvoříte.</span><span class="sxs-lookup"><span data-stu-id="01b0c-133">Here are a few of the screens you'll create.</span></span>

![Studenti indexová stránka](intro/_static/students-index.png)

![Stránky pro úpravu studentů](intro/_static/student-edit.png)

<span data-ttu-id="01b0c-136">Styl uživatelského rozhraní tohoto webu má blízko co je generována pomocí integrovaných šablon, ukládají, abyste se mohli zaměřit kurz hlavně na tom, jak používat rozhraní Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="01b0c-136">The UI style of this site has been kept close to what's generated by the built-in templates, so that the tutorial can focus mainly on how to use the Entity Framework.</span></span>

## <a name="create-aspnet-core-mvc-web-app"></a><span data-ttu-id="01b0c-137">Vytvoření webové aplikace ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="01b0c-137">Create ASP.NET Core MVC web app</span></span>

<span data-ttu-id="01b0c-138">Otevřít Visual Studio a vytvořte nové technologie ASP.NET Core C# projekt webu s názvem "ContosoUniversity".</span><span class="sxs-lookup"><span data-stu-id="01b0c-138">Open Visual Studio and create a new ASP.NET Core C# web project named "ContosoUniversity".</span></span>

* <span data-ttu-id="01b0c-139">Z **souboru** nabídce vyberte možnost **nový > projekt**.</span><span class="sxs-lookup"><span data-stu-id="01b0c-139">From the **File** menu, select **New > Project**.</span></span>

* <span data-ttu-id="01b0c-140">V levém podokně vyberte **nainstalováno > Visual C# > Web**.</span><span class="sxs-lookup"><span data-stu-id="01b0c-140">From the left pane, select **Installed > Visual C# > Web**.</span></span>

* <span data-ttu-id="01b0c-141">Vyberte **webové aplikace ASP.NET Core** šablony projektu.</span><span class="sxs-lookup"><span data-stu-id="01b0c-141">Select the **ASP.NET Core Web Application** project template.</span></span>

* <span data-ttu-id="01b0c-142">Zadejte **ContosoUniversity** jako název a klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="01b0c-142">Enter **ContosoUniversity** as the name and click **OK**.</span></span>

  ![Dialogové okno nového projektu](intro/_static/new-project2.png)

* <span data-ttu-id="01b0c-144">Počkejte **nové technologie ASP.NET Core webová aplikace (.NET Core)** zobrazit dialogové okno</span><span class="sxs-lookup"><span data-stu-id="01b0c-144">Wait for the **New ASP.NET Core Web Application (.NET Core)** dialog to appear</span></span>

  ![Dialogové okno Nový projekt ASP.NET Core](intro/_static/new-aspnet2.png)

* <span data-ttu-id="01b0c-146">Vyberte **2.2 technologie ASP.NET Core** a **webové aplikace (Model-View-Controller)** šablony.</span><span class="sxs-lookup"><span data-stu-id="01b0c-146">Select **ASP.NET Core 2.2** and the **Web Application (Model-View-Controller)** template.</span></span>

  <span data-ttu-id="01b0c-147">**Poznámka:** Tento kurz vyžaduje 2.2 technologie ASP.NET Core a EF Core 2.0 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="01b0c-147">**Note:** This tutorial requires ASP.NET Core 2.2 and EF Core 2.0 or later.</span></span>

* <span data-ttu-id="01b0c-148">Ujistěte se, že **ověřování** je nastavena na **bez ověřování**.</span><span class="sxs-lookup"><span data-stu-id="01b0c-148">Make sure **Authentication** is set to **No Authentication**.</span></span>

* <span data-ttu-id="01b0c-149">Klikněte na tlačítko **OK**</span><span class="sxs-lookup"><span data-stu-id="01b0c-149">Click **OK**</span></span>

## <a name="set-up-the-site-style"></a><span data-ttu-id="01b0c-150">Nastavit styl lokality</span><span class="sxs-lookup"><span data-stu-id="01b0c-150">Set up the site style</span></span>

<span data-ttu-id="01b0c-151">Několik jednoduchých změn se nastavit v nabídce webu, rozložení a domovské stránky.</span><span class="sxs-lookup"><span data-stu-id="01b0c-151">A few simple changes will set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="01b0c-152">Otevřít *Views/Shared/_Layout.cshtml* a proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="01b0c-152">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="01b0c-153">Změňte všechny výskyty "ContosoUniversity" na "University společnosti Contoso".</span><span class="sxs-lookup"><span data-stu-id="01b0c-153">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="01b0c-154">Existují tři výskyty.</span><span class="sxs-lookup"><span data-stu-id="01b0c-154">There are three occurrences.</span></span>

* <span data-ttu-id="01b0c-155">Přidání položek nabídky **o**, **studenty**, **kurzy**, **Instruktoři**, a **oddělení**, a Odstranit **ochrany osobních údajů** položku nabídky.</span><span class="sxs-lookup"><span data-stu-id="01b0c-155">Add menu entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="01b0c-156">Změny jsou zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="01b0c-156">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,32-36,51)]

<span data-ttu-id="01b0c-157">V *Views/Home/Index.cshtml*, nahraďte obsah souboru následující kód, který nahradí text o ASP.NET a MVC o této aplikaci:</span><span class="sxs-lookup"><span data-stu-id="01b0c-157">In *Views/Home/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

<span data-ttu-id="01b0c-158">Stisknutím kláves CTRL + F5 ke spuštění projektu nebo zvolte **ladit > Spustit bez ladění** z nabídky.</span><span class="sxs-lookup"><span data-stu-id="01b0c-158">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="01b0c-159">Zobrazí domovská stránka s kartami pro stránky, které vytvoříte v těchto kurzech.</span><span class="sxs-lookup"><span data-stu-id="01b0c-159">You see the home page with tabs for the pages you'll create in these tutorials.</span></span>

![Domovská stránka vysoké školy contoso](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a><span data-ttu-id="01b0c-161">Informace o balíčcích EF Core NuGet</span><span class="sxs-lookup"><span data-stu-id="01b0c-161">About EF Core NuGet packages</span></span>

<span data-ttu-id="01b0c-162">Do projektu přidat podporu EF Core, nainstalujte poskytovatele databáze, kterou chcete cílit.</span><span class="sxs-lookup"><span data-stu-id="01b0c-162">To add EF Core support to a project, install the database provider that you want to target.</span></span> <span data-ttu-id="01b0c-163">Tento kurz používá systém SQL Server a je balíček zprostředkovatele [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="01b0c-163">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span> <span data-ttu-id="01b0c-164">Tento balíček je součástí [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app), takže není nutné, pokud vaše aplikace obsahuje odkaz na balíček pro odkazujte na balíček `Microsoft.AspNetCore.App` balíčku.</span><span class="sxs-lookup"><span data-stu-id="01b0c-164">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to reference the package if your app has a package reference for the `Microsoft.AspNetCore.App` package.</span></span>

<span data-ttu-id="01b0c-165">Tento balíček a jeho závislosti (`Microsoft.EntityFrameworkCore` a `Microsoft.EntityFrameworkCore.Relational`) poskytují podporu runtime pro EF.</span><span class="sxs-lookup"><span data-stu-id="01b0c-165">This package and its dependencies (`Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`) provide runtime support for EF.</span></span> <span data-ttu-id="01b0c-166">Přidejte balíček nástroje v pozdější [migrace](migrations.md) kurzu.</span><span class="sxs-lookup"><span data-stu-id="01b0c-166">You'll add a tooling package later, in the [Migrations](migrations.md) tutorial.</span></span>

<span data-ttu-id="01b0c-167">Informace o dalších poskytovatelů databáze, které jsou dostupné pro Entity Framework Core najdete v tématu [databáze poskytovatelé](/ef/core/providers/).</span><span class="sxs-lookup"><span data-stu-id="01b0c-167">For information about other database providers that are available for Entity Framework Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="01b0c-168">Vytvoření datového modelu</span><span class="sxs-lookup"><span data-stu-id="01b0c-168">Create the data model</span></span>

<span data-ttu-id="01b0c-169">Dále vytvoříte tříd entit pro aplikaci Contoso University.</span><span class="sxs-lookup"><span data-stu-id="01b0c-169">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="01b0c-170">Začnete s následující tři entity.</span><span class="sxs-lookup"><span data-stu-id="01b0c-170">You'll start with the following three entities.</span></span>

![Kurz – registrace – studentech modelového diagramu](intro/_static/data-model-diagram.png)

<span data-ttu-id="01b0c-172">Existuje vztah jeden mnoho mezi `Student` a `Enrollment` entity, a existuje vztah jeden mnoho mezi `Course` a `Enrollment` entity.</span><span class="sxs-lookup"><span data-stu-id="01b0c-172">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="01b0c-173">Jinými slovy student možné zaregistrovat libovolný počet kurzy a kurzu může mít libovolný počet studentů zaregistrovaná do něj.</span><span class="sxs-lookup"><span data-stu-id="01b0c-173">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="01b0c-174">V následujících částech vytvoříte třídu pro každou z těchto entit.</span><span class="sxs-lookup"><span data-stu-id="01b0c-174">In the following sections you'll create a class for each one of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="01b0c-175">Entita studenta</span><span class="sxs-lookup"><span data-stu-id="01b0c-175">The Student entity</span></span>

![Diagram entity studenta](intro/_static/student-entity.png)

<span data-ttu-id="01b0c-177">V *modely* složce vytvořte soubor třídy *Student.cs* a nahraďte kód šablony následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="01b0c-177">In the *Models* folder, create a class file named *Student.cs* and replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="01b0c-178">`ID` Vlastnost se stane sloupec primárního klíče tabulky databáze, která odpovídá této třídy.</span><span class="sxs-lookup"><span data-stu-id="01b0c-178">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="01b0c-179">Ve výchozím nastavení interpretuje Entity Framework vlastnost s názvem `ID` nebo `classnameID` jako primární klíč.</span><span class="sxs-lookup"><span data-stu-id="01b0c-179">By default, the Entity Framework interprets a property that's named `ID` or `classnameID` as the primary key.</span></span>

<span data-ttu-id="01b0c-180">`Enrollments` Je vlastnost [navigační vlastnost](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="01b0c-180">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="01b0c-181">Vlastnosti navigace podržte dalšími subjekty, které se vztahují k této entity.</span><span class="sxs-lookup"><span data-stu-id="01b0c-181">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="01b0c-182">V takovém případě `Enrollments` vlastnost `Student entity` bude obsahovat všechny `Enrollment` entity, které se vztahují k, které `Student` entity.</span><span class="sxs-lookup"><span data-stu-id="01b0c-182">In this case, the `Enrollments` property of a `Student entity` will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="01b0c-183">Jinými slovy, pokud daný řádek studentů v databázi má dva související řádky registrace (řádky, které obsahují hodnotu primárního klíče student získal v jejich StudentID sloupec cizího klíče), který `Student` entity `Enrollments` navigační vlastnost bude obsahovat tyto dvě `Enrollment` entity.</span><span class="sxs-lookup"><span data-stu-id="01b0c-183">In other words, if a given Student row in the database has two related Enrollment rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="01b0c-184">Pokud vlastnost navigace může obsahovat více entit (jako v relace m: n nebo 1 n), jeho typ musí být seznam, ve kterém položky lze přidávat, odstranit a aktualizovat, například `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="01b0c-184">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection<T>`.</span></span> <span data-ttu-id="01b0c-185">Můžete zadat `ICollection<T>` nebo typu jako `List<T>` nebo `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="01b0c-185">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="01b0c-186">Pokud zadáte `ICollection<T>`, vytvoří EF `HashSet<T>` kolekcí ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="01b0c-186">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="01b0c-187">Registrace entity</span><span class="sxs-lookup"><span data-stu-id="01b0c-187">The Enrollment entity</span></span>

![Diagram entity registrace](intro/_static/enrollment-entity.png)

<span data-ttu-id="01b0c-189">V *modely* složku, vytvořte *Enrollment.cs* a nahraďte existující kód následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="01b0c-189">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="01b0c-190">`EnrollmentID` Bude mít vlastnost primárního klíče; tato entita používá `classnameID` vzorku místo `ID` samostatně jako jste viděli v `Student` entity.</span><span class="sxs-lookup"><span data-stu-id="01b0c-190">The `EnrollmentID` property will be the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="01b0c-191">Obvykle by zvolte jeden model a použít ho v rámci datového modelu.</span><span class="sxs-lookup"><span data-stu-id="01b0c-191">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="01b0c-192">Tady variantu ukazuje, které můžete použít buď vzor.</span><span class="sxs-lookup"><span data-stu-id="01b0c-192">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="01b0c-193">V [pozdějších kurzech](inheritance.md), uvidíte, jak pomocí ID bez classname usnadňuje implementaci dědičnosti v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="01b0c-193">In a [later tutorial](inheritance.md), you'll see how using ID without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="01b0c-194">`Grade` Vlastnost je `enum`.</span><span class="sxs-lookup"><span data-stu-id="01b0c-194">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="01b0c-195">Otazník po `Grade` deklarace typu znamená, že `Grade` vlastnost může mít hodnotu Null.</span><span class="sxs-lookup"><span data-stu-id="01b0c-195">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="01b0c-196">Na podnikové úrovni, který má hodnotu null se liší od nulové třída – null znamená, že známku vyjádřenou není znám nebo ještě nebyly přiřazeny.</span><span class="sxs-lookup"><span data-stu-id="01b0c-196">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="01b0c-197">`StudentID` Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Student`.</span><span class="sxs-lookup"><span data-stu-id="01b0c-197">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="01b0c-198">`Enrollment` Entita je přidružený nejméně k jednomu `Student` entity, tak vlastnost může obsahovat pouze jeden `Student` entity (na rozdíl od `Student.Enrollments` navigační vlastnost předchozímu příkladu, který může obsahovat více `Enrollment` entity).</span><span class="sxs-lookup"><span data-stu-id="01b0c-198">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="01b0c-199">`CourseID` Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Course`.</span><span class="sxs-lookup"><span data-stu-id="01b0c-199">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="01b0c-200">`Enrollment` Entita je přidružený nejméně k jednomu `Course` entity.</span><span class="sxs-lookup"><span data-stu-id="01b0c-200">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="01b0c-201">Nastavení interpretuje Entity Framework vlastnost jako vlastnost cizího klíče Pokud je název `<navigation property name><primary key property name>` (například `StudentID` pro `Student` navigační vlastnost, protože `Student` je primární klíč entity `ID`).</span><span class="sxs-lookup"><span data-stu-id="01b0c-201">Entity Framework interprets a property as a foreign key property if it's named `<navigation property name><primary key property name>` (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="01b0c-202">Vlastnosti cizího klíče může nazývat také jednoduše `<primary key property name>` (například `CourseID` od `Course` je primární klíč entity `CourseID`).</span><span class="sxs-lookup"><span data-stu-id="01b0c-202">Foreign key properties can also be named simply `<primary key property name>` (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="01b0c-203">Kurz entity</span><span class="sxs-lookup"><span data-stu-id="01b0c-203">The Course entity</span></span>

![Diagram kurzu entity](intro/_static/course-entity.png)

<span data-ttu-id="01b0c-205">V *modely* složku, vytvořte *Course.cs* a nahraďte existující kód následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="01b0c-205">In the *Models* folder, create *Course.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="01b0c-206">`Enrollments` Je navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="01b0c-206">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="01b0c-207">A `Course` entit může souviset s libovolným počtem `Enrollment` entity.</span><span class="sxs-lookup"><span data-stu-id="01b0c-207">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="01b0c-208">Informace o kliknu `DatabaseGenerated` atribut v [pozdějších kurzech](complex-data-model.md) v této sérii.</span><span class="sxs-lookup"><span data-stu-id="01b0c-208">We'll say more about the `DatabaseGenerated` attribute in a [later tutorial](complex-data-model.md) in this series.</span></span> <span data-ttu-id="01b0c-209">V podstatě tento atribut umožňuje zadat primární klíč pro kurz namísto nutnosti databáze jeho vygenerování.</span><span class="sxs-lookup"><span data-stu-id="01b0c-209">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="01b0c-210">Vytvořte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="01b0c-210">Create the database context</span></span>

<span data-ttu-id="01b0c-211">Hlavní třída, která koordinuje funkce Entity Framework pro daný datový model je třídy kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="01b0c-211">The main class that coordinates Entity Framework functionality for a given data model is the database context class.</span></span> <span data-ttu-id="01b0c-212">Vytvoření této třídy odvozené z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="01b0c-212">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="01b0c-213">V kódu určíte entit, které jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="01b0c-213">In your code you specify which entities are included in the data model.</span></span> <span data-ttu-id="01b0c-214">Můžete také přizpůsobit chování určité Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="01b0c-214">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="01b0c-215">V tomto projektu je s názvem třídy `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="01b0c-215">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="01b0c-216">Ve složce projektu vytvořte složku s názvem *Data*.</span><span class="sxs-lookup"><span data-stu-id="01b0c-216">In the project folder, create a folder named *Data*.</span></span>

<span data-ttu-id="01b0c-217">V *Data* složku vytvořte nový soubor třídy *SchoolContext.cs*a nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="01b0c-217">In the *Data* folder create a new class file named *SchoolContext.cs*, and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="01b0c-218">Tento kód vytvoří `DbSet` vlastností pro každou sadu entit.</span><span class="sxs-lookup"><span data-stu-id="01b0c-218">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="01b0c-219">V terminologii Entity Framework obvykle sadu entit odpovídá databázové tabulky a entity odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="01b0c-219">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<span data-ttu-id="01b0c-220">Mohli jste byl vynechán `DbSet<Enrollment>` a `DbSet<Course>` příkazy a bude fungovat stejně.</span><span class="sxs-lookup"><span data-stu-id="01b0c-220">You could've omitted the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="01b0c-221">Entity Framework bude zahrnovat je implicitně protože `Student` odkazy na entity `Enrollment` entity a `Enrollment` odkazy na entity `Course` entity.</span><span class="sxs-lookup"><span data-stu-id="01b0c-221">The Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="01b0c-222">Když se vytvoří databáze, EF vytvoří tabulky, které mají názvy, stejně jako `DbSet` názvy vlastností.</span><span class="sxs-lookup"><span data-stu-id="01b0c-222">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="01b0c-223">Pro kolekce v názvech vlastností se obvykle množném čísle (studenti spíše než Student), ale vývojáři Nesouhlasím o tom, jestli by měl názvy tabulek pluralized nebo ne.</span><span class="sxs-lookup"><span data-stu-id="01b0c-223">Property names for collections are typically plural (Students rather than Student), but developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="01b0c-224">Pro tyto kurzy přepíšete výchozí chování tak, že zadáte názvy singulární tabulek v uvolněn objekt DbContext.</span><span class="sxs-lookup"><span data-stu-id="01b0c-224">For these tutorials you'll override the default behavior by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="01b0c-225">K tomu, přidejte následující zvýrazněný kód za poslední DbSet vlastnost.</span><span class="sxs-lookup"><span data-stu-id="01b0c-225">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a><span data-ttu-id="01b0c-226">Zaregistrujte SchoolContext</span><span class="sxs-lookup"><span data-stu-id="01b0c-226">Register the SchoolContext</span></span>

<span data-ttu-id="01b0c-227">ASP.NET Core implementuje [injektáž závislostí](../../fundamentals/dependency-injection.md) ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="01b0c-227">ASP.NET Core implements [dependency injection](../../fundamentals/dependency-injection.md) by default.</span></span> <span data-ttu-id="01b0c-228">Služby (například v kontextu databáze EF) jsou registrované pomocí vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="01b0c-228">Services (such as the EF database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="01b0c-229">Komponenty, které vyžadují tyto služby (jako jsou řadiče MVC) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="01b0c-229">Components that require these services (such as MVC controllers) are provided these services via constructor parameters.</span></span> <span data-ttu-id="01b0c-230">Zobrazí se vám kód konstruktoru kontroler, který získá instance kontextu později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="01b0c-230">You'll see the controller constructor code that gets a context instance later in this tutorial.</span></span>

<span data-ttu-id="01b0c-231">K registraci `SchoolContext` jako služby, otevřete *Startup.cs*a přidejte zvýrazněné řádky a `ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="01b0c-231">To register `SchoolContext` as a service, open *Startup.cs*, and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=3-4)]

<span data-ttu-id="01b0c-232">Název připojovacího řetězce je předán v rámci voláním metody na `DbContextOptionsBuilder` objektu.</span><span class="sxs-lookup"><span data-stu-id="01b0c-232">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="01b0c-233">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="01b0c-233">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="01b0c-234">Přidat `using` příkazy pro `ContosoUniversity.Data` a `Microsoft.EntityFrameworkCore` obory názvů a pak sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="01b0c-234">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces, and then build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="01b0c-235">Otevřít *appsettings.json* a přidejte připojovací řetězec, jak je znázorněno v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="01b0c-235">Open the *appsettings.json* file and add a connection string as shown in the following example.</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a><span data-ttu-id="01b0c-236">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="01b0c-236">SQL Server Express LocalDB</span></span>

<span data-ttu-id="01b0c-237">Připojovací řetězec Určuje databázi SQL Server LocalDB.</span><span class="sxs-lookup"><span data-stu-id="01b0c-237">The connection string specifies a SQL Server LocalDB database.</span></span> <span data-ttu-id="01b0c-238">LocalDB je Odlehčená verze SQL serveru Express Database Engine a je určená pro vývoj aplikací, není použití v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="01b0c-238">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for application development, not production use.</span></span> <span data-ttu-id="01b0c-239">LocalDB spustí na vyžádání a běží v uživatelském režimu, takže není bez složité konfigurace.</span><span class="sxs-lookup"><span data-stu-id="01b0c-239">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="01b0c-240">Ve výchozím nastavení LocalDB vytvoří *.mdf* databázové soubory v `C:/Users/<user>` adresáře.</span><span class="sxs-lookup"><span data-stu-id="01b0c-240">By default, LocalDB creates *.mdf* database files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="01b0c-241">Inicializace databáze s testovací data</span><span class="sxs-lookup"><span data-stu-id="01b0c-241">Initialize DB with test data</span></span>

<span data-ttu-id="01b0c-242">Entity Framework pro vytvoření prázdné databáze.</span><span class="sxs-lookup"><span data-stu-id="01b0c-242">The Entity Framework will create an empty database for you.</span></span> <span data-ttu-id="01b0c-243">V této části napíšete metodu, která je volána po vytvoření databáze, aby bylo možné naplnit ho daty testu.</span><span class="sxs-lookup"><span data-stu-id="01b0c-243">In this section, you write a method that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="01b0c-244">Zde použijete `EnsureCreated` metoda automaticky vytvořit databázi.</span><span class="sxs-lookup"><span data-stu-id="01b0c-244">Here you'll use the `EnsureCreated` method to automatically create the database.</span></span> <span data-ttu-id="01b0c-245">V [pozdějších kurzech](migrations.md) uvidíte, jak zpracovat změny modelu pomocí migrace Code First pro změnu schématu databáze místo vyřadit a znovu vytvořit databázi.</span><span class="sxs-lookup"><span data-stu-id="01b0c-245">In a [later tutorial](migrations.md) you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="01b0c-246">V *Data* složku, vytvořte nový soubor třídy *DbInitializer.cs* a nahraďte kód šablony následujícím kódem, což způsobí, že databáze má být vytvořen v případě potřeby a načte testovací data do nové databáze.</span><span class="sxs-lookup"><span data-stu-id="01b0c-246">In the *Data* folder, create a new class file named *DbInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="01b0c-247">Kód kontroluje, jestli jsou všechny studenty v databázi a pokud ne, předpokládá se nové databáze a musí být nasazený s testovací data.</span><span class="sxs-lookup"><span data-stu-id="01b0c-247">The code checks if there are any students in the database, and if not, it assumes the database is new and needs to be seeded with test data.</span></span> <span data-ttu-id="01b0c-248">Načte testovací data do pole spíše než `List<T>` kolekce za účelem optimalizace výkonu.</span><span class="sxs-lookup"><span data-stu-id="01b0c-248">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="01b0c-249">V *Program.cs*, změnit `Main` metoda na spuštění aplikace, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="01b0c-249">In *Program.cs*, modify the `Main` method to do the following on application startup:</span></span>

* <span data-ttu-id="01b0c-250">Instance kontextu databáze získáte z kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="01b0c-250">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="01b0c-251">Volejte metodu počáteční hodnoty předání kontextu.</span><span class="sxs-lookup"><span data-stu-id="01b0c-251">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="01b0c-252">Kontext Dispose po dokončení počáteční hodnoty metody.</span><span class="sxs-lookup"><span data-stu-id="01b0c-252">Dispose the context when the seed method is done.</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

<span data-ttu-id="01b0c-253">Přidat `using` příkazy:</span><span class="sxs-lookup"><span data-stu-id="01b0c-253">Add `using` statements:</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Usings)]

<span data-ttu-id="01b0c-254">V kurzech starší se může zobrazit podobný kód v `Configure` metoda *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="01b0c-254">In older tutorials, you may see similar code in the `Configure` method in *Startup.cs*.</span></span> <span data-ttu-id="01b0c-255">Doporučujeme použít `Configure` metodu pouze k vytvoření kanálu požadavku.</span><span class="sxs-lookup"><span data-stu-id="01b0c-255">We recommend that you use the `Configure` method only to set up the request pipeline.</span></span> <span data-ttu-id="01b0c-256">Spouštěcímu kódu aplikace, do kterých patří `Main` metody.</span><span class="sxs-lookup"><span data-stu-id="01b0c-256">Application startup code belongs in the `Main` method.</span></span>

<span data-ttu-id="01b0c-257">Nyní při prvním spuštění aplikace, databáze bude vytvoří a nasadí se testovací data.</span><span class="sxs-lookup"><span data-stu-id="01b0c-257">Now the first time you run the application, the database will be created and seeded with test data.</span></span> <span data-ttu-id="01b0c-258">Pokaždé, když změníte datový model, můžete odstranit databázi, aktualizovat vaše seed – metoda a začít znovu s novou databázi stejným způsobem.</span><span class="sxs-lookup"><span data-stu-id="01b0c-258">Whenever you change your data model, you can delete the database, update your seed method, and start afresh with a new database the same way.</span></span> <span data-ttu-id="01b0c-259">V budoucích kurzech uvidíte, jak upravit databázi, když datového modelu změny, bez odstranění a vytvoříte ho znovu.</span><span class="sxs-lookup"><span data-stu-id="01b0c-259">In later tutorials, you'll see how to modify the database when the data model changes, without deleting and re-creating it.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="01b0c-260">Vytvoření kontroleru a zobrazení</span><span class="sxs-lookup"><span data-stu-id="01b0c-260">Create controller and views</span></span>

<span data-ttu-id="01b0c-261">V dalším kroku použijete modul generování uživatelského rozhraní v sadě Visual Studio přidat kontroler MVC a zobrazení, která bude používat EF pro dotazování a uložit data.</span><span class="sxs-lookup"><span data-stu-id="01b0c-261">Next, you'll use the scaffolding engine in Visual Studio to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="01b0c-262">Automatické vytváření metody akcí CRUD a zobrazení se označuje jako generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="01b0c-262">The automatic creation of CRUD action methods and views is known as scaffolding.</span></span> <span data-ttu-id="01b0c-263">Generování uživatelského rozhraní se liší od vytváření kódu v tom, že automaticky generovaný kód je výchozím bodem, který můžete upravit tak, aby vyhovoval vašim požadavkům, zatímco obvykle neupravujte generovaného kódu.</span><span class="sxs-lookup"><span data-stu-id="01b0c-263">Scaffolding differs from code generation in that the scaffolded code is a starting point that you can modify to suit your own requirements, whereas you typically don't modify generated code.</span></span> <span data-ttu-id="01b0c-264">Když budete potřebovat pro vygenerovaný kód upravit, použijte částečné třídy nebo při změně věci se znovu vygenerovat kód.</span><span class="sxs-lookup"><span data-stu-id="01b0c-264">When you need to customize generated code, you use partial classes or you regenerate the code when things change.</span></span>

* <span data-ttu-id="01b0c-265">Klikněte pravým tlačítkem myši **řadiče** složky v **Průzkumníka řešení** a vyberte **Přidat > novou vygenerovanou položku**.</span><span class="sxs-lookup"><span data-stu-id="01b0c-265">Right-click the **Controllers** folder in **Solution Explorer** and select **Add > New Scaffolded Item**.</span></span>

<span data-ttu-id="01b0c-266">Pokud **přidat závislosti MVC** se zobrazí dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="01b0c-266">If the **Add MVC Dependencies** dialog appears:</span></span>

* <span data-ttu-id="01b0c-267">[Aktualizace na nejnovější verzi sady Visual Studio](https://www.visualstudio.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017).</span><span class="sxs-lookup"><span data-stu-id="01b0c-267">[Update Visual Studio to the latest version](https://www.visualstudio.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017).</span></span> <span data-ttu-id="01b0c-268">Visual Studio verze starší než 15.5 zobrazí tento dialog.</span><span class="sxs-lookup"><span data-stu-id="01b0c-268">Visual Studio versions prior to 15.5 show this dialog.</span></span>
* <span data-ttu-id="01b0c-269">Pokud nelze aktualizovat, vyberte **přidat**a pak postupujte podle kroků přidat kontroler.</span><span class="sxs-lookup"><span data-stu-id="01b0c-269">If you can't update, select **ADD**, and then follow the add controller steps again.</span></span>

* <span data-ttu-id="01b0c-270">V **přidat vygenerované uživatelské rozhraní** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="01b0c-270">In the **Add Scaffold** dialog box:</span></span>

  * <span data-ttu-id="01b0c-271">Vyberte **kontroler MVC se zobrazeními pomocí Entity Frameworku**.</span><span class="sxs-lookup"><span data-stu-id="01b0c-271">Select **MVC controller with views, using Entity Framework**.</span></span>

  * <span data-ttu-id="01b0c-272">Klikněte na **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="01b0c-272">Click **Add**.</span></span> <span data-ttu-id="01b0c-273">**Přidat kontroler MVC se zobrazeními, používá nástroj Entity Framework** zobrazí se dialogové okno.</span><span class="sxs-lookup"><span data-stu-id="01b0c-273">The **Add MVC Controller with views, using Entity Framework** dialog box appears.</span></span>

    ![Student vygenerované uživatelské rozhraní](intro/_static/scaffold-student2.png)

  * <span data-ttu-id="01b0c-275">V **třída modelu** vyberte **Student**.</span><span class="sxs-lookup"><span data-stu-id="01b0c-275">In **Model class** select **Student**.</span></span>

  * <span data-ttu-id="01b0c-276">V **třída kontextu dat** vyberte **SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="01b0c-276">In **Data context class** select **SchoolContext**.</span></span>

  * <span data-ttu-id="01b0c-277">Přijměte výchozí nastavení **StudentsController** jako název.</span><span class="sxs-lookup"><span data-stu-id="01b0c-277">Accept the default **StudentsController** as the name.</span></span>

  * <span data-ttu-id="01b0c-278">Klikněte na **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="01b0c-278">Click **Add**.</span></span>

  <span data-ttu-id="01b0c-279">Po kliknutí na **přidat**, vytvoří modul generování uživatelského rozhraní sady Visual Studio *StudentsController.cs* souboru a nastavte zobrazení (*.cshtml* soubory), které fungují s kontrolerem.</span><span class="sxs-lookup"><span data-stu-id="01b0c-279">When you click **Add**, the Visual Studio scaffolding engine creates a *StudentsController.cs* file and a set of views (*.cshtml* files) that work with the controller.</span></span>

<span data-ttu-id="01b0c-280">(Modul generování uživatelského rozhraní můžete také vytvořit kontext databáze za vás Pokud nechcete vytvořit ručně nejprve, jako jste to udělali dříve v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="01b0c-280">(The scaffolding engine can also create the database context for you if you don't create it manually first as you did earlier for this tutorial.</span></span> <span data-ttu-id="01b0c-281">Můžete zadat novou třídu v kontextu **přidat kontroler** otevřete ho kliknutím na znaménko plus vedle **třída kontextu dat**.</span><span class="sxs-lookup"><span data-stu-id="01b0c-281">You can specify a new context class in the **Add Controller** box by clicking the plus sign to the right of **Data context class**.</span></span>  <span data-ttu-id="01b0c-282">Visual Studio vytvoří vaše `DbContext` třídy a také kontroler a zobrazení.)</span><span class="sxs-lookup"><span data-stu-id="01b0c-282">Visual Studio will then create your `DbContext` class as well as the controller and views.)</span></span>

<span data-ttu-id="01b0c-283">Všimněte si, že trvá kontroleru `SchoolContext` jako parametr konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="01b0c-283">You'll notice that the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="01b0c-284">Injektáž závislostí ASP.NET Core se postará o předáním instance `SchoolContext` do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="01b0c-284">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="01b0c-285">Jste nakonfigurovali, že *Startup.cs* soubor výše.</span><span class="sxs-lookup"><span data-stu-id="01b0c-285">You configured that in the *Startup.cs* file earlier.</span></span>

<span data-ttu-id="01b0c-286">Obsahuje kontroler `Index` metodě akce, která zobrazuje všechny studenty v databázi.</span><span class="sxs-lookup"><span data-stu-id="01b0c-286">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="01b0c-287">Metoda získá seznam studentů z entity studenty ve čtení nastavení `Students` vlastnost instance kontextu databáze:</span><span class="sxs-lookup"><span data-stu-id="01b0c-287">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="01b0c-288">Později v tomto kurzu získáte informace o asynchronní programovací prvky v tomto kódu.</span><span class="sxs-lookup"><span data-stu-id="01b0c-288">You'll learn about the asynchronous programming elements in this code later in the tutorial.</span></span>

<span data-ttu-id="01b0c-289">*Views/Students/Index.cshtml* zobrazení seznamu v tabulce:</span><span class="sxs-lookup"><span data-stu-id="01b0c-289">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="01b0c-290">Stisknutím kláves CTRL + F5 ke spuštění projektu nebo zvolte **ladit > Spustit bez ladění** z nabídky.</span><span class="sxs-lookup"><span data-stu-id="01b0c-290">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="01b0c-291">Klikněte na kartu studenty a zobrazit testovací data, která `DbInitializer.Initialize` metoda vložen.</span><span class="sxs-lookup"><span data-stu-id="01b0c-291">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="01b0c-292">V závislosti na tom, jak úzké okno prohlížeče, je, zobrazí se vám `Student` odkaz kartě v horní části stránky nebo je budete muset kliknout na navigační ikonu v pravém horním rohu na odkaz zobrazíte.</span><span class="sxs-lookup"><span data-stu-id="01b0c-292">Depending on how narrow your browser window is, you'll see the `Student` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Domovská stránka Contoso University úzký](intro/_static/home-page-narrow.png)

![Studenti indexová stránka](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="01b0c-295">Zobrazení databáze</span><span class="sxs-lookup"><span data-stu-id="01b0c-295">View the database</span></span>

<span data-ttu-id="01b0c-296">Při spuštění aplikace, `DbInitializer.Initialize` volání metody `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="01b0c-296">When you started the application, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="01b0c-297">EF viděli, že se žádná databáze a proto vytvoří jeden a zbytek `Initialize` kódu metody naplnit databázi daty.</span><span class="sxs-lookup"><span data-stu-id="01b0c-297">EF saw that there was no database and so it created one, then the remainder of the `Initialize` method code populated the database with data.</span></span> <span data-ttu-id="01b0c-298">Můžete použít **Průzkumník objektů systému SQL Server** (SSOX) Chcete-li zobrazit databáze v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="01b0c-298">You can use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span>

<span data-ttu-id="01b0c-299">Zavřete prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="01b0c-299">Close the browser.</span></span>

<span data-ttu-id="01b0c-300">Pokud okno SSOX ještě není otevřeno, vyberte ho z **zobrazení** nabídky v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="01b0c-300">If the SSOX window isn't already open, select it from the **View** menu in Visual Studio.</span></span>

<span data-ttu-id="01b0c-301">V SSOX, klikněte na tlačítko **\MSSQLLocalDB (localdb) > databáze**a potom klikněte na položku pro název databáze, která je v připojovacím řetězci v vaše *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="01b0c-301">In SSOX, click **(localdb)\MSSQLLocalDB > Databases**, and then click the entry for the database name that's in the connection string in your *appsettings.json* file.</span></span>

<span data-ttu-id="01b0c-302">Rozbalte **tabulky** uzel zobrazíte tabulky v databázi.</span><span class="sxs-lookup"><span data-stu-id="01b0c-302">Expand the **Tables** node to see the tables in your database.</span></span>

![Tabulky v SSOX](intro/_static/ssox-tables.png)

<span data-ttu-id="01b0c-304">Klikněte pravým tlačítkem na **Student** tabulky a klikněte na tlačítko **Data zobrazení** zobrazit sloupce, které byly vytvořeny a řádky, které byly vloženy do tabulky.</span><span class="sxs-lookup"><span data-stu-id="01b0c-304">Right-click the **Student** table and click **View Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

![Tabulka Student v SSOX](intro/_static/ssox-student-table.png)

<span data-ttu-id="01b0c-306"><em>.Mdf</em> a <em>.ldf</em> databázové soubory jsou v <em>C:\Users\\ <yourusername> </em> složky.</span><span class="sxs-lookup"><span data-stu-id="01b0c-306">The <em>.mdf</em> and <em>.ldf</em> database files are in the <em>C:\Users\\<yourusername></em> folder.</span></span>

<span data-ttu-id="01b0c-307">Protože voláte `EnsureCreated` v inicializační metoda, která se spustí při spuštění aplikace, může teď provedete změnu `Student` třídy, odstraňte databázi, spusťte aplikaci znovu spustit a databáze bude automaticky znovu vytvořit tak, aby odpovídaly změny.</span><span class="sxs-lookup"><span data-stu-id="01b0c-307">Because you're calling `EnsureCreated` in the initializer method that runs on app start, you could now make a change to the `Student` class, delete the database, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="01b0c-308">Například, pokud chcete přidat `EmailAddress` vlastnost `Student` třídy, zobrazí se vám nový `EmailAddress` sloupec v tabulce znovu vytvořit.</span><span class="sxs-lookup"><span data-stu-id="01b0c-308">For example, if you add an `EmailAddress` property to the `Student` class, you'll see a new `EmailAddress` column in the re-created table.</span></span>

## <a name="conventions"></a><span data-ttu-id="01b0c-309">Konvence</span><span class="sxs-lookup"><span data-stu-id="01b0c-309">Conventions</span></span>

<span data-ttu-id="01b0c-310">Protože se používá konvence nebo předpokladů, které díky rozhraní Entity Framework je minimální množství kódu, kterou jste používali pro zápis v pořadí pro Entity Framework, abyste mohli vytvořit kompletní databáze za vás.</span><span class="sxs-lookup"><span data-stu-id="01b0c-310">The amount of code you had to write in order for the Entity Framework to be able to create a complete database for you is minimal because of the use of conventions, or assumptions that the Entity Framework makes.</span></span>

* <span data-ttu-id="01b0c-311">Názvy `DbSet` vlastnosti slouží jako názvy tabulek.</span><span class="sxs-lookup"><span data-stu-id="01b0c-311">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="01b0c-312">Pro entity není odkazuje `DbSet` vlastnost entity třída názvy jsou použity jako názvy tabulek.</span><span class="sxs-lookup"><span data-stu-id="01b0c-312">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>

* <span data-ttu-id="01b0c-313">Názvy vlastností entity se používají pro názvy sloupců.</span><span class="sxs-lookup"><span data-stu-id="01b0c-313">Entity property names are used for column names.</span></span>

* <span data-ttu-id="01b0c-314">Vlastnosti entity, které jsou pojmenovány ID nebo classnameID jsou rozpoznány jako vlastnosti primárního klíče.</span><span class="sxs-lookup"><span data-stu-id="01b0c-314">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>

* <span data-ttu-id="01b0c-315">Vlastnost je interpretován jako vlastnost cizího klíče, pokud je název *<navigation property name> <primary key property name>* (například `StudentID` pro `Student` navigační vlastnost, protože `Student` je primární klíč entity `ID`).</span><span class="sxs-lookup"><span data-stu-id="01b0c-315">A property is interpreted as a foreign key property if it's named *<navigation property name><primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="01b0c-316">Vlastnosti cizího klíče může nazývat také jednoduše *<primary key property name>* (například `EnrollmentID` od `Enrollment` je primární klíč entity `EnrollmentID`).</span><span class="sxs-lookup"><span data-stu-id="01b0c-316">Foreign key properties can also be named simply *<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="01b0c-317">Konvenční chování můžete přepsat.</span><span class="sxs-lookup"><span data-stu-id="01b0c-317">Conventional behavior can be overridden.</span></span> <span data-ttu-id="01b0c-318">Například můžete explicitně určit názvy tabulek, protože jste viděli dříve v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="01b0c-318">For example, you can explicitly specify table names, as you saw earlier in this tutorial.</span></span> <span data-ttu-id="01b0c-319">A můžete nastavit názvy sloupců a nastavte libovolnou vlastnost jako primární klíč, cizí klíč, nebo jak uvidíte v [pozdějších kurzech](complex-data-model.md) této série.</span><span class="sxs-lookup"><span data-stu-id="01b0c-319">And you can set column names and set any property as primary key or foreign key, as you'll see in a [later tutorial](complex-data-model.md) in this series.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="01b0c-320">Asynchronní kód</span><span class="sxs-lookup"><span data-stu-id="01b0c-320">Asynchronous code</span></span>

<span data-ttu-id="01b0c-321">Asynchronní programování je výchozím režimem pro ASP.NET Core a EF Core.</span><span class="sxs-lookup"><span data-stu-id="01b0c-321">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="01b0c-322">Webový server má omezený počet vláken, které jsou k dispozici, a v situacích, vysokého zatížení všech dostupných vláken může být používán.</span><span class="sxs-lookup"><span data-stu-id="01b0c-322">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="01b0c-323">Pokud k tomu dojde, server nemůže zpracovat nové žádosti, dokud se uvolnit vlákna.</span><span class="sxs-lookup"><span data-stu-id="01b0c-323">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="01b0c-324">Přestože se nejedná skutečně každé dílo vzhledem k tomu, že čekání na vstupně-výstupních operací na dokončení, může kódem synchronní svázané několika vlákny.</span><span class="sxs-lookup"><span data-stu-id="01b0c-324">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="01b0c-325">Asynchronní kód když proces čeká na vstupně-výstupních operací na dokončení, je jeho vlákno uvolněn pro server určený pro zpracováním jiných požadavků.</span><span class="sxs-lookup"><span data-stu-id="01b0c-325">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="01b0c-326">V důsledku toho asynchronního kódu umožňuje prostředky serveru použije efektivněji a aby zvládla větší provoz bez zpoždění je povoleno na serveru.</span><span class="sxs-lookup"><span data-stu-id="01b0c-326">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="01b0c-327">Asynchronní kód v době běhu zavést malé množství režie, ale v situacích s nízkým provozem výkonu přístupů je zanedbatelný, při vysoké návštěvnosti situacích, je možné zlepšení výkonu podstatné.</span><span class="sxs-lookup"><span data-stu-id="01b0c-327">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="01b0c-328">V následujícím kódu `async` – klíčové slovo, `Task<T>` návratovou hodnotu, `await` – klíčové slovo, a `ToListAsync` metoda změňte kód spustit asynchronně.</span><span class="sxs-lookup"><span data-stu-id="01b0c-328">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="01b0c-329">`async` – Klíčové slovo instruuje kompilátor generovat zpětná volání pro části tělo metody a automaticky vytvářet `Task<IActionResult>` vráceného objektu.</span><span class="sxs-lookup"><span data-stu-id="01b0c-329">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>

* <span data-ttu-id="01b0c-330">Návratový typ `Task<IActionResult>` představuje probíhající práci s výsledkem typu `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="01b0c-330">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>

* <span data-ttu-id="01b0c-331">`await` – Klíčové slovo způsobí, že kompilátor metodu rozdělit do dvou částí.</span><span class="sxs-lookup"><span data-stu-id="01b0c-331">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="01b0c-332">První část končí operace, která se spustí asynchronně.</span><span class="sxs-lookup"><span data-stu-id="01b0c-332">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="01b0c-333">Druhá část je nepoužili metodu zpětného volání, která je volána po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="01b0c-333">The second part is put into a callback method that's called when the operation completes.</span></span>

* <span data-ttu-id="01b0c-334">`ToListAsync` je asynchronní verze `ToList` – metoda rozšíření.</span><span class="sxs-lookup"><span data-stu-id="01b0c-334">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="01b0c-335">Je potřeba vědět při psaní asynchronního kódu, který používá Entity Framework pár věcí:</span><span class="sxs-lookup"><span data-stu-id="01b0c-335">Some things to be aware of when you are writing asynchronous code that uses the Entity Framework:</span></span>

* <span data-ttu-id="01b0c-336">Jenom příkazy, které způsobují dotazy nebo příkazy, které se odesílají do databáze se provedl asynchronně.</span><span class="sxs-lookup"><span data-stu-id="01b0c-336">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="01b0c-337">Který obsahuje, například `ToListAsync`, `SingleOrDefaultAsync`, a `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="01b0c-337">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="01b0c-338">Neměl by zahrnovat, například příkazy, které stačí změnit `IQueryable`, jako například `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="01b0c-338">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>

* <span data-ttu-id="01b0c-339">Objekt context EF není bezpečné pro vlákna: nedoporučujeme provádět více operací paralelně.</span><span class="sxs-lookup"><span data-stu-id="01b0c-339">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="01b0c-340">Při volání asynchronní metody EF, vždy používejte `await` – klíčové slovo.</span><span class="sxs-lookup"><span data-stu-id="01b0c-340">When you call any async EF method, always use the `await` keyword.</span></span>

* <span data-ttu-id="01b0c-341">Pokud chcete využít výhod výkony těží z asynchronní kód, ujistěte se, že všechny knihovny balíčky, které používáte (například stránkování), asynchronní použijte i v případě volají všechny Entity Framework metody, které způsobují dotazů k odeslání do databáze.</span><span class="sxs-lookup"><span data-stu-id="01b0c-341">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="01b0c-342">Další informace o asynchronním programování v rozhraní .NET najdete v tématu [asynchronní přehled](/dotnet/articles/standard/async).</span><span class="sxs-lookup"><span data-stu-id="01b0c-342">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="01b0c-343">Získat kód</span><span class="sxs-lookup"><span data-stu-id="01b0c-343">Get the code</span></span>

[<span data-ttu-id="01b0c-344">Stažení nebo zobrazení dokončené aplikace.</span><span class="sxs-lookup"><span data-stu-id="01b0c-344">Download or view the completed application.</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="01b0c-345">Další kroky</span><span class="sxs-lookup"><span data-stu-id="01b0c-345">Next steps</span></span>

<span data-ttu-id="01b0c-346">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="01b0c-346">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="01b0c-347">Vytvořenou webovou aplikaci ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="01b0c-347">Created ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="01b0c-348">Nastavit styl lokality</span><span class="sxs-lookup"><span data-stu-id="01b0c-348">Set up the site style</span></span>
> * <span data-ttu-id="01b0c-349">Dozvěděli jste se o balíčcích EF Core NuGet</span><span class="sxs-lookup"><span data-stu-id="01b0c-349">Learned about EF Core NuGet packages</span></span>
> * <span data-ttu-id="01b0c-350">Vytvoření datového modelu</span><span class="sxs-lookup"><span data-stu-id="01b0c-350">Created the data model</span></span>
> * <span data-ttu-id="01b0c-351">Vytvoří kontext databáze</span><span class="sxs-lookup"><span data-stu-id="01b0c-351">Created the database context</span></span>
> * <span data-ttu-id="01b0c-352">Registrovaný SchoolContext</span><span class="sxs-lookup"><span data-stu-id="01b0c-352">Registered the SchoolContext</span></span>
> * <span data-ttu-id="01b0c-353">Inicializované databáze se testovací data</span><span class="sxs-lookup"><span data-stu-id="01b0c-353">Initialized DB with test data</span></span>
> * <span data-ttu-id="01b0c-354">Vytvořený kontroler a zobrazení</span><span class="sxs-lookup"><span data-stu-id="01b0c-354">Created controller and views</span></span>
> * <span data-ttu-id="01b0c-355">Zobrazení databáze</span><span class="sxs-lookup"><span data-stu-id="01b0c-355">Viewed the database</span></span>

<span data-ttu-id="01b0c-356">V následujícím kurzu se dozvíte jak provést základní CRUD (vytváření, čtení, aktualizace nebo odstranění) operace.</span><span class="sxs-lookup"><span data-stu-id="01b0c-356">In the following tutorial, you'll learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

<span data-ttu-id="01b0c-357">Pokračujte k dalším článku se naučíte, jak provést základní CRUD (vytváření, čtení, aktualizace nebo odstranění) operace.</span><span class="sxs-lookup"><span data-stu-id="01b0c-357">Advance to the next article to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="01b0c-358">Implementace základních funkcí CRUD</span><span class="sxs-lookup"><span data-stu-id="01b0c-358">Implement basic CRUD functionality</span></span>](crud.md)
