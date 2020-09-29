---
title: Razor Stránky s Entity Framework Core v ASP.NET Core – kurz 1 z 8
author: rick-anderson
description: Ukazuje, jak vytvořit Razor aplikaci Pages pomocí Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/intro
ms.openlocfilehash: 35a5758500ae2bc691c8d08eccb22340f9998c39
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424274"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="0cd70-103">Razor Stránky s Entity Framework Core v ASP.NET Core – kurz 1 z 8</span><span class="sxs-lookup"><span data-stu-id="0cd70-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="0cd70-104">[Dykstra](https://github.com/tdykstra) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0cd70-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0cd70-105">Toto je první v sérii kurzů, které ukazují, jak používat Entity Framework (EF) jádro v aplikaci [ASP.NET Core Razor Pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="0cd70-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="0cd70-106">Kurzy vytvářejí web pro fiktivní univerzitě společnosti Contoso.</span><span class="sxs-lookup"><span data-stu-id="0cd70-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="0cd70-107">Tato lokalita obsahuje funkce, jako je například využití studenta, vytváření kurzů a přiřazení instruktorů.</span><span class="sxs-lookup"><span data-stu-id="0cd70-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="0cd70-108">Kurz používá první přístup ke kódu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="0cd70-109">Informace o tomto kurzu s použitím databáze prvního přístupu najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="0cd70-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="0cd70-110">Stažení nebo zobrazení dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="0cd70-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="0cd70-111">[Pokyny ke stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="0cd70-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0cd70-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="0cd70-112">Prerequisites</span></span>

* <span data-ttu-id="0cd70-113">Pokud se Razor stránkami teprve začínáte, Projděte si řadu kurzů [Začínáme se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start) , než začnete s tímto.</span><span class="sxs-lookup"><span data-stu-id="0cd70-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cd70-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cd70-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cd70-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cd70-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="0cd70-116">Databázové moduly</span><span class="sxs-lookup"><span data-stu-id="0cd70-116">Database engines</span></span>

<span data-ttu-id="0cd70-117">Pokyny pro Visual Studio používají [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), verzi SQL Server Express, která se spouští jenom v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="0cd70-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="0cd70-118">Pokyny pro [Visual Studio Code používají nástroj](https://www.sqlite.org/)pro databázový stroj pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="0cd70-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="0cd70-119">Pokud se rozhodnete použít SQLite, Stáhněte a nainstalujte nástroj třetí strany pro správu a zobrazení databáze SQLite, jako je například [prohlížeč databáze pro SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="0cd70-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="0cd70-120">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="0cd70-120">Troubleshooting</span></span>

<span data-ttu-id="0cd70-121">Pokud narazíte na problém, který nemůžete vyřešit, porovnejte kód s [dokončeným projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="0cd70-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="0cd70-122">Dobrý způsob, jak získat pomoc, je odeslání otázky do StackOverflow.com s použitím [značky ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [značky EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="0cd70-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="0cd70-123">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="0cd70-123">The sample app</span></span>

<span data-ttu-id="0cd70-124">Aplikace sestavená v těchto kurzech je základním webem na univerzitě.</span><span class="sxs-lookup"><span data-stu-id="0cd70-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="0cd70-125">Uživatelé můžou zobrazit a aktualizovat informace o studentech, kurzech a instruktorech.</span><span class="sxs-lookup"><span data-stu-id="0cd70-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="0cd70-126">Tady je několik obrazovek vytvořených v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-126">Here are a few of the screens created in the tutorial.</span></span>

![Stránka indexu studentů](intro/_static/students-index30.png)

![Stránka pro úpravy studentů](intro/_static/student-edit30.png)

<span data-ttu-id="0cd70-129">Styl uživatelského rozhraní tohoto webu je založen na předdefinovaných šablonách projektů.</span><span class="sxs-lookup"><span data-stu-id="0cd70-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="0cd70-130">V tomto kurzu se naučíte používat EF Core s ASP.NET Core, nikoli jak přizpůsobit uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0cd70-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

<!-- 
Follow the link at the top of the page to get the source code for the completed project. The *cu50* folder has the code for the ASP.NET Core 5.0 version of the tutorial. Files that reflect the state of the code for tutorials 1-7 can be found in the *cu50snapshots* folder.

# [Visual Studio](#tab/visual-studio)

To run the app after downloading the completed project:

* Build the project.
* In Package Manager Console (PMC) run the following command:

  ```powershell
  Update-Database
  ```

* Run the project to seed the database.

# [Visual Studio Code](#tab/visual-studio-code)

To run the app after downloading the completed project:

* In *Program.cs*, remove the comments from `// webBuilder.UseStartup<StartupSQLite>();`  so `StartupSQLite` is used.
* Copy the contents of *appSettingsSQLite.json* into *appSettings.json*.
* Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.
* Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.
* Build the project.
* At a command prompt in the project folder, run the following commands:

  ```dotnetcli
  dotnet tool install --global dotnet-ef -v 5.0.0-*
  dotnet ef database update
  ```

* In your SQLite tool, run the following SQL statement:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Run the project to seed the database.

---

-->

## <a name="create-the-web-app-project"></a><span data-ttu-id="0cd70-131">Vytvoření projektu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="0cd70-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cd70-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cd70-132">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0cd70-133">V nabídce **soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-133">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0cd70-134">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-134">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="0cd70-135">Pojmenujte projekt *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="0cd70-135">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="0cd70-136">Je důležité použít tento přesný název, včetně velkých a malých písmen, aby se obory názvů shodovaly při zkopírování a vložení kódu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-136">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="0cd70-137">V rozevíracích seznamech vyberte **.NET Core** a **ASP.NET Core 5,0** a potom vyberte **Webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-137">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cd70-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cd70-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0cd70-139">V terminálu přejděte do složky, ve které by měla být vytvořena složka projektu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-139">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="0cd70-140">Spuštěním následujících příkazů vytvořte Razor projekt stránky a `cd` do nové složky projektu:</span><span class="sxs-lookup"><span data-stu-id="0cd70-140">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="0cd70-141">Nastavení stylu webu</span><span class="sxs-lookup"><span data-stu-id="0cd70-141">Set up the site style</span></span>

<span data-ttu-id="0cd70-142">Zkopírujte a vložte následující kód do souboru *Pages/Shared/_Layout. cshtml* : [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span><span class="sxs-lookup"><span data-stu-id="0cd70-142">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span></span>

<span data-ttu-id="0cd70-143">Soubor rozložení nastaví záhlaví, zápatí a nabídku webu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-143">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="0cd70-144">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="0cd70-144">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="0cd70-145">Každý výskyt "ContosoUniversity" na "contoso University".</span><span class="sxs-lookup"><span data-stu-id="0cd70-145">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="0cd70-146">Existují tři výskyty.</span><span class="sxs-lookup"><span data-stu-id="0cd70-146">There are three occurrences.</span></span>
* <span data-ttu-id="0cd70-147">Položky nabídky **Domů** a **Ochrana osobních údajů** jsou odstraněny.</span><span class="sxs-lookup"><span data-stu-id="0cd70-147">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="0cd70-148">Přidávají se položky pro **informace o** **studentech**, **kurzech**, **instruktorech**a **odděleních**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-148">Entries are added for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="0cd70-149">Na *stránce pages/index. cshtml*nahraďte obsah souboru následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="0cd70-149">In *Pages/Index.cshtml*, replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="0cd70-150">Předchozí kód nahrazuje text o ASP.NET Core textem o této aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0cd70-150">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="0cd70-151">Spusťte aplikaci, abyste ověřili, že se zobrazí domovská stránka.</span><span class="sxs-lookup"><span data-stu-id="0cd70-151">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="0cd70-152">Datový model</span><span class="sxs-lookup"><span data-stu-id="0cd70-152">The data model</span></span>

<span data-ttu-id="0cd70-153">V následujících částech se vytvoří datový model:</span><span class="sxs-lookup"><span data-stu-id="0cd70-153">The following sections create a data model:</span></span>

![Kurz – registrace – diagram datového modelu studenta](intro/_static/data-model-diagram.png)

<span data-ttu-id="0cd70-155">Student se může zaregistrovat v jakémkoli počtu kurzů a kurz může mít zaregistrovaný libovolný počet studentů.</span><span class="sxs-lookup"><span data-stu-id="0cd70-155">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="0cd70-156">Entita studenta</span><span class="sxs-lookup"><span data-stu-id="0cd70-156">The Student entity</span></span>

![Diagram entity studenta](intro/_static/student-entity.png)

* <span data-ttu-id="0cd70-158">Vytvořte složku *modely* ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-158">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="0cd70-159">Vytvořte *modely/studenta. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="0cd70-159">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="0cd70-160">`ID`Vlastnost se zobrazí jako sloupec primárního klíče tabulky databáze, která odpovídá této třídě.</span><span class="sxs-lookup"><span data-stu-id="0cd70-160">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="0cd70-161">Ve výchozím nastavení EF Core interpretuje vlastnost s názvem `ID` nebo `classnameID` jako primární klíč.</span><span class="sxs-lookup"><span data-stu-id="0cd70-161">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="0cd70-162">Proto je alternativní automaticky rozpoznaný název pro `Student` primární klíč třídy `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-162">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="0cd70-163">Další informace najdete v tématu [EF Core-Keys](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="0cd70-163">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="0cd70-164">`Enrollments`Vlastnost je [navigační vlastnost](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="0cd70-164">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="0cd70-165">Navigační vlastnosti obsahují další entity, které se vztahují k této entitě.</span><span class="sxs-lookup"><span data-stu-id="0cd70-165">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="0cd70-166">V takovém případě `Enrollments` vlastnost `Student` entity obsahuje všechny `Enrollment` entity, které se vztahují k danému studentovi.</span><span class="sxs-lookup"><span data-stu-id="0cd70-166">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="0cd70-167">Například pokud má řádek studenta v databázi dva související řádky registrace, `Enrollments` navigační vlastnost obsahuje tyto dvě entity registrace.</span><span class="sxs-lookup"><span data-stu-id="0cd70-167">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="0cd70-168">V databázi se řádek registrace vztahuje k řádku studenta, pokud jeho sloupec StudentID obsahuje hodnotu ID studenta.</span><span class="sxs-lookup"><span data-stu-id="0cd70-168">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="0cd70-169">Předpokládejme například, že řádek studenta má ID = 1.</span><span class="sxs-lookup"><span data-stu-id="0cd70-169">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="0cd70-170">Související řádky registrace budou mít StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="0cd70-170">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="0cd70-171">StudentID je *cizí klíč* v tabulce zápisu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-171">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="0cd70-172">`Enrollments`Vlastnost je definována tak, `ICollection<Enrollment>` že může existovat více souvisejících entit zápisu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-172">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="0cd70-173">Můžete použít jiné typy kolekce, například `List<Enrollment>` nebo `HashSet<Enrollment>` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-173">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="0cd70-174">Při `ICollection<Enrollment>` použití EF Core vytvoří `HashSet<Enrollment>` ve výchozím nastavení kolekci.</span><span class="sxs-lookup"><span data-stu-id="0cd70-174">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="0cd70-175">Entita registrace</span><span class="sxs-lookup"><span data-stu-id="0cd70-175">The Enrollment entity</span></span>

![Diagram entity registrace](intro/_static/enrollment-entity.png)

<span data-ttu-id="0cd70-177">Vytvořte *modely/registrace. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="0cd70-177">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="0cd70-178">`EnrollmentID`Vlastnost je primární klíč. Tato entita používá `classnameID` vzor namísto `ID` samotného.</span><span class="sxs-lookup"><span data-stu-id="0cd70-178">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="0cd70-179">Pro model produkčních dat vyberte jeden vzor a používejte ho konzistentně.</span><span class="sxs-lookup"><span data-stu-id="0cd70-179">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="0cd70-180">Tento kurz používá pouze k ilustraci toho, jak funguje.</span><span class="sxs-lookup"><span data-stu-id="0cd70-180">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="0cd70-181">Použití `ID` bez toho usnadňuje `classname` implementaci některých druhů změn datového modelu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-181">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="0cd70-182">`Grade`Vlastnost je `enum` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-182">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="0cd70-183">Otazník po `Grade` deklaraci typu označuje, že vlastnost může `Grade` [mít hodnotu null](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="0cd70-183">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="0cd70-184">Třídu, která má hodnotu null, se liší od nulové třídy &mdash; null znamená, že známka není známa nebo ještě nebyla přiřazena.</span><span class="sxs-lookup"><span data-stu-id="0cd70-184">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="0cd70-185">`StudentID`Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Student` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-185">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="0cd70-186">`Enrollment`Entita je přidružená k jedné `Student` entitě, takže vlastnost obsahuje jednu `Student` entitu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-186">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="0cd70-187">`CourseID`Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Course` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-187">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="0cd70-188">`Enrollment`Entita je přidružená k jedné `Course` entitě.</span><span class="sxs-lookup"><span data-stu-id="0cd70-188">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="0cd70-189">EF Core interpretuje vlastnost jako cizí klíč, pokud má název `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-189">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="0cd70-190">Například `StudentID` je cizí klíč pro `Student` navigační vlastnost, protože `Student` primární klíč entity je `ID` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-190">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="0cd70-191">Lze také pojmenovat vlastnosti cizího klíče `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-191">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="0cd70-192">Například `CourseID` vzhledem k tomu, že `Course` primární klíč entity je `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-192">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="0cd70-193">Entita kurzu</span><span class="sxs-lookup"><span data-stu-id="0cd70-193">The Course entity</span></span>

![Diagram entity kurzu](intro/_static/course-entity.png)

<span data-ttu-id="0cd70-195">Vytvořte *modely/Course. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="0cd70-195">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="0cd70-196">`Enrollments`Vlastnost je navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="0cd70-196">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="0cd70-197">`Course`Entita může souviset s libovolným počtem `Enrollment` entit.</span><span class="sxs-lookup"><span data-stu-id="0cd70-197">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="0cd70-198">`DatabaseGenerated`Atribut umožňuje aplikaci určit primární klíč místo toho, aby ho databáze vygenerovala.</span><span class="sxs-lookup"><span data-stu-id="0cd70-198">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="0cd70-199">Sestavte projekt, aby se ověřilo, že nejsou k dispozici žádné chyby kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="0cd70-199">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="0cd70-200">Stránky studenta pro generování uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="0cd70-200">Scaffold Student pages</span></span>

<span data-ttu-id="0cd70-201">V této části použijete nástroj ASP.NET Core pro generování uživatelského rozhraní k vygenerování:</span><span class="sxs-lookup"><span data-stu-id="0cd70-201">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="0cd70-202">Třída EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-202">An EF Core `DbContext` class.</span></span> <span data-ttu-id="0cd70-203">Kontext je hlavní třída, která koordinuje funkce Entity Framework pro daný datový model.</span><span class="sxs-lookup"><span data-stu-id="0cd70-203">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="0cd70-204">Je odvozen z <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> třídy.</span><span class="sxs-lookup"><span data-stu-id="0cd70-204">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="0cd70-205">Razor stránky, které zpracovávají operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro danou `Student` entitu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-205">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cd70-206">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cd70-206">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0cd70-207">Vytvořte složku *stránky/studenty* .</span><span class="sxs-lookup"><span data-stu-id="0cd70-207">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="0cd70-208">V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *Pages/Students* a vyberte **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-208">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0cd70-209">V dialogovém okně **Přidat novou položku uživatelského rozhraní** :</span><span class="sxs-lookup"><span data-stu-id="0cd70-209">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="0cd70-210">Na levé kartě vyberte **nainstalované > společné > Razor stránky** .</span><span class="sxs-lookup"><span data-stu-id="0cd70-210">In the left tab, select **Installed > Common > Razor Pages**</span></span>
  * <span data-ttu-id="0cd70-211">Vyberte \*\* Razor stránky pomocí Entity Framework (CRUD)\*\* > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-211">Select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="0cd70-212">V dialogovém okně **Přidat Razor stránky pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="0cd70-212">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="0cd70-213">V rozevíracím seznamu **třída modelu** vyberte **student (ContosoUniversity. Models)**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-213">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="0cd70-214">V řádku **třídy kontextu dat** vyberte **+** znaménko (plus).</span><span class="sxs-lookup"><span data-stu-id="0cd70-214">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="0cd70-215">Změňte název kontextu dat na End `SchoolContext` místo `ContosoUniversityContext` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-215">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="0cd70-216">Aktualizovaný název kontextu: `ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="0cd70-216">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="0cd70-217">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-217">Select **Add**.</span></span>

<span data-ttu-id="0cd70-218">Automaticky se nainstalují tyto balíčky:</span><span class="sxs-lookup"><span data-stu-id="0cd70-218">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cd70-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cd70-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0cd70-220">Spuštěním následujících příkazů .NET Core CLI nainstalujte požadované balíčky NuGet:</span><span class="sxs-lookup"><span data-stu-id="0cd70-220">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="0cd70-221">Pro generování uživatelského rozhraní je vyžadován balíček Microsoft. VisualStudio. Web. strategii. Design.</span><span class="sxs-lookup"><span data-stu-id="0cd70-221">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="0cd70-222">I když aplikace nebude používat SQL Server, nástroj pro generování uživatelského rozhraní potřebuje balíček SQL Server.</span><span class="sxs-lookup"><span data-stu-id="0cd70-222">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="0cd70-223">Vytvořte složku *stránky/studenty* .</span><span class="sxs-lookup"><span data-stu-id="0cd70-223">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="0cd70-224">Spusťte následující příkaz pro instalaci nástroje pro [generování uživatelského rozhraní ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="0cd70-224">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="0cd70-225">Spusťte následující příkaz pro generování uživatelského rozhraní stránek studenta.</span><span class="sxs-lookup"><span data-stu-id="0cd70-225">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="0cd70-226">**V systému Windows**</span><span class="sxs-lookup"><span data-stu-id="0cd70-226">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="0cd70-227">**V systému macOS nebo Linux**</span><span class="sxs-lookup"><span data-stu-id="0cd70-227">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="0cd70-228">Pokud předchozí krok neproběhne úspěšně, sestavte projekt a opakujte krok generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0cd70-228">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="0cd70-229">Proces generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="0cd70-229">The scaffolding process:</span></span>

* <span data-ttu-id="0cd70-230">Vytvoří Razor stránky ve složce *Pages/Students* :</span><span class="sxs-lookup"><span data-stu-id="0cd70-230">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="0cd70-231">*Vytvoření. cshtml* a *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0cd70-231">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="0cd70-232">*Odstranění. cshtml* a *DELETE.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0cd70-232">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="0cd70-233">*Podrobnosti. cshtml* a *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0cd70-233">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="0cd70-234">*Upravit. cshtml* a *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0cd70-234">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="0cd70-235">*Index. cshtml* a *index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0cd70-235">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="0cd70-236">Vytvoří *data/SchoolContext. cs*.</span><span class="sxs-lookup"><span data-stu-id="0cd70-236">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="0cd70-237">Přidá kontext do injektáže závislosti v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="0cd70-237">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="0cd70-238">Přidá připojovací řetězec databáze, do kterého se má *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="0cd70-238">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="0cd70-239">Připojovací řetězec databáze</span><span class="sxs-lookup"><span data-stu-id="0cd70-239">Database connection string</span></span>

<span data-ttu-id="0cd70-240">Nástroj pro generování uživatelského rozhraní generuje připojovací řetězec v souboru *appsettings.js* .</span><span class="sxs-lookup"><span data-stu-id="0cd70-240">The scaffolding tool generates a connection string in the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cd70-241">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cd70-241">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0cd70-242">Připojovací řetězec Určuje [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span><span class="sxs-lookup"><span data-stu-id="0cd70-242">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

<span data-ttu-id="0cd70-243">LocalDB je zjednodušená verze databázového stroje SQL Server Express a je určena pro vývoj aplikací, nikoli za použití v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="0cd70-243">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="0cd70-244">Ve výchozím nastavení LocalDB vytvoří soubory *. mdf* v `C:/Users/<user>` adresáři.</span><span class="sxs-lookup"><span data-stu-id="0cd70-244">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cd70-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cd70-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0cd70-246">Zkraťte připojovací řetězec SQLite na *cu. DB*:</span><span class="sxs-lookup"><span data-stu-id="0cd70-246">Shorten the SQLite connection string to *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="0cd70-247">Aktualizuje třídu kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="0cd70-247">Update the database context class</span></span>

<span data-ttu-id="0cd70-248">Hlavní třída, která koordinuje funkce EF Core pro daný datový model, je třída kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="0cd70-248">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="0cd70-249">Kontext je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="0cd70-249">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="0cd70-250">Kontext určuje, které entity jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-250">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="0cd70-251">V tomto projektu je třída pojmenována `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-251">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="0cd70-252">Aktualizujte *data/SchoolContext. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="0cd70-252">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="0cd70-253">Předchozí kód se změní z čísla v čísle na číslo v `DbSet<Student> Student` množném čísle `DbSet<Student> Students` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-253">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="0cd70-254">Chcete-li Razor , aby kód stránky odpovídal novému `DBSet` názvu, proveďte globální změnu z: `_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="0cd70-254">To make the Razor Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="0cd70-255">schopn `_context.Students.`</span><span class="sxs-lookup"><span data-stu-id="0cd70-255">to: `_context.Students.`</span></span>

<span data-ttu-id="0cd70-256">K dispozici je 8 výskytů.</span><span class="sxs-lookup"><span data-stu-id="0cd70-256">There are 8 occurrences.</span></span>

<span data-ttu-id="0cd70-257">Vzhledem k tomu, že sada entit obsahuje více entit, mnoho vývojářů `DBSet` dává přednost názvům vlastností plural.</span><span class="sxs-lookup"><span data-stu-id="0cd70-257">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="0cd70-258">Zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="0cd70-258">The highlighted code:</span></span>

* <span data-ttu-id="0cd70-259">Vytvoří vlastnost [negenerickými \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro každou sadu entit.</span><span class="sxs-lookup"><span data-stu-id="0cd70-259">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="0cd70-260">V terminologii EF Core:</span><span class="sxs-lookup"><span data-stu-id="0cd70-260">In EF Core terminology:</span></span>
  * <span data-ttu-id="0cd70-261">Sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="0cd70-261">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="0cd70-262">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="0cd70-262">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="0cd70-263">Volání <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .</span><span class="sxs-lookup"><span data-stu-id="0cd70-263">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="0cd70-264">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="0cd70-264">`OnModelCreating`:</span></span>
  * <span data-ttu-id="0cd70-265">Se volá, když se `SchoolContext` inicializuje, ale předtím, než se model zamkne a použije se k inicializaci kontextu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-265">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="0cd70-266">Je vyžadováno, protože v tomto kurzu `Student` bude entita obsahovat odkazy na jiné entity.</span><span class="sxs-lookup"><span data-stu-id="0cd70-266">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="0cd70-267">Sestavte projekt, aby se ověřilo, že nedošlo k chybám kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="0cd70-267">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="0cd70-268">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="0cd70-268">Startup.cs</span></span>

<span data-ttu-id="0cd70-269">ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0cd70-269">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0cd70-270">Služby, jako `SchoolContext` jsou zaregistrované u injektáže závislosti při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="0cd70-270">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="0cd70-271">Komponenty, které vyžadují tyto služby, jako Razor jsou stránky, poskytují tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="0cd70-271">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="0cd70-272">Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-272">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="0cd70-273">Nástroj pro generování uživatelského rozhraní automaticky zaregistroval třídu kontextu pomocí kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="0cd70-273">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cd70-274">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cd70-274">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0cd70-275">Do generátoru byly přidány následující zvýrazněné řádky:</span><span class="sxs-lookup"><span data-stu-id="0cd70-275">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cd70-276">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cd70-276">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0cd70-277">Ověřte kód přidaný voláním generování uživatelského rozhraní `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-277">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="0cd70-278">Informace o použití provozní databáze najdete v tématu [použití SQLite pro vývoj, SQL Server pro produkční](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) prostředí.</span><span class="sxs-lookup"><span data-stu-id="0cd70-278">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="0cd70-279">Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="0cd70-279">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="0cd70-280">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) přečte připojovací řetězec z *appsettings.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="0cd70-280">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="0cd70-281">Přidat filtr výjimky databáze</span><span class="sxs-lookup"><span data-stu-id="0cd70-281">Add the database exception filter</span></span>

<span data-ttu-id="0cd70-282">Přidejte `AddDatabaseDeveloperPageExceptionFilter` do `ConfigureServices` , jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="0cd70-282">Add `AddDatabaseDeveloperPageExceptionFilter` to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cd70-283">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cd70-283">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="0cd70-284">Přidejte balíček NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) .</span><span class="sxs-lookup"><span data-stu-id="0cd70-284">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="0cd70-285">Do PMC zadejte následující příkaz pro přidání balíčku NuGet:</span><span class="sxs-lookup"><span data-stu-id="0cd70-285">In the PMC, enter the following command to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.1.20451.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cd70-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cd70-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="0cd70-287">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore`Balíček NuGet poskytuje ASP.NET Core middlewaru pro Entity Framework Core chybové stránky.</span><span class="sxs-lookup"><span data-stu-id="0cd70-287">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="0cd70-288">Tento middleware pomáhá detekovat a diagnostikovat chyby pomocí Entity Framework Core migrace.</span><span class="sxs-lookup"><span data-stu-id="0cd70-288">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="0cd70-289">Vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="0cd70-289">Create the database</span></span>

<span data-ttu-id="0cd70-290">Aktualizujte *program.cs* , aby se vytvořila databáze, pokud neexistuje:</span><span class="sxs-lookup"><span data-stu-id="0cd70-290">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="0cd70-291">Metoda [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) neprovede žádnou akci, pokud existuje databáze pro kontext.</span><span class="sxs-lookup"><span data-stu-id="0cd70-291">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="0cd70-292">Pokud žádná databáze neexistuje, vytvoří databázi a schéma.</span><span class="sxs-lookup"><span data-stu-id="0cd70-292">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="0cd70-293">`EnsureCreated` povolí následující pracovní postup pro zpracování změn datového modelu:</span><span class="sxs-lookup"><span data-stu-id="0cd70-293">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="0cd70-294">Odstraňte databázi.</span><span class="sxs-lookup"><span data-stu-id="0cd70-294">Delete the database.</span></span> <span data-ttu-id="0cd70-295">Všechna existující data budou ztracena.</span><span class="sxs-lookup"><span data-stu-id="0cd70-295">Any existing data is lost.</span></span>
* <span data-ttu-id="0cd70-296">Změňte datový model.</span><span class="sxs-lookup"><span data-stu-id="0cd70-296">Change the data model.</span></span> <span data-ttu-id="0cd70-297">Například přidejte `EmailAddress` pole.</span><span class="sxs-lookup"><span data-stu-id="0cd70-297">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="0cd70-298">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0cd70-298">Run the app.</span></span>
* <span data-ttu-id="0cd70-299">`EnsureCreated` vytvoří databázi s novým schématem.</span><span class="sxs-lookup"><span data-stu-id="0cd70-299">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="0cd70-300">Tento pracovní postup funguje dobře v rané fázi vývoje, když se schéma rychle vyvíjí, pokud nepotřebujete zachovat data.</span><span class="sxs-lookup"><span data-stu-id="0cd70-300">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="0cd70-301">Tato situace se liší v případě, že data, která byla zadána do databáze, musí být zachována.</span><span class="sxs-lookup"><span data-stu-id="0cd70-301">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="0cd70-302">V takovém případě použijte migrace.</span><span class="sxs-lookup"><span data-stu-id="0cd70-302">When that is the case, use migrations.</span></span>

<span data-ttu-id="0cd70-303">Později v rámci série kurzů odstraníte databázi, kterou vytvořil, `EnsureCreated` a místo toho použijete migrace.</span><span class="sxs-lookup"><span data-stu-id="0cd70-303">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="0cd70-304">Databázi vytvořenou nástrojem `EnsureCreated` nelze aktualizovat pomocí migrací.</span><span class="sxs-lookup"><span data-stu-id="0cd70-304">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="0cd70-305">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="0cd70-305">Test the app</span></span>

* <span data-ttu-id="0cd70-306">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0cd70-306">Run the app.</span></span>
* <span data-ttu-id="0cd70-307">Vyberte odkaz **Students** a pak **vytvořte nový**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-307">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="0cd70-308">Otestujte odkazy Upravit, Podrobnosti a Odstranit.</span><span class="sxs-lookup"><span data-stu-id="0cd70-308">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="0cd70-309">Dosazení databáze</span><span class="sxs-lookup"><span data-stu-id="0cd70-309">Seed the database</span></span>

<span data-ttu-id="0cd70-310">`EnsureCreated`Metoda vytvoří prázdnou databázi.</span><span class="sxs-lookup"><span data-stu-id="0cd70-310">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="0cd70-311">V této části se přidá kód, který naplní databázi testovacími daty.</span><span class="sxs-lookup"><span data-stu-id="0cd70-311">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="0cd70-312">Vytvořte *data/DbInitializer. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="0cd70-312">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="0cd70-313">Kód kontroluje, zda v databázi existují studenti.</span><span class="sxs-lookup"><span data-stu-id="0cd70-313">The code checks if there are any students in the database.</span></span> <span data-ttu-id="0cd70-314">Pokud neexistují studenti, přidá testovací data do databáze.</span><span class="sxs-lookup"><span data-stu-id="0cd70-314">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="0cd70-315">Vytvoří testovací data v polích, nikoli `List<T>` kolekce pro optimalizaci výkonu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-315">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="0cd70-316">V *program.cs*nahraďte `EnsureCreated` volání `DbInitializer.Initialize` voláním:</span><span class="sxs-lookup"><span data-stu-id="0cd70-316">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="0cd70-317">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cd70-317">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0cd70-318">Zastavte aplikaci, pokud je spuštěná, a spusťte následující příkaz v **konzole správce balíčků** (PMC):</span><span class="sxs-lookup"><span data-stu-id="0cd70-318">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="0cd70-319">Pokud `Y` chcete databázi odstranit, odpovězte na.</span><span class="sxs-lookup"><span data-stu-id="0cd70-319">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cd70-320">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cd70-320">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0cd70-321">Zastavte aplikaci, pokud je spuštěná, a odstraňte soubor *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="0cd70-321">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="0cd70-322">Restartujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0cd70-322">Restart the app.</span></span>
* <span data-ttu-id="0cd70-323">Vyberte stránku Students a zobrazte si dosazený údaj.</span><span class="sxs-lookup"><span data-stu-id="0cd70-323">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="0cd70-324">Zobrazení databáze</span><span class="sxs-lookup"><span data-stu-id="0cd70-324">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cd70-325">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cd70-325">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0cd70-326">Otevřete **Průzkumník objektů systému SQL Server** (SSOX) z nabídky **Zobrazit** v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0cd70-326">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="0cd70-327">V SSOX vyberte **(LocalDB) \MSSQLLocalDB > databáze > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-327">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="0cd70-328">Název databáze je vygenerován z dříve zadaného názvu kontextu plus pomlčka a identifikátor GUID.</span><span class="sxs-lookup"><span data-stu-id="0cd70-328">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="0cd70-329">Rozbalte uzel **tabulky** .</span><span class="sxs-lookup"><span data-stu-id="0cd70-329">Expand the **Tables** node.</span></span>
* <span data-ttu-id="0cd70-330">Klikněte pravým tlačítkem myši na tabulku **student** a kliknutím na **Zobrazit data** Zobrazte vytvořené sloupce a řádky vložené do tabulky.</span><span class="sxs-lookup"><span data-stu-id="0cd70-330">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="0cd70-331">Kliknutím pravým tlačítkem na tabulku **student** a kliknutím na **Zobrazit kód** zjistíte, jak se `Student` model mapuje na `Student` schéma tabulky.</span><span class="sxs-lookup"><span data-stu-id="0cd70-331">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cd70-332">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cd70-332">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0cd70-333">Použijte nástroj SQLite k zobrazení schématu databáze a dat osazených daty.</span><span class="sxs-lookup"><span data-stu-id="0cd70-333">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="0cd70-334">Databázový soubor má název *cu. DB* a je umístěn ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-334">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="0cd70-335">Asynchronní kód</span><span class="sxs-lookup"><span data-stu-id="0cd70-335">Asynchronous code</span></span>

<span data-ttu-id="0cd70-336">Asynchronní programování je výchozí režim pro ASP.NET Core a EF Core.</span><span class="sxs-lookup"><span data-stu-id="0cd70-336">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="0cd70-337">Na webovém serveru je k dispozici omezený počet vláken a v situacích vysokého zatížení se mohou používat všechna dostupná vlákna.</span><span class="sxs-lookup"><span data-stu-id="0cd70-337">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="0cd70-338">Pokud k tomu dojde, server nemůže zpracovat nové požadavky, dokud nebudou vlákna uvolněna.</span><span class="sxs-lookup"><span data-stu-id="0cd70-338">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="0cd70-339">Pomocí synchronního kódu může být mnoho vláken vázáno nahoru, i když nefunguje, protože čekají na dokončení vstupně-výstupních operací.</span><span class="sxs-lookup"><span data-stu-id="0cd70-339">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="0cd70-340">V případě asynchronního kódu, když proces čeká na dokončení vstupně-výstupních operací, je jeho vlákno uvolněno na server, který bude použit pro zpracování jiných požadavků.</span><span class="sxs-lookup"><span data-stu-id="0cd70-340">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="0cd70-341">Výsledkem je, že asynchronní kód umožňuje efektivnější použití prostředků serveru a server může zpracovávat více provozu bez prodlev.</span><span class="sxs-lookup"><span data-stu-id="0cd70-341">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="0cd70-342">Asynchronní kód zavádí v době běhu malé množství režie.</span><span class="sxs-lookup"><span data-stu-id="0cd70-342">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="0cd70-343">V situacích s nízkým provozem je dosaženo zanedbatelného výkonu, zatímco v situacích vysokého provozu je potenciální zlepšení výkonu značné.</span><span class="sxs-lookup"><span data-stu-id="0cd70-343">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="0cd70-344">V následujícím kódu, klíčové slovo [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` návratová hodnota, `await` klíčové slovo a `ToListAsync` Metoda provede asynchronní spouštění kódu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-344">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="0cd70-345">`async`Klíčové slovo dává kompilátoru následující informace:</span><span class="sxs-lookup"><span data-stu-id="0cd70-345">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="0cd70-346">Vygenerujte zpětná volání pro části těla metody.</span><span class="sxs-lookup"><span data-stu-id="0cd70-346">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="0cd70-347">Vytvořte vrácený objekt [úkolu](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) .</span><span class="sxs-lookup"><span data-stu-id="0cd70-347">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="0cd70-348">`Task<T>`Návratový typ představuje probíhající práci.</span><span class="sxs-lookup"><span data-stu-id="0cd70-348">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="0cd70-349">`await`Klíčové slovo způsobí, že kompilátor rozdělí metodu do dvou částí.</span><span class="sxs-lookup"><span data-stu-id="0cd70-349">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="0cd70-350">První část končí asynchronně spuštěnou operací.</span><span class="sxs-lookup"><span data-stu-id="0cd70-350">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="0cd70-351">Druhá část je vložena do metody zpětného volání, která je volána po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="0cd70-351">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="0cd70-352">`ToListAsync` je asynchronní verze `ToList` metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="0cd70-352">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="0cd70-353">Některé věci, které je potřeba znát při psaní asynchronního kódu, který používá EF Core:</span><span class="sxs-lookup"><span data-stu-id="0cd70-353">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="0cd70-354">Asynchronně jsou spouštěny pouze příkazy, které způsobují odeslání dotazů nebo příkazů do databáze.</span><span class="sxs-lookup"><span data-stu-id="0cd70-354">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="0cd70-355">To zahrnuje `ToListAsync` , `SingleOrDefaultAsync` , `FirstOrDefaultAsync` a `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-355">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="0cd70-356">Neobsahuje příkazy, které mění pouze `IQueryable` , například `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-356">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="0cd70-357">Kontext EF Core není bezpečný pro přístup z více vláken: Nepokoušejte se současně provést více operací.</span><span class="sxs-lookup"><span data-stu-id="0cd70-357">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="0cd70-358">Chcete-li využít výhody z hlediska výkonu asynchronního kódu, ověřte, že balíčky knihoven (například pro stránkování) používají async, pokud volají EF Core metody, které odesílají dotazy do databáze.</span><span class="sxs-lookup"><span data-stu-id="0cd70-358">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="0cd70-359">Další informace o asynchronním programování v rozhraní .NET naleznete v tématu [Async Overview](/dotnet/standard/async) a [Asynchronous Programming with Async a await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="0cd70-359">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="0cd70-360">Otázky výkonu</span><span class="sxs-lookup"><span data-stu-id="0cd70-360">Performance considerations</span></span>

<span data-ttu-id="0cd70-361">Obecně platí, že webová stránka by neměla načítat libovolný počet řádků.</span><span class="sxs-lookup"><span data-stu-id="0cd70-361">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="0cd70-362">Dotaz by měl používat stránkování nebo omezující přístup.</span><span class="sxs-lookup"><span data-stu-id="0cd70-362">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="0cd70-363">Například předchozí dotaz může použít `Take` k omezení vrácených řádků:</span><span class="sxs-lookup"><span data-stu-id="0cd70-363">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="0cd70-364">Vytvoření výčtu velké tabulky v zobrazení může vrátit částečně vytvořenou odpověď HTTP 200, pokud dojde k výjimce databáze prostřednictvím výčtu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-364">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="0cd70-365"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> Výchozí hodnota je 1024.</span><span class="sxs-lookup"><span data-stu-id="0cd70-365"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="0cd70-366">Následující sady kódů `MaxModelBindingCollectionSize` :</span><span class="sxs-lookup"><span data-stu-id="0cd70-366">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="0cd70-367">Stránkování se zabývá později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-367">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="0cd70-368">Další kroky</span><span class="sxs-lookup"><span data-stu-id="0cd70-368">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="0cd70-369">Další kurz</span><span class="sxs-lookup"><span data-stu-id="0cd70-369">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="0cd70-370">Toto je první v sérii kurzů, které ukazují, jak používat Entity Framework (EF) jádro v aplikaci [ASP.NET Core Razor Pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="0cd70-370">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="0cd70-371">Kurzy vytvářejí web pro fiktivní univerzitě společnosti Contoso.</span><span class="sxs-lookup"><span data-stu-id="0cd70-371">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="0cd70-372">Tato lokalita obsahuje funkce, jako je například využití studenta, vytváření kurzů a přiřazení instruktorů.</span><span class="sxs-lookup"><span data-stu-id="0cd70-372">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="0cd70-373">Kurz používá první přístup ke kódu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-373">The tutorial uses the code first approach.</span></span> <span data-ttu-id="0cd70-374">Informace o tomto kurzu s použitím databáze prvního přístupu najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="0cd70-374">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="0cd70-375">Stažení nebo zobrazení dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="0cd70-375">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="0cd70-376">[Pokyny ke stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="0cd70-376">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0cd70-377">Požadavky</span><span class="sxs-lookup"><span data-stu-id="0cd70-377">Prerequisites</span></span>

* <span data-ttu-id="0cd70-378">Pokud se Razor stránkami teprve začínáte, Projděte si řadu kurzů [Začínáme se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start) , než začnete s tímto.</span><span class="sxs-lookup"><span data-stu-id="0cd70-378">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cd70-379">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cd70-379">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cd70-380">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cd70-380">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="0cd70-381">Databázové moduly</span><span class="sxs-lookup"><span data-stu-id="0cd70-381">Database engines</span></span>

<span data-ttu-id="0cd70-382">Pokyny pro Visual Studio používají [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), verzi SQL Server Express, která se spouští jenom v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="0cd70-382">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="0cd70-383">Pokyny pro [Visual Studio Code používají nástroj](https://www.sqlite.org/)pro databázový stroj pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="0cd70-383">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="0cd70-384">Pokud se rozhodnete použít SQLite, Stáhněte a nainstalujte nástroj třetí strany pro správu a zobrazení databáze SQLite, jako je například [prohlížeč databáze pro SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="0cd70-384">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="0cd70-385">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="0cd70-385">Troubleshooting</span></span>

<span data-ttu-id="0cd70-386">Pokud narazíte na problém, který nemůžete vyřešit, porovnejte kód s [dokončeným projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="0cd70-386">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="0cd70-387">Dobrý způsob, jak získat pomoc, je odeslání otázky do StackOverflow.com s použitím [značky ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [značky EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="0cd70-387">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="0cd70-388">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="0cd70-388">The sample app</span></span>

<span data-ttu-id="0cd70-389">Aplikace sestavená v těchto kurzech je základním webem na univerzitě.</span><span class="sxs-lookup"><span data-stu-id="0cd70-389">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="0cd70-390">Uživatelé můžou zobrazit a aktualizovat informace o studentech, kurzech a instruktorech.</span><span class="sxs-lookup"><span data-stu-id="0cd70-390">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="0cd70-391">Tady je několik obrazovek vytvořených v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-391">Here are a few of the screens created in the tutorial.</span></span>

![Stránka indexu studentů](intro/_static/students-index30.png)

![Stránka pro úpravy studentů](intro/_static/student-edit30.png)

<span data-ttu-id="0cd70-394">Styl uživatelského rozhraní tohoto webu je založen na předdefinovaných šablonách projektů.</span><span class="sxs-lookup"><span data-stu-id="0cd70-394">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="0cd70-395">V tomto kurzu se naučíte, jak používat EF Core, nikoli způsob přizpůsobení uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0cd70-395">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="0cd70-396">Pomocí odkazu v horní části stránky Získejte zdrojový kód dokončeného projektu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-396">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="0cd70-397">Složka *cu30* obsahuje kód pro verzi kurzu ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="0cd70-397">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="0cd70-398">Soubory, které reflektují stav kódu pro kurzy 1-7, najdete ve složce *cu30snapshots* .</span><span class="sxs-lookup"><span data-stu-id="0cd70-398">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cd70-399">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cd70-399">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0cd70-400">Spuštění aplikace po stažení dokončeného projektu:</span><span class="sxs-lookup"><span data-stu-id="0cd70-400">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="0cd70-401">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="0cd70-401">Build the project.</span></span>
* <span data-ttu-id="0cd70-402">V konzole správce balíčků (PMC) spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="0cd70-402">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="0cd70-403">Spusťte projekt k osazení databáze.</span><span class="sxs-lookup"><span data-stu-id="0cd70-403">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cd70-404">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cd70-404">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0cd70-405">Spuštění aplikace po stažení dokončeného projektu:</span><span class="sxs-lookup"><span data-stu-id="0cd70-405">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="0cd70-406">Odstraňte *ContosoUniversity. csproj*a přejmenujte *ContosoUniversitySQLite. csproj* na *ContosoUniversity. csproj*.</span><span class="sxs-lookup"><span data-stu-id="0cd70-406">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="0cd70-407">V *program.cs*se Odkomentujte `#define Startup` tak, aby `StartupSQLite` se použil.</span><span class="sxs-lookup"><span data-stu-id="0cd70-407">In *Program.cs*, comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="0cd70-408">Odstraňte *appSettings.jsna*a přejmenujte *appSettingsSQLite.jsna* na *appSettings.jszapnuto*.</span><span class="sxs-lookup"><span data-stu-id="0cd70-408">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="0cd70-409">Odstraňte složku *migrace* a přejmenujte *MigrationsSQL* na *migrace*.</span><span class="sxs-lookup"><span data-stu-id="0cd70-409">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="0cd70-410">Proveďte globální hledání `#if SQLiteVersion` a odeberte `#if SQLiteVersion` příkaz a přidružený `#endif` příkaz.</span><span class="sxs-lookup"><span data-stu-id="0cd70-410">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="0cd70-411">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="0cd70-411">Build the project.</span></span>
* <span data-ttu-id="0cd70-412">Na příkazovém řádku ve složce projektu spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="0cd70-412">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="0cd70-413">V nástroji SQLite spusťte následující příkaz SQL:</span><span class="sxs-lookup"><span data-stu-id="0cd70-413">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="0cd70-414">Spusťte projekt k osazení databáze.</span><span class="sxs-lookup"><span data-stu-id="0cd70-414">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="0cd70-415">Vytvoření projektu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="0cd70-415">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cd70-416">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cd70-416">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0cd70-417">V nabídce **soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-417">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0cd70-418">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-418">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="0cd70-419">Pojmenujte projekt *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="0cd70-419">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="0cd70-420">Je důležité použít tento přesný název, včetně velkých a malých písmen, aby se obory názvů shodovaly při zkopírování a vložení kódu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-420">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="0cd70-421">V rozevíracích seznamech vyberte **.NET Core** a **ASP.NET Core 3,0** a potom vyberte **Webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-421">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cd70-422">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cd70-422">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0cd70-423">V terminálu přejděte do složky, ve které by měla být vytvořena složka projektu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-423">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="0cd70-424">Spuštěním následujících příkazů vytvořte Razor projekt stránky a `cd` do nové složky projektu:</span><span class="sxs-lookup"><span data-stu-id="0cd70-424">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="0cd70-425">Nastavení stylu webu</span><span class="sxs-lookup"><span data-stu-id="0cd70-425">Set up the site style</span></span>

<span data-ttu-id="0cd70-426">Pomocí aktualizace *stránek/Shared/_Layout. cshtml*nastavte záhlaví webu, zápatí a nabídku. cshtml:</span><span class="sxs-lookup"><span data-stu-id="0cd70-426">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="0cd70-427">Změňte všechny výskyty "ContosoUniversity" na "contoso University".</span><span class="sxs-lookup"><span data-stu-id="0cd70-427">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="0cd70-428">Existují tři výskyty.</span><span class="sxs-lookup"><span data-stu-id="0cd70-428">There are three occurrences.</span></span>

* <span data-ttu-id="0cd70-429">Odstraňte položky nabídky **Domů** a **Ochrana osobních údajů** a přidejte záznamy **o o** **studentech**, **kurzech**, **instruktorech**a **odděleních**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-429">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="0cd70-430">Změny jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="0cd70-430">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="0cd70-431">Na *stránce pages/index. cshtml*nahraďte obsah souboru následujícím kódem, který nahradí text o ASP.NET Core textem této aplikace:</span><span class="sxs-lookup"><span data-stu-id="0cd70-431">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="0cd70-432">Spusťte aplikaci, abyste ověřili, že se zobrazí domovská stránka.</span><span class="sxs-lookup"><span data-stu-id="0cd70-432">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="0cd70-433">Datový model</span><span class="sxs-lookup"><span data-stu-id="0cd70-433">The data model</span></span>

<span data-ttu-id="0cd70-434">V následujících částech se vytvoří datový model:</span><span class="sxs-lookup"><span data-stu-id="0cd70-434">The following sections create a data model:</span></span>

![Kurz – registrace – diagram datového modelu studenta](intro/_static/data-model-diagram.png)

<span data-ttu-id="0cd70-436">Student se může zaregistrovat v jakémkoli počtu kurzů a kurz může mít zaregistrovaný libovolný počet studentů.</span><span class="sxs-lookup"><span data-stu-id="0cd70-436">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="0cd70-437">Entita studenta</span><span class="sxs-lookup"><span data-stu-id="0cd70-437">The Student entity</span></span>

![Diagram entity studenta](intro/_static/student-entity.png)

* <span data-ttu-id="0cd70-439">Vytvořte složku *modely* ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-439">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="0cd70-440">Vytvořte *modely/studenta. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="0cd70-440">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="0cd70-441">`ID`Vlastnost se zobrazí jako sloupec primárního klíče tabulky databáze, která odpovídá této třídě.</span><span class="sxs-lookup"><span data-stu-id="0cd70-441">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="0cd70-442">Ve výchozím nastavení EF Core interpretuje vlastnost s názvem `ID` nebo `classnameID` jako primární klíč.</span><span class="sxs-lookup"><span data-stu-id="0cd70-442">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="0cd70-443">Proto je alternativní automaticky rozpoznaný název pro `Student` primární klíč třídy `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-443">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="0cd70-444">Další informace najdete v tématu [EF Core-Keys](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="0cd70-444">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="0cd70-445">`Enrollments`Vlastnost je [navigační vlastnost](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="0cd70-445">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="0cd70-446">Navigační vlastnosti obsahují další entity, které se vztahují k této entitě.</span><span class="sxs-lookup"><span data-stu-id="0cd70-446">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="0cd70-447">V takovém případě `Enrollments` vlastnost `Student` entity obsahuje všechny `Enrollment` entity, které se vztahují k danému studentovi.</span><span class="sxs-lookup"><span data-stu-id="0cd70-447">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="0cd70-448">Například pokud má řádek studenta v databázi dva související řádky registrace, `Enrollments` navigační vlastnost obsahuje tyto dvě entity registrace.</span><span class="sxs-lookup"><span data-stu-id="0cd70-448">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="0cd70-449">V databázi se řádek registrace vztahuje k řádku studenta, pokud jeho sloupec StudentID obsahuje hodnotu ID studenta.</span><span class="sxs-lookup"><span data-stu-id="0cd70-449">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="0cd70-450">Předpokládejme například, že řádek studenta má ID = 1.</span><span class="sxs-lookup"><span data-stu-id="0cd70-450">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="0cd70-451">Související řádky registrace budou mít StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="0cd70-451">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="0cd70-452">StudentID je *cizí klíč* v tabulce zápisu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-452">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="0cd70-453">`Enrollments`Vlastnost je definována tak, `ICollection<Enrollment>` že může existovat více souvisejících entit zápisu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-453">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="0cd70-454">Můžete použít jiné typy kolekce, například `List<Enrollment>` nebo `HashSet<Enrollment>` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-454">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="0cd70-455">Při `ICollection<Enrollment>` použití EF Core vytvoří `HashSet<Enrollment>` ve výchozím nastavení kolekci.</span><span class="sxs-lookup"><span data-stu-id="0cd70-455">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="0cd70-456">Entita registrace</span><span class="sxs-lookup"><span data-stu-id="0cd70-456">The Enrollment entity</span></span>

![Diagram entity registrace](intro/_static/enrollment-entity.png)

<span data-ttu-id="0cd70-458">Vytvořte *modely/registrace. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="0cd70-458">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="0cd70-459">`EnrollmentID`Vlastnost je primární klíč. Tato entita používá `classnameID` vzor namísto `ID` samotného.</span><span class="sxs-lookup"><span data-stu-id="0cd70-459">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="0cd70-460">Pro model produkčních dat vyberte jeden vzor a používejte ho konzistentně.</span><span class="sxs-lookup"><span data-stu-id="0cd70-460">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="0cd70-461">Tento kurz používá pouze k ilustraci toho, jak funguje.</span><span class="sxs-lookup"><span data-stu-id="0cd70-461">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="0cd70-462">Použití `ID` bez toho usnadňuje `classname` implementaci některých druhů změn datového modelu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-462">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="0cd70-463">`Grade`Vlastnost je `enum` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-463">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="0cd70-464">Otazník po `Grade` deklaraci typu označuje, že vlastnost může `Grade` [mít hodnotu null](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="0cd70-464">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="0cd70-465">Třídu, která má hodnotu null, se liší od nulové třídy &mdash; null znamená, že známka není známa nebo ještě nebyla přiřazena.</span><span class="sxs-lookup"><span data-stu-id="0cd70-465">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="0cd70-466">`StudentID`Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Student` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-466">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="0cd70-467">`Enrollment`Entita je přidružená k jedné `Student` entitě, takže vlastnost obsahuje jednu `Student` entitu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-467">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="0cd70-468">`CourseID`Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Course` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-468">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="0cd70-469">`Enrollment`Entita je přidružená k jedné `Course` entitě.</span><span class="sxs-lookup"><span data-stu-id="0cd70-469">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="0cd70-470">EF Core interpretuje vlastnost jako cizí klíč, pokud má název `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-470">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="0cd70-471">Například `StudentID` je cizí klíč pro `Student` navigační vlastnost, protože `Student` primární klíč entity je `ID` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-471">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="0cd70-472">Lze také pojmenovat vlastnosti cizího klíče `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-472">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="0cd70-473">Například `CourseID` vzhledem k tomu, že `Course` primární klíč entity je `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-473">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="0cd70-474">Entita kurzu</span><span class="sxs-lookup"><span data-stu-id="0cd70-474">The Course entity</span></span>

![Diagram entity kurzu](intro/_static/course-entity.png)

<span data-ttu-id="0cd70-476">Vytvořte *modely/Course. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="0cd70-476">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="0cd70-477">`Enrollments`Vlastnost je navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="0cd70-477">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="0cd70-478">`Course`Entita může souviset s libovolným počtem `Enrollment` entit.</span><span class="sxs-lookup"><span data-stu-id="0cd70-478">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="0cd70-479">`DatabaseGenerated`Atribut umožňuje aplikaci určit primární klíč místo toho, aby ho databáze vygenerovala.</span><span class="sxs-lookup"><span data-stu-id="0cd70-479">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="0cd70-480">Sestavte projekt, aby se ověřilo, že nejsou k dispozici žádné chyby kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="0cd70-480">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="0cd70-481">Stránky studenta pro generování uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="0cd70-481">Scaffold Student pages</span></span>

<span data-ttu-id="0cd70-482">V této části použijete nástroj ASP.NET Core pro generování uživatelského rozhraní k vygenerování:</span><span class="sxs-lookup"><span data-stu-id="0cd70-482">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="0cd70-483">Třída *kontextu* EF Core.</span><span class="sxs-lookup"><span data-stu-id="0cd70-483">An EF Core *context* class.</span></span> <span data-ttu-id="0cd70-484">Kontext je hlavní třída, která koordinuje funkce Entity Framework pro daný datový model.</span><span class="sxs-lookup"><span data-stu-id="0cd70-484">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="0cd70-485">Je odvozen z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="0cd70-485">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="0cd70-486">Razor stránky, které zpracovávají operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro danou `Student` entitu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-486">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cd70-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cd70-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0cd70-488">Vytvořte složku *Students* ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="0cd70-488">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="0cd70-489">V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *Pages/Students* a vyberte **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-489">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0cd70-490">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte \*\* Razor stránky pomocí Entity Framework (CRUD)\*\* > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-490">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="0cd70-491">V dialogovém okně **Přidat Razor stránky pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="0cd70-491">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="0cd70-492">V rozevíracím seznamu **třída modelu** vyberte **student (ContosoUniversity. Models)**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-492">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="0cd70-493">V řádku **třídy kontextu dat** vyberte **+** znaménko (plus).</span><span class="sxs-lookup"><span data-stu-id="0cd70-493">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="0cd70-494">Změňte název kontextu dat z *ContosoUniversity. Models. ContosoUniversityContext* na *ContosoUniversity. data. SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="0cd70-494">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="0cd70-495">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-495">Select **Add**.</span></span>

<span data-ttu-id="0cd70-496">Automaticky se nainstalují tyto balíčky:</span><span class="sxs-lookup"><span data-stu-id="0cd70-496">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cd70-497">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cd70-497">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0cd70-498">Spuštěním následujících příkazů .NET Core CLI nainstalujte požadované balíčky NuGet:</span><span class="sxs-lookup"><span data-stu-id="0cd70-498">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  <span data-ttu-id="0cd70-499">Pro generování uživatelského rozhraní je vyžadován balíček Microsoft. VisualStudio. Web. strategii. Design.</span><span class="sxs-lookup"><span data-stu-id="0cd70-499">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="0cd70-500">I když aplikace nebude používat SQL Server, nástroj pro generování uživatelského rozhraní potřebuje balíček SQL Server.</span><span class="sxs-lookup"><span data-stu-id="0cd70-500">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="0cd70-501">Vytvořte složku *stránky/studenty* .</span><span class="sxs-lookup"><span data-stu-id="0cd70-501">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="0cd70-502">Spusťte následující příkaz pro instalaci nástroje pro [generování uživatelského rozhraní ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="0cd70-502">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="0cd70-503">Spusťte následující příkaz pro generování uživatelského rozhraní stránek studenta.</span><span class="sxs-lookup"><span data-stu-id="0cd70-503">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="0cd70-504">**V systému Windows**</span><span class="sxs-lookup"><span data-stu-id="0cd70-504">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="0cd70-505">**V systému macOS nebo Linux**</span><span class="sxs-lookup"><span data-stu-id="0cd70-505">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="0cd70-506">Pokud máte problém s předchozím krokem, sestavte projekt a opakujte krok generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0cd70-506">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="0cd70-507">Proces generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="0cd70-507">The scaffolding process:</span></span>

* <span data-ttu-id="0cd70-508">Vytvoří Razor stránky ve složce *Pages/Students* :</span><span class="sxs-lookup"><span data-stu-id="0cd70-508">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="0cd70-509">*Vytvoření. cshtml* a *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0cd70-509">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="0cd70-510">*Odstranění. cshtml* a *DELETE.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0cd70-510">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="0cd70-511">*Podrobnosti. cshtml* a *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0cd70-511">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="0cd70-512">*Upravit. cshtml* a *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0cd70-512">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="0cd70-513">*Index. cshtml* a *index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0cd70-513">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="0cd70-514">Vytvoří *data/SchoolContext. cs*.</span><span class="sxs-lookup"><span data-stu-id="0cd70-514">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="0cd70-515">Přidá kontext do injektáže závislosti v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="0cd70-515">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="0cd70-516">Přidá připojovací řetězec databáze, do kterého se má *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="0cd70-516">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="0cd70-517">Připojovací řetězec databáze</span><span class="sxs-lookup"><span data-stu-id="0cd70-517">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cd70-518">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cd70-518">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0cd70-519">*appsettings.jsv* souboru Určuje připojovací řetězec [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="0cd70-519">The *appsettings.json* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="0cd70-520">LocalDB je zjednodušená verze databázového stroje SQL Server Express a je určena pro vývoj aplikací, nikoli za použití v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="0cd70-520">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="0cd70-521">Ve výchozím nastavení LocalDB vytvoří soubory *. mdf* v `C:/Users/<user>` adresáři.</span><span class="sxs-lookup"><span data-stu-id="0cd70-521">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cd70-522">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cd70-522">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0cd70-523">Změňte připojovací řetězec tak, aby odkazoval na soubor databáze SQLite s názvem *cu. DB*:</span><span class="sxs-lookup"><span data-stu-id="0cd70-523">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="0cd70-524">Aktualizuje třídu kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="0cd70-524">Update the database context class</span></span>

<span data-ttu-id="0cd70-525">Hlavní třída, která koordinuje funkce EF Core pro daný datový model, je třída kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="0cd70-525">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="0cd70-526">Kontext je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="0cd70-526">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="0cd70-527">Kontext určuje, které entity jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-527">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="0cd70-528">V tomto projektu je třída pojmenována `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-528">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="0cd70-529">Aktualizujte *data/SchoolContext. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="0cd70-529">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="0cd70-530">Zvýrazněný kód vytvoří vlastnost [negenerickými \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro každou sadu entit.</span><span class="sxs-lookup"><span data-stu-id="0cd70-530">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="0cd70-531">V terminologii EF Core:</span><span class="sxs-lookup"><span data-stu-id="0cd70-531">In EF Core terminology:</span></span>

* <span data-ttu-id="0cd70-532">Sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="0cd70-532">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="0cd70-533">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="0cd70-533">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="0cd70-534">Vzhledem k tomu, že sada entit obsahuje více entit, musí mít vlastnosti Negenerickými plurální názvy.</span><span class="sxs-lookup"><span data-stu-id="0cd70-534">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="0cd70-535">Vzhledem k tomu, že nástroj pro generování uživatelského rozhraní vytvořil `Student` negenerickými, tento krok změny změní na plural `Students` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-535">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="0cd70-536">Chcete-li Razor , aby kód stránky odpovídal novému negenerickými názvu, proveďte globální změnu v celém projektu `_context.Student` na `_context.Students` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-536">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="0cd70-537">K dispozici je 8 výskytů.</span><span class="sxs-lookup"><span data-stu-id="0cd70-537">There are 8 occurrences.</span></span>

<span data-ttu-id="0cd70-538">Sestavte projekt, aby se ověřilo, že nedošlo k chybám kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="0cd70-538">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="0cd70-539">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="0cd70-539">Startup.cs</span></span>

<span data-ttu-id="0cd70-540">ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0cd70-540">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0cd70-541">Služby (například kontext databáze EF Core) jsou registrovány pomocí injektáže závislosti při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="0cd70-541">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="0cd70-542">Komponenty, které vyžadují tyto služby (například Razor stránky), poskytují tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="0cd70-542">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="0cd70-543">Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-543">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="0cd70-544">Nástroj pro generování uživatelského rozhraní automaticky zaregistroval třídu kontextu pomocí kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="0cd70-544">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cd70-545">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cd70-545">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0cd70-546">V `ConfigureServices` byly zvýrazněné řádky přidány pomocí generátoru:</span><span class="sxs-lookup"><span data-stu-id="0cd70-546">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cd70-547">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cd70-547">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0cd70-548">V nástroji se ujistěte, `ConfigureServices` že kód přidaný voláním uživatelského rozhraní `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-548">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="0cd70-549">Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="0cd70-549">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="0cd70-550">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) přečte připojovací řetězec z *appsettings.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="0cd70-550">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="0cd70-551">Vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="0cd70-551">Create the database</span></span>

<span data-ttu-id="0cd70-552">Aktualizujte *program.cs* , aby se vytvořila databáze, pokud neexistuje:</span><span class="sxs-lookup"><span data-stu-id="0cd70-552">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="0cd70-553">Metoda [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) neprovede žádnou akci, pokud existuje databáze pro kontext.</span><span class="sxs-lookup"><span data-stu-id="0cd70-553">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="0cd70-554">Pokud žádná databáze neexistuje, vytvoří databázi a schéma.</span><span class="sxs-lookup"><span data-stu-id="0cd70-554">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="0cd70-555">`EnsureCreated` povolí následující pracovní postup pro zpracování změn datového modelu:</span><span class="sxs-lookup"><span data-stu-id="0cd70-555">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="0cd70-556">Odstraňte databázi.</span><span class="sxs-lookup"><span data-stu-id="0cd70-556">Delete the database.</span></span> <span data-ttu-id="0cd70-557">Všechna existující data budou ztracena.</span><span class="sxs-lookup"><span data-stu-id="0cd70-557">Any existing data is lost.</span></span>
* <span data-ttu-id="0cd70-558">Změňte datový model.</span><span class="sxs-lookup"><span data-stu-id="0cd70-558">Change the data model.</span></span> <span data-ttu-id="0cd70-559">Například přidejte `EmailAddress` pole.</span><span class="sxs-lookup"><span data-stu-id="0cd70-559">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="0cd70-560">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0cd70-560">Run the app.</span></span>
* <span data-ttu-id="0cd70-561">`EnsureCreated` vytvoří databázi s novým schématem.</span><span class="sxs-lookup"><span data-stu-id="0cd70-561">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="0cd70-562">Tento pracovní postup funguje dobře v rané fázi vývoje, když se schéma rychle vyvíjí, pokud nepotřebujete zachovat data.</span><span class="sxs-lookup"><span data-stu-id="0cd70-562">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="0cd70-563">Tato situace se liší v případě, že data, která byla zadána do databáze, musí být zachována.</span><span class="sxs-lookup"><span data-stu-id="0cd70-563">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="0cd70-564">V takovém případě použijte migrace.</span><span class="sxs-lookup"><span data-stu-id="0cd70-564">When that is the case, use migrations.</span></span>

<span data-ttu-id="0cd70-565">Později v rámci série kurzů odstraníte databázi, kterou vytvořil, `EnsureCreated` a místo toho použijete migrace.</span><span class="sxs-lookup"><span data-stu-id="0cd70-565">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="0cd70-566">Databázi vytvořenou nástrojem `EnsureCreated` nelze aktualizovat pomocí migrací.</span><span class="sxs-lookup"><span data-stu-id="0cd70-566">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="0cd70-567">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="0cd70-567">Test the app</span></span>

* <span data-ttu-id="0cd70-568">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0cd70-568">Run the app.</span></span>
* <span data-ttu-id="0cd70-569">Vyberte odkaz **Students** a pak **vytvořte nový**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-569">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="0cd70-570">Otestujte odkazy Upravit, Podrobnosti a Odstranit.</span><span class="sxs-lookup"><span data-stu-id="0cd70-570">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="0cd70-571">Dosazení databáze</span><span class="sxs-lookup"><span data-stu-id="0cd70-571">Seed the database</span></span>

<span data-ttu-id="0cd70-572">`EnsureCreated`Metoda vytvoří prázdnou databázi.</span><span class="sxs-lookup"><span data-stu-id="0cd70-572">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="0cd70-573">V této části se přidá kód, který naplní databázi testovacími daty.</span><span class="sxs-lookup"><span data-stu-id="0cd70-573">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="0cd70-574">Vytvořte *data/DbInitializer. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="0cd70-574">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="0cd70-575">Kód kontroluje, zda v databázi existují studenti.</span><span class="sxs-lookup"><span data-stu-id="0cd70-575">The code checks if there are any students in the database.</span></span> <span data-ttu-id="0cd70-576">Pokud neexistují studenti, přidá testovací data do databáze.</span><span class="sxs-lookup"><span data-stu-id="0cd70-576">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="0cd70-577">Vytvoří testovací data v polích, nikoli `List<T>` kolekce pro optimalizaci výkonu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-577">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="0cd70-578">V *program.cs*nahraďte `EnsureCreated` volání `DbInitializer.Initialize` voláním:</span><span class="sxs-lookup"><span data-stu-id="0cd70-578">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="0cd70-579">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cd70-579">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0cd70-580">Zastavte aplikaci, pokud je spuštěná, a spusťte následující příkaz v **konzole správce balíčků** (PMC):</span><span class="sxs-lookup"><span data-stu-id="0cd70-580">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cd70-581">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cd70-581">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0cd70-582">Zastavte aplikaci, pokud je spuštěná, a odstraňte soubor *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="0cd70-582">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="0cd70-583">Restartujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0cd70-583">Restart the app.</span></span>

* <span data-ttu-id="0cd70-584">Vyberte stránku Students a zobrazte si dosazený údaj.</span><span class="sxs-lookup"><span data-stu-id="0cd70-584">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="0cd70-585">Zobrazení databáze</span><span class="sxs-lookup"><span data-stu-id="0cd70-585">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cd70-586">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cd70-586">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0cd70-587">Otevřete **Průzkumník objektů systému SQL Server** (SSOX) z nabídky **Zobrazit** v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0cd70-587">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="0cd70-588">V SSOX vyberte **(LocalDB) \MSSQLLocalDB > databáze > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-588">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="0cd70-589">Název databáze je vygenerován z dříve zadaného názvu kontextu plus pomlčka a identifikátor GUID.</span><span class="sxs-lookup"><span data-stu-id="0cd70-589">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="0cd70-590">Rozbalte uzel **tabulky** .</span><span class="sxs-lookup"><span data-stu-id="0cd70-590">Expand the **Tables** node.</span></span>
* <span data-ttu-id="0cd70-591">Klikněte pravým tlačítkem myši na tabulku **student** a kliknutím na **Zobrazit data** Zobrazte vytvořené sloupce a řádky vložené do tabulky.</span><span class="sxs-lookup"><span data-stu-id="0cd70-591">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="0cd70-592">Kliknutím pravým tlačítkem na tabulku **student** a kliknutím na **Zobrazit kód** zjistíte, jak se `Student` model mapuje na `Student` schéma tabulky.</span><span class="sxs-lookup"><span data-stu-id="0cd70-592">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cd70-593">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cd70-593">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0cd70-594">Použijte nástroj SQLite k zobrazení schématu databáze a dat osazených daty.</span><span class="sxs-lookup"><span data-stu-id="0cd70-594">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="0cd70-595">Databázový soubor má název *cu. DB* a je umístěn ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-595">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="0cd70-596">Asynchronní kód</span><span class="sxs-lookup"><span data-stu-id="0cd70-596">Asynchronous code</span></span>

<span data-ttu-id="0cd70-597">Asynchronní programování je výchozí režim pro ASP.NET Core a EF Core.</span><span class="sxs-lookup"><span data-stu-id="0cd70-597">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="0cd70-598">Na webovém serveru je k dispozici omezený počet vláken a v situacích vysokého zatížení se mohou používat všechna dostupná vlákna.</span><span class="sxs-lookup"><span data-stu-id="0cd70-598">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="0cd70-599">Pokud k tomu dojde, server nemůže zpracovat nové požadavky, dokud nebudou vlákna uvolněna.</span><span class="sxs-lookup"><span data-stu-id="0cd70-599">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="0cd70-600">Pomocí synchronního kódu může být mnoho vláken svázáno s tím, že ve skutečnosti neprovádí žádnou práci, protože čeká na dokončení vstupně-výstupních operací.</span><span class="sxs-lookup"><span data-stu-id="0cd70-600">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="0cd70-601">V případě asynchronního kódu, když proces čeká na dokončení vstupně-výstupních operací, je jeho vlákno uvolněno na server, který bude použit pro zpracování jiných požadavků.</span><span class="sxs-lookup"><span data-stu-id="0cd70-601">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="0cd70-602">Výsledkem je, že asynchronní kód umožňuje efektivnější použití prostředků serveru a server může zpracovávat více provozu bez prodlev.</span><span class="sxs-lookup"><span data-stu-id="0cd70-602">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="0cd70-603">Asynchronní kód zavádí v době běhu malé množství režie.</span><span class="sxs-lookup"><span data-stu-id="0cd70-603">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="0cd70-604">V situacích s nízkým provozem je dosaženo zanedbatelného výkonu, zatímco v situacích vysokého provozu je potenciální zlepšení výkonu značné.</span><span class="sxs-lookup"><span data-stu-id="0cd70-604">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="0cd70-605">V následujícím kódu, klíčové slovo [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` návratová hodnota, `await` klíčové slovo a `ToListAsync` Metoda provede asynchronní spouštění kódu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-605">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="0cd70-606">`async`Klíčové slovo dává kompilátoru následující informace:</span><span class="sxs-lookup"><span data-stu-id="0cd70-606">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="0cd70-607">Vygenerujte zpětná volání pro části těla metody.</span><span class="sxs-lookup"><span data-stu-id="0cd70-607">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="0cd70-608">Vytvořte vrácený objekt [úkolu](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) .</span><span class="sxs-lookup"><span data-stu-id="0cd70-608">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="0cd70-609">`Task<T>`Návratový typ představuje probíhající práci.</span><span class="sxs-lookup"><span data-stu-id="0cd70-609">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="0cd70-610">`await`Klíčové slovo způsobí, že kompilátor rozdělí metodu do dvou částí.</span><span class="sxs-lookup"><span data-stu-id="0cd70-610">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="0cd70-611">První část končí asynchronně spuštěnou operací.</span><span class="sxs-lookup"><span data-stu-id="0cd70-611">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="0cd70-612">Druhá část je vložena do metody zpětného volání, která je volána po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="0cd70-612">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="0cd70-613">`ToListAsync` je asynchronní verze `ToList` metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="0cd70-613">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="0cd70-614">Některé věci, které je potřeba znát při psaní asynchronního kódu, který používá EF Core:</span><span class="sxs-lookup"><span data-stu-id="0cd70-614">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="0cd70-615">Asynchronně jsou spouštěny pouze příkazy, které způsobují odeslání dotazů nebo příkazů do databáze.</span><span class="sxs-lookup"><span data-stu-id="0cd70-615">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="0cd70-616">To zahrnuje `ToListAsync` , `SingleOrDefaultAsync` , `FirstOrDefaultAsync` a `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-616">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="0cd70-617">Neobsahuje příkazy, které mění pouze `IQueryable` , například `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-617">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="0cd70-618">Kontext EF Core není bezpečný pro přístup z více vláken: Nepokoušejte se současně provést více operací.</span><span class="sxs-lookup"><span data-stu-id="0cd70-618">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="0cd70-619">Chcete-li využít výhody z hlediska výkonu asynchronního kódu, ověřte, že balíčky knihoven (například pro stránkování) používají async, pokud volají EF Core metody, které odesílají dotazy do databáze.</span><span class="sxs-lookup"><span data-stu-id="0cd70-619">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="0cd70-620">Další informace o asynchronním programování v rozhraní .NET naleznete v tématu [Async Overview](/dotnet/standard/async) a [Asynchronous Programming with Async a await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="0cd70-620">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="0cd70-621">Další kroky</span><span class="sxs-lookup"><span data-stu-id="0cd70-621">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="0cd70-622">Další kurz</span><span class="sxs-lookup"><span data-stu-id="0cd70-622">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0cd70-623">Ukázková webová aplikace společnosti Contoso University ukazuje, jak vytvořit Razor aplikaci ASP.NET Core Pages s využitím jádra Entity Framework (EF).</span><span class="sxs-lookup"><span data-stu-id="0cd70-623">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="0cd70-624">Ukázková aplikace je web pro fiktivní univerzitě společnosti Contoso.</span><span class="sxs-lookup"><span data-stu-id="0cd70-624">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="0cd70-625">Zahrnuje funkce, jako je například využití studenta, vytváření kurzu a přiřazení instruktora.</span><span class="sxs-lookup"><span data-stu-id="0cd70-625">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="0cd70-626">Tato stránka je první v sérii kurzů, které vysvětlují, jak vytvořit ukázkovou aplikaci Contoso University.</span><span class="sxs-lookup"><span data-stu-id="0cd70-626">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="0cd70-627">Stažení nebo zobrazení dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="0cd70-627">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="0cd70-628">[Pokyny ke stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="0cd70-628">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0cd70-629">Požadavky</span><span class="sxs-lookup"><span data-stu-id="0cd70-629">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cd70-630">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cd70-630">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cd70-631">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cd70-631">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="0cd70-632">Znalost [ Razor stránek](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="0cd70-632">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="0cd70-633">Noví Programátori by měli před spuštěním této série dokončit [Začínáme se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="0cd70-633">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="0cd70-634">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="0cd70-634">Troubleshooting</span></span>

<span data-ttu-id="0cd70-635">Pokud narazíte na problém, který nelze vyřešit, můžete řešení obecně najít porovnáním kódu s [dokončeným projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="0cd70-635">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="0cd70-636">Dobrým způsobem, jak získat pomoc, je odeslání otázky do [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) pro [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="0cd70-636">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="0cd70-637">Webová aplikace společnosti Contoso University</span><span class="sxs-lookup"><span data-stu-id="0cd70-637">The Contoso University web app</span></span>

<span data-ttu-id="0cd70-638">Aplikace sestavená v těchto kurzech je základním webem na univerzitě.</span><span class="sxs-lookup"><span data-stu-id="0cd70-638">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="0cd70-639">Uživatelé můžou zobrazit a aktualizovat informace o studentech, kurzech a instruktorech.</span><span class="sxs-lookup"><span data-stu-id="0cd70-639">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="0cd70-640">Tady je několik obrazovek vytvořených v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-640">Here are a few of the screens created in the tutorial.</span></span>

![Stránka indexu studentů](intro/_static/students-index.png)

![Stránka pro úpravy studentů](intro/_static/student-edit.png)

<span data-ttu-id="0cd70-643">Styl uživatelského rozhraní tohoto webu je blízko toho, co vygenerovaly předdefinované šablony.</span><span class="sxs-lookup"><span data-stu-id="0cd70-643">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="0cd70-644">Tento kurz se zaměřuje na EF Core se Razor stránkami, nikoli v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0cd70-644">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="0cd70-645">Vytvoření Razor webové aplikace ContosoUniversity Pages</span><span class="sxs-lookup"><span data-stu-id="0cd70-645">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cd70-646">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cd70-646">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0cd70-647">V nabídce **soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-647">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0cd70-648">Vytvořte novou ASP.NET Core webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0cd70-648">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="0cd70-649">Pojmenujte projekt **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-649">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="0cd70-650">Je důležité pojmenovat projekt *ContosoUniversity* , aby se obory názvů shodovaly, když je kód zkopírován/vložen.</span><span class="sxs-lookup"><span data-stu-id="0cd70-650">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="0cd70-651">V rozevíracím seznamu vyberte **ASP.NET Core 2,1** a potom vyberte možnost **Webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-651">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="0cd70-652">Obrázky předchozích kroků najdete v tématu [vytvoření Razor webové aplikace](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="0cd70-652">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="0cd70-653">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0cd70-653">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cd70-654">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cd70-654">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="0cd70-655">Nastavení stylu webu</span><span class="sxs-lookup"><span data-stu-id="0cd70-655">Set up the site style</span></span>

<span data-ttu-id="0cd70-656">Několik změn nastaví nabídku webu, rozložení a domovskou stránku.</span><span class="sxs-lookup"><span data-stu-id="0cd70-656">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="0cd70-657">Update *Pages/Shared/_Layout. cshtml* s následujícími změnami:</span><span class="sxs-lookup"><span data-stu-id="0cd70-657">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="0cd70-658">Změňte všechny výskyty "ContosoUniversity" na "contoso University".</span><span class="sxs-lookup"><span data-stu-id="0cd70-658">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="0cd70-659">Existují tři výskyty.</span><span class="sxs-lookup"><span data-stu-id="0cd70-659">There are three occurrences.</span></span>

* <span data-ttu-id="0cd70-660">Přidejte položky nabídky pro **studenty**, **kurzy**, **instruktory**a **oddělení**a odstraňte položku nabídky **kontakt** .</span><span class="sxs-lookup"><span data-stu-id="0cd70-660">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="0cd70-661">Změny jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="0cd70-661">The changes are highlighted.</span></span> <span data-ttu-id="0cd70-662">(Všechny *značky se nezobrazí* .)</span><span class="sxs-lookup"><span data-stu-id="0cd70-662">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="0cd70-663">Na *stránce pages/index. cshtml*nahraďte obsah souboru následujícím kódem, který nahradí text o ASP.NET a MVC textem o této aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0cd70-663">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="0cd70-664">Vytvoření datového modelu</span><span class="sxs-lookup"><span data-stu-id="0cd70-664">Create the data model</span></span>

<span data-ttu-id="0cd70-665">Vytvořte třídy entit pro aplikaci Contoso University.</span><span class="sxs-lookup"><span data-stu-id="0cd70-665">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="0cd70-666">Začněte následujícími třemi entitami:</span><span class="sxs-lookup"><span data-stu-id="0cd70-666">Start with the following three entities:</span></span>

![Kurz – registrace – diagram datového modelu studenta](intro/_static/data-model-diagram.png)

<span data-ttu-id="0cd70-668">Mezi `Student` entitami a entitami je vztah 1: n `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-668">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="0cd70-669">Mezi `Course` entitami a entitami je vztah 1: n `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-669">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="0cd70-670">Student se může zaregistrovat v jakémkoli počtu kurzů.</span><span class="sxs-lookup"><span data-stu-id="0cd70-670">A student can enroll in any number of courses.</span></span> <span data-ttu-id="0cd70-671">Kurz může mít zaregistrovaný libovolný počet studentů.</span><span class="sxs-lookup"><span data-stu-id="0cd70-671">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="0cd70-672">V následujících oddílech je vytvořena třída pro každou z těchto entit.</span><span class="sxs-lookup"><span data-stu-id="0cd70-672">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="0cd70-673">Entita studenta</span><span class="sxs-lookup"><span data-stu-id="0cd70-673">The Student entity</span></span>

![Diagram entity studenta](intro/_static/student-entity.png)

<span data-ttu-id="0cd70-675">Vytvořte složku *modelů* .</span><span class="sxs-lookup"><span data-stu-id="0cd70-675">Create a *Models* folder.</span></span> <span data-ttu-id="0cd70-676">Ve složce *modely* vytvořte soubor třídy s názvem *student.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="0cd70-676">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="0cd70-677">`ID`Vlastnost se zobrazí jako sloupec primárního klíče tabulky databáze (DB), která odpovídá této třídě.</span><span class="sxs-lookup"><span data-stu-id="0cd70-677">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="0cd70-678">Ve výchozím nastavení EF Core interpretuje vlastnost s názvem `ID` nebo `classnameID` jako primární klíč.</span><span class="sxs-lookup"><span data-stu-id="0cd70-678">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="0cd70-679">V `classnameID` `classname` je název třídy.</span><span class="sxs-lookup"><span data-stu-id="0cd70-679">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="0cd70-680">Alternativní automaticky rozpoznaný primární klíč je `StudentID` v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-680">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="0cd70-681">`Enrollments`Vlastnost je [navigační vlastnost](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="0cd70-681">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="0cd70-682">Vlastnosti navigace odkazují na další entity, které se vztahují k této entitě.</span><span class="sxs-lookup"><span data-stu-id="0cd70-682">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="0cd70-683">V tomto případě `Enrollments` vlastnost `Student entity` obsahuje všechny `Enrollment` entity, které jsou v relaci `Student` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-683">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="0cd70-684">Například pokud má řádek studenta v databázi dva související řádky registrace, `Enrollments` navigační vlastnost obsahuje tyto dvě `Enrollment` entity.</span><span class="sxs-lookup"><span data-stu-id="0cd70-684">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="0cd70-685">Související `Enrollment` řádek je řádek, který obsahuje hodnotu primárního klíče tohoto studenta ve `StudentID` sloupci.</span><span class="sxs-lookup"><span data-stu-id="0cd70-685">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="0cd70-686">Předpokládejme například, že student s ID = 1 má dva řádky v `Enrollment` tabulce.</span><span class="sxs-lookup"><span data-stu-id="0cd70-686">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="0cd70-687">`Enrollment`Tabulka má dva řádky s `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="0cd70-687">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="0cd70-688">`StudentID` je cizí klíč v `Enrollment` tabulce, který určuje studenta v `Student` tabulce.</span><span class="sxs-lookup"><span data-stu-id="0cd70-688">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="0cd70-689">Pokud navigační vlastnost může obsahovat více entit, musí být vlastnost navigace typu seznam, například `ICollection<T>` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-689">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="0cd70-690">`ICollection<T>` lze zadat nebo typ jako `List<T>` nebo `HashSet<T>` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-690">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="0cd70-691">Při `ICollection<T>` použití EF Core vytvoří `HashSet<T>` ve výchozím nastavení kolekci.</span><span class="sxs-lookup"><span data-stu-id="0cd70-691">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="0cd70-692">Navigační vlastnosti, které obsahují více entit, přicházejí ze vztahů m:n a 1: n.</span><span class="sxs-lookup"><span data-stu-id="0cd70-692">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="0cd70-693">Entita registrace</span><span class="sxs-lookup"><span data-stu-id="0cd70-693">The Enrollment entity</span></span>

![Diagram entity registrace](intro/_static/enrollment-entity.png)

<span data-ttu-id="0cd70-695">Ve složce *modely* vytvořte *Enrollment.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="0cd70-695">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="0cd70-696">`EnrollmentID`Vlastnost je primární klíč.</span><span class="sxs-lookup"><span data-stu-id="0cd70-696">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="0cd70-697">Tato entita používá `classnameID` vzor místo `ID` podobně jako `Student` entita.</span><span class="sxs-lookup"><span data-stu-id="0cd70-697">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="0cd70-698">Vývojáři obvykle volí jeden model a používají ho v rámci datového modelu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-698">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="0cd70-699">V pozdějším kurzu se zobrazuje použití ID bez ClassName, které usnadňuje implementaci dědičnosti v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-699">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="0cd70-700">`Grade`Vlastnost je `enum` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-700">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="0cd70-701">Otazník po `Grade` deklaraci typu označuje, že vlastnost může `Grade` mít hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="0cd70-701">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="0cd70-702">Hodnota, která je null, se liší od nulové třídy – hodnota null znamená, že se nejedná o známku nebo ještě není přiřazená.</span><span class="sxs-lookup"><span data-stu-id="0cd70-702">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="0cd70-703">`StudentID`Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Student` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-703">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="0cd70-704">`Enrollment`Entita je přidružená k jedné `Student` entitě, takže vlastnost obsahuje jednu `Student` entitu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-704">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="0cd70-705">`Student`Entita se liší od `Student.Enrollments` navigační vlastnosti, která obsahuje více `Enrollment` entit.</span><span class="sxs-lookup"><span data-stu-id="0cd70-705">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="0cd70-706">`CourseID`Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Course` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-706">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="0cd70-707">`Enrollment`Entita je přidružená k jedné `Course` entitě.</span><span class="sxs-lookup"><span data-stu-id="0cd70-707">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="0cd70-708">EF Core interpretuje vlastnost jako cizí klíč, pokud má název `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-708">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="0cd70-709">Například `StudentID` pro `Student` vlastnost navigace, protože `Student` primární klíč entity je `ID` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-709">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="0cd70-710">Lze také pojmenovat vlastnosti cizího klíče `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-710">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="0cd70-711">Například `CourseID` vzhledem k tomu, že `Course` primární klíč entity je `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-711">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="0cd70-712">Entita kurzu</span><span class="sxs-lookup"><span data-stu-id="0cd70-712">The Course entity</span></span>

![Diagram entity kurzu](intro/_static/course-entity.png)

<span data-ttu-id="0cd70-714">Ve složce *modely* vytvořte *Course.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="0cd70-714">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="0cd70-715">`Enrollments`Vlastnost je navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="0cd70-715">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="0cd70-716">`Course`Entita může souviset s libovolným počtem `Enrollment` entit.</span><span class="sxs-lookup"><span data-stu-id="0cd70-716">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="0cd70-717">`DatabaseGenerated`Atribut umožňuje aplikaci zadat primární klíč, a ne vytvořit databázi.</span><span class="sxs-lookup"><span data-stu-id="0cd70-717">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="0cd70-718">Generování uživatelského rozhraní modelu studenta</span><span class="sxs-lookup"><span data-stu-id="0cd70-718">Scaffold the student model</span></span>

<span data-ttu-id="0cd70-719">V této části je model studenta vygenerovaný jako generátor.</span><span class="sxs-lookup"><span data-stu-id="0cd70-719">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="0cd70-720">To znamená, že nástroj pro generování uživatelského rozhraní vytváří stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model studenta.</span><span class="sxs-lookup"><span data-stu-id="0cd70-720">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="0cd70-721">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="0cd70-721">Build the project.</span></span>
* <span data-ttu-id="0cd70-722">Vytvořte složku *stránky/studenty* .</span><span class="sxs-lookup"><span data-stu-id="0cd70-722">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cd70-723">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cd70-723">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0cd70-724">V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *Pages/Students* > **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-724">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0cd70-725">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte \*\* Razor stránky pomocí Entity Framework (CRUD)\*\* > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-725">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="0cd70-726">Dokončete dialog **Přidat Razor stránky pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="0cd70-726">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="0cd70-727">V rozevíracím seznamu **třída modelu** vyberte **student (ContosoUniversity. Models)**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-727">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="0cd70-728">V řádku **třídy kontextu dat** vyberte **+** znaménko (plus) a změňte vygenerovaný název na **ContosoUniversity. Models. SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-728">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="0cd70-729">V rozevíracím seznamu **Třída kontextu dat** vyberte **ContosoUniversity. Models. SchoolContext.**</span><span class="sxs-lookup"><span data-stu-id="0cd70-729">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="0cd70-730">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-730">Select **Add**.</span></span>

![Dialogové okno CRUD](intro/_static/s1.png)

<span data-ttu-id="0cd70-732">Pokud máte problém s předchozím krokem, podívejte [se do části vygenerované modely filmů](xref:tutorials/razor-pages/model#scaffold-the-movie-model) .</span><span class="sxs-lookup"><span data-stu-id="0cd70-732">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cd70-733">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cd70-733">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0cd70-734">Spusťte následující příkazy pro generování uživatelského rozhraní modelu studenta.</span><span class="sxs-lookup"><span data-stu-id="0cd70-734">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="0cd70-735">Proces generování uživatelského rozhraní byl vytvořen a změnil následující soubory:</span><span class="sxs-lookup"><span data-stu-id="0cd70-735">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="0cd70-736">Vytvořené soubory</span><span class="sxs-lookup"><span data-stu-id="0cd70-736">Files created</span></span>

* <span data-ttu-id="0cd70-737">*Stránky/studenty* Vytvořit, odstranit, podrobnosti, upravit, index.</span><span class="sxs-lookup"><span data-stu-id="0cd70-737">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="0cd70-738">*Data/SchoolContext. cs*</span><span class="sxs-lookup"><span data-stu-id="0cd70-738">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="0cd70-739">Aktualizace souborů</span><span class="sxs-lookup"><span data-stu-id="0cd70-739">File updates</span></span>

* <span data-ttu-id="0cd70-740">*Startup.cs* : změny v tomto souboru jsou podrobně popsané v další části.</span><span class="sxs-lookup"><span data-stu-id="0cd70-740">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="0cd70-741">*appsettings.js* : připojovací řetězec použitý k připojení k místní databázi je přidán.</span><span class="sxs-lookup"><span data-stu-id="0cd70-741">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="0cd70-742">Kontrola kontextu zaregistrovaného vkládáním závislostí</span><span class="sxs-lookup"><span data-stu-id="0cd70-742">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="0cd70-743">ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0cd70-743">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0cd70-744">Služby (například kontext EF Core DB) jsou registrovány pomocí injektáže závislosti při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="0cd70-744">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="0cd70-745">Komponenty, které vyžadují tyto služby (například Razor stránky), poskytují tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="0cd70-745">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="0cd70-746">Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-746">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="0cd70-747">Nástroj pro generování uživatelského rozhraní automaticky vytvořil kontext databáze a zaregistroval ho pomocí kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="0cd70-747">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="0cd70-748">Projděte si `ConfigureServices` metodu v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="0cd70-748">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="0cd70-749">Zvýrazněný řádek byl přidán do modulu generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="0cd70-749">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="0cd70-750">Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="0cd70-750">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="0cd70-751">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) přečte připojovací řetězec z *appsettings.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="0cd70-751">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="0cd70-752">Aktualizace Main</span><span class="sxs-lookup"><span data-stu-id="0cd70-752">Update main</span></span>

<span data-ttu-id="0cd70-753">V *program.cs*upravte `Main` metodu a proveďte následující:</span><span class="sxs-lookup"><span data-stu-id="0cd70-753">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="0cd70-754">Získá instanci kontextu databáze z kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="0cd70-754">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="0cd70-755">Zavolejte  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="0cd70-755">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="0cd70-756">Vyřazení kontextu po `EnsureCreated` dokončení metody.</span><span class="sxs-lookup"><span data-stu-id="0cd70-756">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="0cd70-757">Následující kód ukazuje aktualizovaný soubor *program.cs* .</span><span class="sxs-lookup"><span data-stu-id="0cd70-757">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="0cd70-758">`EnsureCreated` zajišťuje, aby databáze pro kontext existovala.</span><span class="sxs-lookup"><span data-stu-id="0cd70-758">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="0cd70-759">Pokud existuje, není provedena žádná akce.</span><span class="sxs-lookup"><span data-stu-id="0cd70-759">If it exists, no action is taken.</span></span> <span data-ttu-id="0cd70-760">Pokud neexistuje, vytvoří se databáze a všechny její schéma.</span><span class="sxs-lookup"><span data-stu-id="0cd70-760">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="0cd70-761">`EnsureCreated` nepoužívá k vytvoření databáze migrace.</span><span class="sxs-lookup"><span data-stu-id="0cd70-761">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="0cd70-762">Databázi, která je vytvořená pomocí, se `EnsureCreated` nedá později aktualizovat pomocí migrace.</span><span class="sxs-lookup"><span data-stu-id="0cd70-762">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="0cd70-763">`EnsureCreated` se volá při spuštění aplikace, což umožňuje následující pracovní postup:</span><span class="sxs-lookup"><span data-stu-id="0cd70-763">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="0cd70-764">Odstraňte databázi.</span><span class="sxs-lookup"><span data-stu-id="0cd70-764">Delete the DB.</span></span>
* <span data-ttu-id="0cd70-765">Změňte schéma databáze (například přidat `EmailAddress` pole).</span><span class="sxs-lookup"><span data-stu-id="0cd70-765">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="0cd70-766">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0cd70-766">Run the app.</span></span>
* <span data-ttu-id="0cd70-767">`EnsureCreated` vytvoří databázi se `EmailAddress` sloupcem.</span><span class="sxs-lookup"><span data-stu-id="0cd70-767">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="0cd70-768">`EnsureCreated` je pohodlná v brzké fázi vývoje, když se schéma rychle vyvíjí.</span><span class="sxs-lookup"><span data-stu-id="0cd70-768">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="0cd70-769">Později v kurzu se databáze odstraní a použijí se migrace.</span><span class="sxs-lookup"><span data-stu-id="0cd70-769">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="0cd70-770">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="0cd70-770">Test the app</span></span>

<span data-ttu-id="0cd70-771">Spusťte aplikaci a přijměte tuto cookie zásadu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-771">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="0cd70-772">Tato aplikace neuchovává osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="0cd70-772">This app doesn't keep personal information.</span></span> <span data-ttu-id="0cd70-773">Informace o cookie zásadách najdete v tématu Podpora pro [EU obecné nařízení O ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="0cd70-773">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="0cd70-774">Vyberte odkaz **Students** a pak **vytvořte nový**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-774">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="0cd70-775">Otestujte odkazy Upravit, Podrobnosti a Odstranit.</span><span class="sxs-lookup"><span data-stu-id="0cd70-775">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="0cd70-776">Projděte si kontext SchoolContext DB.</span><span class="sxs-lookup"><span data-stu-id="0cd70-776">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="0cd70-777">Hlavní třída, která koordinuje funkce EF Core pro daný datový model, je třída kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="0cd70-777">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="0cd70-778">Kontext dat je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="0cd70-778">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="0cd70-779">Kontext dat určuje, které entity jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-779">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="0cd70-780">V tomto projektu je třída pojmenována `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-780">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="0cd70-781">*SchoolContext.cs* aktualizujte pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="0cd70-781">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="0cd70-782">Zvýrazněný kód vytvoří vlastnost [negenerickými \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro každou sadu entit.</span><span class="sxs-lookup"><span data-stu-id="0cd70-782">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="0cd70-783">V terminologii EF Core:</span><span class="sxs-lookup"><span data-stu-id="0cd70-783">In EF Core terminology:</span></span>

* <span data-ttu-id="0cd70-784">Sada entit obvykle odpovídá tabulce databáze.</span><span class="sxs-lookup"><span data-stu-id="0cd70-784">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="0cd70-785">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="0cd70-785">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="0cd70-786">`DbSet<Enrollment>` a `DbSet<Course>` může být vynecháno.</span><span class="sxs-lookup"><span data-stu-id="0cd70-786">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="0cd70-787">EF Core je implicitně obsahuje, protože `Student` entita odkazuje na `Enrollment` entitu a `Enrollment` entita odkazuje na `Course` entitu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-787">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="0cd70-788">V tomto kurzu ponechejte `DbSet<Enrollment>` a `DbSet<Course>` v `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-788">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="0cd70-789">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="0cd70-789">SQL Server Express LocalDB</span></span>

<span data-ttu-id="0cd70-790">Připojovací řetězec Určuje [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="0cd70-790">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="0cd70-791">LocalDB je zjednodušená verze databázového stroje SQL Server Express a je určena pro vývoj aplikací, nikoli za použití v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="0cd70-791">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="0cd70-792">LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje složitá konfigurace.</span><span class="sxs-lookup"><span data-stu-id="0cd70-792">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="0cd70-793">Ve výchozím nastavení LocalDB vytvoří soubory *. mdf* DB v `C:/Users/<user>` adresáři.</span><span class="sxs-lookup"><span data-stu-id="0cd70-793">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="0cd70-794">Přidejte kód pro inicializaci databáze s testovacími daty</span><span class="sxs-lookup"><span data-stu-id="0cd70-794">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="0cd70-795">EF Core vytvoří prázdnou databázi.</span><span class="sxs-lookup"><span data-stu-id="0cd70-795">EF Core creates an empty DB.</span></span> <span data-ttu-id="0cd70-796">V této části `Initialize` je zapsána metoda pro naplnění testovacích dat.</span><span class="sxs-lookup"><span data-stu-id="0cd70-796">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="0cd70-797">Ve složce *data* vytvořte nový soubor třídy s názvem *DbInitializer.cs* a přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="0cd70-797">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="0cd70-798">Poznámka: předchozí kód používá `Models` pro obor názvů ( `namespace ContosoUniversity.Models` ) spíše než `Data` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-798">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="0cd70-799">`Models` je konzistentní s kódem generovaným pomocí generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0cd70-799">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="0cd70-800">Další informace najdete v [tomto problému s vygenerováním uživatelského rozhraní GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="0cd70-800">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="0cd70-801">Kód zkontroluje, jestli v databázi existují studenti.</span><span class="sxs-lookup"><span data-stu-id="0cd70-801">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="0cd70-802">Pokud databáze neobsahuje žádné studenty, databáze se inicializuje s testovacími daty.</span><span class="sxs-lookup"><span data-stu-id="0cd70-802">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="0cd70-803">Načte testovací data do polí `List<T>` , nikoli kolekce pro optimalizaci výkonu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-803">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="0cd70-804">`EnsureCreated`Metoda automaticky vytvoří databázi pro kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="0cd70-804">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="0cd70-805">Pokud databáze existuje, `EnsureCreated` vrátí se beze změny databáze.</span><span class="sxs-lookup"><span data-stu-id="0cd70-805">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="0cd70-806">V *program.cs*upravte `Main` metodu pro volání `Initialize` :</span><span class="sxs-lookup"><span data-stu-id="0cd70-806">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="0cd70-807">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cd70-807">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0cd70-808">Zastavte aplikaci, pokud je spuštěná, a spusťte následující příkaz v **konzole správce balíčků** (PMC):</span><span class="sxs-lookup"><span data-stu-id="0cd70-808">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cd70-809">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cd70-809">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0cd70-810">Zastavte aplikaci, pokud je spuštěná, a odstraňte soubor *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="0cd70-810">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="0cd70-811">Zobrazení databáze</span><span class="sxs-lookup"><span data-stu-id="0cd70-811">View the DB</span></span>

<span data-ttu-id="0cd70-812">Název databáze je vygenerován z dříve zadaného názvu kontextu plus pomlčka a identifikátor GUID.</span><span class="sxs-lookup"><span data-stu-id="0cd70-812">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="0cd70-813">Proto bude název databáze "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="0cd70-813">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="0cd70-814">Identifikátor GUID bude pro každého uživatele odlišný.</span><span class="sxs-lookup"><span data-stu-id="0cd70-814">The GUID will be different for each user.</span></span>
<span data-ttu-id="0cd70-815">Otevřete **Průzkumník objektů systému SQL Server** (SSOX) z nabídky **Zobrazit** v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0cd70-815">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="0cd70-816">V SSOX klikněte na **(LocalDB) \MSSQLLocalDB > databáze > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="0cd70-816">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="0cd70-817">Rozbalte uzel **tabulky** .</span><span class="sxs-lookup"><span data-stu-id="0cd70-817">Expand the **Tables** node.</span></span>

<span data-ttu-id="0cd70-818">Klikněte pravým tlačítkem myši na tabulku **student** a kliknutím na **Zobrazit data** Zobrazte vytvořené sloupce a řádky vložené do tabulky.</span><span class="sxs-lookup"><span data-stu-id="0cd70-818">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="0cd70-819">Asynchronní kód</span><span class="sxs-lookup"><span data-stu-id="0cd70-819">Asynchronous code</span></span>

<span data-ttu-id="0cd70-820">Asynchronní programování je výchozí režim pro ASP.NET Core a EF Core.</span><span class="sxs-lookup"><span data-stu-id="0cd70-820">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="0cd70-821">Na webovém serveru je k dispozici omezený počet vláken a v situacích vysokého zatížení se mohou používat všechna dostupná vlákna.</span><span class="sxs-lookup"><span data-stu-id="0cd70-821">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="0cd70-822">Pokud k tomu dojde, server nemůže zpracovat nové požadavky, dokud nebudou vlákna uvolněna.</span><span class="sxs-lookup"><span data-stu-id="0cd70-822">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="0cd70-823">Pomocí synchronního kódu může být mnoho vláken svázáno s tím, že ve skutečnosti neprovádí žádnou práci, protože čeká na dokončení vstupně-výstupních operací.</span><span class="sxs-lookup"><span data-stu-id="0cd70-823">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="0cd70-824">V případě asynchronního kódu, když proces čeká na dokončení vstupně-výstupních operací, je jeho vlákno uvolněno na server, který bude použit pro zpracování jiných požadavků.</span><span class="sxs-lookup"><span data-stu-id="0cd70-824">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="0cd70-825">Výsledkem je, že asynchronní kód umožňuje efektivnější použití prostředků serveru a server je povolen pro zpracování většího objemu dat bez prodlev.</span><span class="sxs-lookup"><span data-stu-id="0cd70-825">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="0cd70-826">Asynchronní kód zavádí v době běhu malé množství režie.</span><span class="sxs-lookup"><span data-stu-id="0cd70-826">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="0cd70-827">V situacích s nízkým provozem je dosaženo zanedbatelného výkonu, zatímco v situacích vysokého provozu je potenciální zlepšení výkonu značné.</span><span class="sxs-lookup"><span data-stu-id="0cd70-827">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="0cd70-828">V následujícím kódu, klíčové slovo [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` návratová hodnota, `await` klíčové slovo a `ToListAsync` Metoda provede asynchronní spouštění kódu.</span><span class="sxs-lookup"><span data-stu-id="0cd70-828">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="0cd70-829">`async`Klíčové slovo dává kompilátoru následující informace:</span><span class="sxs-lookup"><span data-stu-id="0cd70-829">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="0cd70-830">Vygenerujte zpětná volání pro části těla metody.</span><span class="sxs-lookup"><span data-stu-id="0cd70-830">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="0cd70-831">Automaticky vytvořte vrácený objekt [úkolu](/dotnet/api/system.threading.tasks.task) .</span><span class="sxs-lookup"><span data-stu-id="0cd70-831">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="0cd70-832">Další informace najdete v tématu [návratový typ úlohy](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="0cd70-832">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="0cd70-833">Implicitní návratový typ `Task` představuje probíhající práci.</span><span class="sxs-lookup"><span data-stu-id="0cd70-833">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="0cd70-834">`await`Klíčové slovo způsobí, že kompilátor rozdělí metodu do dvou částí.</span><span class="sxs-lookup"><span data-stu-id="0cd70-834">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="0cd70-835">První část končí asynchronně spuštěnou operací.</span><span class="sxs-lookup"><span data-stu-id="0cd70-835">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="0cd70-836">Druhá část je vložena do metody zpětného volání, která je volána po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="0cd70-836">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="0cd70-837">`ToListAsync` je asynchronní verze `ToList` metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="0cd70-837">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="0cd70-838">Některé věci, které je potřeba znát při psaní asynchronního kódu, který používá EF Core:</span><span class="sxs-lookup"><span data-stu-id="0cd70-838">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="0cd70-839">Asynchronně jsou spouštěny pouze příkazy, které způsobují odeslání dotazů nebo příkazů do databáze.</span><span class="sxs-lookup"><span data-stu-id="0cd70-839">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="0cd70-840">To zahrnuje, `ToListAsync` , `SingleOrDefaultAsync` , `FirstOrDefaultAsync` a `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-840">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="0cd70-841">Neobsahuje příkazy, které mění pouze `IQueryable` , například `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="0cd70-841">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="0cd70-842">Kontext EF Core není bezpečný pro přístup z více vláken: Nepokoušejte se současně provést více operací.</span><span class="sxs-lookup"><span data-stu-id="0cd70-842">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="0cd70-843">Chcete-li využít výhody z hlediska výkonu asynchronního kódu, ověřte, že balíčky knihoven (například pro stránkování) používají async, pokud volají EF Core metody, které odesílají dotazy do databáze.</span><span class="sxs-lookup"><span data-stu-id="0cd70-843">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="0cd70-844">Další informace o asynchronním programování v rozhraní .NET naleznete v tématu [Async Overview](/dotnet/standard/async) a [Asynchronous Programming with Async a await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="0cd70-844">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="0cd70-845">V dalším kurzu se přezkoumávají základní operace CRUD (vytváření, čtení, aktualizace, odstranění).</span><span class="sxs-lookup"><span data-stu-id="0cd70-845">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="0cd70-846">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0cd70-846">Additional resources</span></span>

* [<span data-ttu-id="0cd70-847">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="0cd70-847">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="0cd70-848">Další</span><span class="sxs-lookup"><span data-stu-id="0cd70-848">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
