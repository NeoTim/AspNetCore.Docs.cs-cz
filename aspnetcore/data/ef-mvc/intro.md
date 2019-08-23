---
title: 'Kurz: Začínáme s EF Core ve webové aplikaci ASP.NET MVC'
description: Toto je první v sérii kurzů, které vysvětlují, jak vytvořit ukázkovou aplikaci Contoso University od začátku.
author: tdykstra
ms.author: riande
ms.custom: mvc
ms.date: 02/06/2019
ms.topic: tutorial
uid: data/ef-mvc/intro
ms.openlocfilehash: 3450ac5b46e2a03b5d58c8760b78a52065343992
ms.sourcegitcommit: 6189b0ced9c115248c6ede02efcd0b29d31f2115
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "69985363"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a><span data-ttu-id="e61e6-103">Kurz: Začínáme s EF Core ve webové aplikaci ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="e61e6-103">Tutorial: Get started with EF Core in an ASP.NET MVC web app</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e61e6-104">Tento kurz se neaktualizoval na ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="e61e6-104">This tutorial has **not** been updated to ASP.NET Core 3.0.</span></span> <span data-ttu-id="e61e6-105">[Verze Razor Pages](xref:data/ef-rp/intro) byla aktualizována.</span><span class="sxs-lookup"><span data-stu-id="e61e6-105">The [Razor Pages version](xref:data/ef-rp/intro) has been updated.</span></span> <span data-ttu-id="e61e6-106">Informace o tom, kdy je možné aktualizaci aktualizovat, najdete v [tomto problému GitHubu](https://github.com/aspnet/AspNetCore.Docs/issues/13920).</span><span class="sxs-lookup"><span data-stu-id="e61e6-106">For information on when this might be updated, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/13920).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="e61e6-107">Ukázková webová aplikace společnosti Contoso University ukazuje, jak vytvářet webové aplikace ASP.NET Core 2,2 MVC pomocí Entity Framework (EF) Core 2,2 a Visual Studio 2017 nebo 2019.</span><span class="sxs-lookup"><span data-stu-id="e61e6-107">The Contoso University sample web application demonstrates how to create ASP.NET Core 2.2 MVC web applications using Entity Framework (EF) Core 2.2 and Visual Studio 2017 or 2019.</span></span>

<span data-ttu-id="e61e6-108">Ukázková aplikace je web pro fiktivní univerzitě společnosti Contoso.</span><span class="sxs-lookup"><span data-stu-id="e61e6-108">The sample application is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="e61e6-109">Zahrnuje funkce, jako student přijetí, kurz vytvoření a přiřazení instruktorem.</span><span class="sxs-lookup"><span data-stu-id="e61e6-109">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="e61e6-110">Toto je první v sérii kurzů, které vysvětlují, jak vytvořit ukázkovou aplikaci Contoso University od začátku.</span><span class="sxs-lookup"><span data-stu-id="e61e6-110">This is the first in a series of tutorials that explain how to build the Contoso University sample application from scratch.</span></span>

<span data-ttu-id="e61e6-111">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="e61e6-111">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e61e6-112">Vytvoření webové aplikace ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="e61e6-112">Create an ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="e61e6-113">Nastavit styl lokality</span><span class="sxs-lookup"><span data-stu-id="e61e6-113">Set up the site style</span></span>
> * <span data-ttu-id="e61e6-114">Další informace o EF Core balíčcích NuGet</span><span class="sxs-lookup"><span data-stu-id="e61e6-114">Learn about EF Core NuGet packages</span></span>
> * <span data-ttu-id="e61e6-115">Vytvoření datového modelu</span><span class="sxs-lookup"><span data-stu-id="e61e6-115">Create the data model</span></span>
> * <span data-ttu-id="e61e6-116">Vytvoření kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="e61e6-116">Create the database context</span></span>
> * <span data-ttu-id="e61e6-117">Registrace kontextu pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="e61e6-117">Register the context for dependency injection</span></span>
> * <span data-ttu-id="e61e6-118">Inicializace databáze s testovacími daty</span><span class="sxs-lookup"><span data-stu-id="e61e6-118">Initialize the database with test data</span></span>
> * <span data-ttu-id="e61e6-119">Vytvoření kontroleru a zobrazení</span><span class="sxs-lookup"><span data-stu-id="e61e6-119">Create a controller and views</span></span>
> * <span data-ttu-id="e61e6-120">Zobrazení databáze</span><span class="sxs-lookup"><span data-stu-id="e61e6-120">View the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e61e6-121">Požadavky</span><span class="sxs-lookup"><span data-stu-id="e61e6-121">Prerequisites</span></span>

