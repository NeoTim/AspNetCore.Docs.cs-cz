---
title: 'Kurz: Začínáme s EF Core ve ASP.NET webové aplikaci MVC'
description: Toto je první z řady výukových programů, které vysvětlují, jak vytvořit ukázkovou aplikaci Contoso University od začátku.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 02/06/2019
ms.topic: tutorial
uid: data/ef-mvc/intro
ms.openlocfilehash: fca9fdc425506ec8b4eec5c609237208f4c0d7b5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511298"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a><span data-ttu-id="6a437-103">Kurz: Začínáme s EF Core ve ASP.NET webové aplikaci MVC</span><span class="sxs-lookup"><span data-stu-id="6a437-103">Tutorial: Get started with EF Core in an ASP.NET MVC web app</span></span>

<span data-ttu-id="6a437-104">Tento kurz **nebyl** aktualizován na ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="6a437-104">This tutorial has **not** been updated to ASP.NET Core 3.0.</span></span> <span data-ttu-id="6a437-105">[Verze Razor Pages](xref:data/ef-rp/intro) byla aktualizována.</span><span class="sxs-lookup"><span data-stu-id="6a437-105">The [Razor Pages version](xref:data/ef-rp/intro) has been updated.</span></span> <span data-ttu-id="6a437-106">Většina změn kódu pro ASP.NET Core 3.0 a novější verzi tohoto kurzu:</span><span class="sxs-lookup"><span data-stu-id="6a437-106">Most of the code changes for the ASP.NET Core 3.0 and later version of this tutorial:</span></span>

* <span data-ttu-id="6a437-107">Jsou v *Startup.cs* a *Program.cs* souborů.</span><span class="sxs-lookup"><span data-stu-id="6a437-107">Are in the *Startup.cs* and *Program.cs* files.</span></span>
* <span data-ttu-id="6a437-108">Lze nalézt ve [verzi Razor Pages](xref:data/ef-rp/intro).</span><span class="sxs-lookup"><span data-stu-id="6a437-108">Can be found in the [Razor Pages version](xref:data/ef-rp/intro).</span></span> 

<span data-ttu-id="6a437-109">Informace o tom, kdy to může být aktualizován, naleznete [v tomto problému GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/13920).</span><span class="sxs-lookup"><span data-stu-id="6a437-109">For information on when this might be updated, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/13920).</span></span>

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="6a437-110">Ukázková webová aplikace Contoso University ukazuje, jak vytvořit ASP.NET webových aplikací Core 2.2 MVC pomocí core 2.2 rozhraní Entity Framework (EF) a Visual Studio 2017 nebo 2019.</span><span class="sxs-lookup"><span data-stu-id="6a437-110">The Contoso University sample web application demonstrates how to create ASP.NET Core 2.2 MVC web applications using Entity Framework (EF) Core 2.2 and Visual Studio 2017 or 2019.</span></span>

<span data-ttu-id="6a437-111">Ukázková aplikace je webová stránka fiktivní univerzity Contoso.</span><span class="sxs-lookup"><span data-stu-id="6a437-111">The sample application is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="6a437-112">Zahrnuje funkce, jako je přijetí studentů, tvorba kurzů a úkoly instruktora.</span><span class="sxs-lookup"><span data-stu-id="6a437-112">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="6a437-113">Toto je první z řady výukových programů, které vysvětlují, jak vytvořit ukázkovou aplikaci Contoso University od začátku.</span><span class="sxs-lookup"><span data-stu-id="6a437-113">This is the first in a series of tutorials that explain how to build the Contoso University sample application from scratch.</span></span>

<span data-ttu-id="6a437-114">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="6a437-114">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6a437-115">Vytvoření webové aplikace ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="6a437-115">Create an ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="6a437-116">Nastavení stylu webu</span><span class="sxs-lookup"><span data-stu-id="6a437-116">Set up the site style</span></span>
> * <span data-ttu-id="6a437-117">Další informace o balíčcích EF Core NuGet</span><span class="sxs-lookup"><span data-stu-id="6a437-117">Learn about EF Core NuGet packages</span></span>
> * <span data-ttu-id="6a437-118">Vytvoření datového modelu</span><span class="sxs-lookup"><span data-stu-id="6a437-118">Create the data model</span></span>
> * <span data-ttu-id="6a437-119">Vytvoření kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="6a437-119">Create the database context</span></span>
> * <span data-ttu-id="6a437-120">Registrace kontextu pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="6a437-120">Register the context for dependency injection</span></span>
> * <span data-ttu-id="6a437-121">Inicializovat databázi testovacími daty</span><span class="sxs-lookup"><span data-stu-id="6a437-121">Initialize the database with test data</span></span>
> * <span data-ttu-id="6a437-122">Vytvoření ovladače a zobrazení</span><span class="sxs-lookup"><span data-stu-id="6a437-122">Create a controller and views</span></span>
> * <span data-ttu-id="6a437-123">Zobrazit databázi</span><span class="sxs-lookup"><span data-stu-id="6a437-123">View the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6a437-124">Požadavky</span><span class="sxs-lookup"><span data-stu-id="6a437-124">Prerequisites</span></span>

* [<span data-ttu-id="6a437-125">Sada .NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="6a437-125">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download)
* <span data-ttu-id="6a437-126">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s následujícími úlohami:</span><span class="sxs-lookup"><span data-stu-id="6a437-126">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the following workloads:</span></span>
  * <span data-ttu-id="6a437-127">**ASP.NET a pracovní zátěž pro vývoj webových** aplikací</span><span class="sxs-lookup"><span data-stu-id="6a437-127">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="6a437-128">Pracovní **vytížení napříč platformami .NET Core**</span><span class="sxs-lookup"><span data-stu-id="6a437-128">**.NET Core cross-platform development** workload</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="6a437-129">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="6a437-129">Troubleshooting</span></span>

