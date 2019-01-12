---
title: Stránky Razor pomocí Entity Framework Core v ASP.NET Core – kurz 1 z 8
author: rick-anderson
description: Ukazuje, jak vytvořit aplikaci pro stránky Razor pomocí Entity Framework Core
ms.author: riande
ms.custom: seodec18
ms.date: 11/22/2018
uid: data/ef-rp/intro
ms.openlocfilehash: 868163ed621ef9818759efd72ed3d233dc958219
ms.sourcegitcommit: ec71fd5a988f927ae301813aae5ff764feb3bb6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54249500"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="0d0dd-103">Stránky Razor pomocí Entity Framework Core v ASP.NET Core – kurz 1 z 8</span><span class="sxs-lookup"><span data-stu-id="0d0dd-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

[!INCLUDE[2.0 version](~/includes/RP-EF/20-pdf.md)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="0d0dd-104">Podle [Petr Dykstra](https://github.com/tdykstra) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0d0dd-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0d0dd-105">Contoso University ukázkovou webovou aplikaci ukazuje, jak vytvořit aplikaci ASP.NET Core Razor Pages pomocí Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-105">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="0d0dd-106">Ukázková aplikace je webovou stránku pro fiktivní společnosti Contoso University.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-106">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="0d0dd-107">Zahrnuje funkce, jako student přijetí, kurz vytvoření a přiřazení instruktorem.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-107">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="0d0dd-108">Tato stránka je první ze série kurzů, které vysvětlují, jak vytvořit ukázková aplikace Contoso University.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-108">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="0d0dd-109">Stažení nebo zobrazení dokončené aplikace.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-109">Download or view the completed app.</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="0d0dd-110">[Pokyny ke stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="0d0dd-110">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0d0dd-111">Požadavky</span><span class="sxs-lookup"><span data-stu-id="0d0dd-111">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0d0dd-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d0dd-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="0d0dd-113">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="0d0dd-113">.NET Core CLI</span></span>](#tab/netcore-cli)

[!INCLUDE [](~/includes/2.1-SDK.md)]

------

<span data-ttu-id="0d0dd-114">Znalost [stránky Razor](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="0d0dd-114">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="0d0dd-115">By se měla dokončit programátory [Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start) před zahájením této řady.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-115">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="0d0dd-116">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="0d0dd-116">Troubleshooting</span></span>

<span data-ttu-id="0d0dd-117">Pokud narazíte na problém nevyřešíte sami, můžete najít řešení obvykle porovnáním kódu [dokončený projekt](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="0d0dd-117">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="0d0dd-118">Je dobrým způsobem, jak získat pomoc tím, že publikuje dotaz do [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) pro [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="0d0dd-118">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="0d0dd-119">Webové aplikace Contoso University</span><span class="sxs-lookup"><span data-stu-id="0d0dd-119">The Contoso University web app</span></span>

<span data-ttu-id="0d0dd-120">Aplikace vytvořené v těchto kurzech je webová stránka základní university.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-120">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="0d0dd-121">Uživatelé mohou zobrazit a aktualizovat Všichni studenti, kurz a informace instruktorem.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-121">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="0d0dd-122">Tady je několik obrazovek vytvořili v kurzu.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-122">Here are a few of the screens created in the tutorial.</span></span>

![Studenti indexová stránka](intro/_static/students-index.png)

![Stránky pro úpravu studentů](intro/_static/student-edit.png)

<span data-ttu-id="0d0dd-125">Styl uživatelského rozhraní tohoto webu se blíží co je generována pomocí integrovaných šablon.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-125">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="0d0dd-126">Kurz zaměřuje se na EF Core se stránkami Razor, uživatelské rozhraní ne.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-126">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-razor-pages-web-app"></a><span data-ttu-id="0d0dd-127">Vytvoření webové aplikace stránky Razor ContosoUniversity</span><span class="sxs-lookup"><span data-stu-id="0d0dd-127">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0d0dd-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d0dd-128">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0d0dd-129">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-129">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0d0dd-130">Vytvořte novou webovou aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-130">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="0d0dd-131">Pojmenujte projekt **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-131">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="0d0dd-132">Je důležité projekt pojmenujte *ContosoUniversity* tak obory názvů případy, kdy kód je zkopírované a vložené.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-132">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="0d0dd-133">Vyberte **ASP.NET Core 2.1** v rozevíracím seznamu a pak vyberte **webovou aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-133">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="0d0dd-134">Obrázky v předchozích krocích, naleznete v tématu [vytvořit webová aplikace Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="0d0dd-134">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="0d0dd-135">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-135">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="0d0dd-136">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="0d0dd-136">.NET Core CLI</span></span>](#tab/netcore-cli)

```CLI
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

------

## <a name="set-up-the-site-style"></a><span data-ttu-id="0d0dd-137">Nastavit styl lokality</span><span class="sxs-lookup"><span data-stu-id="0d0dd-137">Set up the site style</span></span>

<span data-ttu-id="0d0dd-138">Několik změn nastavení v nabídce webu, rozložení a domovské stránky.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-138">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="0d0dd-139">Aktualizace *Pages/Shared/_Layout.cshtml* s následujícími změnami:</span><span class="sxs-lookup"><span data-stu-id="0d0dd-139">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="0d0dd-140">Změňte všechny výskyty "ContosoUniversity" na "University společnosti Contoso".</span><span class="sxs-lookup"><span data-stu-id="0d0dd-140">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="0d0dd-141">Existují tři výskyty.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-141">There are three occurrences.</span></span>

* <span data-ttu-id="0d0dd-142">Přidání položek nabídky **studenty**, **kurzy**, **Instruktoři**, a **oddělení**a odstranit **kontakt** položku nabídky.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-142">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="0d0dd-143">Změny jsou zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-143">The changes are highlighted.</span></span> <span data-ttu-id="0d0dd-144">(Všechny značky je *není* zobrazit.)</span><span class="sxs-lookup"><span data-stu-id="0d0dd-144">(All the markup is *not* displayed.)</span></span>

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="0d0dd-145">V *Pages/Index.cshtml*, nahraďte obsah souboru následující kód, který nahradí text o ASP.NET a MVC o této aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0d0dd-145">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="0d0dd-146">Vytvoření datového modelu</span><span class="sxs-lookup"><span data-stu-id="0d0dd-146">Create the data model</span></span>

<span data-ttu-id="0d0dd-147">Vytvoření tříd entit pro aplikaci Contoso University.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-147">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="0d0dd-148">Začněte s následující tři entity:</span><span class="sxs-lookup"><span data-stu-id="0d0dd-148">Start with the following three entities:</span></span>

![Kurz – registrace – studentech modelového diagramu](intro/_static/data-model-diagram.png)

<span data-ttu-id="0d0dd-150">Existuje vztah jeden mnoho mezi `Student` a `Enrollment` entity.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-150">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="0d0dd-151">Existuje vztah jeden mnoho mezi `Course` a `Enrollment` entity.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-151">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="0d0dd-152">Student může zaregistrovat libovolný počet kurzů.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-152">A student can enroll in any number of courses.</span></span> <span data-ttu-id="0d0dd-153">Kurz můžete mít libovolný počet studentů zaregistrovaná do něj.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-153">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="0d0dd-154">V následujících částech je vytvořená třída pro každou z těchto entit.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-154">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="0d0dd-155">Entita studenta</span><span class="sxs-lookup"><span data-stu-id="0d0dd-155">The Student entity</span></span>

![Diagram entity studenta](intro/_static/student-entity.png)

<span data-ttu-id="0d0dd-157">Vytvoření *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-157">Create a *Models* folder.</span></span> <span data-ttu-id="0d0dd-158">V *modely* složce vytvořte soubor třídy *Student.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="0d0dd-158">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="0d0dd-159">`ID` Vlastnost se stane sloupec primárního klíče tabulky databáze (databáze), který odpovídá této třídy.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-159">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="0d0dd-160">Ve výchozím nastavení interpretuje EF Core vlastnost s názvem `ID` nebo `classnameID` jako primární klíč.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-160">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="0d0dd-161">V `classnameID`, `classname` je název třídy.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-161">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="0d0dd-162">Alternativním automaticky rozpozná, primární klíč je `StudentID` v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-162">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="0d0dd-163">`Enrollments` Je vlastnost [navigační vlastnost](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="0d0dd-163">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="0d0dd-164">Vlastnosti navigace propojit s dalšími subjekty, které se vztahují k této entity.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-164">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="0d0dd-165">V takovém případě `Enrollments` vlastnost `Student entity` obsahuje všechny `Enrollment` entity, které se vztahují k, které `Student`.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-165">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="0d0dd-166">Například pokud Student řádků v databázi má dva související řádky registrace `Enrollments` navigační vlastnost obsahuje tyto dvě `Enrollment` entity.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-166">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="0d0dd-167">Se souvisejícím `Enrollment` řádek je řádek, který obsahuje hodnotu primárního klíče student získal v `StudentID` sloupce.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-167">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="0d0dd-168">Předpokládejme například, studenta s ID = 1 obsahuje dva řádky `Enrollment` tabulky.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-168">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="0d0dd-169">`Enrollment` Tabulka obsahuje dva řádky s `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-169">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="0d0dd-170">`StudentID` je cizí klíč v `Enrollment` tabulka, která určuje studentů v `Student` tabulky.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-170">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="0d0dd-171">Pokud vlastnost navigace může obsahovat více entit, navigační vlastnost jako musí být typu seznamu `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-171">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="0d0dd-172">`ICollection<T>` můžete zadat, nebo typu jako `List<T>` nebo `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-172">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="0d0dd-173">Když `ICollection<T>` je používají, vytvoří EF Core `HashSet<T>` kolekcí ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-173">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="0d0dd-174">Navigační vlastnosti, které obsahují více entit, pocházejí z many-to-many a jeden mnoho relací.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-174">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="0d0dd-175">Registrace entity</span><span class="sxs-lookup"><span data-stu-id="0d0dd-175">The Enrollment entity</span></span>

![Diagram entity registrace](intro/_static/enrollment-entity.png)

<span data-ttu-id="0d0dd-177">V *modely* složku, vytvořte *Enrollment.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="0d0dd-177">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="0d0dd-178">`EnrollmentID` Vlastnost představuje primární klíč.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-178">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="0d0dd-179">Tato entita používá `classnameID` vzorku místo `ID` stejně jako `Student` entity.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-179">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="0d0dd-180">Vývojáři obvykle zvolte jeden model a použít ho v rámci datového modelu.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-180">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="0d0dd-181">V pozdějších kurzech pomocí ID bez classname zobrazí zjednodušit implementaci dědičnosti v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-181">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="0d0dd-182">`Grade` Vlastnost je `enum`.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-182">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="0d0dd-183">Otazník po `Grade` deklarace typu znamená, že `Grade` vlastnost může mít hodnotu Null.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-183">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="0d0dd-184">Na podnikové úrovni, který má hodnotu null se liší od nulové třída – null znamená, že známku vyjádřenou není znám nebo ještě nebyly přiřazeny.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-184">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="0d0dd-185">`StudentID` Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Student`.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-185">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="0d0dd-186">`Enrollment` Entita je přidružený nejméně k jednomu `Student` entity, tak vlastnost obsahuje jediný `Student` entity.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-186">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="0d0dd-187">`Student` Entity se liší od `Student.Enrollments` navigační vlastnost, která obsahuje více `Enrollment` entity.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-187">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="0d0dd-188">`CourseID` Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Course`.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-188">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="0d0dd-189">`Enrollment` Entita je přidružený nejméně k jednomu `Course` entity.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-189">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="0d0dd-190">EF Core interpretuje vlastnost jako cizí klíč, pokud je název `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-190">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="0d0dd-191">Například`StudentID` pro `Student` navigační vlastnost, protože `Student` je primární klíč entity `ID`.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-191">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="0d0dd-192">Vlastnosti cizího klíče může mít také název `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-192">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="0d0dd-193">Například `CourseID` vzhledem k tomu, `Course` je primární klíč entity `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-193">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="0d0dd-194">Kurz entity</span><span class="sxs-lookup"><span data-stu-id="0d0dd-194">The Course entity</span></span>

![Diagram kurzu entity](intro/_static/course-entity.png)

<span data-ttu-id="0d0dd-196">V *modely* složku, vytvořte *Course.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="0d0dd-196">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="0d0dd-197">`Enrollments` Je navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-197">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="0d0dd-198">A `Course` entit může souviset s libovolným počtem `Enrollment` entity.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-198">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="0d0dd-199">`DatabaseGenerated` Atribut umožňuje aplikacím určit primární klíč, místo databáze s jeho vygenerování.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-199">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="0d0dd-200">Vygenerované uživatelské rozhraní modelu studenta</span><span class="sxs-lookup"><span data-stu-id="0d0dd-200">Scaffold the student model</span></span>

<span data-ttu-id="0d0dd-201">V této části je automaticky generovaný model studentů.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-201">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="0d0dd-202">To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model studentů.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-202">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="0d0dd-203">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-203">Build the project.</span></span>
* <span data-ttu-id="0d0dd-204">Vytvořte *stránek/studenty* složky.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-204">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0d0dd-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d0dd-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0d0dd-206">V **Průzkumníka řešení**, klikněte pravým tlačítkem na *stránek/studenty* složky > **přidat** > **novou vygenerovanou položku**.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-206">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0d0dd-207">V **přidat vygenerované uživatelské rozhraní** dialogového okna, vyberte **stránky Razor pomocí Entity Frameworku (CRUD)** > **přidat**.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-207">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="0d0dd-208">Dokončení **přidat stránky Razor pomocí Entity Frameworku (CRUD)** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="0d0dd-208">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="0d0dd-209">V **třída modelu** rozevíracího seznamu, vyberte **Student (ContosoUniversity.Models)**.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-209">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="0d0dd-210">V **třída kontextu dat** řádek, vyberte **+** (plus) podepsat a změňte vygenerovaný název, aby **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-210">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="0d0dd-211">V **třída kontextu dat** rozevíracího seznamu, vyberte **ContosoUniversity.Models.SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="0d0dd-211">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="0d0dd-212">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-212">Select **Add**.</span></span>

![Dialogové okno CRUD](intro/_static/s1.png)

<span data-ttu-id="0d0dd-214">Zobrazit [generování uživatelského rozhraní modelu film](xref:tutorials/razor-pages/model#scaffold-the-movie-model) Pokud máte potíže s předchozím kroku.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-214">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="0d0dd-215">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="0d0dd-215">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0d0dd-216">Spusťte následující příkazy k modelu studentů.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-216">Run the following commands to scaffold the student model.</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
```
------

<span data-ttu-id="0d0dd-217">Proces vygenerované uživatelské rozhraní vytvořit a změnit následující soubory:</span><span class="sxs-lookup"><span data-stu-id="0d0dd-217">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="0d0dd-218">Soubory vytvořené</span><span class="sxs-lookup"><span data-stu-id="0d0dd-218">Files created</span></span>

* <span data-ttu-id="0d0dd-219">*Stránky/studenty* vytvoření, odstranění, podrobností, úpravy, Index.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-219">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="0d0dd-220">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="0d0dd-220">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="0d0dd-221">Aktualizace souboru</span><span class="sxs-lookup"><span data-stu-id="0d0dd-221">File updates</span></span>

* <span data-ttu-id="0d0dd-222">*Startup.cs* : Změny tohoto souboru jsou podrobně popsané v další části.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-222">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="0d0dd-223">*appSettings.JSON* : Připojovací řetězec použitý pro připojení k místní databázi se přidá.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-223">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="0d0dd-224">Prozkoumání kontextu registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="0d0dd-224">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="0d0dd-225">ASP.NET Core využívá rozhraní [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0d0dd-225">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0d0dd-226">Služby (například kontext EF Core databáze) jsou registrované pomocí vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-226">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="0d0dd-227">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-227">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="0d0dd-228">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-228">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="0d0dd-229">Nástroj pro generování uživatelského rozhraní automaticky vytvoří kontext databáze a zaregistrovaného kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-229">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="0d0dd-230">Zkontrolujte `ConfigureServices` metoda ve *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-230">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="0d0dd-231">Zvýrazněný řádek byl přidán modulem scaffolder:</span><span class="sxs-lookup"><span data-stu-id="0d0dd-231">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="0d0dd-232">Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-232">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="0d0dd-233">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-233">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="0d0dd-234">Aktualizovat hlavní</span><span class="sxs-lookup"><span data-stu-id="0d0dd-234">Update main</span></span>

<span data-ttu-id="0d0dd-235">V *Program.cs*, změnit `Main` metoda můžete provádět následující:</span><span class="sxs-lookup"><span data-stu-id="0d0dd-235">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="0d0dd-236">Instance kontextu databáze získáte z kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-236">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="0d0dd-237">Volání [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="0d0dd-237">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="0d0dd-238">Uvolnění kontextu při `EnsureCreated` dokončení metody.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-238">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="0d0dd-239">Následující kód ukazuje aktualizovaný *Program.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-239">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="0d0dd-240">`EnsureCreated` zajišťuje, že existuje databáze pro daný kontext.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-240">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="0d0dd-241">Pokud existuje, nedojde k žádné akci.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-241">If it exists, no action is taken.</span></span> <span data-ttu-id="0d0dd-242">Pokud neexistuje, pak se vytvoří databázi a její schéma.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-242">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="0d0dd-243">`EnsureCreated` k vytvoření databáze nepoužívá migrace.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-243">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="0d0dd-244">Databázi, která se vytvoří s `EnsureCreated` nelze později aktualizovat pomocí migrace.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-244">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="0d0dd-245">`EnsureCreated` je volána při spuštění aplikace, která umožňuje následující pracovní postup:</span><span class="sxs-lookup"><span data-stu-id="0d0dd-245">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="0d0dd-246">Odstranění databáze.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-246">Delete the DB.</span></span>
* <span data-ttu-id="0d0dd-247">Změna schématu DB (například přidat `EmailAddress` pole).</span><span class="sxs-lookup"><span data-stu-id="0d0dd-247">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="0d0dd-248">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-248">Run the app.</span></span>
* <span data-ttu-id="0d0dd-249">`EnsureCreated` Databáze se vytvoří`EmailAddress` sloupce.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-249">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="0d0dd-250">`EnsureCreated` Pokud schéma je rychle se vyvíjejícími je vhodné v rané fázi vývoje.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-250">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="0d0dd-251">Později v tomto kurzu se odstraní databáze a používají se migrace.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-251">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="0d0dd-252">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="0d0dd-252">Test the app</span></span>

<span data-ttu-id="0d0dd-253">Spusťte aplikaci a přijmout zásady souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-253">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="0d0dd-254">Tato aplikace nemá uchovává osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-254">This app doesn't keep personal information.</span></span> <span data-ttu-id="0d0dd-255">Informace o souboru cookie zásadami [EU obecného Regulation (GDPR) podporu](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="0d0dd-255">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="0d0dd-256">Vyberte **studenty** propojení a potom **vytvořit nový**.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-256">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="0d0dd-257">Otestujte upravit, podrobnosti a odkazy odstranit.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-257">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="0d0dd-258">Prozkoumání kontextu SchoolContext DB</span><span class="sxs-lookup"><span data-stu-id="0d0dd-258">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="0d0dd-259">Hlavní třída, která koordinuje EF Core funkce pro daný datový model je třídy kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-259">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="0d0dd-260">Kontext dat je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="0d0dd-260">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="0d0dd-261">Kontext dat určuje entit, které jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-261">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="0d0dd-262">V tomto projektu je s názvem třídy `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-262">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="0d0dd-263">Aktualizace *SchoolContext.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="0d0dd-263">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="0d0dd-264">Zvýrazněný kód vytvoří [DbSet\<TEntity >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) vlastností pro každou sadu entit.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-264">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="0d0dd-265">V terminologii EF Core:</span><span class="sxs-lookup"><span data-stu-id="0d0dd-265">In EF Core terminology:</span></span>

* <span data-ttu-id="0d0dd-266">Obvykle sadu entit odpovídá Databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-266">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="0d0dd-267">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-267">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="0d0dd-268">`DbSet<Enrollment>` a `DbSet<Course>` může vynechat.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-268">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="0d0dd-269">EF Core je obsahuje implicitně protože `Student` odkazy na entity `Enrollment` entity a `Enrollment` odkazy na entity `Course` entity.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-269">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="0d0dd-270">Pro účely tohoto kurzu ponechte `DbSet<Enrollment>` a `DbSet<Course>` v `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-270">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="0d0dd-271">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="0d0dd-271">SQL Server Express LocalDB</span></span>

<span data-ttu-id="0d0dd-272">Připojovací řetězec Určuje [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="0d0dd-272">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="0d0dd-273">LocalDB je Odlehčená verze SQL serveru Express Database Engine a je určená pro vývoj aplikací, není použití v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-273">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="0d0dd-274">LocalDB spustí na vyžádání a běží v uživatelském režimu, takže není bez složité konfigurace.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-274">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="0d0dd-275">Ve výchozím nastavení LocalDB vytvoří *.mdf* DB soubory `C:/Users/<user>` adresáře.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-275">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="0d0dd-276">Přidejte kód pro inicializaci databáze se testovací data</span><span class="sxs-lookup"><span data-stu-id="0d0dd-276">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="0d0dd-277">EF Core vytvoří prázdná databáze.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-277">EF Core creates an empty DB.</span></span> <span data-ttu-id="0d0dd-278">V této části `Initialize` metoda je zapsána do naplnit ho daty testu.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-278">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="0d0dd-279">V *Data* složku, vytvořte nový soubor třídy *DbInitializer.cs* a přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="0d0dd-279">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="0d0dd-280">Poznámka: Předchozí kód používá `Models` pro obor názvů (`namespace ContosoUniversity.Models`) spíše než `Data`.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-280">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="0d0dd-281">`Models` je v souladu s generované generátor kódu.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-281">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="0d0dd-282">Další informace najdete v tématu [tento problém generování uživatelského rozhraní Githubu](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="0d0dd-282">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="0d0dd-283">Kód kontroluje, jestli na databáze nejsou všechny studenty.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-283">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="0d0dd-284">Pokud v databázi nejsou žádní studenti, databáze je inicializována s testovací data.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-284">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="0d0dd-285">Načte testovací data do pole spíše než `List<T>` kolekce za účelem optimalizace výkonu.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-285">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="0d0dd-286">`EnsureCreated` Metoda automaticky vytvoří databáze pro kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-286">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="0d0dd-287">Pokud existuje databáze `EnsureCreated` vrátí beze změny databáze.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-287">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="0d0dd-288">V *Program.cs*, upravte `Main` metodu chce volat `Initialize`:</span><span class="sxs-lookup"><span data-stu-id="0d0dd-288">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

<span data-ttu-id="0d0dd-289">Odstranit záznamy, které studenty a restartujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-289">Delete any student records and restart the app.</span></span> <span data-ttu-id="0d0dd-290">Pokud databáze není inicializován, nastavte zarážky `Initialize` a Diagnostikujte problém.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-290">If the DB is not initialized, set a break point in `Initialize` to diagnose the problem.</span></span>

## <a name="view-the-db"></a><span data-ttu-id="0d0dd-291">Zobrazení databáze</span><span class="sxs-lookup"><span data-stu-id="0d0dd-291">View the DB</span></span>

<span data-ttu-id="0d0dd-292">Otevřít **Průzkumník objektů systému SQL Server** (SSOX) z **zobrazení** nabídky v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-292">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="0d0dd-293">V SSOX, klikněte na tlačítko **\MSSQLLocalDB (localdb) > databáze > ContosoUniversity1**.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-293">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > ContosoUniversity1**.</span></span>

<span data-ttu-id="0d0dd-294">Rozbalte **tabulky** uzlu.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-294">Expand the **Tables** node.</span></span>

<span data-ttu-id="0d0dd-295">Klikněte pravým tlačítkem na **Student** tabulky a klikněte na tlačítko **Data zobrazení** vytvořené sloupce a řádky vložené do tabulky.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-295">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="0d0dd-296">Asynchronní kód</span><span class="sxs-lookup"><span data-stu-id="0d0dd-296">Asynchronous code</span></span>

<span data-ttu-id="0d0dd-297">Asynchronní programování je výchozím režimem pro ASP.NET Core a EF Core.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-297">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="0d0dd-298">Webový server má omezený počet vláken, které jsou k dispozici, a v situacích, vysokého zatížení všech dostupných vláken může být používán.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-298">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="0d0dd-299">Pokud k tomu dojde, server nemůže zpracovat nové žádosti, dokud se uvolnit vlákna.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-299">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="0d0dd-300">Přestože se nejedná skutečně každé dílo vzhledem k tomu, že čekání na vstupně-výstupních operací na dokončení, může kódem synchronní svázané několika vlákny.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-300">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="0d0dd-301">Asynchronní kód když proces čeká na vstupně-výstupních operací na dokončení, je jeho vlákno uvolněn pro server určený pro zpracováním jiných požadavků.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-301">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="0d0dd-302">V důsledku toho asynchronního kódu umožňuje prostředky serveru použije efektivněji a aby zvládla větší provoz bez zpoždění je povoleno na serveru.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-302">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="0d0dd-303">Asynchronní kód zavést malé množství režie v době běhu.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-303">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="0d0dd-304">V situacích s nízkým provozem stiskněte výkonu je zanedbatelný, při vysoké návštěvnosti situacích, potenciálních zlepšení výkonu je důležitým.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-304">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="0d0dd-305">V následujícím kódu [asynchronní](/dotnet/csharp/language-reference/keywords/async) – klíčové slovo, `Task<T>` návratovou hodnotu, `await` – klíčové slovo, a `ToListAsync` metoda změňte kód spustit asynchronně.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-305">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="0d0dd-306">`async` – Klíčové slovo instruuje kompilátor, aby:</span><span class="sxs-lookup"><span data-stu-id="0d0dd-306">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="0d0dd-307">Generovat zpětná volání pro části tělo metody.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-307">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="0d0dd-308">Automaticky vytvořit [úloh](/dotnet/api/system.threading.tasks.task) vráceného objektu.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-308">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="0d0dd-309">Další informace najdete v tématu [návratový typ úkolu](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="0d0dd-309">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="0d0dd-310">Implicitní návratový typ `Task` představuje probíhající práci.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-310">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="0d0dd-311">`await` – Klíčové slovo způsobí, že kompilátor metodu rozdělit do dvou částí.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-311">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="0d0dd-312">První část končí operace, která se spustí asynchronně.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-312">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="0d0dd-313">Druhá část je nepoužili metodu zpětného volání, která je volána po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-313">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="0d0dd-314">`ToListAsync` je asynchronní verze `ToList` – metoda rozšíření.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-314">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="0d0dd-315">Některé co je potřeba mít na paměti při zápisu asynchronního kódu, který používá EF Core:</span><span class="sxs-lookup"><span data-stu-id="0d0dd-315">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="0d0dd-316">Jenom příkazy, které způsobují dotazy nebo příkazy k odeslání do databáze se provedl asynchronně.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-316">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="0d0dd-317">Který obsahuje `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, a `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-317">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="0d0dd-318">Neměl by zahrnovat příkazy, které stačí změnit `IQueryable`, jako například `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-318">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="0d0dd-319">Objekt context EF Core není bezpečné pro vlákna: nedoporučujeme provádět více operací paralelně.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-319">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="0d0dd-320">Abyste mohli využívat výkony těží z asynchronní kód, ověřte, že balíčků knihovny (například pro stránkování) používat asynchronní, pokud volání metody EF Core, které odesílání dotazů do databáze.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-320">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="0d0dd-321">Další informace o asynchronním programování v rozhraní .NET najdete v tématu [asynchronní přehled](/dotnet/standard/async) a [asynchronní programování pomocí modifikátoru async a operátoru await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="0d0dd-321">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="0d0dd-322">V dalším kurzu, základní CRUD (vytváření, čtení, aktualizace nebo odstranění) jsou zkoumány operace.</span><span class="sxs-lookup"><span data-stu-id="0d0dd-322">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>

::: moniker-end

> [!div class="step-by-step"]
> [<span data-ttu-id="0d0dd-323">Next</span><span class="sxs-lookup"><span data-stu-id="0d0dd-323">Next</span></span>](xref:data/ef-rp/crud)
