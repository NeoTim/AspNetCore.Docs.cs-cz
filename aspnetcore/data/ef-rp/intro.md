---
title: Stránky Razor pomocí Entity Framework Core v ASP.NET Core – kurz 1 z 8
author: tdykstra
description: Ukazuje, jak vytvořit aplikaci pro stránky Razor pomocí Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 07/22/2019
uid: data/ef-rp/intro
ms.openlocfilehash: 3b3f159382bba82cf1b55ca34f52d3db38c5dc7c
ms.sourcegitcommit: dc5b293e08336dc236de66ed1834f7ef78359531
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2019
ms.locfileid: "71011113"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="0c18d-103">Stránky Razor pomocí Entity Framework Core v ASP.NET Core – kurz 1 z 8</span><span class="sxs-lookup"><span data-stu-id="0c18d-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="0c18d-104">Podle [Petr Dykstra](https://github.com/tdykstra) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0c18d-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0c18d-105">Toto je první v sérii kurzů, které ukazují, jak používat Entity Framework (EF) jádro v aplikaci ASP.NET Core Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="0c18d-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an ASP.NET Core Razor Pages app.</span></span> <span data-ttu-id="0c18d-106">Kurzy vytvářejí web pro fiktivní univerzitě společnosti Contoso.</span><span class="sxs-lookup"><span data-stu-id="0c18d-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="0c18d-107">Tato lokalita obsahuje funkce, jako je například využití studenta, vytváření kurzů a přiřazení instruktorů.</span><span class="sxs-lookup"><span data-stu-id="0c18d-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span>