<span data-ttu-id="6a437-130">Pokud narazíte na problém, který nelze vyřešit, můžete obecně najít řešení porovnáním kódu s [dokončeným projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final).</span><span class="sxs-lookup"><span data-stu-id="6a437-130">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final).</span></span> <span data-ttu-id="6a437-131">Seznam běžných chyb a jejich řešení naleznete [v části Poradce při potížích v posledním kurzu v řadě](advanced.md#common-errors).</span><span class="sxs-lookup"><span data-stu-id="6a437-131">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="6a437-132">Pokud tam nenajdete to, co potřebujete, můžete odeslat dotaz, který StackOverflow.com pro [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="6a437-132">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="6a437-133">Jedná se o sérii 10 výukových programů, z nichž každý staví na tom, co se děje v předchozích kurzech.</span><span class="sxs-lookup"><span data-stu-id="6a437-133">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="6a437-134">Zvažte uložení kopie projektu po každém úspěšném dokončení kurzu.</span><span class="sxs-lookup"><span data-stu-id="6a437-134">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="6a437-135">Pak, pokud narazíte na problémy, můžete začít znovu z předchozího kurzu namísto návratu na začátek celé série.</span><span class="sxs-lookup"><span data-stu-id="6a437-135">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="6a437-136">Webová aplikace Univerzity Contoso</span><span class="sxs-lookup"><span data-stu-id="6a437-136">Contoso University web app</span></span>

<span data-ttu-id="6a437-137">Aplikace, kterou budete vytvářet v těchto kurzech, je jednoduchá univerzita webové stránky.</span><span class="sxs-lookup"><span data-stu-id="6a437-137">The application you'll be building in these tutorials is a simple university web site.</span></span>

<span data-ttu-id="6a437-138">Uživatelé mohou zobrazit a aktualizovat informace o studentovi, kurzu a instruktorovi.</span><span class="sxs-lookup"><span data-stu-id="6a437-138">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="6a437-139">Zde je několik obrazovek, které vytvoříte.</span><span class="sxs-lookup"><span data-stu-id="6a437-139">Here are a few of the screens you'll create.</span></span>

![Stránka Studentský index](intro/_static/students-index.png)

![Stránka Úpravy studentů](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="6a437-142">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="6a437-142">Create web app</span></span>

* <span data-ttu-id="6a437-143">Otevřete sadu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6a437-143">Open Visual Studio.</span></span>

* <span data-ttu-id="6a437-144">V nabídce **Soubor** vyberte **Nový > Project**.</span><span class="sxs-lookup"><span data-stu-id="6a437-144">From the **File** menu, select **New > Project**.</span></span>

* <span data-ttu-id="6a437-145">V levém podokně vyberte **možnost Nainstalováno > visual c# > web**.</span><span class="sxs-lookup"><span data-stu-id="6a437-145">From the left pane, select **Installed > Visual C# > Web**.</span></span>

* <span data-ttu-id="6a437-146">Vyberte šablonu projektu **základní webové aplikace ASP.NET.**</span><span class="sxs-lookup"><span data-stu-id="6a437-146">Select the **ASP.NET Core Web Application** project template.</span></span>

* <span data-ttu-id="6a437-147">Jako název zadejte **ContosoUniversity** a klepněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="6a437-147">Enter **ContosoUniversity** as the name and click **OK**.</span></span>

  ![Dialogové okno Nový projekt](intro/_static/new-project2.png)

* <span data-ttu-id="6a437-149">Počkejte, až se zobrazí dialogové okno **Nová ASP.NET základní webová aplikace.**</span><span class="sxs-lookup"><span data-stu-id="6a437-149">Wait for the **New ASP.NET Core Web Application** dialog to appear.</span></span>

* <span data-ttu-id="6a437-150">Vyberte **.NET Core** **, ASP.NET Core 2.2** a **šablonu webové aplikace (Model-View-Controller).**</span><span class="sxs-lookup"><span data-stu-id="6a437-150">Select **.NET Core**, **ASP.NET Core 2.2** and the **Web Application (Model-View-Controller)** template.</span></span>

* <span data-ttu-id="6a437-151">Ujistěte **se,** že je možnost Ověřování nastavena na **možnost Žádné ověřování**.</span><span class="sxs-lookup"><span data-stu-id="6a437-151">Make sure **Authentication** is set to **No Authentication**.</span></span>

* <span data-ttu-id="6a437-152">Vybrat **OK**</span><span class="sxs-lookup"><span data-stu-id="6a437-152">Select **OK**</span></span>

  ![Dialogové okno Nový ASP.NET základní projekt](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a><span data-ttu-id="6a437-154">Nastavení stylu webu</span><span class="sxs-lookup"><span data-stu-id="6a437-154">Set up the site style</span></span>

<span data-ttu-id="6a437-155">Několik jednoduchých změn nastaví nabídku webu, rozložení a domovskou stránku.</span><span class="sxs-lookup"><span data-stu-id="6a437-155">A few simple changes will set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="6a437-156">Otevřete *zobrazení/Sdílené/_Layout.cshtml* a proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="6a437-156">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="6a437-157">Změňte každý výskyt "ContosoUniversity" na "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="6a437-157">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="6a437-158">Existují tři výskyty.</span><span class="sxs-lookup"><span data-stu-id="6a437-158">There are three occurrences.</span></span>

* <span data-ttu-id="6a437-159">Přidejte položky nabídky **Pro Informace**, **Studenti**, **Kurzy**, **Instruktoři**a **Oddělení**a odstraňte položku nabídky **Ochrana osobních údajů.**</span><span class="sxs-lookup"><span data-stu-id="6a437-159">Add menu entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="6a437-160">Změny jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="6a437-160">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

<span data-ttu-id="6a437-161">V *části Zobrazení/Home/Index.cshtml*nahraďte obsah souboru následujícím kódem, který nahradí text o ASP.NET a MVC textem o této aplikaci:</span><span class="sxs-lookup"><span data-stu-id="6a437-161">In *Views/Home/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

<span data-ttu-id="6a437-162">Stisknutím kláves CTRL+F5 spusťte projekt nebo z nabídky zvolte **Ladění > Spustit bez ladění.**</span><span class="sxs-lookup"><span data-stu-id="6a437-162">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="6a437-163">Zobrazí se domovská stránka s kartami pro stránky, které vytvoříte v těchto kurzech.</span><span class="sxs-lookup"><span data-stu-id="6a437-163">You see the home page with tabs for the pages you'll create in these tutorials.</span></span>

![Domovská stránka Univerzity Contoso](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a><span data-ttu-id="6a437-165">O balíčcích EF Core NuGet</span><span class="sxs-lookup"><span data-stu-id="6a437-165">About EF Core NuGet packages</span></span>

<span data-ttu-id="6a437-166">Chcete-li přidat podporu EF Core do projektu, nainstalujte poskytovatele databáze, na kterého chcete cílit.</span><span class="sxs-lookup"><span data-stu-id="6a437-166">To add EF Core support to a project, install the database provider that you want to target.</span></span> <span data-ttu-id="6a437-167">Tento kurz používá SQL Server a balíček zprostředkovatele je [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="6a437-167">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span> <span data-ttu-id="6a437-168">Tento balíček je součástí [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), takže na balíček nemusíte odkazovat.</span><span class="sxs-lookup"><span data-stu-id="6a437-168">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to reference the package.</span></span>

<span data-ttu-id="6a437-169">Balíček EF SQL Server a`Microsoft.EntityFrameworkCore` `Microsoft.EntityFrameworkCore.Relational`jeho závislosti ( a ) poskytují podporu modulu runtime pro EF.</span><span class="sxs-lookup"><span data-stu-id="6a437-169">The EF SQL Server package and its dependencies (`Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`) provide runtime support for EF.</span></span> <span data-ttu-id="6a437-170">Balíček nástrojů přidáte později v kurzu [Migrace.](migrations.md)</span><span class="sxs-lookup"><span data-stu-id="6a437-170">You'll add a tooling package later, in the [Migrations](migrations.md) tutorial.</span></span>

<span data-ttu-id="6a437-171">Informace o jiných zprostředkovatelích databáze, které jsou k dispozici pro core entity frameworku, naleznete v [tématu zprostředkovatelé databáze](/ef/core/providers/).</span><span class="sxs-lookup"><span data-stu-id="6a437-171">For information about other database providers that are available for Entity Framework Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="6a437-172">Vytvoření datového modelu</span><span class="sxs-lookup"><span data-stu-id="6a437-172">Create the data model</span></span>

<span data-ttu-id="6a437-173">Dále vytvoříte třídy entit pro aplikaci Contoso University.</span><span class="sxs-lookup"><span data-stu-id="6a437-173">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="6a437-174">Začnete s následujícími třemi entitami.</span><span class="sxs-lookup"><span data-stu-id="6a437-174">You'll start with the following three entities.</span></span>

![Diagram datového modelu zápisu do kurzu-studenta](intro/_static/data-model-diagram.png)

<span data-ttu-id="6a437-176">Existuje `Student` vztah 1:N mezi entitami `Enrollment` a existuje vztah 1:N mezi `Course` entitami. `Enrollment`</span><span class="sxs-lookup"><span data-stu-id="6a437-176">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="6a437-177">Jinými slovy, student může být zapsán do libovolného počtu kurzů a kurz může mít libovolný počet studentů zapsaných v něm.</span><span class="sxs-lookup"><span data-stu-id="6a437-177">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="6a437-178">V následujících částech vytvoříte třídu pro každou z těchto entit.</span><span class="sxs-lookup"><span data-stu-id="6a437-178">In the following sections you'll create a class for each one of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="6a437-179">Studentská entita</span><span class="sxs-lookup"><span data-stu-id="6a437-179">The Student entity</span></span>

![Diagram studentských entit](intro/_static/student-entity.png)

<span data-ttu-id="6a437-181">Ve složce *Modely* vytvořte soubor třídy s názvem *Student.cs* a nahraďte kód šablony následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="6a437-181">In the *Models* folder, create a class file named *Student.cs* and replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="6a437-182">Vlastnost `ID` se stane sloupcem primárního klíče databázové tabulky, který odpovídá této třídě.</span><span class="sxs-lookup"><span data-stu-id="6a437-182">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="6a437-183">Ve výchozím nastavení interpretuje Entity Framework vlastnost s názvem `ID` nebo `classnameID` jako primární klíč.</span><span class="sxs-lookup"><span data-stu-id="6a437-183">By default, the Entity Framework interprets a property that's named `ID` or `classnameID` as the primary key.</span></span>

<span data-ttu-id="6a437-184">Vlastnost `Enrollments` je [navigační vlastnost](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="6a437-184">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="6a437-185">Navigační vlastnosti mají další entity, které souvisejí s touto entitou.</span><span class="sxs-lookup"><span data-stu-id="6a437-185">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="6a437-186">V tomto případě `Enrollments` bude `Student entity` vlastnost a obsahovat všechny `Enrollment` entity, `Student` které jsou ve spojení s touto entitou.</span><span class="sxs-lookup"><span data-stu-id="6a437-186">In this case, the `Enrollments` property of a `Student entity` will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="6a437-187">Jinými slovy, pokud daný řádek Student v databázi obsahuje dva související řádky zápisu (řádky, které obsahují `Student` hodnotu `Enrollments` primárního klíče studenta ve sloupci cizího klíče StudentID), navigační vlastnost této entity bude obsahovat tyto dvě `Enrollment` entity.</span><span class="sxs-lookup"><span data-stu-id="6a437-187">In other words, if a given Student row in the database has two related Enrollment rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="6a437-188">Pokud navigační vlastnost může obsahovat více entit (jako v relacích N:N nebo 1:N), musí být jejím typem `ICollection<T>`seznam, ve kterém lze položky přidávat, odstraňovat a aktualizovat, například .</span><span class="sxs-lookup"><span data-stu-id="6a437-188">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection<T>`.</span></span> <span data-ttu-id="6a437-189">Můžete zadat `ICollection<T>` nebo typ, `List<T>` `HashSet<T>`například nebo .</span><span class="sxs-lookup"><span data-stu-id="6a437-189">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="6a437-190">Pokud zadáte `ICollection<T>`, EF `HashSet<T>` vytvoří kolekci ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="6a437-190">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="6a437-191">Entita Zápis</span><span class="sxs-lookup"><span data-stu-id="6a437-191">The Enrollment entity</span></span>

![Diagram entit zápisu](intro/_static/enrollment-entity.png)

<span data-ttu-id="6a437-193">Ve složce *Modely* vytvořte *Enrollment.cs* a nahraďte existující kód následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="6a437-193">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="6a437-194">Vlastnost `EnrollmentID` bude primárním klíčem; tato entita `classnameID` používá `ID` vzor místo sebe, `Student` jak jste viděli v entitě.</span><span class="sxs-lookup"><span data-stu-id="6a437-194">The `EnrollmentID` property will be the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="6a437-195">Obvykle byste zvolit jeden vzor a použít jej v celém datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="6a437-195">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="6a437-196">Zde varianta ilustruje, že můžete použít buď vzor.</span><span class="sxs-lookup"><span data-stu-id="6a437-196">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="6a437-197">V [pozdějším kurzu](inheritance.md)uvidíte, jak použití ID bez názvu třídy usnadňuje implementaci dědičnosti v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="6a437-197">In a [later tutorial](inheritance.md), you'll see how using ID without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="6a437-198">Ubytovací `Grade` zařízení `enum`je .</span><span class="sxs-lookup"><span data-stu-id="6a437-198">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="6a437-199">Otazník za `Grade` deklarací typu `Grade` označuje, že vlastnost je nullable.</span><span class="sxs-lookup"><span data-stu-id="6a437-199">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="6a437-200">Stupeň, který je null se liší od nulové třídy - null znamená, že platová třída není známa nebo ještě nebyla přiřazena.</span><span class="sxs-lookup"><span data-stu-id="6a437-200">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="6a437-201">Vlastnost `StudentID` je cizí klíč a odpovídající navigační `Student`vlastnost je .</span><span class="sxs-lookup"><span data-stu-id="6a437-201">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="6a437-202">`Enrollment` Entita je `Student` přidružena k jedné entitě, takže vlastnost může obsahovat pouze jednu `Student` entitu `Student.Enrollments` (na rozdíl od navigační vlastnosti, kterou jste viděli dříve, která může obsahovat více `Enrollment` entit).</span><span class="sxs-lookup"><span data-stu-id="6a437-202">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="6a437-203">Vlastnost `CourseID` je cizí klíč a odpovídající navigační `Course`vlastnost je .</span><span class="sxs-lookup"><span data-stu-id="6a437-203">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="6a437-204">Entita `Enrollment` je `Course` přidružena k jedné entitě.</span><span class="sxs-lookup"><span data-stu-id="6a437-204">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="6a437-205">Entity Framework interpretuje vlastnost jako vlastnost cizího `<navigation property name><primary key property name>` klíče, `StudentID` pokud `Student` je pojmenována `Student` (například pro `ID`navigační vlastnost, protože je primární klíč entity ).</span><span class="sxs-lookup"><span data-stu-id="6a437-205">Entity Framework interprets a property as a foreign key property if it's named `<navigation property name><primary key property name>` (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="6a437-206">Vlastnosti cizího klíče `<primary key property name>` lze také `CourseID` jednoduše `Course` pojmenovat (například `CourseID`vzhledem k tomu, že je primárníklíč entity).</span><span class="sxs-lookup"><span data-stu-id="6a437-206">Foreign key properties can also be named simply `<primary key property name>` (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="6a437-207">Entita Kurzu</span><span class="sxs-lookup"><span data-stu-id="6a437-207">The Course entity</span></span>

![Diagram entity kurzu](intro/_static/course-entity.png)

<span data-ttu-id="6a437-209">Ve složce *Modely* vytvořte *Course.cs* a nahraďte existující kód následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="6a437-209">In the *Models* folder, create *Course.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="6a437-210">Vlastnost `Enrollments` je navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="6a437-210">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="6a437-211">Entita `Course` může souviset `Enrollment` s libovolným počtem entit.</span><span class="sxs-lookup"><span data-stu-id="6a437-211">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="6a437-212">O atributu `DatabaseGenerated` řekneme více v [pozdějším kurzu](complex-data-model.md) v této sérii.</span><span class="sxs-lookup"><span data-stu-id="6a437-212">We'll say more about the `DatabaseGenerated` attribute in a [later tutorial](complex-data-model.md) in this series.</span></span> <span data-ttu-id="6a437-213">V podstatě tento atribut umožňuje zadat primární klíč pro kurz, nikoli databáze generovat.</span><span class="sxs-lookup"><span data-stu-id="6a437-213">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="6a437-214">Vytvoření kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="6a437-214">Create the database context</span></span>

<span data-ttu-id="6a437-215">Hlavní třída, která koordinuje entity framework funkce pro daný datový model je třída kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="6a437-215">The main class that coordinates Entity Framework functionality for a given data model is the database context class.</span></span> <span data-ttu-id="6a437-216">Tuto třídu vytvoříte odvozením `Microsoft.EntityFrameworkCore.DbContext` ze třídy.</span><span class="sxs-lookup"><span data-stu-id="6a437-216">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="6a437-217">V kódu určíte, které entity budou zahrnuty do datového modelu.</span><span class="sxs-lookup"><span data-stu-id="6a437-217">In your code you specify which entities are included in the data model.</span></span> <span data-ttu-id="6a437-218">Můžete také přizpůsobit určité chování entity framework.</span><span class="sxs-lookup"><span data-stu-id="6a437-218">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="6a437-219">V tomto projektu je `SchoolContext`třída pojmenována .</span><span class="sxs-lookup"><span data-stu-id="6a437-219">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="6a437-220">Ve složce projektu vytvořte složku s názvem *Data*.</span><span class="sxs-lookup"><span data-stu-id="6a437-220">In the project folder, create a folder named *Data*.</span></span>

<span data-ttu-id="6a437-221">Ve složce *Data* vytvořte nový soubor třídy s názvem *SchoolContext.cs*a nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="6a437-221">In the *Data* folder create a new class file named *SchoolContext.cs*, and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="6a437-222">Tento kód `DbSet` vytvoří vlastnost pro každou sadu entit.</span><span class="sxs-lookup"><span data-stu-id="6a437-222">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="6a437-223">V terminologii entity Framework sada entit obvykle odpovídá databázové tabulce a entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="6a437-223">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<span data-ttu-id="6a437-224">Mohli jste vynechat `DbSet<Enrollment>` a `DbSet<Course>` prohlášení a fungovalo by to stejně.</span><span class="sxs-lookup"><span data-stu-id="6a437-224">You could've omitted the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="6a437-225">Rámec entity by je implicitně `Student` zahrnoval, `Enrollment` protože účetní `Enrollment` jednotka odkazuje `Course` na účetní jednotku a účetní jednotka na účetní jednotku odkazuje.</span><span class="sxs-lookup"><span data-stu-id="6a437-225">The Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="6a437-226">Při vytvoření databáze EF vytvoří tabulky, které mají `DbSet` názvy stejné jako názvy vlastností.</span><span class="sxs-lookup"><span data-stu-id="6a437-226">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="6a437-227">Názvy vlastností pro kolekce jsou obvykle množné číslo (Studenti spíše než Student), ale vývojáři nesouhlasí o tom, zda názvy tabulek by měly být pluralized nebo ne.</span><span class="sxs-lookup"><span data-stu-id="6a437-227">Property names for collections are typically plural (Students rather than Student), but developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="6a437-228">Pro tyto kurzy budete přepsat výchozí chování zadáním názvů tabulek jednotného čísla v DbContext.</span><span class="sxs-lookup"><span data-stu-id="6a437-228">For these tutorials you'll override the default behavior by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="6a437-229">Chcete-li to provést, přidejte následující zvýrazněný kód za poslední Vlastnost DbSet.</span><span class="sxs-lookup"><span data-stu-id="6a437-229">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a><span data-ttu-id="6a437-230">Zaregistrujte schoolcontext</span><span class="sxs-lookup"><span data-stu-id="6a437-230">Register the SchoolContext</span></span>

<span data-ttu-id="6a437-231">ASP.NET Core implementuje [vkládání závislostí](../../fundamentals/dependency-injection.md) ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="6a437-231">ASP.NET Core implements [dependency injection](../../fundamentals/dependency-injection.md) by default.</span></span> <span data-ttu-id="6a437-232">Služby (například kontext databáze EF) jsou registrovány s vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="6a437-232">Services (such as the EF database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="6a437-233">Součásti, které vyžadují tyto služby (například řadiče MVC) jsou poskytovány tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="6a437-233">Components that require these services (such as MVC controllers) are provided these services via constructor parameters.</span></span> <span data-ttu-id="6a437-234">Uvidíte kód konstruktoru řadiče, který získá instanci kontextu dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="6a437-234">You'll see the controller constructor code that gets a context instance later in this tutorial.</span></span>

<span data-ttu-id="6a437-235">Chcete-li se zaregistrovat `SchoolContext` jako služba, otevřete `ConfigureServices` *Startup.cs*a přidejte zvýrazněné řádky do metody.</span><span class="sxs-lookup"><span data-stu-id="6a437-235">To register `SchoolContext` as a service, open *Startup.cs*, and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

<span data-ttu-id="6a437-236">Název připojovacího řetězce je předán do kontextu `DbContextOptionsBuilder` voláním metody na objektu.</span><span class="sxs-lookup"><span data-stu-id="6a437-236">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="6a437-237">Pro místní vývoj [ASP.NET konfiguračnísystém Core](xref:fundamentals/configuration/index) přečte připojovací řetězec ze souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="6a437-237">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="6a437-238">Přidejte `using` `ContosoUniversity.Data` příkazy pro a `Microsoft.EntityFrameworkCore` obory názvů a pak vytvořte projekt.</span><span class="sxs-lookup"><span data-stu-id="6a437-238">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces, and then build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="6a437-239">Otevřete soubor *appsettings.json* a přidejte připojovací řetězec, jak je znázorněno v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="6a437-239">Open the *appsettings.json* file and add a connection string as shown in the following example.</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a><span data-ttu-id="6a437-240">Místní databáze SQL Server Express</span><span class="sxs-lookup"><span data-stu-id="6a437-240">SQL Server Express LocalDB</span></span>

<span data-ttu-id="6a437-241">Připojovací řetězec určuje databázi SQL Server LocalDB.</span><span class="sxs-lookup"><span data-stu-id="6a437-241">The connection string specifies a SQL Server LocalDB database.</span></span> <span data-ttu-id="6a437-242">LocalDB je odlehčená verze sql server express databázový stroj a je určen pro vývoj aplikací, nikoli produkční použití.</span><span class="sxs-lookup"><span data-stu-id="6a437-242">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for application development, not production use.</span></span> <span data-ttu-id="6a437-243">LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje žádná složitá konfigurace.</span><span class="sxs-lookup"><span data-stu-id="6a437-243">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="6a437-244">Ve výchozím nastavení localdb vytvoří *.mdf* databázové soubory v adresáři. `C:/Users/<user>`</span><span class="sxs-lookup"><span data-stu-id="6a437-244">By default, LocalDB creates *.mdf* database files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="6a437-245">Inicializovat DB s testovacími daty</span><span class="sxs-lookup"><span data-stu-id="6a437-245">Initialize DB with test data</span></span>

<span data-ttu-id="6a437-246">Entity Framework vytvoří prázdnou databázi pro vás.</span><span class="sxs-lookup"><span data-stu-id="6a437-246">The Entity Framework will create an empty database for you.</span></span> <span data-ttu-id="6a437-247">V této části napíšete metodu, která je volána po vytvoření databáze, aby byla naplněna testovacími daty.</span><span class="sxs-lookup"><span data-stu-id="6a437-247">In this section, you write a method that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="6a437-248">Zde budete používat `EnsureCreated` metodu k automatickému vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="6a437-248">Here you'll use the `EnsureCreated` method to automatically create the database.</span></span> <span data-ttu-id="6a437-249">V [pozdějším kurzu](migrations.md) uvidíte, jak zpracovat změny modelu pomocí migrace Code First změnit schéma databáze namísto uvolnění a opětovné vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="6a437-249">In a [later tutorial](migrations.md) you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="6a437-250">Ve složce *Data* vytvořte nový soubor třídy s názvem *DbInitializer.cs* a nahraďte kód šablony následujícím kódem, který způsobí, že databáze bude v případě potřeby vytvořena a načte testovací data do nové databáze.</span><span class="sxs-lookup"><span data-stu-id="6a437-250">In the *Data* folder, create a new class file named *DbInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="6a437-251">Kód zkontroluje, zda jsou v databázi nějaké studenty, a pokud ne, předpokládá, že databáze je nová a musí být nasazena s testovacími daty.</span><span class="sxs-lookup"><span data-stu-id="6a437-251">The code checks if there are any students in the database, and if not, it assumes the database is new and needs to be seeded with test data.</span></span> <span data-ttu-id="6a437-252">Načte testovací data do `List<T>` polí, nikoli do kolekcí pro optimalizaci výkonu.</span><span class="sxs-lookup"><span data-stu-id="6a437-252">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="6a437-253">V *Program.cs*upravte metodu `Main` tak, aby při spuštění aplikace učinila následující kroky:</span><span class="sxs-lookup"><span data-stu-id="6a437-253">In *Program.cs*, modify the `Main` method to do the following on application startup:</span></span>

* <span data-ttu-id="6a437-254">Získejte instanci kontextu databáze z kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="6a437-254">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="6a437-255">Volání metody osiva, předávání k ní kontextu.</span><span class="sxs-lookup"><span data-stu-id="6a437-255">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="6a437-256">Dispose kontextu po dokončení metody osiva.</span><span class="sxs-lookup"><span data-stu-id="6a437-256">Dispose the context when the seed method is done.</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

<span data-ttu-id="6a437-257">Přidat `using` příkazy:</span><span class="sxs-lookup"><span data-stu-id="6a437-257">Add `using` statements:</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Usings)]

<span data-ttu-id="6a437-258">Ve starších kurzech, můžete vidět `Configure` podobný kód v metodě v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="6a437-258">In older tutorials, you may see similar code in the `Configure` method in *Startup.cs*.</span></span> <span data-ttu-id="6a437-259">Doporučujeme použít metodu `Configure` pouze k nastavení kanálu požadavku.</span><span class="sxs-lookup"><span data-stu-id="6a437-259">We recommend that you use the `Configure` method only to set up the request pipeline.</span></span> <span data-ttu-id="6a437-260">Kód spuštění aplikace `Main` patří do metody.</span><span class="sxs-lookup"><span data-stu-id="6a437-260">Application startup code belongs in the `Main` method.</span></span>

<span data-ttu-id="6a437-261">Nyní při prvním spuštění aplikace, bude databáze vytvořena a nasazena s testovacími daty.</span><span class="sxs-lookup"><span data-stu-id="6a437-261">Now the first time you run the application, the database will be created and seeded with test data.</span></span> <span data-ttu-id="6a437-262">Kdykoli změníte datový model, můžete odstranit databázi, aktualizovat metodu osiva a začít znovu s novou databází stejným způsobem.</span><span class="sxs-lookup"><span data-stu-id="6a437-262">Whenever you change your data model, you can delete the database, update your seed method, and start afresh with a new database the same way.</span></span> <span data-ttu-id="6a437-263">V pozdějších kurzech uvidíte, jak upravit databázi při změně datového modelu, aniž byste ji museli snímat a znovu vytvářet.</span><span class="sxs-lookup"><span data-stu-id="6a437-263">In later tutorials, you'll see how to modify the database when the data model changes, without deleting and re-creating it.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="6a437-264">Vytvoření ovladače a zobrazení</span><span class="sxs-lookup"><span data-stu-id="6a437-264">Create controller and views</span></span>

<span data-ttu-id="6a437-265">Dále budete používat modul generování uživatelského a uživatelského čítaču v sadě Visual Studio k přidání řadiče MVC a zobrazení, která budou používat EF k dotazování a ukládání dat.</span><span class="sxs-lookup"><span data-stu-id="6a437-265">Next, you'll use the scaffolding engine in Visual Studio to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="6a437-266">Automatické vytváření metod a zobrazení akce CRUD se označuje jako generování uživatelského líačku.</span><span class="sxs-lookup"><span data-stu-id="6a437-266">The automatic creation of CRUD action methods and views is known as scaffolding.</span></span> <span data-ttu-id="6a437-267">Generování uživatelského rozhraní se liší od generování kódu v tom, že kód generování generování generování uživatelského rozhraní je výchozím bodem, který můžete upravit tak, aby vyhovoval vašim vlastním požadavkům, zatímco obvykle neupravujete generovaný kód.</span><span class="sxs-lookup"><span data-stu-id="6a437-267">Scaffolding differs from code generation in that the scaffolded code is a starting point that you can modify to suit your own requirements, whereas you typically don't modify generated code.</span></span> <span data-ttu-id="6a437-268">Když potřebujete přizpůsobit generovaný kód, použijte částečné třídy nebo znovu vygenerovat kód při změně věci.</span><span class="sxs-lookup"><span data-stu-id="6a437-268">When you need to customize generated code, you use partial classes or you regenerate the code when things change.</span></span>

* <span data-ttu-id="6a437-269">Klepněte pravým tlačítkem myši na složku **Řadiče** v **Průzkumníku řešení** a vyberte **přidat > novou položku scaffolded item**.</span><span class="sxs-lookup"><span data-stu-id="6a437-269">Right-click the **Controllers** folder in **Solution Explorer** and select **Add > New Scaffolded Item**.</span></span>

* <span data-ttu-id="6a437-270">V dialogovém okně **Přidat kancovací okno:**</span><span class="sxs-lookup"><span data-stu-id="6a437-270">In the **Add Scaffold** dialog box:</span></span>

  * <span data-ttu-id="6a437-271">Vyberte **řadič MVC se zobrazeními pomocí entity Framework**.</span><span class="sxs-lookup"><span data-stu-id="6a437-271">Select **MVC controller with views, using Entity Framework**.</span></span>

  * <span data-ttu-id="6a437-272">Klikněte na **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="6a437-272">Click **Add**.</span></span> <span data-ttu-id="6a437-273">Zobrazí se dialogové **okno Přidat řadič MVC se zobrazeními pomocí entity Framework.**</span><span class="sxs-lookup"><span data-stu-id="6a437-273">The **Add MVC Controller with views, using Entity Framework** dialog box appears.</span></span>

    ![Lešení Student](intro/_static/scaffold-student2.png)

  * <span data-ttu-id="6a437-275">Ve **třídě Model** vyberte možnost **Student**.</span><span class="sxs-lookup"><span data-stu-id="6a437-275">In **Model class** select **Student**.</span></span>

  * <span data-ttu-id="6a437-276">V **datovém kontextu třídy** vyberte **SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="6a437-276">In **Data context class** select **SchoolContext**.</span></span>

  * <span data-ttu-id="6a437-277">Přijmout výchozí **StudentsController** jako název.</span><span class="sxs-lookup"><span data-stu-id="6a437-277">Accept the default **StudentsController** as the name.</span></span>

  * <span data-ttu-id="6a437-278">Klikněte na **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="6a437-278">Click **Add**.</span></span>

  <span data-ttu-id="6a437-279">Po klepnutí na tlačítko **Přidat**vytvoří modul zaslechnutí pomocí sady uživatelského zařízení *sady StudentsController.cs* soubor a sadu zobrazení (*soubory CSHTML),* které pracují s řadičem.</span><span class="sxs-lookup"><span data-stu-id="6a437-279">When you click **Add**, the Visual Studio scaffolding engine creates a *StudentsController.cs* file and a set of views (*.cshtml* files) that work with the controller.</span></span>

<span data-ttu-id="6a437-280">(Modul zaschlovacího systému můžete také vytvořit kontext databáze pro vás, pokud nechcete vytvořit ručně nejprve jako dříve pro tento kurz.</span><span class="sxs-lookup"><span data-stu-id="6a437-280">(The scaffolding engine can also create the database context for you if you don't create it manually first as you did earlier for this tutorial.</span></span> <span data-ttu-id="6a437-281">Novou třídu kontextu můžete zadat v poli **Přidat kontroleru** klepnutím na znaménko plus napravo od **třídy kontextu Dat**.</span><span class="sxs-lookup"><span data-stu-id="6a437-281">You can specify a new context class in the **Add Controller** box by clicking the plus sign to the right of **Data context class**.</span></span>  <span data-ttu-id="6a437-282">Visual Studio pak `DbContext` vytvoří třídu, stejně jako řadič a zobrazení.)</span><span class="sxs-lookup"><span data-stu-id="6a437-282">Visual Studio will then create your `DbContext` class as well as the controller and views.)</span></span>

<span data-ttu-id="6a437-283">Všimněte si, že řadič `SchoolContext` bere jako parametr konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="6a437-283">You'll notice that the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="6a437-284">ASP.NET vkládání závislostí jádra se `SchoolContext` postará o předání instance do řadiče.</span><span class="sxs-lookup"><span data-stu-id="6a437-284">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="6a437-285">Nakonfiguroval jste to v *Startup.cs* souboru dříve.</span><span class="sxs-lookup"><span data-stu-id="6a437-285">You configured that in the *Startup.cs* file earlier.</span></span>

<span data-ttu-id="6a437-286">Kontrolid `Index` obsahuje metodu akce, která zobrazuje všechny studenty v databázi.</span><span class="sxs-lookup"><span data-stu-id="6a437-286">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="6a437-287">Metoda získá seznam studentů z entity Students nastavenou čtením vlastnosti `Students` instance kontextu databáze:</span><span class="sxs-lookup"><span data-stu-id="6a437-287">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="6a437-288">O asynchronních programovacích prvcích v tomto kódu se dozvíte dále v kurzu.</span><span class="sxs-lookup"><span data-stu-id="6a437-288">You'll learn about the asynchronous programming elements in this code later in the tutorial.</span></span>

<span data-ttu-id="6a437-289">*Zobrazení/Studenti/Index.cshtml* zobrazí tento seznam v tabulce:</span><span class="sxs-lookup"><span data-stu-id="6a437-289">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="6a437-290">Stisknutím kláves CTRL+F5 spusťte projekt nebo z nabídky zvolte **Ladění > Spustit bez ladění.**</span><span class="sxs-lookup"><span data-stu-id="6a437-290">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="6a437-291">Kliknutím na kartu Studenti zobrazíte `DbInitializer.Initialize` testovací data, která metoda vložila.</span><span class="sxs-lookup"><span data-stu-id="6a437-291">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="6a437-292">V závislosti na tom, jak je okno `Students` prohlížeče úzké, uvidíte odkaz na kartu v horní části stránky nebo budete muset kliknout na ikonu navigace v pravém horním rohu, abyste viděli odkaz.</span><span class="sxs-lookup"><span data-stu-id="6a437-292">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Domovská stránka Univerzity Contoso úzká](intro/_static/home-page-narrow.png)

![Stránka Studentský index](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="6a437-295">Zobrazit databázi</span><span class="sxs-lookup"><span data-stu-id="6a437-295">View the database</span></span>

<span data-ttu-id="6a437-296">Při spuštění aplikace `DbInitializer.Initialize` volání `EnsureCreated`metody .</span><span class="sxs-lookup"><span data-stu-id="6a437-296">When you started the application, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="6a437-297">EF viděl, že neexistuje žádná databáze, a tak `Initialize` ji vytvořil, pak zbytek kódu metody naplnil databázi daty.</span><span class="sxs-lookup"><span data-stu-id="6a437-297">EF saw that there was no database and so it created one, then the remainder of the `Initialize` method code populated the database with data.</span></span> <span data-ttu-id="6a437-298">K zobrazení databáze v sadě Visual Studio můžete použít **Průzkumník objektů serveru SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="6a437-298">You can use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span>

<span data-ttu-id="6a437-299">Zavřete prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="6a437-299">Close the browser.</span></span>

<span data-ttu-id="6a437-300">Pokud okno SSOX ještě není otevřené, vyberte ho z nabídky **Zobrazení** v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6a437-300">If the SSOX window isn't already open, select it from the **View** menu in Visual Studio.</span></span>

<span data-ttu-id="6a437-301">Ve ssoxu klikněte na **(localdb)\MSSQLLocalDB > Databases**a potom klikněte na položku názvu databáze, která je v připojovacím řetězci v souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="6a437-301">In SSOX, click **(localdb)\MSSQLLocalDB > Databases**, and then click the entry for the database name that's in the connection string in your *appsettings.json* file.</span></span>

<span data-ttu-id="6a437-302">Rozbalte uzel **Tabulky,** abyste viděli tabulky v databázi.</span><span class="sxs-lookup"><span data-stu-id="6a437-302">Expand the **Tables** node to see the tables in your database.</span></span>

![Tabulky v SSOX](intro/_static/ssox-tables.png)

<span data-ttu-id="6a437-304">Klikněte pravým tlačítkem myši na tabulku **Student** a kliknutím na **Zobrazit data** zobrazte sloupce, které byly vytvořeny, a řádky, které byly vloženy do tabulky.</span><span class="sxs-lookup"><span data-stu-id="6a437-304">Right-click the **Student** table and click **View Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

![Studentský stůl ve SSOX](intro/_static/ssox-student-table.png)

<span data-ttu-id="6a437-306">Soubory databáze *MDF* a *LDF* jsou ve složce *C:\Users, ve\\\<které je vaše uživatelské jméno>.*</span><span class="sxs-lookup"><span data-stu-id="6a437-306">The *.mdf* and *.ldf* database files are in the *C:\Users\\\<yourusername>* folder.</span></span>

<span data-ttu-id="6a437-307">Vzhledem k `EnsureCreated` tomu, že voláte metodu initializer, která běží `Student` při spuštění aplikace, můžete nyní provést změnu třídy, odstranit databázi, znovu spustit aplikaci a databáze by byla automaticky znovu vytvořena tak, aby odpovídala vaší změně.</span><span class="sxs-lookup"><span data-stu-id="6a437-307">Because you're calling `EnsureCreated` in the initializer method that runs on app start, you could now make a change to the `Student` class, delete the database, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="6a437-308">Pokud například přidáte `EmailAddress` vlastnost do `Student` třídy, zobrazí se `EmailAddress` v znovu vytvořené tabulce nový sloupec.</span><span class="sxs-lookup"><span data-stu-id="6a437-308">For example, if you add an `EmailAddress` property to the `Student` class, you'll see a new `EmailAddress` column in the re-created table.</span></span>

## <a name="conventions"></a><span data-ttu-id="6a437-309">Zásady</span><span class="sxs-lookup"><span data-stu-id="6a437-309">Conventions</span></span>

<span data-ttu-id="6a437-310">Množství kódu, který jste museli napsat, aby rozhraní Entity Framework bylo možné vytvořit úplnou databázi pro vás je minimální z důvodu použití konvencí nebo předpoklady, které umožňuje entity framework.</span><span class="sxs-lookup"><span data-stu-id="6a437-310">The amount of code you had to write in order for the Entity Framework to be able to create a complete database for you is minimal because of the use of conventions, or assumptions that the Entity Framework makes.</span></span>

* <span data-ttu-id="6a437-311">Názvy `DbSet` vlastností se používají jako názvy tabulek.</span><span class="sxs-lookup"><span data-stu-id="6a437-311">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="6a437-312">Pro entity, na které `DbSet` není odkazováno vlastností, se názvy tříd entit používají jako názvy tabulek.</span><span class="sxs-lookup"><span data-stu-id="6a437-312">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>

* <span data-ttu-id="6a437-313">Názvy vlastností entity se používají pro názvy sloupců.</span><span class="sxs-lookup"><span data-stu-id="6a437-313">Entity property names are used for column names.</span></span>

* <span data-ttu-id="6a437-314">Vlastnosti entity, které jsou pojmenovány ID nebo classnameID jsou rozpoznány jako vlastnosti primárního klíče.</span><span class="sxs-lookup"><span data-stu-id="6a437-314">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>

* <span data-ttu-id="6a437-315">Vlastnost je interpretována jako vlastnost cizího klíče, `StudentID` pokud je pojmenována `Student` `Student` `ID` \* \<název vlastnosti navigace>\<název vlastnosti primárního klíče>\* (například pro vlastnost navigace od doby, kdy je primární klíč entity ).</span><span class="sxs-lookup"><span data-stu-id="6a437-315">A property is interpreted as a foreign key property if it's named *\<navigation property name>\<primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="6a437-316">Vlastnosti cizího klíče lze také pojmenovat jednoduše `EnrollmentID` `Enrollment` `EnrollmentID` \* \<název vlastnosti primárního klíče>\* (například vzhledem k tomu, že primární klíč entity je ).</span><span class="sxs-lookup"><span data-stu-id="6a437-316">Foreign key properties can also be named simply *\<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="6a437-317">Konvenční chování může být přepsáno.</span><span class="sxs-lookup"><span data-stu-id="6a437-317">Conventional behavior can be overridden.</span></span> <span data-ttu-id="6a437-318">Můžete například explicitně zadat názvy tabulek, jak jste viděli dříve v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="6a437-318">For example, you can explicitly specify table names, as you saw earlier in this tutorial.</span></span> <span data-ttu-id="6a437-319">A můžete nastavit názvy sloupců a nastavit libovolnou vlastnost jako primární klíč nebo cizí klíč, jak uvidíte v [pozdějším kurzu](complex-data-model.md) v této sérii.</span><span class="sxs-lookup"><span data-stu-id="6a437-319">And you can set column names and set any property as primary key or foreign key, as you'll see in a [later tutorial](complex-data-model.md) in this series.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="6a437-320">Asynchronní kód</span><span class="sxs-lookup"><span data-stu-id="6a437-320">Asynchronous code</span></span>

<span data-ttu-id="6a437-321">Asynchronní programování je výchozí režim pro ASP.NET Core a EF Core.</span><span class="sxs-lookup"><span data-stu-id="6a437-321">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="6a437-322">Webový server má omezený počet vláken k dispozici a v situacích s vysokým zatížením všechny dostupné podprocesy mohou být používány.</span><span class="sxs-lookup"><span data-stu-id="6a437-322">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="6a437-323">V takovém případě server nemůže zpracovat nové požadavky, dokud vlákna jsou uvolněny.</span><span class="sxs-lookup"><span data-stu-id="6a437-323">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="6a437-324">Pomocí synchronního kódu může být mnoho vláken svázáno, zatímco ve skutečnosti nepracují, protože čekají na dokončení vstupně-up.</span><span class="sxs-lookup"><span data-stu-id="6a437-324">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="6a437-325">S asynchronním kódem, když proces čeká na dokončení vstupně-va, jeho vlákno je uvolněno pro server pro zpracování jiných požadavků.</span><span class="sxs-lookup"><span data-stu-id="6a437-325">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="6a437-326">V důsledku toho asynchronní kód umožňuje efektivnější použití prostředků serveru a server je povolen pro zpracování většího provozu bez zpoždění.</span><span class="sxs-lookup"><span data-stu-id="6a437-326">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="6a437-327">Asynchronní kód zavést malé množství režie za běhu, ale pro situace s nízkým provozem je přístupů k výkonu zanedbatelný, zatímco pro vysoké dopravní situace potenciální zlepšení výkonu je podstatné.</span><span class="sxs-lookup"><span data-stu-id="6a437-327">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="6a437-328">V následujícím kódu `async` klíčové `Task<T>` slovo, `await` vrácená `ToListAsync` hodnota, klíčové slovo a metoda provést spuštění kódu asynchronně.</span><span class="sxs-lookup"><span data-stu-id="6a437-328">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="6a437-329">Klíčové `async` slovo říká kompilátoru generovat zpětná volání pro části `Task<IActionResult>` těla metody a automaticky vytvořit objekt, který je vrácen.</span><span class="sxs-lookup"><span data-stu-id="6a437-329">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>

* <span data-ttu-id="6a437-330">Návratový `Task<IActionResult>` typ představuje probíhající práci `IActionResult`s výsledkem typu .</span><span class="sxs-lookup"><span data-stu-id="6a437-330">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>

* <span data-ttu-id="6a437-331">Klíčové `await` slovo způsobí, že kompilátor rozdělit metodu do dvou částí.</span><span class="sxs-lookup"><span data-stu-id="6a437-331">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="6a437-332">První část končí operací, která byla spuštěna asynchronně.</span><span class="sxs-lookup"><span data-stu-id="6a437-332">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="6a437-333">Druhá část je umístěn a callback metoda, která je volána po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="6a437-333">The second part is put into a callback method that's called when the operation completes.</span></span>

* <span data-ttu-id="6a437-334">`ToListAsync`je asynchronní verze metody `ToList` rozšíření.</span><span class="sxs-lookup"><span data-stu-id="6a437-334">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="6a437-335">Některé věci, které je třeba znát při psaní asynchronního kódu, který používá entity framework:</span><span class="sxs-lookup"><span data-stu-id="6a437-335">Some things to be aware of when you are writing asynchronous code that uses the Entity Framework:</span></span>

* <span data-ttu-id="6a437-336">Pouze příkazy, které způsobují dotazy nebo příkazy, které mají být odeslány do databáze jsou prováděny asynchronně.</span><span class="sxs-lookup"><span data-stu-id="6a437-336">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="6a437-337">To zahrnuje například `ToListAsync` `SingleOrDefaultAsync`, `SaveChangesAsync`, a .</span><span class="sxs-lookup"><span data-stu-id="6a437-337">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="6a437-338">Neobsahuje například příkazy, které pouze `IQueryable`mění , `var students = context.Students.Where(s => s.LastName == "Davolio")`například .</span><span class="sxs-lookup"><span data-stu-id="6a437-338">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>

* <span data-ttu-id="6a437-339">Kontext EF není bezpečné pro přístup z více vláken: nepokoušejte se provést více operací paralelně.</span><span class="sxs-lookup"><span data-stu-id="6a437-339">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="6a437-340">Při volání jakékoli asynchronní metody `await` EF vždy použijte klíčové slovo.</span><span class="sxs-lookup"><span data-stu-id="6a437-340">When you call any async EF method, always use the `await` keyword.</span></span>

* <span data-ttu-id="6a437-341">Pokud chcete využít výhod výkonu asynchronního kódu, ujistěte se, že všechny balíčky knihovny, které používáte (například pro stránkování), také použít async, pokud volají všechny entity Framework metody, které způsobují dotazy, které mají být odeslány do databáze.</span><span class="sxs-lookup"><span data-stu-id="6a437-341">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="6a437-342">Další informace o asynchronním programování v rozhraní .NET naleznete v [tématu Async Overview](/dotnet/articles/standard/async).</span><span class="sxs-lookup"><span data-stu-id="6a437-342">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="6a437-343">Získání kódu</span><span class="sxs-lookup"><span data-stu-id="6a437-343">Get the code</span></span>

[<span data-ttu-id="6a437-344">Stáhněte nebo zobrazte dokončenou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6a437-344">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="6a437-345">Další kroky</span><span class="sxs-lookup"><span data-stu-id="6a437-345">Next steps</span></span>

<span data-ttu-id="6a437-346">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="6a437-346">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6a437-347">Vytvořeno ASP.NET základní webové aplikace MVC</span><span class="sxs-lookup"><span data-stu-id="6a437-347">Created ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="6a437-348">Nastavení stylu webu</span><span class="sxs-lookup"><span data-stu-id="6a437-348">Set up the site style</span></span>
> * <span data-ttu-id="6a437-349">Informace o balíčcích EF Core NuGet</span><span class="sxs-lookup"><span data-stu-id="6a437-349">Learned about EF Core NuGet packages</span></span>
> * <span data-ttu-id="6a437-350">Vytvoření datového modelu</span><span class="sxs-lookup"><span data-stu-id="6a437-350">Created the data model</span></span>
> * <span data-ttu-id="6a437-351">Vytvoření kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="6a437-351">Created the database context</span></span>
> * <span data-ttu-id="6a437-352">Zaregistrováno SchoolContext</span><span class="sxs-lookup"><span data-stu-id="6a437-352">Registered the SchoolContext</span></span>
> * <span data-ttu-id="6a437-353">Inicializovaná DB s testovacími daty</span><span class="sxs-lookup"><span data-stu-id="6a437-353">Initialized DB with test data</span></span>
> * <span data-ttu-id="6a437-354">Vytvořený ovladač a zobrazení</span><span class="sxs-lookup"><span data-stu-id="6a437-354">Created controller and views</span></span>
> * <span data-ttu-id="6a437-355">Zobrazení databáze</span><span class="sxs-lookup"><span data-stu-id="6a437-355">Viewed the database</span></span>

<span data-ttu-id="6a437-356">V následujícím kurzu se dozvíte, jak provádět základní operace CRUD (vytvářet, číst, aktualizovat, odstraňovat).</span><span class="sxs-lookup"><span data-stu-id="6a437-356">In the following tutorial, you'll learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

<span data-ttu-id="6a437-357">Přejdete k dalšímu kurzu, kde se dozvíte, jak provádět základní operace CRUD (vytvářet, číst, aktualizovat, odstraňovat).</span><span class="sxs-lookup"><span data-stu-id="6a437-357">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="6a437-358">Implementace základních funkcí CRUD</span><span class="sxs-lookup"><span data-stu-id="6a437-358">Implement basic CRUD functionality</span></span>](crud.md)