* [<span data-ttu-id="e61e6-122">.NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="e61e6-122">.NET Core SDK 2.2</span></span>](https://www.microsoft.com/net/download)
* <span data-ttu-id="e61e6-123">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s následujícími úlohami:</span><span class="sxs-lookup"><span data-stu-id="e61e6-123">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the following workloads:</span></span>
  * <span data-ttu-id="e61e6-124">**ASP.NET a webové vývojové** úlohy</span><span class="sxs-lookup"><span data-stu-id="e61e6-124">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="e61e6-125">**Vývojová úloha .NET Core pro různé platformy**</span><span class="sxs-lookup"><span data-stu-id="e61e6-125">**.NET Core cross-platform development** workload</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="e61e6-126">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="e61e6-126">Troubleshooting</span></span>

<span data-ttu-id="e61e6-127">Pokud narazíte na problém nevyřešíte sami, můžete najít řešení obvykle porovnáním kódu [dokončený projekt](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final).</span><span class="sxs-lookup"><span data-stu-id="e61e6-127">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final).</span></span> <span data-ttu-id="e61e6-128">Seznam běžných chyb a jejich řešení najdete v [části věnované řešení potíží v posledním kurzu v řadě](advanced.md#common-errors).</span><span class="sxs-lookup"><span data-stu-id="e61e6-128">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="e61e6-129">Pokud tam nenajdete, co potřebujete, můžete odeslat otázku do StackOverflow.com pro [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="e61e6-129">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="e61e6-130">Toto je série 10 kurzů, z nichž každá sestaví na tom, co se děje v předchozích kurzech.</span><span class="sxs-lookup"><span data-stu-id="e61e6-130">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="e61e6-131">Zvažte uložení kopie projektu po každém úspěšném dokončení kurzu.</span><span class="sxs-lookup"><span data-stu-id="e61e6-131">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="e61e6-132">Pak Pokud narazíte na problémy, můžete začít znovu z předchozího kurzu a nemusíte se vrátit na začátek celé řady.</span><span class="sxs-lookup"><span data-stu-id="e61e6-132">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="e61e6-133">Webová aplikace Contoso University</span><span class="sxs-lookup"><span data-stu-id="e61e6-133">Contoso University web app</span></span>

<span data-ttu-id="e61e6-134">Aplikace, kterou budete sestavovat v těchto kurzech, je jednoduchý web na univerzitě.</span><span class="sxs-lookup"><span data-stu-id="e61e6-134">The application you'll be building in these tutorials is a simple university web site.</span></span>

<span data-ttu-id="e61e6-135">Uživatelé mohou zobrazit a aktualizovat Všichni studenti, kurz a informace instruktorem.</span><span class="sxs-lookup"><span data-stu-id="e61e6-135">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="e61e6-136">Tady je několik obrazovek, které vytvoříte.</span><span class="sxs-lookup"><span data-stu-id="e61e6-136">Here are a few of the screens you'll create.</span></span>

![Studenti indexová stránka](intro/_static/students-index.png)

![Stránky pro úpravu studentů](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="e61e6-139">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="e61e6-139">Create web app</span></span>

* <span data-ttu-id="e61e6-140">Otevřít Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e61e6-140">Open Visual Studio.</span></span>

* <span data-ttu-id="e61e6-141">V nabídce **soubor** vyberte **Nový > projekt**.</span><span class="sxs-lookup"><span data-stu-id="e61e6-141">From the **File** menu, select **New > Project**.</span></span>

* <span data-ttu-id="e61e6-142">V levém podokně vyberte **nainstalované > Visual C# > Web**.</span><span class="sxs-lookup"><span data-stu-id="e61e6-142">From the left pane, select **Installed > Visual C# > Web**.</span></span>

* <span data-ttu-id="e61e6-143">Vyberte šablonu projektu **ASP.NET Core webové aplikace** .</span><span class="sxs-lookup"><span data-stu-id="e61e6-143">Select the **ASP.NET Core Web Application** project template.</span></span>

* <span data-ttu-id="e61e6-144">Jako název zadejte **ContosoUniversity** a klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="e61e6-144">Enter **ContosoUniversity** as the name and click **OK**.</span></span>

  ![Dialogové okno nového projektu](intro/_static/new-project2.png)

* <span data-ttu-id="e61e6-146">Počkejte, než se zobrazí dialogové okno **nové webové aplikace ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="e61e6-146">Wait for the **New ASP.NET Core Web Application** dialog to appear.</span></span>

* <span data-ttu-id="e61e6-147">Vyberte **.NET Core**, **ASP.NET Core 2,2** a šablonu **Webová aplikace (model-zobrazení-kontroler)** .</span><span class="sxs-lookup"><span data-stu-id="e61e6-147">Select **.NET Core**, **ASP.NET Core 2.2** and the **Web Application (Model-View-Controller)** template.</span></span>

* <span data-ttu-id="e61e6-148">Ujistěte se, že je **ověřování** nastaveno na **bez ověřování**.</span><span class="sxs-lookup"><span data-stu-id="e61e6-148">Make sure **Authentication** is set to **No Authentication**.</span></span>

* <span data-ttu-id="e61e6-149">Vybrat **OK**</span><span class="sxs-lookup"><span data-stu-id="e61e6-149">Select **OK**</span></span>

  ![Dialog nového projektu ASP.NET Core](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a><span data-ttu-id="e61e6-151">Nastavit styl lokality</span><span class="sxs-lookup"><span data-stu-id="e61e6-151">Set up the site style</span></span>

<span data-ttu-id="e61e6-152">V několika jednoduchých změnách se nastaví nabídka web, rozložení a Domovská stránka.</span><span class="sxs-lookup"><span data-stu-id="e61e6-152">A few simple changes will set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="e61e6-153">Otevřete *views/Shared/_Layout. cshtml* a proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="e61e6-153">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="e61e6-154">Změňte všechny výskyty "ContosoUniversity" na "University společnosti Contoso".</span><span class="sxs-lookup"><span data-stu-id="e61e6-154">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="e61e6-155">Existují tři výskyty.</span><span class="sxs-lookup"><span data-stu-id="e61e6-155">There are three occurrences.</span></span>

* <span data-ttu-id="e61e6-156">Přidejte položky nabídky pro, **studenty**, **kurzy**, **instruktory**a **oddělení**a odstraňte položku nabídky **osobních údajů** .</span><span class="sxs-lookup"><span data-stu-id="e61e6-156">Add menu entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="e61e6-157">Změny jsou zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="e61e6-157">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

<span data-ttu-id="e61e6-158">V *zobrazeních/Home/index. cshtml*nahraďte obsah souboru následujícím kódem, který nahradí text o ASP.NET a MVC textem o této aplikaci:</span><span class="sxs-lookup"><span data-stu-id="e61e6-158">In *Views/Home/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

<span data-ttu-id="e61e6-159">Stisknutím kombinace kláves CTRL + F5 spusťte projekt nebo zvolte možnost **ladění > spustit bez ladění** z nabídky.</span><span class="sxs-lookup"><span data-stu-id="e61e6-159">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="e61e6-160">Zobrazí se stránka domů s kartami pro stránky, které vytvoříte v těchto kurzech.</span><span class="sxs-lookup"><span data-stu-id="e61e6-160">You see the home page with tabs for the pages you'll create in these tutorials.</span></span>

![Domovská stránka společnosti Contoso University](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a><span data-ttu-id="e61e6-162">Informace o EF Core balíčků NuGet</span><span class="sxs-lookup"><span data-stu-id="e61e6-162">About EF Core NuGet packages</span></span>

<span data-ttu-id="e61e6-163">Chcete-li přidat do projektu podporu EF Core, nainstalujte poskytovatele databáze, na který chcete cílit.</span><span class="sxs-lookup"><span data-stu-id="e61e6-163">To add EF Core support to a project, install the database provider that you want to target.</span></span> <span data-ttu-id="e61e6-164">V tomto kurzu se používá SQL Server a balíček poskytovatele je [Microsoft. EntityFrameworkCore. SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="e61e6-164">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span> <span data-ttu-id="e61e6-165">Tento balíček je zahrnutý ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), takže nemusíte odkazovat na balíček.</span><span class="sxs-lookup"><span data-stu-id="e61e6-165">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to reference the package.</span></span>

<span data-ttu-id="e61e6-166">Balíček EF SQL Server a jeho závislosti (`Microsoft.EntityFrameworkCore` a `Microsoft.EntityFrameworkCore.Relational`) poskytují podporu modulu runtime pro EF.</span><span class="sxs-lookup"><span data-stu-id="e61e6-166">The EF SQL Server package and its dependencies (`Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`) provide runtime support for EF.</span></span> <span data-ttu-id="e61e6-167">Přidáte balíček nástrojů později v kurzu [migrace](migrations.md) .</span><span class="sxs-lookup"><span data-stu-id="e61e6-167">You'll add a tooling package later, in the [Migrations](migrations.md) tutorial.</span></span>

<span data-ttu-id="e61e6-168">Informace o dalších poskytovatelích databází, které jsou k dispozici pro Entity Framework Core, najdete v tématu [poskytovatelé databáze](/ef/core/providers/).</span><span class="sxs-lookup"><span data-stu-id="e61e6-168">For information about other database providers that are available for Entity Framework Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="e61e6-169">Vytvoření datového modelu</span><span class="sxs-lookup"><span data-stu-id="e61e6-169">Create the data model</span></span>

<span data-ttu-id="e61e6-170">V dalším kroku vytvoříte třídy entit pro aplikaci Contoso University.</span><span class="sxs-lookup"><span data-stu-id="e61e6-170">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="e61e6-171">Začnete s následujícími třemi entitami.</span><span class="sxs-lookup"><span data-stu-id="e61e6-171">You'll start with the following three entities.</span></span>

![Kurz – registrace – studentech modelového diagramu](intro/_static/data-model-diagram.png)

<span data-ttu-id="e61e6-173">Mezi `Student` entitami a `Enrollment` entitami existuje vztah 1: n a mezi `Course` entitami a `Enrollment` entitami je vztah 1:1.</span><span class="sxs-lookup"><span data-stu-id="e61e6-173">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="e61e6-174">Jinými slovy, student může být zaregistrovaný v jakémkoli počtu kurzů a kurz může mít zaregistrovaný libovolný počet studentů.</span><span class="sxs-lookup"><span data-stu-id="e61e6-174">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="e61e6-175">V následujících částech vytvoříte třídu pro každou z těchto entit.</span><span class="sxs-lookup"><span data-stu-id="e61e6-175">In the following sections you'll create a class for each one of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="e61e6-176">Entita studenta</span><span class="sxs-lookup"><span data-stu-id="e61e6-176">The Student entity</span></span>

![Diagram entity studenta](intro/_static/student-entity.png)

<span data-ttu-id="e61e6-178">Ve složce *modely* vytvořte soubor třídy s názvem *student.cs* a nahraďte kód šablony následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="e61e6-178">In the *Models* folder, create a class file named *Student.cs* and replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="e61e6-179">`ID` Vlastnost se změní na sloupec primárního klíče tabulky databáze, který odpovídá této třídě.</span><span class="sxs-lookup"><span data-stu-id="e61e6-179">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="e61e6-180">Ve výchozím nastavení Entity Framework interpretuje vlastnost s názvem `ID` nebo `classnameID` jako primární klíč.</span><span class="sxs-lookup"><span data-stu-id="e61e6-180">By default, the Entity Framework interprets a property that's named `ID` or `classnameID` as the primary key.</span></span>

<span data-ttu-id="e61e6-181">`Enrollments` Je vlastnost [navigační vlastnost](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="e61e6-181">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="e61e6-182">Navigační vlastnosti obsahují další entity, které se vztahují k této entitě.</span><span class="sxs-lookup"><span data-stu-id="e61e6-182">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="e61e6-183">V `Enrollments` takovém případě `Student entity` `Student` bude vlastnost objektu obsahovat všechny entity,kterésevztahujíkdanéentitě.`Enrollment`</span><span class="sxs-lookup"><span data-stu-id="e61e6-183">In this case, the `Enrollments` property of a `Student entity` will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="e61e6-184">Jinými slovy, pokud daný řádek studenta v databázi obsahuje dva související řádky zápisu (řádky, které obsahují hodnotu primárního klíče tohoto studenta ve sloupci StudentID cizí klíč), vlastnost `Student` `Enrollments` navigace této entity bude obsahovat. dvě `Enrollment` entity.</span><span class="sxs-lookup"><span data-stu-id="e61e6-184">In other words, if a given Student row in the database has two related Enrollment rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="e61e6-185">Pokud navigační vlastnost může obsahovat více entit (jako v relacích m:n nebo 1:1), musí se jednat o seznam, ve kterém lze přidávat, odstraňovat a aktualizovat položky, jako `ICollection<T>`je například.</span><span class="sxs-lookup"><span data-stu-id="e61e6-185">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection<T>`.</span></span> <span data-ttu-id="e61e6-186">Můžete zadat `ICollection<T>` nebo typ `List<T>` jako nebo `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="e61e6-186">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="e61e6-187">Pokud zadáte `ICollection<T>`, EF `HashSet<T>` vytvoří ve výchozím nastavení kolekci.</span><span class="sxs-lookup"><span data-stu-id="e61e6-187">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="e61e6-188">Registrace entity</span><span class="sxs-lookup"><span data-stu-id="e61e6-188">The Enrollment entity</span></span>

![Diagram entity registrace](intro/_static/enrollment-entity.png)

<span data-ttu-id="e61e6-190">Ve složce *modely* vytvořte *Enrollment.cs* a nahraďte existující kód následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e61e6-190">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="e61e6-191">Vlastnost bude primární klíč. Tato entita `classnameID` používá vzor místo toho `ID` , jak jste viděli v `Student` entitě. `EnrollmentID`</span><span class="sxs-lookup"><span data-stu-id="e61e6-191">The `EnrollmentID` property will be the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="e61e6-192">Obvykle byste zvolili jeden model a používali ho v rámci svého datového modelu.</span><span class="sxs-lookup"><span data-stu-id="e61e6-192">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="e61e6-193">V tomto příkladu variace znázorňuje, že můžete použít libovolný vzor.</span><span class="sxs-lookup"><span data-stu-id="e61e6-193">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="e61e6-194">V [pozdějším kurzu](inheritance.md)uvidíte, jak použít ID bez ClassName, usnadňuje implementaci dědičnosti v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="e61e6-194">In a [later tutorial](inheritance.md), you'll see how using ID without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="e61e6-195">`Grade` Vlastnost je `enum`.</span><span class="sxs-lookup"><span data-stu-id="e61e6-195">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="e61e6-196">Otazník po `Grade` deklarace typu znamená, že `Grade` vlastnost může mít hodnotu Null.</span><span class="sxs-lookup"><span data-stu-id="e61e6-196">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="e61e6-197">Na podnikové úrovni, který má hodnotu null se liší od nulové třída – null znamená, že známku vyjádřenou není znám nebo ještě nebyly přiřazeny.</span><span class="sxs-lookup"><span data-stu-id="e61e6-197">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="e61e6-198">`StudentID` Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Student`.</span><span class="sxs-lookup"><span data-stu-id="e61e6-198">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="e61e6-199">`Student` `Student.Enrollments` `Enrollment` Entita je přidružená k jedné `Student` entitě, takže vlastnost může uchovávat jenom jednu entitu (na rozdíl od vlastnosti navigace, kterou jste viděli dříve, která může obsahovat víc entit). `Enrollment`</span><span class="sxs-lookup"><span data-stu-id="e61e6-199">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="e61e6-200">`CourseID` Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Course`.</span><span class="sxs-lookup"><span data-stu-id="e61e6-200">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="e61e6-201">`Enrollment` Entita je přidružený nejméně k jednomu `Course` entity.</span><span class="sxs-lookup"><span data-stu-id="e61e6-201">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="e61e6-202">Entity Framework interpretuje vlastnost jako vlastnost cizího klíče, pokud má název `<navigation property name><primary key property name>` (například pro `Student` vlastnost navigace `StudentID` , protože `Student` primární klíč entity je `ID`).</span><span class="sxs-lookup"><span data-stu-id="e61e6-202">Entity Framework interprets a property as a foreign key property if it's named `<navigation property name><primary key property name>` (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="e61e6-203">Vlastnosti cizího klíče lze také pojmenovat `<primary key property name>` jednoduše (například vzhledem `CourseID` `Course` k tomu, že primární klíč entity `CourseID`je).</span><span class="sxs-lookup"><span data-stu-id="e61e6-203">Foreign key properties can also be named simply `<primary key property name>` (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="e61e6-204">Kurz entity</span><span class="sxs-lookup"><span data-stu-id="e61e6-204">The Course entity</span></span>

![Diagram kurzu entity](intro/_static/course-entity.png)

<span data-ttu-id="e61e6-206">Ve složce *modely* vytvořte *Course.cs* a nahraďte existující kód následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e61e6-206">In the *Models* folder, create *Course.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="e61e6-207">`Enrollments` Je navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="e61e6-207">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="e61e6-208">A `Course` entit může souviset s libovolným počtem `Enrollment` entity.</span><span class="sxs-lookup"><span data-stu-id="e61e6-208">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="e61e6-209">V [pozdějším kurzu](complex-data-model.md) v této `DatabaseGenerated` sérii zaznamenáme Další informace o atributu.</span><span class="sxs-lookup"><span data-stu-id="e61e6-209">We'll say more about the `DatabaseGenerated` attribute in a [later tutorial](complex-data-model.md) in this series.</span></span> <span data-ttu-id="e61e6-210">V podstatě vám tento atribut umožňuje zadat primární klíč pro kurz místo toho, aby ho databáze vygenerovala.</span><span class="sxs-lookup"><span data-stu-id="e61e6-210">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="e61e6-211">Vytvoření kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="e61e6-211">Create the database context</span></span>

<span data-ttu-id="e61e6-212">Hlavní třída, která koordinuje funkce Entity Framework pro daný datový model, je třída kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="e61e6-212">The main class that coordinates Entity Framework functionality for a given data model is the database context class.</span></span> <span data-ttu-id="e61e6-213">Tuto třídu vytvoříte odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="e61e6-213">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="e61e6-214">Ve vašem kódu určíte, které entity budou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="e61e6-214">In your code you specify which entities are included in the data model.</span></span> <span data-ttu-id="e61e6-215">Můžete také přizpůsobit určité chování Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="e61e6-215">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="e61e6-216">V tomto projektu je s názvem třídy `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="e61e6-216">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="e61e6-217">Ve složce projektu vytvořte složku s názvem *data*.</span><span class="sxs-lookup"><span data-stu-id="e61e6-217">In the project folder, create a folder named *Data*.</span></span>

<span data-ttu-id="e61e6-218">Ve složce *data* vytvořte nový soubor třídy s názvem *SchoolContext.cs*a nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e61e6-218">In the *Data* folder create a new class file named *SchoolContext.cs*, and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="e61e6-219">Tento kód vytvoří `DbSet` vlastnost pro každou sadu entit.</span><span class="sxs-lookup"><span data-stu-id="e61e6-219">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="e61e6-220">V Entity Framework terminologii sada entit obvykle odpovídá tabulce databáze a entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="e61e6-220">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e61e6-221">Mohli jste vynechat `DbSet<Enrollment>` příkazy a `DbSet<Course>` a to by fungovalo stejně.</span><span class="sxs-lookup"><span data-stu-id="e61e6-221">You could've omitted the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="e61e6-222">Entity Framework by je implicitně zahrnuly, protože `Student` entita `Enrollment` odkazuje na entitu `Course` a `Enrollment` odkazuje na entitu.</span><span class="sxs-lookup"><span data-stu-id="e61e6-222">The Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="e61e6-223">Při vytvoření databáze EF vytvoří tabulky, které mají názvy stejné jako `DbSet` názvy vlastností.</span><span class="sxs-lookup"><span data-stu-id="e61e6-223">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="e61e6-224">Názvy vlastností pro kolekce jsou obvykle plural (Students spíše než student), ale vývojáři nesouhlasí, zda by měly být názvy tabulek v množném čísle.</span><span class="sxs-lookup"><span data-stu-id="e61e6-224">Property names for collections are typically plural (Students rather than Student), but developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="e61e6-225">V těchto kurzech potlačíte výchozí chování zadáním názvů tabulek na jednotném čísle v DbContext.</span><span class="sxs-lookup"><span data-stu-id="e61e6-225">For these tutorials you'll override the default behavior by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="e61e6-226">K tomu přidejte následující zvýrazněný kód za poslední vlastnost Negenerickými.</span><span class="sxs-lookup"><span data-stu-id="e61e6-226">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a><span data-ttu-id="e61e6-227">Zaregistrovat SchoolContext</span><span class="sxs-lookup"><span data-stu-id="e61e6-227">Register the SchoolContext</span></span>

<span data-ttu-id="e61e6-228">ASP.NET Core implementuje [vkládání závislostí](../../fundamentals/dependency-injection.md) ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="e61e6-228">ASP.NET Core implements [dependency injection](../../fundamentals/dependency-injection.md) by default.</span></span> <span data-ttu-id="e61e6-229">Služby (jako kontext databáze EF) jsou registrovány pomocí injektáže závislosti během spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="e61e6-229">Services (such as the EF database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e61e6-230">Komponenty, které vyžadují tyto služby (například řadiče MVC), poskytují tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="e61e6-230">Components that require these services (such as MVC controllers) are provided these services via constructor parameters.</span></span> <span data-ttu-id="e61e6-231">V tomto kurzu se zobrazí kód konstruktoru kontroleru, který získá instanci kontextu později.</span><span class="sxs-lookup"><span data-stu-id="e61e6-231">You'll see the controller constructor code that gets a context instance later in this tutorial.</span></span>

<span data-ttu-id="e61e6-232">Pokud se `SchoolContext` chcete zaregistrovat jako služba, otevřete *Startup.cs*a přidejte `ConfigureServices` zvýrazněné řádky do metody.</span><span class="sxs-lookup"><span data-stu-id="e61e6-232">To register `SchoolContext` as a service, open *Startup.cs*, and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

<span data-ttu-id="e61e6-233">Název připojovacího řetězce je předán do kontextu voláním metody pro `DbContextOptionsBuilder` objekt.</span><span class="sxs-lookup"><span data-stu-id="e61e6-233">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="e61e6-234">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="e61e6-234">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="e61e6-235">Přidejte `using` příkazy pro `ContosoUniversity.Data` a `Microsoft.EntityFrameworkCore` obory názvů a potom Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="e61e6-235">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces, and then build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="e61e6-236">Otevřete soubor *appSettings. JSON* a přidejte připojovací řetězec, jak je znázorněno v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="e61e6-236">Open the *appsettings.json* file and add a connection string as shown in the following example.</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a><span data-ttu-id="e61e6-237">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="e61e6-237">SQL Server Express LocalDB</span></span>

<span data-ttu-id="e61e6-238">Připojovací řetězec určuje SQL Server databázi LocalDB.</span><span class="sxs-lookup"><span data-stu-id="e61e6-238">The connection string specifies a SQL Server LocalDB database.</span></span> <span data-ttu-id="e61e6-239">LocalDB je zjednodušená verze databázového stroje SQL Server Express a je určena pro vývoj aplikací, nikoli za použití v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="e61e6-239">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for application development, not production use.</span></span> <span data-ttu-id="e61e6-240">LocalDB spustí na vyžádání a běží v uživatelském režimu, takže není bez složité konfigurace.</span><span class="sxs-lookup"><span data-stu-id="e61e6-240">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="e61e6-241">Ve výchozím nastavení LocalDB vytvoří soubory databáze *. mdf* v `C:/Users/<user>` adresáři.</span><span class="sxs-lookup"><span data-stu-id="e61e6-241">By default, LocalDB creates *.mdf* database files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="e61e6-242">Inicializovat databázi s testovacími daty</span><span class="sxs-lookup"><span data-stu-id="e61e6-242">Initialize DB with test data</span></span>

<span data-ttu-id="e61e6-243">Entity Framework pro vás vytvoří prázdnou databázi.</span><span class="sxs-lookup"><span data-stu-id="e61e6-243">The Entity Framework will create an empty database for you.</span></span> <span data-ttu-id="e61e6-244">V této části napíšete metodu, která je volána po vytvoření databáze, aby se naplnila testovacími daty.</span><span class="sxs-lookup"><span data-stu-id="e61e6-244">In this section, you write a method that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="e61e6-245">Tady použijete `EnsureCreated` metodu k automatickému vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="e61e6-245">Here you'll use the `EnsureCreated` method to automatically create the database.</span></span> <span data-ttu-id="e61e6-246">V [pozdějším kurzu](migrations.md) se dozvíte, jak zpracovat změny modelu pomocí migrace Code First ke změně schématu databáze místo vyřazení a opětovnému vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="e61e6-246">In a [later tutorial](migrations.md) you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="e61e6-247">Ve složce *data* vytvořte nový soubor třídy s názvem *DbInitializer.cs* a nahraďte kód šablony následujícím kódem, což způsobí, že se databáze vytvoří v případě potřeby a načte testovací data do nové databáze.</span><span class="sxs-lookup"><span data-stu-id="e61e6-247">In the *Data* folder, create a new class file named *DbInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="e61e6-248">Kód kontroluje, zda jsou v databázi nějaké studenty, a pokud ne, předpokládá se, že je databáze nová a je nutné ji naplnit testovacími daty.</span><span class="sxs-lookup"><span data-stu-id="e61e6-248">The code checks if there are any students in the database, and if not, it assumes the database is new and needs to be seeded with test data.</span></span> <span data-ttu-id="e61e6-249">Načte testovací data do pole spíše než `List<T>` kolekce za účelem optimalizace výkonu.</span><span class="sxs-lookup"><span data-stu-id="e61e6-249">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="e61e6-250">V *program.cs*upravte `Main` metodu tak, aby při spuštění aplikace provedete následující:</span><span class="sxs-lookup"><span data-stu-id="e61e6-250">In *Program.cs*, modify the `Main` method to do the following on application startup:</span></span>

* <span data-ttu-id="e61e6-251">Získá instanci kontextu databáze z kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="e61e6-251">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="e61e6-252">Zavolejte metodu počáteční hodnoty a předejte jí kontext.</span><span class="sxs-lookup"><span data-stu-id="e61e6-252">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="e61e6-253">Uvolněte kontext, pokud je metoda počáteční hodnoty hotová.</span><span class="sxs-lookup"><span data-stu-id="e61e6-253">Dispose the context when the seed method is done.</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

<span data-ttu-id="e61e6-254">Přidat `using` příkazy:</span><span class="sxs-lookup"><span data-stu-id="e61e6-254">Add `using` statements:</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Usings)]

<span data-ttu-id="e61e6-255">Ve starších kurzech se můžete podívat na podobný kód v `Configure` metodě v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="e61e6-255">In older tutorials, you may see similar code in the `Configure` method in *Startup.cs*.</span></span> <span data-ttu-id="e61e6-256">Tuto `Configure` metodu doporučujeme použít jenom k nastavení kanálu požadavků.</span><span class="sxs-lookup"><span data-stu-id="e61e6-256">We recommend that you use the `Configure` method only to set up the request pipeline.</span></span> <span data-ttu-id="e61e6-257">Spouštěcí kód aplikace patří do `Main` metody.</span><span class="sxs-lookup"><span data-stu-id="e61e6-257">Application startup code belongs in the `Main` method.</span></span>

<span data-ttu-id="e61e6-258">Nyní při prvním spuštění aplikace se vytvoří databáze a dokončí se testovacími daty.</span><span class="sxs-lookup"><span data-stu-id="e61e6-258">Now the first time you run the application, the database will be created and seeded with test data.</span></span> <span data-ttu-id="e61e6-259">Kdykoli změníte datový model, můžete databázi odstranit, aktualizovat metodu počáteční hodnoty a začít afresh s novou databází stejným způsobem.</span><span class="sxs-lookup"><span data-stu-id="e61e6-259">Whenever you change your data model, you can delete the database, update your seed method, and start afresh with a new database the same way.</span></span> <span data-ttu-id="e61e6-260">V dalších kurzech se dozvíte, jak změnit databázi při změně datového modelu, aniž byste ho museli odstranit a znovu vytvořit.</span><span class="sxs-lookup"><span data-stu-id="e61e6-260">In later tutorials, you'll see how to modify the database when the data model changes, without deleting and re-creating it.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="e61e6-261">Vytvořit kontroler a zobrazení</span><span class="sxs-lookup"><span data-stu-id="e61e6-261">Create controller and views</span></span>

<span data-ttu-id="e61e6-262">V dalším kroku použijete modul generování uživatelského rozhraní v aplikaci Visual Studio a přidáte kontroler MVC a zobrazení, které budou používat EF k dotazování a ukládání dat.</span><span class="sxs-lookup"><span data-stu-id="e61e6-262">Next, you'll use the scaffolding engine in Visual Studio to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="e61e6-263">Automatické vytváření metod a zobrazení akcí CRUD se označuje jako generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e61e6-263">The automatic creation of CRUD action methods and views is known as scaffolding.</span></span> <span data-ttu-id="e61e6-264">Generování uživatelského rozhraní se liší od generování kódu v tom, že generovaný kód je výchozím bodem, který lze upravit tak, aby vyhovoval vašim vlastním požadavkům, zatímco obvykle neupravujete generovaný kód.</span><span class="sxs-lookup"><span data-stu-id="e61e6-264">Scaffolding differs from code generation in that the scaffolded code is a starting point that you can modify to suit your own requirements, whereas you typically don't modify generated code.</span></span> <span data-ttu-id="e61e6-265">Pokud potřebujete přizpůsobit generovaný kód, použijete částečné třídy nebo znovu vygenerujete kód při změně.</span><span class="sxs-lookup"><span data-stu-id="e61e6-265">When you need to customize generated code, you use partial classes or you regenerate the code when things change.</span></span>

* <span data-ttu-id="e61e6-266">V **Průzkumník řešení** klikněte pravým tlačítkem na složku **řadiče** a vyberte **Přidat > Nová vygenerovaná položka**.</span><span class="sxs-lookup"><span data-stu-id="e61e6-266">Right-click the **Controllers** folder in **Solution Explorer** and select **Add > New Scaffolded Item**.</span></span>

* <span data-ttu-id="e61e6-267">V dialogovém okně **Přidat generování uživatelského rozhraní** :</span><span class="sxs-lookup"><span data-stu-id="e61e6-267">In the **Add Scaffold** dialog box:</span></span>

  * <span data-ttu-id="e61e6-268">**Pomocí Entity Framework vyberte kontroler MVC se zobrazeními**.</span><span class="sxs-lookup"><span data-stu-id="e61e6-268">Select **MVC controller with views, using Entity Framework**.</span></span>

  * <span data-ttu-id="e61e6-269">Klikněte na **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e61e6-269">Click **Add**.</span></span> <span data-ttu-id="e61e6-270">Zobrazí se dialogové okno **Přidat kontroler MVC se Entity Framework zobrazeními** .</span><span class="sxs-lookup"><span data-stu-id="e61e6-270">The **Add MVC Controller with views, using Entity Framework** dialog box appears.</span></span>

    ![Student pro generování uživatelského rozhraní](intro/_static/scaffold-student2.png)

  * <span data-ttu-id="e61e6-272">V **třídě model** vyberte **student**.</span><span class="sxs-lookup"><span data-stu-id="e61e6-272">In **Model class** select **Student**.</span></span>

  * <span data-ttu-id="e61e6-273">V oblasti **datový kontext** vyberte **SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="e61e6-273">In **Data context class** select **SchoolContext**.</span></span>

  * <span data-ttu-id="e61e6-274">Přijměte výchozí **StudentsController** jako název.</span><span class="sxs-lookup"><span data-stu-id="e61e6-274">Accept the default **StudentsController** as the name.</span></span>

  * <span data-ttu-id="e61e6-275">Klikněte na **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e61e6-275">Click **Add**.</span></span>

  <span data-ttu-id="e61e6-276">Když kliknete na tlačítko **Přidat**, modul generování uživatelského rozhraní sady Visual Studio vytvoří soubor *StudentsController.cs* a sadu zobrazení (soubory *. cshtml* ), které pracují s řadičem.</span><span class="sxs-lookup"><span data-stu-id="e61e6-276">When you click **Add**, the Visual Studio scaffolding engine creates a *StudentsController.cs* file and a set of views (*.cshtml* files) that work with the controller.</span></span>

<span data-ttu-id="e61e6-277">(Modul generování uživatelského rozhraní může také vytvořit kontext databáze za vás, pokud ho nevytvoříte ručně, jako jste předtím v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="e61e6-277">(The scaffolding engine can also create the database context for you if you don't create it manually first as you did earlier for this tutorial.</span></span> <span data-ttu-id="e61e6-278">Novou třídu kontextu můžete zadat v poli **Přidat kontrolér** kliknutím na znaménko plus napravo od **třídy Context data**.</span><span class="sxs-lookup"><span data-stu-id="e61e6-278">You can specify a new context class in the **Add Controller** box by clicking the plus sign to the right of **Data context class**.</span></span>  <span data-ttu-id="e61e6-279">Visual Studio potom vytvoří `DbContext` třídu a také kontroler a zobrazení.)</span><span class="sxs-lookup"><span data-stu-id="e61e6-279">Visual Studio will then create your `DbContext` class as well as the controller and views.)</span></span>

<span data-ttu-id="e61e6-280">Všimnete si, že kontroler přebírá `SchoolContext` jako parametr konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="e61e6-280">You'll notice that the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="e61e6-281">Vložení závislostí ASP.NET Core se postará o předání `SchoolContext` instance do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e61e6-281">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="e61e6-282">V souboru *Startup.cs* jste předtím nakonfigurovali.</span><span class="sxs-lookup"><span data-stu-id="e61e6-282">You configured that in the *Startup.cs* file earlier.</span></span>

<span data-ttu-id="e61e6-283">Kontroler obsahuje `Index` metodu akce, která zobrazí všechny studenty v databázi.</span><span class="sxs-lookup"><span data-stu-id="e61e6-283">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="e61e6-284">Metoda získá seznam studentů ze sady entit studentů načtením `Students` vlastnosti instance kontextu databáze:</span><span class="sxs-lookup"><span data-stu-id="e61e6-284">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="e61e6-285">O asynchronních programovacích prvcích v tomto kódu se dozvíte později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="e61e6-285">You'll learn about the asynchronous programming elements in this code later in the tutorial.</span></span>

<span data-ttu-id="e61e6-286">Zobrazení */studenty/index. cshtml* zobrazí tento seznam v tabulce:</span><span class="sxs-lookup"><span data-stu-id="e61e6-286">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="e61e6-287">Stisknutím kombinace kláves CTRL + F5 spusťte projekt nebo zvolte možnost **ladění > spustit bez ladění** z nabídky.</span><span class="sxs-lookup"><span data-stu-id="e61e6-287">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="e61e6-288">Kliknutím na kartu Students zobrazíte testovací data, která `DbInitializer.Initialize` metoda vložila.</span><span class="sxs-lookup"><span data-stu-id="e61e6-288">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="e61e6-289">V závislosti na tom, jak úzká je okno prohlížeče, uvidíte `Students` v horní části stránky odkaz na kartu nebo v pravém horním rohu klikněte na navigační ikonu, aby se odkaz zobrazil.</span><span class="sxs-lookup"><span data-stu-id="e61e6-289">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Úzká Domovská stránka společnosti Contoso University](intro/_static/home-page-narrow.png)

![Studenti indexová stránka](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="e61e6-292">Zobrazení databáze</span><span class="sxs-lookup"><span data-stu-id="e61e6-292">View the database</span></span>

<span data-ttu-id="e61e6-293">Při spuštění aplikace `DbInitializer.Initialize` je volána `EnsureCreated`metoda.</span><span class="sxs-lookup"><span data-stu-id="e61e6-293">When you started the application, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="e61e6-294">EF zjistil, že neexistovala žádná databáze a proto byla vytvořena, a potom zbytek `Initialize` kódu metody naplní databázi daty.</span><span class="sxs-lookup"><span data-stu-id="e61e6-294">EF saw that there was no database and so it created one, then the remainder of the `Initialize` method code populated the database with data.</span></span> <span data-ttu-id="e61e6-295">K zobrazení databáze v aplikaci Visual Studio můžete použít **Průzkumník objektů systému SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="e61e6-295">You can use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span>

<span data-ttu-id="e61e6-296">Zavřete prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="e61e6-296">Close the browser.</span></span>

<span data-ttu-id="e61e6-297">Pokud okno SSOX ještě není otevřené, vyberte ho v nabídce **zobrazení** v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e61e6-297">If the SSOX window isn't already open, select it from the **View** menu in Visual Studio.</span></span>

<span data-ttu-id="e61e6-298">V SSOX klikněte na **(LocalDB) \MSSQLLocalDB > databáze**a potom klikněte na položku pro název databáze, která se nachází v připojovacím řetězci v souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="e61e6-298">In SSOX, click **(localdb)\MSSQLLocalDB > Databases**, and then click the entry for the database name that's in the connection string in your *appsettings.json* file.</span></span>

<span data-ttu-id="e61e6-299">Rozbalte uzel **tabulky** , aby se zobrazily tabulky v databázi.</span><span class="sxs-lookup"><span data-stu-id="e61e6-299">Expand the **Tables** node to see the tables in your database.</span></span>

![Tabulky v SSOX](intro/_static/ssox-tables.png)

<span data-ttu-id="e61e6-301">Klikněte pravým tlačítkem myši na tabulku **student** a kliknutím na **Zobrazit data** Zobrazte sloupce, které byly vytvořeny, a řádky, které byly vloženy do tabulky.</span><span class="sxs-lookup"><span data-stu-id="e61e6-301">Right-click the **Student** table and click **View Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

![Tabulka studenta v SSOX](intro/_static/ssox-student-table.png)

<span data-ttu-id="e61e6-303">Soubory databáze *. mdf* a *. ldf* jsou ve složce *\\C:\Users\<uživatelské_jméno >* .</span><span class="sxs-lookup"><span data-stu-id="e61e6-303">The *.mdf* and *.ldf* database files are in the *C:\Users\\\<yourusername>* folder.</span></span>

<span data-ttu-id="e61e6-304">`EnsureCreated` Vzhledem`Student` k tomu, že zavoláte metodu inicializátoru, která se spouští při spuštění aplikace, můžete nyní provést změnu třídy, odstranit databázi, znovu spustit aplikaci a databáze se automaticky znovu vytvoří, aby odpovídala vaší změně.</span><span class="sxs-lookup"><span data-stu-id="e61e6-304">Because you're calling `EnsureCreated` in the initializer method that runs on app start, you could now make a change to the `Student` class, delete the database, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="e61e6-305">Například pokud přidáte `EmailAddress` vlastnost `Student` do třídy, zobrazí se nový `EmailAddress` sloupec v znovu vytvořené tabulce.</span><span class="sxs-lookup"><span data-stu-id="e61e6-305">For example, if you add an `EmailAddress` property to the `Student` class, you'll see a new `EmailAddress` column in the re-created table.</span></span>

## <a name="conventions"></a><span data-ttu-id="e61e6-306">Konvence</span><span class="sxs-lookup"><span data-stu-id="e61e6-306">Conventions</span></span>

<span data-ttu-id="e61e6-307">Množství kódu, který jste museli zapsat, aby Entity Framework mohl vytvořit úplnou databázi, je minimální z důvodu použití konvencí nebo předpokladů, které Entity Framework provádí.</span><span class="sxs-lookup"><span data-stu-id="e61e6-307">The amount of code you had to write in order for the Entity Framework to be able to create a complete database for you is minimal because of the use of conventions, or assumptions that the Entity Framework makes.</span></span>

* <span data-ttu-id="e61e6-308">Názvy `DbSet` vlastností se používají jako názvy tabulek.</span><span class="sxs-lookup"><span data-stu-id="e61e6-308">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="e61e6-309">Pro entity, na které není `DbSet` odkazováno pomocí vlastnosti, se názvy tříd entit používají jako názvy tabulek.</span><span class="sxs-lookup"><span data-stu-id="e61e6-309">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>

* <span data-ttu-id="e61e6-310">Názvy vlastností entit se používají pro názvy sloupců.</span><span class="sxs-lookup"><span data-stu-id="e61e6-310">Entity property names are used for column names.</span></span>

* <span data-ttu-id="e61e6-311">Vlastnosti entity s názvem ID nebo classnameID jsou rozpoznány jako vlastnosti primárního klíče.</span><span class="sxs-lookup"><span data-stu-id="e61e6-311">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>

* <span data-ttu-id="e61e6-312">Vlastnost je interpretována jako vlastnost cizího klíče, pokud se `StudentID` nazývá  *\<název navigační vlastnosti >\<název vlastnosti primárního klíče >* (například pro `Student` vlastnost navigace od `Student`primární klíč entity je `ID`).</span><span class="sxs-lookup"><span data-stu-id="e61e6-312">A property is interpreted as a foreign key property if it's named *\<navigation property name>\<primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="e61e6-313">Vlastnosti cizího klíče mohou mít také  *\<název jednoduché vlastnosti primárního klíče >* ( `Enrollment` například vzhledem k `EnrollmentID` tomu, že primární klíč entity je `EnrollmentID`).</span><span class="sxs-lookup"><span data-stu-id="e61e6-313">Foreign key properties can also be named simply *\<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="e61e6-314">Konvenční chování se dá přepsat.</span><span class="sxs-lookup"><span data-stu-id="e61e6-314">Conventional behavior can be overridden.</span></span> <span data-ttu-id="e61e6-315">Můžete například explicitně zadat názvy tabulek, jak jste viděli dříve v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="e61e6-315">For example, you can explicitly specify table names, as you saw earlier in this tutorial.</span></span> <span data-ttu-id="e61e6-316">A můžete nastavit názvy sloupců a nastavit jakoukoli vlastnost jako primární klíč nebo cizí klíč, protože se v [pozdějším kurzu](complex-data-model.md) v této sérii zobrazí.</span><span class="sxs-lookup"><span data-stu-id="e61e6-316">And you can set column names and set any property as primary key or foreign key, as you'll see in a [later tutorial](complex-data-model.md) in this series.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="e61e6-317">Asynchronní kód</span><span class="sxs-lookup"><span data-stu-id="e61e6-317">Asynchronous code</span></span>

<span data-ttu-id="e61e6-318">Asynchronní programování je výchozím režimem pro ASP.NET Core a EF Core.</span><span class="sxs-lookup"><span data-stu-id="e61e6-318">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="e61e6-319">Webový server má omezený počet vláken, které jsou k dispozici, a v situacích, vysokého zatížení všech dostupných vláken může být používán.</span><span class="sxs-lookup"><span data-stu-id="e61e6-319">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="e61e6-320">Pokud k tomu dojde, server nemůže zpracovat nové žádosti, dokud se uvolnit vlákna.</span><span class="sxs-lookup"><span data-stu-id="e61e6-320">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="e61e6-321">Přestože se nejedná skutečně každé dílo vzhledem k tomu, že čekání na vstupně-výstupních operací na dokončení, může kódem synchronní svázané několika vlákny.</span><span class="sxs-lookup"><span data-stu-id="e61e6-321">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="e61e6-322">Asynchronní kód když proces čeká na vstupně-výstupních operací na dokončení, je jeho vlákno uvolněn pro server určený pro zpracováním jiných požadavků.</span><span class="sxs-lookup"><span data-stu-id="e61e6-322">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="e61e6-323">V důsledku toho asynchronního kódu umožňuje prostředky serveru použije efektivněji a aby zvládla větší provoz bez zpoždění je povoleno na serveru.</span><span class="sxs-lookup"><span data-stu-id="e61e6-323">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="e61e6-324">Asynchronní kód zavádí malé množství režie za běhu, ale u situací s nízkým objemem provozu je dosaženo zanedbatelného výkonu, zatímco v případě vysoké situace v provozu je potenciální zlepšení výkonu značné.</span><span class="sxs-lookup"><span data-stu-id="e61e6-324">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="e61e6-325">V následujícím kódu `async` klíčové slovo, `Task<T>` vrácené hodnoty, `await` klíčové slovo a `ToListAsync` metoda provede asynchronní spouštění kódu.</span><span class="sxs-lookup"><span data-stu-id="e61e6-325">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="e61e6-326">Klíčové slovo instruuje kompilátor, aby vygeneroval zpětná volání pro části těla metody a automaticky `Task<IActionResult>` vytvořila vrácený objekt. `async`</span><span class="sxs-lookup"><span data-stu-id="e61e6-326">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>

* <span data-ttu-id="e61e6-327">Návratový typ `Task<IActionResult>` představuje průběžnou práci s výsledkem typu `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="e61e6-327">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>

* <span data-ttu-id="e61e6-328">`await` – Klíčové slovo způsobí, že kompilátor metodu rozdělit do dvou částí.</span><span class="sxs-lookup"><span data-stu-id="e61e6-328">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="e61e6-329">První část končí operace, která se spustí asynchronně.</span><span class="sxs-lookup"><span data-stu-id="e61e6-329">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="e61e6-330">Druhá část je nepoužili metodu zpětného volání, která je volána po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="e61e6-330">The second part is put into a callback method that's called when the operation completes.</span></span>

* <span data-ttu-id="e61e6-331">`ToListAsync` je asynchronní verze `ToList` – metoda rozšíření.</span><span class="sxs-lookup"><span data-stu-id="e61e6-331">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="e61e6-332">Některé věci, které je potřeba znát při psaní asynchronního kódu, který používá Entity Framework:</span><span class="sxs-lookup"><span data-stu-id="e61e6-332">Some things to be aware of when you are writing asynchronous code that uses the Entity Framework:</span></span>

* <span data-ttu-id="e61e6-333">Asynchronně jsou spouštěny pouze příkazy, které způsobují odeslání dotazů nebo příkazů do databáze.</span><span class="sxs-lookup"><span data-stu-id="e61e6-333">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="e61e6-334">Který obsahuje `ToListAsync` `SingleOrDefaultAsync`například,, a `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="e61e6-334">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="e61e6-335">Neobsahuje například příkazy, které pouze mění `IQueryable`, `var students = context.Students.Where(s => s.LastName == "Davolio")`například.</span><span class="sxs-lookup"><span data-stu-id="e61e6-335">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>

* <span data-ttu-id="e61e6-336">Kontext EF není bezpečný pro přístup z více vláken: Nepokoušejte se souběžně provádět více operací.</span><span class="sxs-lookup"><span data-stu-id="e61e6-336">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="e61e6-337">Při volání jakékoli asynchronní metody EF vždy použijte `await` klíčové slovo.</span><span class="sxs-lookup"><span data-stu-id="e61e6-337">When you call any async EF method, always use the `await` keyword.</span></span>

* <span data-ttu-id="e61e6-338">Chcete-li využít výhod výkonu asynchronního kódu, zajistěte, aby všechny balíčky knihovny, které používáte (například pro stránkování), používaly také Async, pokud volají jakékoli Entity Framework metody, které způsobují odesílání dotazů do databáze.</span><span class="sxs-lookup"><span data-stu-id="e61e6-338">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="e61e6-339">Další informace o asynchronním programování v rozhraní .NET naleznete v tématu [Async Overview](/dotnet/articles/standard/async).</span><span class="sxs-lookup"><span data-stu-id="e61e6-339">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="e61e6-340">Získat kód</span><span class="sxs-lookup"><span data-stu-id="e61e6-340">Get the code</span></span>

[<span data-ttu-id="e61e6-341">Stažení nebo zobrazení dokončené aplikace.</span><span class="sxs-lookup"><span data-stu-id="e61e6-341">Download or view the completed application.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="e61e6-342">Další kroky</span><span class="sxs-lookup"><span data-stu-id="e61e6-342">Next steps</span></span>

<span data-ttu-id="e61e6-343">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="e61e6-343">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e61e6-344">Vytvořená webová aplikace ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="e61e6-344">Created ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="e61e6-345">Nastavit styl lokality</span><span class="sxs-lookup"><span data-stu-id="e61e6-345">Set up the site style</span></span>
> * <span data-ttu-id="e61e6-346">Seznámili jste se s EF Core balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="e61e6-346">Learned about EF Core NuGet packages</span></span>
> * <span data-ttu-id="e61e6-347">Vytvoření datového modelu</span><span class="sxs-lookup"><span data-stu-id="e61e6-347">Created the data model</span></span>
> * <span data-ttu-id="e61e6-348">Byl vytvořen kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="e61e6-348">Created the database context</span></span>
> * <span data-ttu-id="e61e6-349">Zaregistrováno rozhraní SchoolContext</span><span class="sxs-lookup"><span data-stu-id="e61e6-349">Registered the SchoolContext</span></span>
> * <span data-ttu-id="e61e6-350">Inicializovaná databáze s testovacími daty</span><span class="sxs-lookup"><span data-stu-id="e61e6-350">Initialized DB with test data</span></span>
> * <span data-ttu-id="e61e6-351">Vytvořen kontroler a zobrazení</span><span class="sxs-lookup"><span data-stu-id="e61e6-351">Created controller and views</span></span>
> * <span data-ttu-id="e61e6-352">Zobrazení databáze</span><span class="sxs-lookup"><span data-stu-id="e61e6-352">Viewed the database</span></span>

<span data-ttu-id="e61e6-353">V následujícím kurzu se dozvíte, jak provádět základní operace CRUD (vytváření, čtení, aktualizace, odstranění).</span><span class="sxs-lookup"><span data-stu-id="e61e6-353">In the following tutorial, you'll learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

<span data-ttu-id="e61e6-354">Přejděte k dalšímu kurzu, kde se dozvíte, jak provádět základní operace CRUD (vytváření, čtení, aktualizace, odstranění).</span><span class="sxs-lookup"><span data-stu-id="e61e6-354">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="e61e6-355">Implementace základní funkce CRUD</span><span class="sxs-lookup"><span data-stu-id="e61e6-355">Implement basic CRUD functionality</span></span>](crud.md)

::: moniker-end