[<span data-ttu-id="0c18d-108">Stažení nebo zobrazení dokončené aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c18d-108">Download or view the completed app.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="0c18d-109">[Pokyny ke stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="0c18d-109">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0c18d-110">Požadavky</span><span class="sxs-lookup"><span data-stu-id="0c18d-110">Prerequisites</span></span>

* <span data-ttu-id="0c18d-111">Pokud s Razor Pages teprve začínáte, Projděte si kurz [Začínáme s Razor Pagesm](xref:tutorials/razor-pages/razor-pages-start) , než začnete s tímto.</span><span class="sxs-lookup"><span data-stu-id="0c18d-111">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0c18d-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0c18d-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0c18d-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0c18d-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="0c18d-114">Databázové moduly</span><span class="sxs-lookup"><span data-stu-id="0c18d-114">Database engines</span></span>

<span data-ttu-id="0c18d-115">Pokyny pro Visual Studio používají [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), verzi SQL Server Express, která se spouští jenom v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="0c18d-115">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="0c18d-116">Pokyny pro [Visual Studio Code používají nástroj](https://www.sqlite.org/)pro databázový stroj pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="0c18d-116">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="0c18d-117">Pokud se rozhodnete použít SQLite, Stáhněte a nainstalujte nástroj třetí strany pro správu a zobrazení databáze SQLite, jako je například [prohlížeč databáze pro SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="0c18d-117">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="0c18d-118">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="0c18d-118">Troubleshooting</span></span>

<span data-ttu-id="0c18d-119">Pokud narazíte na problém, který nemůžete vyřešit, porovnejte kód s [dokončeným projektem](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="0c18d-119">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="0c18d-120">Dobrý způsob, jak získat pomoc, je odeslání otázky do StackOverflow.com s použitím [značky ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [značky EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="0c18d-120">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="0c18d-121">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="0c18d-121">The sample app</span></span>

<span data-ttu-id="0c18d-122">Aplikace vytvořené v těchto kurzech je webová stránka základní university.</span><span class="sxs-lookup"><span data-stu-id="0c18d-122">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="0c18d-123">Uživatelé mohou zobrazit a aktualizovat Všichni studenti, kurz a informace instruktorem.</span><span class="sxs-lookup"><span data-stu-id="0c18d-123">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="0c18d-124">Tady je několik obrazovek vytvořili v kurzu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-124">Here are a few of the screens created in the tutorial.</span></span>

![Studenti indexová stránka](intro/_static/students-index30.png)

![Stránky pro úpravu studentů](intro/_static/student-edit30.png)

<span data-ttu-id="0c18d-127">Styl uživatelského rozhraní tohoto webu je založen na předdefinovaných šablonách projektů.</span><span class="sxs-lookup"><span data-stu-id="0c18d-127">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="0c18d-128">V tomto kurzu se naučíte, jak používat EF Core, nikoli způsob přizpůsobení uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0c18d-128">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="0c18d-129">Pomocí odkazu v horní části stránky Získejte zdrojový kód dokončeného projektu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-129">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="0c18d-130">Složka *cu30* obsahuje kód pro verzi kurzu ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="0c18d-130">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="0c18d-131">Soubory, které reflektují stav kódu pro kurzy 1-7, najdete ve složce *cu30snapshots* .</span><span class="sxs-lookup"><span data-stu-id="0c18d-131">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0c18d-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0c18d-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0c18d-133">Spuštění aplikace po stažení dokončeného projektu:</span><span class="sxs-lookup"><span data-stu-id="0c18d-133">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="0c18d-134">Odstraňte tři soubory a jednu složku, která má v názvu název *SQLite* .</span><span class="sxs-lookup"><span data-stu-id="0c18d-134">Delete three files and one folder that have *SQLite* in the name.</span></span>
* <span data-ttu-id="0c18d-135">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="0c18d-135">Build the project.</span></span>
* <span data-ttu-id="0c18d-136">V konzole správce balíčků (PMC) spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="0c18d-136">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="0c18d-137">Spusťte projekt k osazení databáze.</span><span class="sxs-lookup"><span data-stu-id="0c18d-137">Run the project to seed the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0c18d-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0c18d-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0c18d-139">Spuštění aplikace po stažení dokončeného projektu:</span><span class="sxs-lookup"><span data-stu-id="0c18d-139">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="0c18d-140">Odstraňte *ContosoUniversity. csproj*a přejmenujte *ContosoUniversitySQLite. csproj* na *ContosoUniversity. csproj*.</span><span class="sxs-lookup"><span data-stu-id="0c18d-140">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="0c18d-141">Odstraňte *Startup.cs*a přejmenujte *StartupSQLite.cs* na *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="0c18d-141">Delete *Startup.cs*, and rename *StartupSQLite.cs* to *Startup.cs*.</span></span>
* <span data-ttu-id="0c18d-142">Odstraňte *appSettings. JSON*a přejmenujte *appSettingsSQLite.* JSON na *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="0c18d-142">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="0c18d-143">Odstraňte složku *migrace* a přejmenujte *MigrationsSQL* na *migrace*.</span><span class="sxs-lookup"><span data-stu-id="0c18d-143">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="0c18d-144">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="0c18d-144">Build the project.</span></span>
* <span data-ttu-id="0c18d-145">Na příkazovém řádku ve složce projektu spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="0c18d-145">At a command prompt in the project folder, run the following commands:</span></span>

  ```console
  dotnet tool install --global dotnet-ef --version 3.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="0c18d-146">V nástroji SQLite spusťte následující příkaz SQL:</span><span class="sxs-lookup"><span data-stu-id="0c18d-146">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="0c18d-147">Spusťte projekt k osazení databáze.</span><span class="sxs-lookup"><span data-stu-id="0c18d-147">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="0c18d-148">Vytvoření projektu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="0c18d-148">Create the web app project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0c18d-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0c18d-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0c18d-150">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="0c18d-150">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0c18d-151">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="0c18d-151">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="0c18d-152">Pojmenujte projekt *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="0c18d-152">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="0c18d-153">Je důležité použít tento přesný název, včetně velkých a malých písmen, aby se obory názvů shodovaly při zkopírování a vložení kódu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-153">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="0c18d-154">V rozevíracích seznamech vyberte **.NET Core** a **ASP.NET Core 3,0** a potom vyberte **Webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="0c18d-154">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0c18d-155">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0c18d-155">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0c18d-156">V terminálu přejděte do složky, ve které by měla být vytvořena složka projektu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-156">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="0c18d-157">Spuštěním následujících příkazů vytvořte projekt Razor Pages a `cd` do nové složky projektu:</span><span class="sxs-lookup"><span data-stu-id="0c18d-157">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```console
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="0c18d-158">Nastavit styl lokality</span><span class="sxs-lookup"><span data-stu-id="0c18d-158">Set up the site style</span></span>

<span data-ttu-id="0c18d-159">Pomocí aktualizace *stránek/Shared/_Layout. cshtml*nastavte záhlaví webu, zápatí a nabídku.</span><span class="sxs-lookup"><span data-stu-id="0c18d-159">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="0c18d-160">Změňte všechny výskyty "ContosoUniversity" na "University společnosti Contoso".</span><span class="sxs-lookup"><span data-stu-id="0c18d-160">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="0c18d-161">Existují tři výskyty.</span><span class="sxs-lookup"><span data-stu-id="0c18d-161">There are three occurrences.</span></span>

* <span data-ttu-id="0c18d-162">Odstraňte položky nabídky **Domů** a **Ochrana osobních údajů** a přidejte záznamy **o o** **studentech**, **kurzech**, **instruktorech**a **odděleních**.</span><span class="sxs-lookup"><span data-stu-id="0c18d-162">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="0c18d-163">Změny jsou zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="0c18d-163">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="0c18d-164">Na *stránce pages/index. cshtml*nahraďte obsah souboru následujícím kódem, který nahradí text o ASP.NET Core textem této aplikace:</span><span class="sxs-lookup"><span data-stu-id="0c18d-164">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="0c18d-165">Spusťte aplikaci, abyste ověřili, že se zobrazí domovská stránka.</span><span class="sxs-lookup"><span data-stu-id="0c18d-165">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="0c18d-166">Datový model</span><span class="sxs-lookup"><span data-stu-id="0c18d-166">The data model</span></span>

<span data-ttu-id="0c18d-167">V následujících částech se vytvoří datový model:</span><span class="sxs-lookup"><span data-stu-id="0c18d-167">The following sections create a data model:</span></span>

![Kurz – registrace – studentech modelového diagramu](intro/_static/data-model-diagram.png)

<span data-ttu-id="0c18d-169">Student se může zaregistrovat v jakémkoli počtu kurzů a kurz může mít zaregistrovaný libovolný počet studentů.</span><span class="sxs-lookup"><span data-stu-id="0c18d-169">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="0c18d-170">Entita studenta</span><span class="sxs-lookup"><span data-stu-id="0c18d-170">The Student entity</span></span>

![Diagram entity studenta](intro/_static/student-entity.png)

* <span data-ttu-id="0c18d-172">Vytvořte složku *modely* ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-172">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="0c18d-173">Vytvořte *modely/studenta. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="0c18d-173">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="0c18d-174">`ID` Vlastnost se zobrazí jako sloupec primárního klíče tabulky databáze, která odpovídá této třídě.</span><span class="sxs-lookup"><span data-stu-id="0c18d-174">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="0c18d-175">Ve výchozím nastavení interpretuje EF Core vlastnost s názvem `ID` nebo `classnameID` jako primární klíč.</span><span class="sxs-lookup"><span data-stu-id="0c18d-175">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="0c18d-176">Proto je `Student` `StudentID`alternativní automaticky rozpoznaný název pro primární klíč třídy.</span><span class="sxs-lookup"><span data-stu-id="0c18d-176">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span>

<span data-ttu-id="0c18d-177">`Enrollments` Je vlastnost [navigační vlastnost](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="0c18d-177">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="0c18d-178">Navigační vlastnosti obsahují další entity, které se vztahují k této entitě.</span><span class="sxs-lookup"><span data-stu-id="0c18d-178">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="0c18d-179">V takovém případě `Enrollments` vlastnost `Student` `Enrollment` entity obsahuje všechny entity, které se vztahují k danému studentovi.</span><span class="sxs-lookup"><span data-stu-id="0c18d-179">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="0c18d-180">Například pokud má řádek studenta v databázi dva související řádky registrace, `Enrollments` navigační vlastnost obsahuje tyto dvě entity registrace.</span><span class="sxs-lookup"><span data-stu-id="0c18d-180">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="0c18d-181">V databázi se řádek registrace vztahuje k řádku studenta, pokud jeho sloupec StudentID obsahuje hodnotu ID studenta.</span><span class="sxs-lookup"><span data-stu-id="0c18d-181">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="0c18d-182">Předpokládejme například, že řádek studenta má ID = 1.</span><span class="sxs-lookup"><span data-stu-id="0c18d-182">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="0c18d-183">Související řádky registrace budou mít StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="0c18d-183">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="0c18d-184">StudentID je *cizí klíč* v tabulce zápisu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-184">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="0c18d-185">Vlastnost je definována tak, `ICollection<Enrollment>` že může existovat více souvisejících entit zápisu. `Enrollments`</span><span class="sxs-lookup"><span data-stu-id="0c18d-185">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="0c18d-186">Můžete použít jiné typy kolekce, například `List<Enrollment>` nebo. `HashSet<Enrollment>`</span><span class="sxs-lookup"><span data-stu-id="0c18d-186">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="0c18d-187">Když `ICollection<Enrollment>` je používají, vytvoří EF Core `HashSet<Enrollment>` kolekcí ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="0c18d-187">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="0c18d-188">Registrace entity</span><span class="sxs-lookup"><span data-stu-id="0c18d-188">The Enrollment entity</span></span>

![Diagram entity registrace](intro/_static/enrollment-entity.png)

<span data-ttu-id="0c18d-190">Vytvořte *modely/registrace. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="0c18d-190">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="0c18d-191">Vlastnost je primární klíč. Tato entita `classnameID` používá vzor namísto `ID` samotného. `EnrollmentID`</span><span class="sxs-lookup"><span data-stu-id="0c18d-191">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="0c18d-192">Pro model produkčních dat vyberte jeden vzor a používejte ho konzistentně.</span><span class="sxs-lookup"><span data-stu-id="0c18d-192">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="0c18d-193">Tento kurz používá pouze k ilustraci toho, jak funguje.</span><span class="sxs-lookup"><span data-stu-id="0c18d-193">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="0c18d-194">Použití `ID` bez `classname` toho usnadňuje implementaci některých druhů změn datového modelu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-194">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="0c18d-195">`Grade` Vlastnost je `enum`.</span><span class="sxs-lookup"><span data-stu-id="0c18d-195">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="0c18d-196">Otazník po `Grade` deklaraci typu označuje, že vlastnost může `Grade` [mít hodnotu null](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="0c18d-196">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="0c18d-197">Třídu, která má hodnotu null, se liší od nulové&mdash;třídy null znamená, že známka není známa nebo ještě nebyla přiřazena.</span><span class="sxs-lookup"><span data-stu-id="0c18d-197">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="0c18d-198">`StudentID` Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Student`.</span><span class="sxs-lookup"><span data-stu-id="0c18d-198">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="0c18d-199">`Enrollment` Entita je přidružený nejméně k jednomu `Student` entity, tak vlastnost obsahuje jediný `Student` entity.</span><span class="sxs-lookup"><span data-stu-id="0c18d-199">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="0c18d-200">`CourseID` Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Course`.</span><span class="sxs-lookup"><span data-stu-id="0c18d-200">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="0c18d-201">`Enrollment` Entita je přidružený nejméně k jednomu `Course` entity.</span><span class="sxs-lookup"><span data-stu-id="0c18d-201">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="0c18d-202">EF Core interpretuje vlastnost jako cizí klíč, pokud je název `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="0c18d-202">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="0c18d-203">Například`StudentID` je cizí klíč `Student` pro `Student` navigační vlastnost, protože primární klíč entity je `ID`.</span><span class="sxs-lookup"><span data-stu-id="0c18d-203">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="0c18d-204">Vlastnosti cizího klíče může mít také název `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="0c18d-204">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="0c18d-205">Například `CourseID` vzhledem k tomu, `Course` je primární klíč entity `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="0c18d-205">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="0c18d-206">Kurz entity</span><span class="sxs-lookup"><span data-stu-id="0c18d-206">The Course entity</span></span>

![Diagram kurzu entity](intro/_static/course-entity.png)

<span data-ttu-id="0c18d-208">Vytvořte *modely/Course. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="0c18d-208">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="0c18d-209">`Enrollments` Je navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="0c18d-209">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="0c18d-210">A `Course` entit může souviset s libovolným počtem `Enrollment` entity.</span><span class="sxs-lookup"><span data-stu-id="0c18d-210">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="0c18d-211">`DatabaseGenerated` Atribut umožňuje aplikaci určit primární klíč místo toho, aby ho databáze vygenerovala.</span><span class="sxs-lookup"><span data-stu-id="0c18d-211">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="0c18d-212">Sestavte projekt, aby se ověřilo, že nejsou k dispozici žádné chyby kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="0c18d-212">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="0c18d-213">Stránky studenta pro generování uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="0c18d-213">Scaffold Student pages</span></span>

<span data-ttu-id="0c18d-214">V této části použijete nástroj ASP.NET Core pro generování uživatelského rozhraní k vygenerování:</span><span class="sxs-lookup"><span data-stu-id="0c18d-214">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="0c18d-215">Třída *kontextu* EF Core.</span><span class="sxs-lookup"><span data-stu-id="0c18d-215">An EF Core *context* class.</span></span> <span data-ttu-id="0c18d-216">Kontext je hlavní třída, která koordinuje funkce Entity Framework pro daný datový model.</span><span class="sxs-lookup"><span data-stu-id="0c18d-216">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="0c18d-217">Je odvozen z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="0c18d-217">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="0c18d-218">Stránky Razor, které zpracovávají operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro `Student` entitu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-218">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0c18d-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0c18d-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0c18d-220">Vytvořte složku *Students* ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="0c18d-220">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="0c18d-221">V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *Pages/Students* a vyberte **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="0c18d-221">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0c18d-222">V **přidat vygenerované uživatelské rozhraní** dialogového okna, vyberte **stránky Razor pomocí Entity Frameworku (CRUD)** > **přidat**.</span><span class="sxs-lookup"><span data-stu-id="0c18d-222">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="0c18d-223">V dialogovém okně **přidat Razor Pages pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="0c18d-223">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="0c18d-224">V **třída modelu** rozevíracího seznamu, vyberte **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="0c18d-224">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="0c18d-225">V řádku **třídy kontextu dat** vyberte **+** znaménko (plus).</span><span class="sxs-lookup"><span data-stu-id="0c18d-225">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="0c18d-226">Změňte název kontextu dat z *ContosoUniversity. Models. ContosoUniversityContext* na *ContosoUniversity. data. SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="0c18d-226">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="0c18d-227">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="0c18d-227">Select **Add**.</span></span>

<span data-ttu-id="0c18d-228">Automaticky se nainstalují tyto balíčky:</span><span class="sxs-lookup"><span data-stu-id="0c18d-228">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0c18d-229">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0c18d-229">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0c18d-230">Spuštěním následujících příkazů .NET Core CLI nainstalujte požadované balíčky NuGet:</span><span class="sxs-lookup"><span data-stu-id="0c18d-230">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```console
  dotnet add package Microsoft.EntityFrameworkCore.SQLite --version 3.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools --version 3.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
  dotnet add package Microsoft.Extensions.Logging.Debug --version 3.0.0-*
  ```

  <span data-ttu-id="0c18d-231">Pro generování uživatelského rozhraní je vyžadován balíček Microsoft. VisualStudio. Web. strategii. Design.</span><span class="sxs-lookup"><span data-stu-id="0c18d-231">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="0c18d-232">I když aplikace nebude používat SQL Server, nástroj pro generování uživatelského rozhraní potřebuje balíček SQL Server.</span><span class="sxs-lookup"><span data-stu-id="0c18d-232">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="0c18d-233">Vytvořte složku *stránky/studenty* .</span><span class="sxs-lookup"><span data-stu-id="0c18d-233">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="0c18d-234">Spusťte následující příkaz pro instalaci nástroje pro [generování uživatelského rozhraní ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="0c18d-234">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```console
  dotnet tool install --global dotnet-aspnet-codegenerator --version 3.0.0-*
  ```

* <span data-ttu-id="0c18d-235">Spusťte následující příkaz pro generování uživatelského rozhraní stránek studenta.</span><span class="sxs-lookup"><span data-stu-id="0c18d-235">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="0c18d-236">**Ve Windows**</span><span class="sxs-lookup"><span data-stu-id="0c18d-236">**On Windows**</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="0c18d-237">**V systému macOS nebo Linux**</span><span class="sxs-lookup"><span data-stu-id="0c18d-237">**On macOS or Linux**</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="0c18d-238">Pokud máte problém s předchozím krokem, sestavte projekt a opakujte krok generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0c18d-238">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="0c18d-239">Proces generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="0c18d-239">The scaffolding process:</span></span>

* <span data-ttu-id="0c18d-240">Vytvoří stránky Razor ve složce *Pages/Students* :</span><span class="sxs-lookup"><span data-stu-id="0c18d-240">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="0c18d-241">*Vytvoření. cshtml* a *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0c18d-241">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="0c18d-242">*Odstranění. cshtml* a *DELETE.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0c18d-242">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="0c18d-243">*Podrobnosti. cshtml* a *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0c18d-243">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="0c18d-244">*Upravit. cshtml* a *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0c18d-244">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="0c18d-245">*Index. cshtml* a *index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0c18d-245">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="0c18d-246">Vytvoří *data/SchoolContext. cs*.</span><span class="sxs-lookup"><span data-stu-id="0c18d-246">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="0c18d-247">Přidá kontext do injektáže závislosti v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="0c18d-247">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="0c18d-248">Přidá připojovací řetězec databáze do souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="0c18d-248">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="0c18d-249">Připojovací řetězec databáze</span><span class="sxs-lookup"><span data-stu-id="0c18d-249">Database connection string</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0c18d-250">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0c18d-250">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0c18d-251">Připojovací řetězec Určuje [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="0c18d-251">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="0c18d-252">LocalDB je Odlehčená verze SQL serveru Express Database Engine a je určená pro vývoj aplikací, není použití v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="0c18d-252">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="0c18d-253">Ve výchozím nastavení LocalDB vytvoří soubory *. mdf* v `C:/Users/<user>` adresáři.</span><span class="sxs-lookup"><span data-stu-id="0c18d-253">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0c18d-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0c18d-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0c18d-255">Změňte připojovací řetězec tak, aby odkazoval na soubor databáze SQLite s názvem *cu. DB*:</span><span class="sxs-lookup"><span data-stu-id="0c18d-255">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="0c18d-256">Aktualizuje třídu kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="0c18d-256">Update the database context class</span></span>

<span data-ttu-id="0c18d-257">Hlavní třída, která koordinuje funkce EF Core pro daný datový model, je třída kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="0c18d-257">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="0c18d-258">Kontext je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="0c18d-258">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="0c18d-259">Kontext určuje, které entity jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-259">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="0c18d-260">V tomto projektu je s názvem třídy `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="0c18d-260">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="0c18d-261">Aktualizace *SchoolContext.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="0c18d-261">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="0c18d-262">Zvýrazněný kód vytvoří [DbSet\<TEntity >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) vlastností pro každou sadu entit.</span><span class="sxs-lookup"><span data-stu-id="0c18d-262">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="0c18d-263">V terminologii EF Core:</span><span class="sxs-lookup"><span data-stu-id="0c18d-263">In EF Core terminology:</span></span>

* <span data-ttu-id="0c18d-264">Sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="0c18d-264">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="0c18d-265">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="0c18d-265">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="0c18d-266">Vzhledem k tomu, že sada entit obsahuje více entit, musí mít vlastnosti Negenerickými plurální názvy.</span><span class="sxs-lookup"><span data-stu-id="0c18d-266">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="0c18d-267">Vzhledem k tomu, že nástroj pro`Student` generování uživatelského rozhraní vytvořil negenerickými, tento krok `Students`změny změní na plural.</span><span class="sxs-lookup"><span data-stu-id="0c18d-267">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="0c18d-268">Chcete-li, aby kód Razor Pages odpovídal novému názvu negenerickými, proveďte globální změnu v celém projektu `_context.Student` na. `_context.Students`</span><span class="sxs-lookup"><span data-stu-id="0c18d-268">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="0c18d-269">K dispozici je 8 výskytů.</span><span class="sxs-lookup"><span data-stu-id="0c18d-269">There are 8 occurrences.</span></span>

<span data-ttu-id="0c18d-270">Sestavte projekt, aby se ověřilo, že nedošlo k chybám kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="0c18d-270">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="0c18d-271">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="0c18d-271">Startup.cs</span></span>

<span data-ttu-id="0c18d-272">ASP.NET Core využívá rozhraní [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0c18d-272">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0c18d-273">Služby (například kontext databáze EF Core) jsou registrovány pomocí injektáže závislosti při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c18d-273">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="0c18d-274">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="0c18d-274">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="0c18d-275">Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-275">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="0c18d-276">Nástroj pro generování uživatelského rozhraní automaticky zaregistroval třídu kontextu pomocí kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="0c18d-276">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0c18d-277">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0c18d-277">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0c18d-278">V `ConfigureServices`byly zvýrazněné řádky přidány pomocí generátoru:</span><span class="sxs-lookup"><span data-stu-id="0c18d-278">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0c18d-279">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0c18d-279">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0c18d-280">V `ConfigureServices`nástroji se ujistěte, že kód přidaný voláním `UseSqlite`uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0c18d-280">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="0c18d-281">Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-281">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="0c18d-282">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="0c18d-282">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="0c18d-283">Vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="0c18d-283">Create the database</span></span>

<span data-ttu-id="0c18d-284">Aktualizujte *program.cs* , aby se vytvořila databáze, pokud neexistuje:</span><span class="sxs-lookup"><span data-stu-id="0c18d-284">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="0c18d-285">Metoda [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) neprovede žádnou akci, pokud existuje databáze pro kontext.</span><span class="sxs-lookup"><span data-stu-id="0c18d-285">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="0c18d-286">Pokud žádná databáze neexistuje, vytvoří databázi a schéma.</span><span class="sxs-lookup"><span data-stu-id="0c18d-286">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="0c18d-287">`EnsureCreated`povolí následující pracovní postup pro zpracování změn datového modelu:</span><span class="sxs-lookup"><span data-stu-id="0c18d-287">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="0c18d-288">Odstraňte databázi.</span><span class="sxs-lookup"><span data-stu-id="0c18d-288">Delete the database.</span></span> <span data-ttu-id="0c18d-289">Všechna existující data budou ztracena.</span><span class="sxs-lookup"><span data-stu-id="0c18d-289">Any existing data is lost.</span></span>
* <span data-ttu-id="0c18d-290">Změňte datový model.</span><span class="sxs-lookup"><span data-stu-id="0c18d-290">Change the data model.</span></span> <span data-ttu-id="0c18d-291">Například přidejte `EmailAddress` pole.</span><span class="sxs-lookup"><span data-stu-id="0c18d-291">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="0c18d-292">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0c18d-292">Run the app.</span></span>
* <span data-ttu-id="0c18d-293">`EnsureCreated`vytvoří databázi s novým schématem.</span><span class="sxs-lookup"><span data-stu-id="0c18d-293">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="0c18d-294">Tento pracovní postup funguje dobře v rané fázi vývoje, když se schéma rychle vyvíjí, pokud nepotřebujete zachovat data.</span><span class="sxs-lookup"><span data-stu-id="0c18d-294">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="0c18d-295">Tato situace se liší v případě, že data, která byla zadána do databáze, musí být zachována.</span><span class="sxs-lookup"><span data-stu-id="0c18d-295">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="0c18d-296">V takovém případě použijte migrace.</span><span class="sxs-lookup"><span data-stu-id="0c18d-296">When that is the case, use migrations.</span></span>

<span data-ttu-id="0c18d-297">Později v rámci série kurzů odstraníte databázi, kterou vytvořil `EnsureCreated` , a místo toho použijete migrace.</span><span class="sxs-lookup"><span data-stu-id="0c18d-297">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="0c18d-298">Databázi vytvořenou nástrojem `EnsureCreated` nelze aktualizovat pomocí migrací.</span><span class="sxs-lookup"><span data-stu-id="0c18d-298">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="0c18d-299">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="0c18d-299">Test the app</span></span>

* <span data-ttu-id="0c18d-300">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0c18d-300">Run the app.</span></span>
* <span data-ttu-id="0c18d-301">Vyberte **studenty** propojení a potom **vytvořit nový**.</span><span class="sxs-lookup"><span data-stu-id="0c18d-301">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="0c18d-302">Otestujte upravit, podrobnosti a odkazy odstranit.</span><span class="sxs-lookup"><span data-stu-id="0c18d-302">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="0c18d-303">Přidání dat do databáze</span><span class="sxs-lookup"><span data-stu-id="0c18d-303">Seed the database</span></span>

<span data-ttu-id="0c18d-304">`EnsureCreated` Metoda vytvoří prázdnou databázi.</span><span class="sxs-lookup"><span data-stu-id="0c18d-304">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="0c18d-305">V této části se přidá kód, který naplní databázi testovacími daty.</span><span class="sxs-lookup"><span data-stu-id="0c18d-305">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="0c18d-306">Vytvořte *data/DbInitializer. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="0c18d-306">Create *Data/DbInitializer.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="0c18d-307">Kód kontroluje, zda v databázi existují studenti.</span><span class="sxs-lookup"><span data-stu-id="0c18d-307">The code checks if there are any students in the database.</span></span> <span data-ttu-id="0c18d-308">Pokud neexistují studenti, přidá testovací data do databáze.</span><span class="sxs-lookup"><span data-stu-id="0c18d-308">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="0c18d-309">Vytvoří testovací data v polích, nikoli `List<T>` kolekce pro optimalizaci výkonu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-309">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="0c18d-310">V *program.cs*nahraďte `EnsureCreated` volání `DbInitializer.Initialize` voláním:</span><span class="sxs-lookup"><span data-stu-id="0c18d-310">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ````

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0c18d-311">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0c18d-311">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0c18d-312">Zastavte aplikaci, pokud je spuštěná, a spusťte následující příkaz v **konzole správce balíčků** (PMC):</span><span class="sxs-lookup"><span data-stu-id="0c18d-312">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0c18d-313">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0c18d-313">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0c18d-314">Zastavte aplikaci, pokud je spuštěná, a odstraňte soubor *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="0c18d-314">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="0c18d-315">Restartujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0c18d-315">Restart the app.</span></span>

* <span data-ttu-id="0c18d-316">Vyberte stránku Students a zobrazte si dosazený údaj.</span><span class="sxs-lookup"><span data-stu-id="0c18d-316">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="0c18d-317">Zobrazení databáze</span><span class="sxs-lookup"><span data-stu-id="0c18d-317">View the database</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0c18d-318">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0c18d-318">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0c18d-319">Otevřít **Průzkumník objektů systému SQL Server** (SSOX) z **zobrazení** nabídky v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0c18d-319">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="0c18d-320">V SSOX vyberte **(LocalDB) \MSSQLLocalDB > databáze > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="0c18d-320">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="0c18d-321">Název databáze je vygenerován z dříve zadaného názvu kontextu plus pomlčka a identifikátor GUID.</span><span class="sxs-lookup"><span data-stu-id="0c18d-321">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="0c18d-322">Rozbalte **tabulky** uzlu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-322">Expand the **Tables** node.</span></span>
* <span data-ttu-id="0c18d-323">Klikněte pravým tlačítkem na **Student** tabulky a klikněte na tlačítko **Data zobrazení** vytvořené sloupce a řádky vložené do tabulky.</span><span class="sxs-lookup"><span data-stu-id="0c18d-323">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="0c18d-324">Kliknutím pravým tlačítkem na tabulku **student** a kliknutím na **Zobrazit kód** zjistíte `Student` , jak se model `Student` mapuje na schéma tabulky.</span><span class="sxs-lookup"><span data-stu-id="0c18d-324">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0c18d-325">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0c18d-325">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0c18d-326">Použijte nástroj SQLite k zobrazení schématu databáze a dat osazených daty.</span><span class="sxs-lookup"><span data-stu-id="0c18d-326">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="0c18d-327">Databázový soubor má název *cu. DB* a je umístěn ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-327">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="0c18d-328">Asynchronní kód</span><span class="sxs-lookup"><span data-stu-id="0c18d-328">Asynchronous code</span></span>

<span data-ttu-id="0c18d-329">Asynchronní programování je výchozím režimem pro ASP.NET Core a EF Core.</span><span class="sxs-lookup"><span data-stu-id="0c18d-329">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="0c18d-330">Webový server má omezený počet vláken, které jsou k dispozici, a v situacích, vysokého zatížení všech dostupných vláken může být používán.</span><span class="sxs-lookup"><span data-stu-id="0c18d-330">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="0c18d-331">Pokud k tomu dojde, server nemůže zpracovat nové žádosti, dokud se uvolnit vlákna.</span><span class="sxs-lookup"><span data-stu-id="0c18d-331">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="0c18d-332">Přestože se nejedná skutečně každé dílo vzhledem k tomu, že čekání na vstupně-výstupních operací na dokončení, může kódem synchronní svázané několika vlákny.</span><span class="sxs-lookup"><span data-stu-id="0c18d-332">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="0c18d-333">Asynchronní kód když proces čeká na vstupně-výstupních operací na dokončení, je jeho vlákno uvolněn pro server určený pro zpracováním jiných požadavků.</span><span class="sxs-lookup"><span data-stu-id="0c18d-333">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="0c18d-334">Výsledkem je, že asynchronní kód umožňuje efektivnější použití prostředků serveru a server může zpracovávat více provozu bez prodlev.</span><span class="sxs-lookup"><span data-stu-id="0c18d-334">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="0c18d-335">Asynchronní kód zavést malé množství režie v době běhu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-335">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="0c18d-336">V situacích s nízkým provozem stiskněte výkonu je zanedbatelný, při vysoké návštěvnosti situacích, potenciálních zlepšení výkonu je důležitým.</span><span class="sxs-lookup"><span data-stu-id="0c18d-336">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="0c18d-337">V následujícím kódu [asynchronní](/dotnet/csharp/language-reference/keywords/async) – klíčové slovo, `Task<T>` návratovou hodnotu, `await` – klíčové slovo, a `ToListAsync` metoda změňte kód spustit asynchronně.</span><span class="sxs-lookup"><span data-stu-id="0c18d-337">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="0c18d-338">`async` – Klíčové slovo instruuje kompilátor, aby:</span><span class="sxs-lookup"><span data-stu-id="0c18d-338">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="0c18d-339">Generovat zpětná volání pro části tělo metody.</span><span class="sxs-lookup"><span data-stu-id="0c18d-339">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="0c18d-340">Vytvořte vrácený objekt [úkolu](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) .</span><span class="sxs-lookup"><span data-stu-id="0c18d-340">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="0c18d-341">`Task<T>` Návratový typ představuje probíhající práci.</span><span class="sxs-lookup"><span data-stu-id="0c18d-341">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="0c18d-342">`await` – Klíčové slovo způsobí, že kompilátor metodu rozdělit do dvou částí.</span><span class="sxs-lookup"><span data-stu-id="0c18d-342">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="0c18d-343">První část končí operace, která se spustí asynchronně.</span><span class="sxs-lookup"><span data-stu-id="0c18d-343">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="0c18d-344">Druhá část je nepoužili metodu zpětného volání, která je volána po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="0c18d-344">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="0c18d-345">`ToListAsync` je asynchronní verze `ToList` – metoda rozšíření.</span><span class="sxs-lookup"><span data-stu-id="0c18d-345">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="0c18d-346">Některé co je potřeba mít na paměti při zápisu asynchronního kódu, který používá EF Core:</span><span class="sxs-lookup"><span data-stu-id="0c18d-346">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="0c18d-347">Asynchronně jsou spouštěny pouze příkazy, které způsobují odeslání dotazů nebo příkazů do databáze.</span><span class="sxs-lookup"><span data-stu-id="0c18d-347">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="0c18d-348">To zahrnuje `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`a .`SaveChangesAsync`</span><span class="sxs-lookup"><span data-stu-id="0c18d-348">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="0c18d-349">Neměl by zahrnovat příkazy, které stačí změnit `IQueryable`, jako například `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="0c18d-349">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="0c18d-350">Objekt context EF Core není bezpečné pro vlákna: nedoporučujeme provádět více operací paralelně.</span><span class="sxs-lookup"><span data-stu-id="0c18d-350">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="0c18d-351">Chcete-li využít výhody z hlediska výkonu asynchronního kódu, ověřte, že balíčky knihoven (například pro stránkování) používají async, pokud volají EF Core metody, které odesílají dotazy do databáze.</span><span class="sxs-lookup"><span data-stu-id="0c18d-351">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="0c18d-352">Další informace o asynchronním programování v rozhraní .NET najdete v tématu [asynchronní přehled](/dotnet/standard/async) a [asynchronní programování pomocí modifikátoru async a operátoru await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="0c18d-352">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="0c18d-353">Další kroky</span><span class="sxs-lookup"><span data-stu-id="0c18d-353">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="0c18d-354">Další kurz</span><span class="sxs-lookup"><span data-stu-id="0c18d-354">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0c18d-355">Contoso University ukázkovou webovou aplikaci ukazuje, jak vytvořit aplikaci ASP.NET Core Razor Pages pomocí Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="0c18d-355">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="0c18d-356">Ukázková aplikace je webovou stránku pro fiktivní společnosti Contoso University.</span><span class="sxs-lookup"><span data-stu-id="0c18d-356">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="0c18d-357">Zahrnuje funkce, jako student přijetí, kurz vytvoření a přiřazení instruktorem.</span><span class="sxs-lookup"><span data-stu-id="0c18d-357">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="0c18d-358">Tato stránka je první ze série kurzů, které vysvětlují, jak vytvořit ukázková aplikace Contoso University.</span><span class="sxs-lookup"><span data-stu-id="0c18d-358">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="0c18d-359">Stažení nebo zobrazení dokončené aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c18d-359">Download or view the completed app.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="0c18d-360">[Pokyny ke stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="0c18d-360">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0c18d-361">Požadavky</span><span class="sxs-lookup"><span data-stu-id="0c18d-361">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0c18d-362">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0c18d-362">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0c18d-363">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0c18d-363">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="0c18d-364">Znalost [stránky Razor](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="0c18d-364">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="0c18d-365">By se měla dokončit programátory [Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start) před zahájením této řady.</span><span class="sxs-lookup"><span data-stu-id="0c18d-365">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="0c18d-366">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="0c18d-366">Troubleshooting</span></span>

<span data-ttu-id="0c18d-367">Pokud narazíte na problém nevyřešíte sami, můžete najít řešení obvykle porovnáním kódu [dokončený projekt](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="0c18d-367">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="0c18d-368">Je dobrým způsobem, jak získat pomoc tím, že publikuje dotaz do [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) pro [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="0c18d-368">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="0c18d-369">Webové aplikace Contoso University</span><span class="sxs-lookup"><span data-stu-id="0c18d-369">The Contoso University web app</span></span>

<span data-ttu-id="0c18d-370">Aplikace vytvořené v těchto kurzech je webová stránka základní university.</span><span class="sxs-lookup"><span data-stu-id="0c18d-370">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="0c18d-371">Uživatelé mohou zobrazit a aktualizovat Všichni studenti, kurz a informace instruktorem.</span><span class="sxs-lookup"><span data-stu-id="0c18d-371">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="0c18d-372">Tady je několik obrazovek vytvořili v kurzu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-372">Here are a few of the screens created in the tutorial.</span></span>

![Studenti indexová stránka](intro/_static/students-index.png)

![Stránky pro úpravu studentů](intro/_static/student-edit.png)

<span data-ttu-id="0c18d-375">Styl uživatelského rozhraní tohoto webu se blíží co je generována pomocí integrovaných šablon.</span><span class="sxs-lookup"><span data-stu-id="0c18d-375">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="0c18d-376">Kurz zaměřuje se na EF Core se stránkami Razor, uživatelské rozhraní ne.</span><span class="sxs-lookup"><span data-stu-id="0c18d-376">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-razor-pages-web-app"></a><span data-ttu-id="0c18d-377">Vytvoření webové aplikace stránky Razor ContosoUniversity</span><span class="sxs-lookup"><span data-stu-id="0c18d-377">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0c18d-378">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0c18d-378">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0c18d-379">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="0c18d-379">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0c18d-380">Vytvořte novou webovou aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0c18d-380">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="0c18d-381">Pojmenujte projekt **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="0c18d-381">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="0c18d-382">Je důležité projekt pojmenujte *ContosoUniversity* tak obory názvů případy, kdy kód je zkopírované a vložené.</span><span class="sxs-lookup"><span data-stu-id="0c18d-382">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="0c18d-383">Vyberte **ASP.NET Core 2.1** v rozevíracím seznamu a pak vyberte **webovou aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="0c18d-383">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="0c18d-384">Obrázky v předchozích krocích, naleznete v tématu [vytvořit webová aplikace Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="0c18d-384">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="0c18d-385">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0c18d-385">Run the app.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0c18d-386">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0c18d-386">Visual Studio Code</span></span>](#tab/visual-studio-code)

```CLI
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="0c18d-387">Nastavit styl lokality</span><span class="sxs-lookup"><span data-stu-id="0c18d-387">Set up the site style</span></span>

<span data-ttu-id="0c18d-388">Několik změn nastavení v nabídce webu, rozložení a domovské stránky.</span><span class="sxs-lookup"><span data-stu-id="0c18d-388">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="0c18d-389">Aktualizace *Pages/Shared/_Layout.cshtml* s následujícími změnami:</span><span class="sxs-lookup"><span data-stu-id="0c18d-389">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="0c18d-390">Změňte všechny výskyty "ContosoUniversity" na "University společnosti Contoso".</span><span class="sxs-lookup"><span data-stu-id="0c18d-390">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="0c18d-391">Existují tři výskyty.</span><span class="sxs-lookup"><span data-stu-id="0c18d-391">There are three occurrences.</span></span>

* <span data-ttu-id="0c18d-392">Přidání položek nabídky **studenty**, **kurzy**, **Instruktoři**, a **oddělení**a odstranit **kontakt** položku nabídky.</span><span class="sxs-lookup"><span data-stu-id="0c18d-392">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="0c18d-393">Změny jsou zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="0c18d-393">The changes are highlighted.</span></span> <span data-ttu-id="0c18d-394">(Všechny značky je *není* zobrazit.)</span><span class="sxs-lookup"><span data-stu-id="0c18d-394">(All the markup is *not* displayed.)</span></span>

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="0c18d-395">V *Pages/Index.cshtml*, nahraďte obsah souboru následující kód, který nahradí text o ASP.NET a MVC o této aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0c18d-395">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="0c18d-396">Vytvoření datového modelu</span><span class="sxs-lookup"><span data-stu-id="0c18d-396">Create the data model</span></span>

<span data-ttu-id="0c18d-397">Vytvoření tříd entit pro aplikaci Contoso University.</span><span class="sxs-lookup"><span data-stu-id="0c18d-397">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="0c18d-398">Začněte s následující tři entity:</span><span class="sxs-lookup"><span data-stu-id="0c18d-398">Start with the following three entities:</span></span>

![Kurz – registrace – studentech modelového diagramu](intro/_static/data-model-diagram.png)

<span data-ttu-id="0c18d-400">Existuje vztah jeden mnoho mezi `Student` a `Enrollment` entity.</span><span class="sxs-lookup"><span data-stu-id="0c18d-400">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="0c18d-401">Existuje vztah jeden mnoho mezi `Course` a `Enrollment` entity.</span><span class="sxs-lookup"><span data-stu-id="0c18d-401">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="0c18d-402">Student může zaregistrovat libovolný počet kurzů.</span><span class="sxs-lookup"><span data-stu-id="0c18d-402">A student can enroll in any number of courses.</span></span> <span data-ttu-id="0c18d-403">Kurz můžete mít libovolný počet studentů zaregistrovaná do něj.</span><span class="sxs-lookup"><span data-stu-id="0c18d-403">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="0c18d-404">V následujících částech je vytvořená třída pro každou z těchto entit.</span><span class="sxs-lookup"><span data-stu-id="0c18d-404">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="0c18d-405">Entita studenta</span><span class="sxs-lookup"><span data-stu-id="0c18d-405">The Student entity</span></span>

![Diagram entity studenta](intro/_static/student-entity.png)

<span data-ttu-id="0c18d-407">Vytvoření *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="0c18d-407">Create a *Models* folder.</span></span> <span data-ttu-id="0c18d-408">V *modely* složce vytvořte soubor třídy *Student.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="0c18d-408">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="0c18d-409">`ID` Vlastnost se stane sloupec primárního klíče tabulky databáze (databáze), který odpovídá této třídy.</span><span class="sxs-lookup"><span data-stu-id="0c18d-409">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="0c18d-410">Ve výchozím nastavení interpretuje EF Core vlastnost s názvem `ID` nebo `classnameID` jako primární klíč.</span><span class="sxs-lookup"><span data-stu-id="0c18d-410">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="0c18d-411">V `classnameID`, `classname` je název třídy.</span><span class="sxs-lookup"><span data-stu-id="0c18d-411">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="0c18d-412">Alternativním automaticky rozpozná, primární klíč je `StudentID` v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-412">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="0c18d-413">`Enrollments` Je vlastnost [navigační vlastnost](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="0c18d-413">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="0c18d-414">Vlastnosti navigace propojit s dalšími subjekty, které se vztahují k této entity.</span><span class="sxs-lookup"><span data-stu-id="0c18d-414">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="0c18d-415">V takovém případě `Enrollments` vlastnost `Student entity` obsahuje všechny `Enrollment` entity, které se vztahují k, které `Student`.</span><span class="sxs-lookup"><span data-stu-id="0c18d-415">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="0c18d-416">Například pokud Student řádků v databázi má dva související řádky registrace `Enrollments` navigační vlastnost obsahuje tyto dvě `Enrollment` entity.</span><span class="sxs-lookup"><span data-stu-id="0c18d-416">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="0c18d-417">Se souvisejícím `Enrollment` řádek je řádek, který obsahuje hodnotu primárního klíče student získal v `StudentID` sloupce.</span><span class="sxs-lookup"><span data-stu-id="0c18d-417">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="0c18d-418">Předpokládejme například, studenta s ID = 1 obsahuje dva řádky `Enrollment` tabulky.</span><span class="sxs-lookup"><span data-stu-id="0c18d-418">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="0c18d-419">`Enrollment` Tabulka obsahuje dva řádky s `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="0c18d-419">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="0c18d-420">`StudentID` je cizí klíč v `Enrollment` tabulka, která určuje studentů v `Student` tabulky.</span><span class="sxs-lookup"><span data-stu-id="0c18d-420">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="0c18d-421">Pokud vlastnost navigace může obsahovat více entit, navigační vlastnost jako musí být typu seznamu `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="0c18d-421">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="0c18d-422">`ICollection<T>` můžete zadat, nebo typu jako `List<T>` nebo `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="0c18d-422">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="0c18d-423">Když `ICollection<T>` je používají, vytvoří EF Core `HashSet<T>` kolekcí ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="0c18d-423">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="0c18d-424">Navigační vlastnosti, které obsahují více entit, pocházejí z many-to-many a jeden mnoho relací.</span><span class="sxs-lookup"><span data-stu-id="0c18d-424">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="0c18d-425">Registrace entity</span><span class="sxs-lookup"><span data-stu-id="0c18d-425">The Enrollment entity</span></span>

![Diagram entity registrace](intro/_static/enrollment-entity.png)

<span data-ttu-id="0c18d-427">V *modely* složku, vytvořte *Enrollment.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="0c18d-427">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="0c18d-428">`EnrollmentID` Vlastnost představuje primární klíč.</span><span class="sxs-lookup"><span data-stu-id="0c18d-428">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="0c18d-429">Tato entita používá `classnameID` vzorku místo `ID` stejně jako `Student` entity.</span><span class="sxs-lookup"><span data-stu-id="0c18d-429">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="0c18d-430">Vývojáři obvykle zvolte jeden model a použít ho v rámci datového modelu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-430">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="0c18d-431">V pozdějších kurzech pomocí ID bez classname zobrazí zjednodušit implementaci dědičnosti v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-431">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="0c18d-432">`Grade` Vlastnost je `enum`.</span><span class="sxs-lookup"><span data-stu-id="0c18d-432">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="0c18d-433">Otazník po `Grade` deklarace typu znamená, že `Grade` vlastnost může mít hodnotu Null.</span><span class="sxs-lookup"><span data-stu-id="0c18d-433">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="0c18d-434">Na podnikové úrovni, který má hodnotu null se liší od nulové třída – null znamená, že známku vyjádřenou není znám nebo ještě nebyly přiřazeny.</span><span class="sxs-lookup"><span data-stu-id="0c18d-434">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="0c18d-435">`StudentID` Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Student`.</span><span class="sxs-lookup"><span data-stu-id="0c18d-435">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="0c18d-436">`Enrollment` Entita je přidružený nejméně k jednomu `Student` entity, tak vlastnost obsahuje jediný `Student` entity.</span><span class="sxs-lookup"><span data-stu-id="0c18d-436">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="0c18d-437">`Student` Entity se liší od `Student.Enrollments` navigační vlastnost, která obsahuje více `Enrollment` entity.</span><span class="sxs-lookup"><span data-stu-id="0c18d-437">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="0c18d-438">`CourseID` Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Course`.</span><span class="sxs-lookup"><span data-stu-id="0c18d-438">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="0c18d-439">`Enrollment` Entita je přidružený nejméně k jednomu `Course` entity.</span><span class="sxs-lookup"><span data-stu-id="0c18d-439">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="0c18d-440">EF Core interpretuje vlastnost jako cizí klíč, pokud je název `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="0c18d-440">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="0c18d-441">Například`StudentID` pro `Student` navigační vlastnost, protože `Student` je primární klíč entity `ID`.</span><span class="sxs-lookup"><span data-stu-id="0c18d-441">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="0c18d-442">Vlastnosti cizího klíče může mít také název `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="0c18d-442">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="0c18d-443">Například `CourseID` vzhledem k tomu, `Course` je primární klíč entity `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="0c18d-443">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="0c18d-444">Kurz entity</span><span class="sxs-lookup"><span data-stu-id="0c18d-444">The Course entity</span></span>

![Diagram kurzu entity](intro/_static/course-entity.png)

<span data-ttu-id="0c18d-446">V *modely* složku, vytvořte *Course.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="0c18d-446">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="0c18d-447">`Enrollments` Je navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="0c18d-447">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="0c18d-448">A `Course` entit může souviset s libovolným počtem `Enrollment` entity.</span><span class="sxs-lookup"><span data-stu-id="0c18d-448">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="0c18d-449">`DatabaseGenerated` Atribut umožňuje aplikacím určit primární klíč, místo databáze s jeho vygenerování.</span><span class="sxs-lookup"><span data-stu-id="0c18d-449">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="0c18d-450">Vygenerované uživatelské rozhraní modelu studenta</span><span class="sxs-lookup"><span data-stu-id="0c18d-450">Scaffold the student model</span></span>

<span data-ttu-id="0c18d-451">V této části je automaticky generovaný model studentů.</span><span class="sxs-lookup"><span data-stu-id="0c18d-451">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="0c18d-452">To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model studentů.</span><span class="sxs-lookup"><span data-stu-id="0c18d-452">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="0c18d-453">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="0c18d-453">Build the project.</span></span>
* <span data-ttu-id="0c18d-454">Vytvořte *stránek/studenty* složky.</span><span class="sxs-lookup"><span data-stu-id="0c18d-454">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0c18d-455">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0c18d-455">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0c18d-456">V **Průzkumníka řešení**, klikněte pravým tlačítkem na *stránek/studenty* složky > **přidat** > **novou vygenerovanou položku**.</span><span class="sxs-lookup"><span data-stu-id="0c18d-456">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0c18d-457">V **přidat vygenerované uživatelské rozhraní** dialogového okna, vyberte **stránky Razor pomocí Entity Frameworku (CRUD)** > **přidat**.</span><span class="sxs-lookup"><span data-stu-id="0c18d-457">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="0c18d-458">Dokončení **přidat stránky Razor pomocí Entity Frameworku (CRUD)** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="0c18d-458">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="0c18d-459">V **třída modelu** rozevíracího seznamu, vyberte **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="0c18d-459">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="0c18d-460">V **třída kontextu dat** řádek, vyberte **+** (plus) podepsat a změňte vygenerovaný název, aby **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="0c18d-460">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="0c18d-461">V **třída kontextu dat** rozevíracího seznamu, vyberte **ContosoUniversity.Models.SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="0c18d-461">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="0c18d-462">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="0c18d-462">Select **Add**.</span></span>

![Dialogové okno CRUD](intro/_static/s1.png)

<span data-ttu-id="0c18d-464">Zobrazit [generování uživatelského rozhraní modelu film](xref:tutorials/razor-pages/model#scaffold-the-movie-model) Pokud máte potíže s předchozím kroku.</span><span class="sxs-lookup"><span data-stu-id="0c18d-464">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0c18d-465">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0c18d-465">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0c18d-466">Spusťte následující příkazy k modelu studentů.</span><span class="sxs-lookup"><span data-stu-id="0c18d-466">Run the following commands to scaffold the student model.</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="0c18d-467">Proces vygenerované uživatelské rozhraní vytvořit a změnit následující soubory:</span><span class="sxs-lookup"><span data-stu-id="0c18d-467">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="0c18d-468">Soubory vytvořené</span><span class="sxs-lookup"><span data-stu-id="0c18d-468">Files created</span></span>

* <span data-ttu-id="0c18d-469">*Stránky/studenty* vytvoření, odstranění, podrobností, úpravy, Index.</span><span class="sxs-lookup"><span data-stu-id="0c18d-469">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="0c18d-470">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="0c18d-470">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="0c18d-471">Aktualizace souboru</span><span class="sxs-lookup"><span data-stu-id="0c18d-471">File updates</span></span>

* <span data-ttu-id="0c18d-472">*Startup.cs* : Změny v tomto souboru jsou podrobně popsané v další části.</span><span class="sxs-lookup"><span data-stu-id="0c18d-472">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="0c18d-473">*appsettings.json* : Přidaný připojovací řetězec použitý k připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="0c18d-473">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="0c18d-474">Prozkoumání kontextu registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="0c18d-474">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="0c18d-475">ASP.NET Core využívá rozhraní [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0c18d-475">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0c18d-476">Služby (například kontext EF Core databáze) jsou registrované pomocí vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c18d-476">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="0c18d-477">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="0c18d-477">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="0c18d-478">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="0c18d-478">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="0c18d-479">Nástroj pro generování uživatelského rozhraní automaticky vytvoří kontext databáze a zaregistrovaného kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="0c18d-479">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="0c18d-480">Zkontrolujte `ConfigureServices` metoda ve *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="0c18d-480">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="0c18d-481">Zvýrazněný řádek byl přidán modulem scaffolder:</span><span class="sxs-lookup"><span data-stu-id="0c18d-481">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="0c18d-482">Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-482">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="0c18d-483">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="0c18d-483">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="0c18d-484">Aktualizovat hlavní</span><span class="sxs-lookup"><span data-stu-id="0c18d-484">Update main</span></span>

<span data-ttu-id="0c18d-485">V *Program.cs*, změnit `Main` metoda můžete provádět následující:</span><span class="sxs-lookup"><span data-stu-id="0c18d-485">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="0c18d-486">Instance kontextu databáze získáte z kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="0c18d-486">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="0c18d-487">Volání [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="0c18d-487">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="0c18d-488">Uvolnění kontextu při `EnsureCreated` dokončení metody.</span><span class="sxs-lookup"><span data-stu-id="0c18d-488">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="0c18d-489">Následující kód ukazuje aktualizovaný *Program.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="0c18d-489">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="0c18d-490">`EnsureCreated` zajišťuje, že existuje databáze pro daný kontext.</span><span class="sxs-lookup"><span data-stu-id="0c18d-490">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="0c18d-491">Pokud existuje, nedojde k žádné akci.</span><span class="sxs-lookup"><span data-stu-id="0c18d-491">If it exists, no action is taken.</span></span> <span data-ttu-id="0c18d-492">Pokud neexistuje, pak se vytvoří databázi a její schéma.</span><span class="sxs-lookup"><span data-stu-id="0c18d-492">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="0c18d-493">`EnsureCreated` k vytvoření databáze nepoužívá migrace.</span><span class="sxs-lookup"><span data-stu-id="0c18d-493">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="0c18d-494">Databázi, která se vytvoří s `EnsureCreated` nelze později aktualizovat pomocí migrace.</span><span class="sxs-lookup"><span data-stu-id="0c18d-494">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="0c18d-495">`EnsureCreated` je volána při spuštění aplikace, která umožňuje následující pracovní postup:</span><span class="sxs-lookup"><span data-stu-id="0c18d-495">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="0c18d-496">Odstranění databáze.</span><span class="sxs-lookup"><span data-stu-id="0c18d-496">Delete the DB.</span></span>
* <span data-ttu-id="0c18d-497">Změna schématu DB (například přidat `EmailAddress` pole).</span><span class="sxs-lookup"><span data-stu-id="0c18d-497">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="0c18d-498">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0c18d-498">Run the app.</span></span>
* <span data-ttu-id="0c18d-499">`EnsureCreated` Databáze se vytvoří`EmailAddress` sloupce.</span><span class="sxs-lookup"><span data-stu-id="0c18d-499">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="0c18d-500">`EnsureCreated` Pokud schéma je rychle se vyvíjejícími je vhodné v rané fázi vývoje.</span><span class="sxs-lookup"><span data-stu-id="0c18d-500">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="0c18d-501">Později v tomto kurzu se odstraní databáze a používají se migrace.</span><span class="sxs-lookup"><span data-stu-id="0c18d-501">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="0c18d-502">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="0c18d-502">Test the app</span></span>

<span data-ttu-id="0c18d-503">Spusťte aplikaci a přijmout zásady souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="0c18d-503">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="0c18d-504">Tato aplikace nemá uchovává osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="0c18d-504">This app doesn't keep personal information.</span></span> <span data-ttu-id="0c18d-505">Informace o souboru cookie zásadami [EU obecného Regulation (GDPR) podporu](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="0c18d-505">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="0c18d-506">Vyberte **studenty** propojení a potom **vytvořit nový**.</span><span class="sxs-lookup"><span data-stu-id="0c18d-506">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="0c18d-507">Otestujte upravit, podrobnosti a odkazy odstranit.</span><span class="sxs-lookup"><span data-stu-id="0c18d-507">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="0c18d-508">Prozkoumání kontextu SchoolContext DB</span><span class="sxs-lookup"><span data-stu-id="0c18d-508">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="0c18d-509">Hlavní třída, která koordinuje EF Core funkce pro daný datový model je třídy kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="0c18d-509">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="0c18d-510">Kontext dat je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="0c18d-510">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="0c18d-511">Kontext dat určuje entit, které jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-511">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="0c18d-512">V tomto projektu je s názvem třídy `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="0c18d-512">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="0c18d-513">Aktualizace *SchoolContext.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="0c18d-513">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="0c18d-514">Zvýrazněný kód vytvoří [DbSet\<TEntity >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) vlastností pro každou sadu entit.</span><span class="sxs-lookup"><span data-stu-id="0c18d-514">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="0c18d-515">V terminologii EF Core:</span><span class="sxs-lookup"><span data-stu-id="0c18d-515">In EF Core terminology:</span></span>

* <span data-ttu-id="0c18d-516">Obvykle sadu entit odpovídá Databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="0c18d-516">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="0c18d-517">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="0c18d-517">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="0c18d-518">`DbSet<Enrollment>` a `DbSet<Course>` může vynechat.</span><span class="sxs-lookup"><span data-stu-id="0c18d-518">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="0c18d-519">EF Core je obsahuje implicitně protože `Student` odkazy na entity `Enrollment` entity a `Enrollment` odkazy na entity `Course` entity.</span><span class="sxs-lookup"><span data-stu-id="0c18d-519">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="0c18d-520">Pro účely tohoto kurzu ponechte `DbSet<Enrollment>` a `DbSet<Course>` v `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="0c18d-520">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="0c18d-521">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="0c18d-521">SQL Server Express LocalDB</span></span>

<span data-ttu-id="0c18d-522">Připojovací řetězec Určuje [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="0c18d-522">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="0c18d-523">LocalDB je Odlehčená verze SQL serveru Express Database Engine a je určená pro vývoj aplikací, není použití v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="0c18d-523">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="0c18d-524">LocalDB spustí na vyžádání a běží v uživatelském režimu, takže není bez složité konfigurace.</span><span class="sxs-lookup"><span data-stu-id="0c18d-524">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="0c18d-525">Ve výchozím nastavení LocalDB vytvoří *.mdf* DB soubory `C:/Users/<user>` adresáře.</span><span class="sxs-lookup"><span data-stu-id="0c18d-525">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="0c18d-526">Přidejte kód pro inicializaci databáze se testovací data</span><span class="sxs-lookup"><span data-stu-id="0c18d-526">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="0c18d-527">EF Core vytvoří prázdná databáze.</span><span class="sxs-lookup"><span data-stu-id="0c18d-527">EF Core creates an empty DB.</span></span> <span data-ttu-id="0c18d-528">V této části `Initialize` metoda je zapsána do naplnit ho daty testu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-528">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="0c18d-529">V *Data* složku, vytvořte nový soubor třídy *DbInitializer.cs* a přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="0c18d-529">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="0c18d-530">Poznámka: Předchozí kód používá `Models` pro obor názvů (`namespace ContosoUniversity.Models`) spíše než `Data`.</span><span class="sxs-lookup"><span data-stu-id="0c18d-530">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="0c18d-531">`Models`je konzistentní s kódem generovaným pomocí generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0c18d-531">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="0c18d-532">Další informace najdete v [tomto problému s vygenerováním uživatelského rozhraní GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="0c18d-532">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="0c18d-533">Kód kontroluje, jestli na databáze nejsou všechny studenty.</span><span class="sxs-lookup"><span data-stu-id="0c18d-533">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="0c18d-534">Pokud v databázi nejsou žádní studenti, databáze je inicializována s testovací data.</span><span class="sxs-lookup"><span data-stu-id="0c18d-534">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="0c18d-535">Načte testovací data do pole spíše než `List<T>` kolekce za účelem optimalizace výkonu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-535">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="0c18d-536">`EnsureCreated` Metoda automaticky vytvoří databáze pro kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="0c18d-536">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="0c18d-537">Pokud existuje databáze `EnsureCreated` vrátí beze změny databáze.</span><span class="sxs-lookup"><span data-stu-id="0c18d-537">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="0c18d-538">V *Program.cs*, upravte `Main` metodu chce volat `Initialize`:</span><span class="sxs-lookup"><span data-stu-id="0c18d-538">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0c18d-539">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0c18d-539">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0c18d-540">Zastavte aplikaci, pokud je spuštěná, a spusťte následující příkaz v **konzole správce balíčků** (PMC):</span><span class="sxs-lookup"><span data-stu-id="0c18d-540">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0c18d-541">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0c18d-541">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0c18d-542">Zastavte aplikaci, pokud je spuštěná, a odstraňte soubor *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="0c18d-542">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="0c18d-543">Zobrazení databáze</span><span class="sxs-lookup"><span data-stu-id="0c18d-543">View the DB</span></span>

<span data-ttu-id="0c18d-544">Název databáze je vygenerován z dříve zadaného názvu kontextu plus pomlčka a identifikátor GUID.</span><span class="sxs-lookup"><span data-stu-id="0c18d-544">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="0c18d-545">Proto bude název databáze "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="0c18d-545">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="0c18d-546">Identifikátor GUID bude pro každého uživatele odlišný.</span><span class="sxs-lookup"><span data-stu-id="0c18d-546">The GUID will be different for each user.</span></span>
<span data-ttu-id="0c18d-547">Otevřít **Průzkumník objektů systému SQL Server** (SSOX) z **zobrazení** nabídky v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0c18d-547">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="0c18d-548">V SSOX klikněte na **(LocalDB) \MSSQLLocalDB > databáze > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="0c18d-548">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="0c18d-549">Rozbalte **tabulky** uzlu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-549">Expand the **Tables** node.</span></span>

<span data-ttu-id="0c18d-550">Klikněte pravým tlačítkem na **Student** tabulky a klikněte na tlačítko **Data zobrazení** vytvořené sloupce a řádky vložené do tabulky.</span><span class="sxs-lookup"><span data-stu-id="0c18d-550">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="0c18d-551">Asynchronní kód</span><span class="sxs-lookup"><span data-stu-id="0c18d-551">Asynchronous code</span></span>

<span data-ttu-id="0c18d-552">Asynchronní programování je výchozím režimem pro ASP.NET Core a EF Core.</span><span class="sxs-lookup"><span data-stu-id="0c18d-552">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="0c18d-553">Webový server má omezený počet vláken, které jsou k dispozici, a v situacích, vysokého zatížení všech dostupných vláken může být používán.</span><span class="sxs-lookup"><span data-stu-id="0c18d-553">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="0c18d-554">Pokud k tomu dojde, server nemůže zpracovat nové žádosti, dokud se uvolnit vlákna.</span><span class="sxs-lookup"><span data-stu-id="0c18d-554">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="0c18d-555">Přestože se nejedná skutečně každé dílo vzhledem k tomu, že čekání na vstupně-výstupních operací na dokončení, může kódem synchronní svázané několika vlákny.</span><span class="sxs-lookup"><span data-stu-id="0c18d-555">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="0c18d-556">Asynchronní kód když proces čeká na vstupně-výstupních operací na dokončení, je jeho vlákno uvolněn pro server určený pro zpracováním jiných požadavků.</span><span class="sxs-lookup"><span data-stu-id="0c18d-556">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="0c18d-557">V důsledku toho asynchronního kódu umožňuje prostředky serveru použije efektivněji a aby zvládla větší provoz bez zpoždění je povoleno na serveru.</span><span class="sxs-lookup"><span data-stu-id="0c18d-557">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="0c18d-558">Asynchronní kód zavést malé množství režie v době běhu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-558">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="0c18d-559">V situacích s nízkým provozem stiskněte výkonu je zanedbatelný, při vysoké návštěvnosti situacích, potenciálních zlepšení výkonu je důležitým.</span><span class="sxs-lookup"><span data-stu-id="0c18d-559">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="0c18d-560">V následujícím kódu [asynchronní](/dotnet/csharp/language-reference/keywords/async) – klíčové slovo, `Task<T>` návratovou hodnotu, `await` – klíčové slovo, a `ToListAsync` metoda změňte kód spustit asynchronně.</span><span class="sxs-lookup"><span data-stu-id="0c18d-560">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="0c18d-561">`async` – Klíčové slovo instruuje kompilátor, aby:</span><span class="sxs-lookup"><span data-stu-id="0c18d-561">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="0c18d-562">Generovat zpětná volání pro části tělo metody.</span><span class="sxs-lookup"><span data-stu-id="0c18d-562">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="0c18d-563">Automaticky vytvořit [úloh](/dotnet/api/system.threading.tasks.task) vráceného objektu.</span><span class="sxs-lookup"><span data-stu-id="0c18d-563">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="0c18d-564">Další informace najdete v tématu [návratový typ úkolu](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="0c18d-564">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="0c18d-565">Implicitní návratový typ `Task` představuje probíhající práci.</span><span class="sxs-lookup"><span data-stu-id="0c18d-565">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="0c18d-566">`await` – Klíčové slovo způsobí, že kompilátor metodu rozdělit do dvou částí.</span><span class="sxs-lookup"><span data-stu-id="0c18d-566">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="0c18d-567">První část končí operace, která se spustí asynchronně.</span><span class="sxs-lookup"><span data-stu-id="0c18d-567">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="0c18d-568">Druhá část je nepoužili metodu zpětného volání, která je volána po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="0c18d-568">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="0c18d-569">`ToListAsync` je asynchronní verze `ToList` – metoda rozšíření.</span><span class="sxs-lookup"><span data-stu-id="0c18d-569">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="0c18d-570">Některé co je potřeba mít na paměti při zápisu asynchronního kódu, který používá EF Core:</span><span class="sxs-lookup"><span data-stu-id="0c18d-570">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="0c18d-571">Jenom příkazy, které způsobují dotazy nebo příkazy k odeslání do databáze se provedl asynchronně.</span><span class="sxs-lookup"><span data-stu-id="0c18d-571">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="0c18d-572">Který obsahuje `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, a `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="0c18d-572">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="0c18d-573">Neměl by zahrnovat příkazy, které stačí změnit `IQueryable`, jako například `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="0c18d-573">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="0c18d-574">Objekt context EF Core není bezpečné pro vlákna: nedoporučujeme provádět více operací paralelně.</span><span class="sxs-lookup"><span data-stu-id="0c18d-574">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="0c18d-575">Abyste mohli využívat výkony těží z asynchronní kód, ověřte, že balíčků knihovny (například pro stránkování) používat asynchronní, pokud volání metody EF Core, které odesílání dotazů do databáze.</span><span class="sxs-lookup"><span data-stu-id="0c18d-575">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="0c18d-576">Další informace o asynchronním programování v rozhraní .NET najdete v tématu [asynchronní přehled](/dotnet/standard/async) a [asynchronní programování pomocí modifikátoru async a operátoru await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="0c18d-576">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="0c18d-577">V dalším kurzu, základní CRUD (vytváření, čtení, aktualizace nebo odstranění) jsou zkoumány operace.</span><span class="sxs-lookup"><span data-stu-id="0c18d-577">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="0c18d-578">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0c18d-578">Additional resources</span></span>

* [<span data-ttu-id="0c18d-579">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="0c18d-579">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="0c18d-580">Next</span><span class="sxs-lookup"><span data-stu-id="0c18d-580">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
