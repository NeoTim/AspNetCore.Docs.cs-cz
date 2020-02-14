---
title: Stránky Razor pomocí Entity Framework Core v ASP.NET Core – kurz 1 z 8
author: rick-anderson
description: Ukazuje, jak vytvořit aplikaci pro stránky Razor pomocí Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: data/ef-rp/intro
ms.openlocfilehash: 1a9d83be9180b1d32ab941932eb3cab8612dff01
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77213399"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="68158-103">Stránky Razor pomocí Entity Framework Core v ASP.NET Core – kurz 1 z 8</span><span class="sxs-lookup"><span data-stu-id="68158-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="68158-104">[Dykstra](https://github.com/tdykstra) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="68158-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="68158-105">Toto je první v sérii kurzů, které ukazují, jak používat Entity Framework (EF) jádro v aplikaci [ASP.NET Core Razor Pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="68158-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="68158-106">Kurzy vytvářejí web pro fiktivní univerzitě společnosti Contoso.</span><span class="sxs-lookup"><span data-stu-id="68158-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="68158-107">Tato lokalita obsahuje funkce, jako je například využití studenta, vytváření kurzů a přiřazení instruktorů.</span><span class="sxs-lookup"><span data-stu-id="68158-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="68158-108">Kurz používá první přístup ke kódu.</span><span class="sxs-lookup"><span data-stu-id="68158-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="68158-109">Informace o tomto kurzu s použitím databáze prvního přístupu najdete v [tomto problému GitHubu](https://github.com/aspnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="68158-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/aspnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="68158-110">Stažení nebo zobrazení dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="68158-110">Download or view the completed app.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="68158-111">[Pokyny ke stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="68158-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="68158-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="68158-112">Prerequisites</span></span>

* <span data-ttu-id="68158-113">Pokud s Razor Pages teprve začínáte, Projděte si kurz [Začínáme s Razor Pagesm](xref:tutorials/razor-pages/razor-pages-start) , než začnete s tímto.</span><span class="sxs-lookup"><span data-stu-id="68158-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="68158-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="68158-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="68158-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="68158-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="68158-116">Databázové moduly</span><span class="sxs-lookup"><span data-stu-id="68158-116">Database engines</span></span>

<span data-ttu-id="68158-117">Pokyny pro Visual Studio používají [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), verzi SQL Server Express, která se spouští jenom v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="68158-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="68158-118">Pokyny pro [Visual Studio Code používají nástroj](https://www.sqlite.org/)pro databázový stroj pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="68158-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="68158-119">Pokud se rozhodnete použít SQLite, Stáhněte a nainstalujte nástroj třetí strany pro správu a zobrazení databáze SQLite, jako je například [prohlížeč databáze pro SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="68158-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="68158-120">Odstraňování potíží</span><span class="sxs-lookup"><span data-stu-id="68158-120">Troubleshooting</span></span>

<span data-ttu-id="68158-121">Pokud narazíte na problém, který nemůžete vyřešit, porovnejte kód s [dokončeným projektem](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="68158-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="68158-122">Dobrý způsob, jak získat pomoc, je odeslání otázky do StackOverflow.com s použitím [značky ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [značky EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="68158-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="68158-123">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="68158-123">The sample app</span></span>

<span data-ttu-id="68158-124">Aplikace vytvořené v těchto kurzech je webová stránka základní university.</span><span class="sxs-lookup"><span data-stu-id="68158-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="68158-125">Uživatelé mohou zobrazit a aktualizovat Všichni studenti, kurz a informace instruktorem.</span><span class="sxs-lookup"><span data-stu-id="68158-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="68158-126">Tady je několik obrazovek vytvořili v kurzu.</span><span class="sxs-lookup"><span data-stu-id="68158-126">Here are a few of the screens created in the tutorial.</span></span>

![Studenti indexová stránka](intro/_static/students-index30.png)

![Stránky pro úpravu studentů](intro/_static/student-edit30.png)

<span data-ttu-id="68158-129">Styl uživatelského rozhraní tohoto webu je založen na předdefinovaných šablonách projektů.</span><span class="sxs-lookup"><span data-stu-id="68158-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="68158-130">V tomto kurzu se naučíte, jak používat EF Core, nikoli způsob přizpůsobení uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="68158-130">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="68158-131">Pomocí odkazu v horní části stránky Získejte zdrojový kód dokončeného projektu.</span><span class="sxs-lookup"><span data-stu-id="68158-131">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="68158-132">Složka *cu30* obsahuje kód pro verzi kurzu ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="68158-132">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="68158-133">Soubory, které reflektují stav kódu pro kurzy 1-7, najdete ve složce *cu30snapshots* .</span><span class="sxs-lookup"><span data-stu-id="68158-133">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="68158-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="68158-134">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="68158-135">Spuštění aplikace po stažení dokončeného projektu:</span><span class="sxs-lookup"><span data-stu-id="68158-135">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="68158-136">Odstraňte tři soubory a jednu složku, která má v názvu název *SQLite* .</span><span class="sxs-lookup"><span data-stu-id="68158-136">Delete three files and one folder that have *SQLite* in the name.</span></span>
* <span data-ttu-id="68158-137">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="68158-137">Build the project.</span></span>
* <span data-ttu-id="68158-138">V konzole správce balíčků (PMC) spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="68158-138">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="68158-139">Spusťte projekt k osazení databáze.</span><span class="sxs-lookup"><span data-stu-id="68158-139">Run the project to seed the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="68158-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="68158-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="68158-141">Spuštění aplikace po stažení dokončeného projektu:</span><span class="sxs-lookup"><span data-stu-id="68158-141">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="68158-142">Odstraňte *ContosoUniversity. csproj*a přejmenujte *ContosoUniversitySQLite. csproj* na *ContosoUniversity. csproj*.</span><span class="sxs-lookup"><span data-stu-id="68158-142">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="68158-143">Odstraňte *Startup.cs*a přejmenujte *StartupSQLite.cs* na *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="68158-143">Delete *Startup.cs*, and rename *StartupSQLite.cs* to *Startup.cs*.</span></span>
* <span data-ttu-id="68158-144">Odstraňte *appSettings. JSON*a přejmenujte *appSettingsSQLite.* JSON na *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="68158-144">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="68158-145">Odstraňte složku *migrace* a přejmenujte *MigrationsSQL* na *migrace*.</span><span class="sxs-lookup"><span data-stu-id="68158-145">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="68158-146">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="68158-146">Build the project.</span></span>
* <span data-ttu-id="68158-147">Na příkazovém řádku ve složce projektu spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="68158-147">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* <span data-ttu-id="68158-148">V nástroji SQLite spusťte následující příkaz SQL:</span><span class="sxs-lookup"><span data-stu-id="68158-148">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="68158-149">Spusťte projekt k osazení databáze.</span><span class="sxs-lookup"><span data-stu-id="68158-149">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="68158-150">Vytvoření projektu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="68158-150">Create the web app project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="68158-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="68158-151">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="68158-152">V nabídce **soubor** v aplikaci Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="68158-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="68158-153">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="68158-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="68158-154">Pojmenujte projekt *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="68158-154">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="68158-155">Je důležité použít tento přesný název, včetně velkých a malých písmen, aby se obory názvů shodovaly při zkopírování a vložení kódu.</span><span class="sxs-lookup"><span data-stu-id="68158-155">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="68158-156">V rozevíracích seznamech vyberte **.NET Core** a **ASP.NET Core 3,0** a potom vyberte **Webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="68158-156">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="68158-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="68158-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="68158-158">V terminálu přejděte do složky, ve které by měla být vytvořena složka projektu.</span><span class="sxs-lookup"><span data-stu-id="68158-158">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="68158-159">Spuštěním následujících příkazů vytvořte projekt Razor Pages a `cd` do nové složky projektu:</span><span class="sxs-lookup"><span data-stu-id="68158-159">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="68158-160">Nastavit styl lokality</span><span class="sxs-lookup"><span data-stu-id="68158-160">Set up the site style</span></span>

<span data-ttu-id="68158-161">Pomocí aktualizace *stránek/Shared/_Layout. cshtml*nastavte záhlaví webu, zápatí a nabídku. cshtml:</span><span class="sxs-lookup"><span data-stu-id="68158-161">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="68158-162">Změňte všechny výskyty "ContosoUniversity" na "University společnosti Contoso".</span><span class="sxs-lookup"><span data-stu-id="68158-162">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="68158-163">Existují tři výskyty.</span><span class="sxs-lookup"><span data-stu-id="68158-163">There are three occurrences.</span></span>

* <span data-ttu-id="68158-164">Odstraňte položky nabídky **Domů** a **Ochrana osobních údajů** a přidejte záznamy **o o** **studentech**, **kurzech**, **instruktorech**a **odděleních**.</span><span class="sxs-lookup"><span data-stu-id="68158-164">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="68158-165">Změny jsou zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="68158-165">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="68158-166">Na *stránce pages/index. cshtml*nahraďte obsah souboru následujícím kódem, který nahradí text o ASP.NET Core textem této aplikace:</span><span class="sxs-lookup"><span data-stu-id="68158-166">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="68158-167">Spusťte aplikaci, abyste ověřili, že se zobrazí domovská stránka.</span><span class="sxs-lookup"><span data-stu-id="68158-167">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="68158-168">Datový model</span><span class="sxs-lookup"><span data-stu-id="68158-168">The data model</span></span>

<span data-ttu-id="68158-169">V následujících částech se vytvoří datový model:</span><span class="sxs-lookup"><span data-stu-id="68158-169">The following sections create a data model:</span></span>

![Kurz – registrace – studentech modelového diagramu](intro/_static/data-model-diagram.png)

<span data-ttu-id="68158-171">Student se může zaregistrovat v jakémkoli počtu kurzů a kurz může mít zaregistrovaný libovolný počet studentů.</span><span class="sxs-lookup"><span data-stu-id="68158-171">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="68158-172">Entita studenta</span><span class="sxs-lookup"><span data-stu-id="68158-172">The Student entity</span></span>

![Diagram entity studenta](intro/_static/student-entity.png)

* <span data-ttu-id="68158-174">Vytvořte složku *modely* ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="68158-174">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="68158-175">Vytvořte *modely/studenta. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="68158-175">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="68158-176">Vlastnost `ID` se zobrazí jako sloupec primárního klíče tabulky databáze, který odpovídá této třídě.</span><span class="sxs-lookup"><span data-stu-id="68158-176">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="68158-177">Ve výchozím nastavení EF Core interpretuje vlastnost s názvem `ID` nebo `classnameID` jako primární klíč.</span><span class="sxs-lookup"><span data-stu-id="68158-177">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="68158-178">Proto je `StudentID`automaticky rozpoznaný název primárního klíče `Student` třídy.</span><span class="sxs-lookup"><span data-stu-id="68158-178">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span>

<span data-ttu-id="68158-179">Vlastnost `Enrollments` je [navigační vlastnost](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="68158-179">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="68158-180">Navigační vlastnosti obsahují další entity, které se vztahují k této entitě.</span><span class="sxs-lookup"><span data-stu-id="68158-180">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="68158-181">V tomto případě obsahuje vlastnost `Enrollments` `Student` entity všechny `Enrollment` entit, které souvisejí s tímto studentem.</span><span class="sxs-lookup"><span data-stu-id="68158-181">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="68158-182">Například pokud má řádek studenta v databázi dva související řádky registrace, navigační vlastnost `Enrollments` obsahuje tyto dvě entity registrace.</span><span class="sxs-lookup"><span data-stu-id="68158-182">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="68158-183">V databázi se řádek registrace vztahuje k řádku studenta, pokud jeho sloupec StudentID obsahuje hodnotu ID studenta.</span><span class="sxs-lookup"><span data-stu-id="68158-183">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="68158-184">Předpokládejme například, že řádek studenta má ID = 1.</span><span class="sxs-lookup"><span data-stu-id="68158-184">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="68158-185">Související řádky registrace budou mít StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="68158-185">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="68158-186">StudentID je *cizí klíč* v tabulce zápisu.</span><span class="sxs-lookup"><span data-stu-id="68158-186">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="68158-187">Vlastnost `Enrollments` je definována jako `ICollection<Enrollment>`, protože může existovat více souvisejících entit zápisu.</span><span class="sxs-lookup"><span data-stu-id="68158-187">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="68158-188">Můžete použít jiné typy kolekce, například `List<Enrollment>` nebo `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="68158-188">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="68158-189">Při použití `ICollection<Enrollment>` EF Core ve výchozím nastavení vytvoří kolekci `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="68158-189">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="68158-190">Registrace entity</span><span class="sxs-lookup"><span data-stu-id="68158-190">The Enrollment entity</span></span>

![Diagram entity registrace](intro/_static/enrollment-entity.png)

<span data-ttu-id="68158-192">Vytvořte *modely/registrace. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="68158-192">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="68158-193">Vlastnost `EnrollmentID` je primárním klíčem; Tato entita používá `classnameID` vzor místo `ID` sám sebou.</span><span class="sxs-lookup"><span data-stu-id="68158-193">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="68158-194">Pro model produkčních dat vyberte jeden vzor a používejte ho konzistentně.</span><span class="sxs-lookup"><span data-stu-id="68158-194">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="68158-195">Tento kurz používá pouze k ilustraci toho, jak funguje.</span><span class="sxs-lookup"><span data-stu-id="68158-195">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="68158-196">Použití `ID` bez `classname` usnadňuje implementaci některých druhů změn datového modelu.</span><span class="sxs-lookup"><span data-stu-id="68158-196">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="68158-197">Vlastnost `Grade` je `enum`.</span><span class="sxs-lookup"><span data-stu-id="68158-197">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="68158-198">Otazník po deklaraci typu `Grade` označuje, že vlastnost `Grade` může [mít hodnotu null](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="68158-198">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="68158-199">Nulová hodnota, která je null, se liší od nulové třídy&mdash;hodnota null znamená, že se neznámí nebo ještě není přiřazená žádná ze stupňů.</span><span class="sxs-lookup"><span data-stu-id="68158-199">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="68158-200">Vlastnost `StudentID` je cizí klíč a odpovídající navigační vlastnost je `Student`.</span><span class="sxs-lookup"><span data-stu-id="68158-200">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="68158-201">Entita `Enrollment` je přidružená k jedné entitě `Student`, takže tato vlastnost obsahuje jednu entitu `Student`.</span><span class="sxs-lookup"><span data-stu-id="68158-201">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="68158-202">Vlastnost `CourseID` je cizí klíč a odpovídající navigační vlastnost je `Course`.</span><span class="sxs-lookup"><span data-stu-id="68158-202">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="68158-203">Entita `Enrollment` je přidružená k jedné entitě `Course`.</span><span class="sxs-lookup"><span data-stu-id="68158-203">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="68158-204">EF Core interpretuje vlastnost jako cizí klíč, pokud má název `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="68158-204">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="68158-205">`StudentID` je například cizí klíč pro vlastnost `Student` navigace, protože je `ID`primární klíč entity `Student`.</span><span class="sxs-lookup"><span data-stu-id="68158-205">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="68158-206">Vlastnosti cizího klíče mohou být také pojmenovány `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="68158-206">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="68158-207">`CourseID` například, protože je primární klíč entity `Course` `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="68158-207">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="68158-208">Kurz entity</span><span class="sxs-lookup"><span data-stu-id="68158-208">The Course entity</span></span>

![Diagram kurzu entity](intro/_static/course-entity.png)

<span data-ttu-id="68158-210">Vytvořte *modely/Course. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="68158-210">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="68158-211">Vlastnost `Enrollments` je navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="68158-211">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="68158-212">Entita `Course` může souviset s libovolným počtem entit `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="68158-212">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="68158-213">Atribut `DatabaseGenerated` umožňuje aplikaci určit primární klíč místo toho, aby ho databáze vygenerovala.</span><span class="sxs-lookup"><span data-stu-id="68158-213">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="68158-214">Sestavte projekt, aby se ověřilo, že nejsou k dispozici žádné chyby kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="68158-214">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="68158-215">Stránky studenta pro generování uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="68158-215">Scaffold Student pages</span></span>

<span data-ttu-id="68158-216">V této části použijete nástroj ASP.NET Core pro generování uživatelského rozhraní k vygenerování:</span><span class="sxs-lookup"><span data-stu-id="68158-216">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="68158-217">Třída *kontextu* EF Core.</span><span class="sxs-lookup"><span data-stu-id="68158-217">An EF Core *context* class.</span></span> <span data-ttu-id="68158-218">Kontext je hlavní třída, která koordinuje funkce Entity Framework pro daný datový model.</span><span class="sxs-lookup"><span data-stu-id="68158-218">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="68158-219">Je odvozena z třídy `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="68158-219">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="68158-220">Stránky Razor, které zpracovávají operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro entitu `Student`.</span><span class="sxs-lookup"><span data-stu-id="68158-220">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="68158-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="68158-221">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="68158-222">Vytvořte složku *Students* ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="68158-222">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="68158-223">V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *Pages/Students* a vyberte **Přidat** > **Nová vygenerovaná položka**.</span><span class="sxs-lookup"><span data-stu-id="68158-223">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="68158-224">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="68158-224">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="68158-225">V dialogovém okně **přidat Razor Pages pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="68158-225">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="68158-226">V rozevíracím seznamu **třída modelu** vyberte **student (ContosoUniversity. Models)** .</span><span class="sxs-lookup"><span data-stu-id="68158-226">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="68158-227">V řádku **Třída kontextu dat** vyberte symbol **+** (plus).</span><span class="sxs-lookup"><span data-stu-id="68158-227">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="68158-228">Změňte název kontextu dat z *ContosoUniversity. Models. ContosoUniversityContext* na *ContosoUniversity. data. SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="68158-228">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="68158-229">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="68158-229">Select **Add**.</span></span>

<span data-ttu-id="68158-230">Automaticky se nainstalují tyto balíčky:</span><span class="sxs-lookup"><span data-stu-id="68158-230">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="68158-231">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="68158-231">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="68158-232">Spuštěním následujících příkazů .NET Core CLI nainstalujte požadované balíčky NuGet:</span><span class="sxs-lookup"><span data-stu-id="68158-232">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="68158-233">Pro generování uživatelského rozhraní je vyžadován balíček Microsoft. VisualStudio. Web. strategii. Design.</span><span class="sxs-lookup"><span data-stu-id="68158-233">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="68158-234">I když aplikace nebude používat SQL Server, nástroj pro generování uživatelského rozhraní potřebuje balíček SQL Server.</span><span class="sxs-lookup"><span data-stu-id="68158-234">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="68158-235">Vytvořte složku *stránky/studenty* .</span><span class="sxs-lookup"><span data-stu-id="68158-235">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="68158-236">Spusťte následující příkaz pro instalaci nástroje pro [generování uživatelského rozhraní ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="68158-236">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="68158-237">Spusťte následující příkaz pro generování uživatelského rozhraní stránek studenta.</span><span class="sxs-lookup"><span data-stu-id="68158-237">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="68158-238">**Ve Windows**</span><span class="sxs-lookup"><span data-stu-id="68158-238">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="68158-239">**V systému macOS nebo Linux**</span><span class="sxs-lookup"><span data-stu-id="68158-239">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="68158-240">Pokud máte problém s předchozím krokem, sestavte projekt a opakujte krok generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="68158-240">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="68158-241">Proces generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="68158-241">The scaffolding process:</span></span>

* <span data-ttu-id="68158-242">Vytvoří stránky Razor ve složce *Pages/Students* :</span><span class="sxs-lookup"><span data-stu-id="68158-242">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="68158-243">*Vytvoření. cshtml* a *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="68158-243">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="68158-244">*Odstranění. cshtml* a *DELETE.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="68158-244">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="68158-245">*Podrobnosti. cshtml* a *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="68158-245">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="68158-246">*Upravit. cshtml* a *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="68158-246">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="68158-247">*Index. cshtml* a *index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="68158-247">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="68158-248">Vytvoří *data/SchoolContext. cs*.</span><span class="sxs-lookup"><span data-stu-id="68158-248">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="68158-249">Přidá kontext do injektáže závislosti v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="68158-249">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="68158-250">Přidá připojovací řetězec databáze do souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="68158-250">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="68158-251">Připojovací řetězec databáze</span><span class="sxs-lookup"><span data-stu-id="68158-251">Database connection string</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="68158-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="68158-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="68158-253">Připojovací řetězec Určuje [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="68158-253">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="68158-254">LocalDB je Odlehčená verze SQL serveru Express Database Engine a je určená pro vývoj aplikací, není použití v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="68158-254">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="68158-255">Ve výchozím nastavení LocalDB vytvoří soubory *. mdf* v adresáři `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="68158-255">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="68158-256">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="68158-256">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="68158-257">Změňte připojovací řetězec tak, aby odkazoval na soubor databáze SQLite s názvem *cu. DB*:</span><span class="sxs-lookup"><span data-stu-id="68158-257">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="68158-258">Aktualizuje třídu kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="68158-258">Update the database context class</span></span>

<span data-ttu-id="68158-259">Hlavní třída, která koordinuje funkce EF Core pro daný datový model, je třída kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="68158-259">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="68158-260">Kontext je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="68158-260">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="68158-261">Kontext určuje, které entity jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="68158-261">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="68158-262">V tomto projektu je třída pojmenována `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="68158-262">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="68158-263">*SchoolContext.cs* aktualizujte pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="68158-263">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="68158-264">Zvýrazněný kód vytvoří vlastnost [negenerickými\<TEntity >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro každou sadu entit.</span><span class="sxs-lookup"><span data-stu-id="68158-264">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="68158-265">V terminologii EF Core:</span><span class="sxs-lookup"><span data-stu-id="68158-265">In EF Core terminology:</span></span>

* <span data-ttu-id="68158-266">Sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="68158-266">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="68158-267">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="68158-267">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="68158-268">Vzhledem k tomu, že sada entit obsahuje více entit, musí mít vlastnosti Negenerickými plurální názvy.</span><span class="sxs-lookup"><span data-stu-id="68158-268">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="68158-269">Vzhledem k tomu, že nástroj pro generování uživatelského rozhraní vytvořil`Student` Negenerickými, tento krok ho změní na plural `Students`.</span><span class="sxs-lookup"><span data-stu-id="68158-269">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="68158-270">Chcete-li, aby kód Razor Pages odpovídal novému názvu Negenerickými, proveďte globální změnu v celém projektu `_context.Student` na `_context.Students`.</span><span class="sxs-lookup"><span data-stu-id="68158-270">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="68158-271">K dispozici je 8 výskytů.</span><span class="sxs-lookup"><span data-stu-id="68158-271">There are 8 occurrences.</span></span>

<span data-ttu-id="68158-272">Sestavte projekt, aby se ověřilo, že nedošlo k chybám kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="68158-272">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="68158-273">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="68158-273">Startup.cs</span></span>

<span data-ttu-id="68158-274">ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="68158-274">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="68158-275">Služby (například kontext databáze EF Core) jsou registrovány pomocí injektáže závislosti při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="68158-275">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="68158-276">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="68158-276">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="68158-277">Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="68158-277">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="68158-278">Nástroj pro generování uživatelského rozhraní automaticky zaregistroval třídu kontextu pomocí kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="68158-278">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="68158-279">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="68158-279">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="68158-280">V `ConfigureServices`byly zvýrazněné řádky přidány pomocí nástroje pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="68158-280">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="68158-281">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="68158-281">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="68158-282">V `ConfigureServices`se ujistěte, že kód přidaný generátorem volá `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="68158-282">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="68158-283">Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="68158-283">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="68158-284">Pro místní vývoj načítá [konfigurační systém ASP.NET Core](xref:fundamentals/configuration/index) připojovací řetězec ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="68158-284">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="68158-285">Vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="68158-285">Create the database</span></span>

<span data-ttu-id="68158-286">Aktualizujte *program.cs* , aby se vytvořila databáze, pokud neexistuje:</span><span class="sxs-lookup"><span data-stu-id="68158-286">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="68158-287">Metoda [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) neprovede žádnou akci, pokud existuje databáze pro kontext.</span><span class="sxs-lookup"><span data-stu-id="68158-287">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="68158-288">Pokud žádná databáze neexistuje, vytvoří databázi a schéma.</span><span class="sxs-lookup"><span data-stu-id="68158-288">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="68158-289">`EnsureCreated` povolí následující pracovní postup pro zpracování změn datového modelu:</span><span class="sxs-lookup"><span data-stu-id="68158-289">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="68158-290">Odstraňte databázi.</span><span class="sxs-lookup"><span data-stu-id="68158-290">Delete the database.</span></span> <span data-ttu-id="68158-291">Všechna existující data budou ztracena.</span><span class="sxs-lookup"><span data-stu-id="68158-291">Any existing data is lost.</span></span>
* <span data-ttu-id="68158-292">Změňte datový model.</span><span class="sxs-lookup"><span data-stu-id="68158-292">Change the data model.</span></span> <span data-ttu-id="68158-293">Přidejte například pole `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="68158-293">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="68158-294">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="68158-294">Run the app.</span></span>
* <span data-ttu-id="68158-295">`EnsureCreated` vytvoří databázi s novým schématem.</span><span class="sxs-lookup"><span data-stu-id="68158-295">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="68158-296">Tento pracovní postup funguje dobře v rané fázi vývoje, když se schéma rychle vyvíjí, pokud nepotřebujete zachovat data.</span><span class="sxs-lookup"><span data-stu-id="68158-296">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="68158-297">Tato situace se liší v případě, že data, která byla zadána do databáze, musí být zachována.</span><span class="sxs-lookup"><span data-stu-id="68158-297">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="68158-298">V takovém případě použijte migrace.</span><span class="sxs-lookup"><span data-stu-id="68158-298">When that is the case, use migrations.</span></span>

<span data-ttu-id="68158-299">Později v rámci série kurzů odstraníte databázi vytvořenou `EnsureCreated` a místo toho použijete migrace.</span><span class="sxs-lookup"><span data-stu-id="68158-299">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="68158-300">Databázi vytvořenou pomocí `EnsureCreated` nelze aktualizovat pomocí migrace.</span><span class="sxs-lookup"><span data-stu-id="68158-300">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="68158-301">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="68158-301">Test the app</span></span>

* <span data-ttu-id="68158-302">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="68158-302">Run the app.</span></span>
* <span data-ttu-id="68158-303">Vyberte odkaz **Students** a pak **vytvořte nový**.</span><span class="sxs-lookup"><span data-stu-id="68158-303">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="68158-304">Otestujte upravit, podrobnosti a odkazy odstranit.</span><span class="sxs-lookup"><span data-stu-id="68158-304">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="68158-305">Přidání dat do databáze</span><span class="sxs-lookup"><span data-stu-id="68158-305">Seed the database</span></span>

<span data-ttu-id="68158-306">Metoda `EnsureCreated` vytvoří prázdnou databázi.</span><span class="sxs-lookup"><span data-stu-id="68158-306">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="68158-307">V této části se přidá kód, který naplní databázi testovacími daty.</span><span class="sxs-lookup"><span data-stu-id="68158-307">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="68158-308">Vytvořte *data/DbInitializer. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="68158-308">Create *Data/DbInitializer.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="68158-309">Kód kontroluje, zda v databázi existují studenti.</span><span class="sxs-lookup"><span data-stu-id="68158-309">The code checks if there are any students in the database.</span></span> <span data-ttu-id="68158-310">Pokud neexistují studenti, přidá testovací data do databáze.</span><span class="sxs-lookup"><span data-stu-id="68158-310">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="68158-311">Vytvoří testovací data v polích místo `List<T>` kolekce pro optimalizaci výkonu.</span><span class="sxs-lookup"><span data-stu-id="68158-311">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="68158-312">V *program.cs*nahraďte volání `EnsureCreated` voláním `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="68158-312">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="68158-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="68158-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="68158-314">Zastavte aplikaci, pokud je spuštěná, a spusťte následující příkaz v **konzole správce balíčků** (PMC):</span><span class="sxs-lookup"><span data-stu-id="68158-314">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="68158-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="68158-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="68158-316">Zastavte aplikaci, pokud je spuštěná, a odstraňte soubor *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="68158-316">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="68158-317">Restartujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="68158-317">Restart the app.</span></span>

* <span data-ttu-id="68158-318">Vyberte stránku Students a zobrazte si dosazený údaj.</span><span class="sxs-lookup"><span data-stu-id="68158-318">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="68158-319">Zobrazení databáze</span><span class="sxs-lookup"><span data-stu-id="68158-319">View the database</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="68158-320">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="68158-320">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="68158-321">Otevřete **Průzkumník objektů systému SQL Server** (SSOX) z nabídky **Zobrazit** v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="68158-321">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="68158-322">V SSOX vyberte **(LocalDB) \MSSQLLocalDB > databáze > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="68158-322">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="68158-323">Název databáze je vygenerován z dříve zadaného názvu kontextu plus pomlčka a identifikátor GUID.</span><span class="sxs-lookup"><span data-stu-id="68158-323">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="68158-324">Rozbalte uzel **tabulky** .</span><span class="sxs-lookup"><span data-stu-id="68158-324">Expand the **Tables** node.</span></span>
* <span data-ttu-id="68158-325">Klikněte pravým tlačítkem myši na tabulku **student** a kliknutím na **Zobrazit data** Zobrazte vytvořené sloupce a řádky vložené do tabulky.</span><span class="sxs-lookup"><span data-stu-id="68158-325">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="68158-326">Kliknutím pravým tlačítkem myši na tabulku **student** a kliknutím na **Zobrazit kód** zjistíte, jak se `Student` model mapuje na schéma `Student` tabulky.</span><span class="sxs-lookup"><span data-stu-id="68158-326">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="68158-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="68158-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="68158-328">Použijte nástroj SQLite k zobrazení schématu databáze a dat osazených daty.</span><span class="sxs-lookup"><span data-stu-id="68158-328">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="68158-329">Databázový soubor má název *cu. DB* a je umístěn ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="68158-329">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="68158-330">Asynchronní kód</span><span class="sxs-lookup"><span data-stu-id="68158-330">Asynchronous code</span></span>

<span data-ttu-id="68158-331">Asynchronní programování je výchozím režimem pro ASP.NET Core a EF Core.</span><span class="sxs-lookup"><span data-stu-id="68158-331">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="68158-332">Webový server má omezený počet vláken, které jsou k dispozici, a v situacích, vysokého zatížení všech dostupných vláken může být používán.</span><span class="sxs-lookup"><span data-stu-id="68158-332">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="68158-333">Pokud k tomu dojde, server nemůže zpracovat nové žádosti, dokud se uvolnit vlákna.</span><span class="sxs-lookup"><span data-stu-id="68158-333">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="68158-334">Přestože se nejedná skutečně každé dílo vzhledem k tomu, že čekání na vstupně-výstupních operací na dokončení, může kódem synchronní svázané několika vlákny.</span><span class="sxs-lookup"><span data-stu-id="68158-334">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="68158-335">Asynchronní kód když proces čeká na vstupně-výstupních operací na dokončení, je jeho vlákno uvolněn pro server určený pro zpracováním jiných požadavků.</span><span class="sxs-lookup"><span data-stu-id="68158-335">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="68158-336">Výsledkem je, že asynchronní kód umožňuje efektivnější použití prostředků serveru a server může zpracovávat více provozu bez prodlev.</span><span class="sxs-lookup"><span data-stu-id="68158-336">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="68158-337">Asynchronní kód zavést malé množství režie v době běhu.</span><span class="sxs-lookup"><span data-stu-id="68158-337">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="68158-338">V situacích s nízkým provozem stiskněte výkonu je zanedbatelný, při vysoké návštěvnosti situacích, potenciálních zlepšení výkonu je důležitým.</span><span class="sxs-lookup"><span data-stu-id="68158-338">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="68158-339">V následujícím kódu, klíčové slovo [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` návratová hodnota `await` klíčová slova a metoda `ToListAsync` provede asynchronní spouštění kódu.</span><span class="sxs-lookup"><span data-stu-id="68158-339">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="68158-340">Klíčové slovo `async` instruuje kompilátor na:</span><span class="sxs-lookup"><span data-stu-id="68158-340">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="68158-341">Generovat zpětná volání pro části tělo metody.</span><span class="sxs-lookup"><span data-stu-id="68158-341">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="68158-342">Vytvořte vrácený objekt [úkolu](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) .</span><span class="sxs-lookup"><span data-stu-id="68158-342">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="68158-343">Návratový typ `Task<T>` představuje probíhající práci.</span><span class="sxs-lookup"><span data-stu-id="68158-343">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="68158-344">Klíčové slovo `await` způsobí, že kompilátor rozdělí metodu do dvou částí.</span><span class="sxs-lookup"><span data-stu-id="68158-344">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="68158-345">První část končí operace, která se spustí asynchronně.</span><span class="sxs-lookup"><span data-stu-id="68158-345">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="68158-346">Druhá část je nepoužili metodu zpětného volání, která je volána po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="68158-346">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="68158-347">`ToListAsync` je asynchronní verze `ToList` metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="68158-347">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="68158-348">Některé co je potřeba mít na paměti při zápisu asynchronního kódu, který používá EF Core:</span><span class="sxs-lookup"><span data-stu-id="68158-348">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="68158-349">Asynchronně jsou spouštěny pouze příkazy, které způsobují odeslání dotazů nebo příkazů do databáze.</span><span class="sxs-lookup"><span data-stu-id="68158-349">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="68158-350">To zahrnuje `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`a `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="68158-350">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="68158-351">Neobsahuje příkazy, které pouze mění `IQueryable`, například `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="68158-351">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="68158-352">Objekt context EF Core není bezpečné pro vlákna: nedoporučujeme provádět více operací paralelně.</span><span class="sxs-lookup"><span data-stu-id="68158-352">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="68158-353">Chcete-li využít výhody z hlediska výkonu asynchronního kódu, ověřte, že balíčky knihoven (například pro stránkování) používají async, pokud volají EF Core metody, které odesílají dotazy do databáze.</span><span class="sxs-lookup"><span data-stu-id="68158-353">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="68158-354">Další informace o asynchronním programování v rozhraní .NET naleznete v tématu [Async Overview](/dotnet/standard/async) a [Asynchronous Programming with Async a await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="68158-354">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="68158-355">Další kroky</span><span class="sxs-lookup"><span data-stu-id="68158-355">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="68158-356">Další kurz</span><span class="sxs-lookup"><span data-stu-id="68158-356">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="68158-357">Contoso University ukázkovou webovou aplikaci ukazuje, jak vytvořit aplikaci ASP.NET Core Razor Pages pomocí Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="68158-357">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="68158-358">Ukázková aplikace je webovou stránku pro fiktivní společnosti Contoso University.</span><span class="sxs-lookup"><span data-stu-id="68158-358">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="68158-359">Zahrnuje funkce, jako student přijetí, kurz vytvoření a přiřazení instruktorem.</span><span class="sxs-lookup"><span data-stu-id="68158-359">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="68158-360">Tato stránka je první ze série kurzů, které vysvětlují, jak vytvořit ukázková aplikace Contoso University.</span><span class="sxs-lookup"><span data-stu-id="68158-360">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="68158-361">Stažení nebo zobrazení dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="68158-361">Download or view the completed app.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="68158-362">[Pokyny ke stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="68158-362">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="68158-363">Požadavky</span><span class="sxs-lookup"><span data-stu-id="68158-363">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="68158-364">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="68158-364">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="68158-365">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="68158-365">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="68158-366">Znalost [Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="68158-366">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="68158-367">Noví Programátori by měli před spuštěním této série dokončit [Začínáme s Razor Pages](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="68158-367">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="68158-368">Odstraňování potíží</span><span class="sxs-lookup"><span data-stu-id="68158-368">Troubleshooting</span></span>

<span data-ttu-id="68158-369">Pokud narazíte na problém, který nelze vyřešit, můžete řešení obecně najít porovnáním kódu s [dokončeným projektem](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="68158-369">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="68158-370">Dobrým způsobem, jak získat pomoc, je odeslání otázky do [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) pro [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="68158-370">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="68158-371">Webové aplikace Contoso University</span><span class="sxs-lookup"><span data-stu-id="68158-371">The Contoso University web app</span></span>

<span data-ttu-id="68158-372">Aplikace vytvořené v těchto kurzech je webová stránka základní university.</span><span class="sxs-lookup"><span data-stu-id="68158-372">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="68158-373">Uživatelé mohou zobrazit a aktualizovat Všichni studenti, kurz a informace instruktorem.</span><span class="sxs-lookup"><span data-stu-id="68158-373">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="68158-374">Tady je několik obrazovek vytvořili v kurzu.</span><span class="sxs-lookup"><span data-stu-id="68158-374">Here are a few of the screens created in the tutorial.</span></span>

![Studenti indexová stránka](intro/_static/students-index.png)

![Stránky pro úpravu studentů](intro/_static/student-edit.png)

<span data-ttu-id="68158-377">Styl uživatelského rozhraní tohoto webu se blíží co je generována pomocí integrovaných šablon.</span><span class="sxs-lookup"><span data-stu-id="68158-377">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="68158-378">Kurz zaměřuje se na EF Core se stránkami Razor, uživatelské rozhraní ne.</span><span class="sxs-lookup"><span data-stu-id="68158-378">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-razor-pages-web-app"></a><span data-ttu-id="68158-379">Vytvoření webové aplikace stránky Razor ContosoUniversity</span><span class="sxs-lookup"><span data-stu-id="68158-379">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="68158-380">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="68158-380">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="68158-381">V nabídce **soubor** v aplikaci Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="68158-381">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="68158-382">Vytvořte novou webovou aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="68158-382">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="68158-383">Pojmenujte projekt **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="68158-383">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="68158-384">Je důležité pojmenovat projekt *ContosoUniversity* , aby se obory názvů shodovaly, když je kód zkopírován/vložen.</span><span class="sxs-lookup"><span data-stu-id="68158-384">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="68158-385">V rozevíracím seznamu vyberte **ASP.NET Core 2,1** a potom vyberte možnost **Webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="68158-385">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="68158-386">Obrázky předchozích kroků najdete v tématu [Vytvoření webové aplikace Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="68158-386">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="68158-387">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="68158-387">Run the app.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="68158-388">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="68158-388">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="68158-389">Nastavit styl lokality</span><span class="sxs-lookup"><span data-stu-id="68158-389">Set up the site style</span></span>

<span data-ttu-id="68158-390">Několik změn nastavení v nabídce webu, rozložení a domovské stránky.</span><span class="sxs-lookup"><span data-stu-id="68158-390">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="68158-391">Update *Pages/Shared/_Layout. cshtml* s následujícími změnami:</span><span class="sxs-lookup"><span data-stu-id="68158-391">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="68158-392">Změňte všechny výskyty "ContosoUniversity" na "University společnosti Contoso".</span><span class="sxs-lookup"><span data-stu-id="68158-392">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="68158-393">Existují tři výskyty.</span><span class="sxs-lookup"><span data-stu-id="68158-393">There are three occurrences.</span></span>

* <span data-ttu-id="68158-394">Přidejte položky nabídky pro **studenty**, **kurzy**, **instruktory**a **oddělení**a odstraňte položku nabídky **kontakt** .</span><span class="sxs-lookup"><span data-stu-id="68158-394">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="68158-395">Změny jsou zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="68158-395">The changes are highlighted.</span></span> <span data-ttu-id="68158-396">(Všechny *značky se nezobrazí* .)</span><span class="sxs-lookup"><span data-stu-id="68158-396">(All the markup is *not* displayed.)</span></span>

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="68158-397">Na *stránce pages/index. cshtml*nahraďte obsah souboru následujícím kódem, který nahradí text o ASP.NET a MVC textem o této aplikaci:</span><span class="sxs-lookup"><span data-stu-id="68158-397">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="68158-398">Vytvoření datového modelu</span><span class="sxs-lookup"><span data-stu-id="68158-398">Create the data model</span></span>

<span data-ttu-id="68158-399">Vytvoření tříd entit pro aplikaci Contoso University.</span><span class="sxs-lookup"><span data-stu-id="68158-399">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="68158-400">Začněte s následující tři entity:</span><span class="sxs-lookup"><span data-stu-id="68158-400">Start with the following three entities:</span></span>

![Kurz – registrace – studentech modelového diagramu](intro/_static/data-model-diagram.png)

<span data-ttu-id="68158-402">Mezi entitami `Student` a `Enrollment` existuje vztah 1: n.</span><span class="sxs-lookup"><span data-stu-id="68158-402">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="68158-403">Mezi entitami `Course` a `Enrollment` existuje vztah 1: n.</span><span class="sxs-lookup"><span data-stu-id="68158-403">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="68158-404">Student může zaregistrovat libovolný počet kurzů.</span><span class="sxs-lookup"><span data-stu-id="68158-404">A student can enroll in any number of courses.</span></span> <span data-ttu-id="68158-405">Kurz můžete mít libovolný počet studentů zaregistrovaná do něj.</span><span class="sxs-lookup"><span data-stu-id="68158-405">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="68158-406">V následujících částech je vytvořená třída pro každou z těchto entit.</span><span class="sxs-lookup"><span data-stu-id="68158-406">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="68158-407">Entita studenta</span><span class="sxs-lookup"><span data-stu-id="68158-407">The Student entity</span></span>

![Diagram entity studenta](intro/_static/student-entity.png)

<span data-ttu-id="68158-409">Vytvořte složku *modelů* .</span><span class="sxs-lookup"><span data-stu-id="68158-409">Create a *Models* folder.</span></span> <span data-ttu-id="68158-410">Ve složce *modely* vytvořte soubor třídy s názvem *student.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="68158-410">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="68158-411">Vlastnost `ID` se zobrazí jako sloupec primárního klíče tabulky databáze (DB), která odpovídá této třídě.</span><span class="sxs-lookup"><span data-stu-id="68158-411">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="68158-412">Ve výchozím nastavení EF Core interpretuje vlastnost s názvem `ID` nebo `classnameID` jako primární klíč.</span><span class="sxs-lookup"><span data-stu-id="68158-412">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="68158-413">V `classnameID``classname` je název třídy.</span><span class="sxs-lookup"><span data-stu-id="68158-413">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="68158-414">Alternativní automaticky rozpoznaný primární klíč je `StudentID` v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="68158-414">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="68158-415">Vlastnost `Enrollments` je [navigační vlastnost](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="68158-415">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="68158-416">Vlastnosti navigace propojit s dalšími subjekty, které se vztahují k této entity.</span><span class="sxs-lookup"><span data-stu-id="68158-416">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="68158-417">V tomto případě vlastnost `Enrollments` `Student entity` uchovává všechny `Enrollment` entity, které se vztahují k tomuto `Student`.</span><span class="sxs-lookup"><span data-stu-id="68158-417">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="68158-418">Například pokud má řádek studenta v databázi dva související řádky registrace, `Enrollments` navigační vlastnost obsahuje tyto dvě entity `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="68158-418">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="68158-419">Související `Enrollment` řádek je řádek, který obsahuje hodnotu primárního klíče studenta ve sloupci `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="68158-419">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="68158-420">Předpokládejme například, že student s ID = 1 má dva řádky v tabulce `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="68158-420">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="68158-421">Tabulka `Enrollment` má dva řádky s `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="68158-421">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="68158-422">`StudentID` je cizí klíč v tabulce `Enrollment`, která určuje studenta v tabulce `Student`.</span><span class="sxs-lookup"><span data-stu-id="68158-422">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="68158-423">Pokud navigační vlastnost může obsahovat více entit, musí být vlastnost navigace typu seznam, například `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="68158-423">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="68158-424">lze zadat `ICollection<T>` nebo typ jako `List<T>` nebo `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="68158-424">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="68158-425">Při použití `ICollection<T>` EF Core ve výchozím nastavení vytvoří kolekci `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="68158-425">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="68158-426">Navigační vlastnosti, které obsahují více entit, pocházejí z many-to-many a jeden mnoho relací.</span><span class="sxs-lookup"><span data-stu-id="68158-426">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="68158-427">Registrace entity</span><span class="sxs-lookup"><span data-stu-id="68158-427">The Enrollment entity</span></span>

![Diagram entity registrace](intro/_static/enrollment-entity.png)

<span data-ttu-id="68158-429">Ve složce *modely* vytvořte *Enrollment.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="68158-429">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="68158-430">Vlastnost `EnrollmentID` je primární klíč.</span><span class="sxs-lookup"><span data-stu-id="68158-430">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="68158-431">Tato entita používá `classnameID` vzor místo `ID` jako entita `Student`.</span><span class="sxs-lookup"><span data-stu-id="68158-431">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="68158-432">Vývojáři obvykle zvolte jeden model a použít ho v rámci datového modelu.</span><span class="sxs-lookup"><span data-stu-id="68158-432">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="68158-433">V pozdějších kurzech pomocí ID bez classname zobrazí zjednodušit implementaci dědičnosti v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="68158-433">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="68158-434">Vlastnost `Grade` je `enum`.</span><span class="sxs-lookup"><span data-stu-id="68158-434">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="68158-435">Otazník po deklaraci typu `Grade` označuje, že vlastnost `Grade` může mít hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="68158-435">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="68158-436">Na podnikové úrovni, který má hodnotu null se liší od nulové třída – null znamená, že známku vyjádřenou není znám nebo ještě nebyly přiřazeny.</span><span class="sxs-lookup"><span data-stu-id="68158-436">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="68158-437">Vlastnost `StudentID` je cizí klíč a odpovídající navigační vlastnost je `Student`.</span><span class="sxs-lookup"><span data-stu-id="68158-437">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="68158-438">Entita `Enrollment` je přidružená k jedné entitě `Student`, takže tato vlastnost obsahuje jednu entitu `Student`.</span><span class="sxs-lookup"><span data-stu-id="68158-438">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="68158-439">Entita `Student` se liší od `Student.Enrollments` navigační vlastnost, která obsahuje více entit `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="68158-439">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="68158-440">Vlastnost `CourseID` je cizí klíč a odpovídající navigační vlastnost je `Course`.</span><span class="sxs-lookup"><span data-stu-id="68158-440">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="68158-441">Entita `Enrollment` je přidružená k jedné entitě `Course`.</span><span class="sxs-lookup"><span data-stu-id="68158-441">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="68158-442">EF Core interpretuje vlastnost jako cizí klíč, pokud má název `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="68158-442">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="68158-443">Například`StudentID` pro vlastnost `Student` navigace, protože je `ID`primární klíč entity `Student`.</span><span class="sxs-lookup"><span data-stu-id="68158-443">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="68158-444">Vlastnosti cizího klíče mohou být také pojmenovány `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="68158-444">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="68158-445">`CourseID` například, protože je primární klíč entity `Course` `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="68158-445">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="68158-446">Kurz entity</span><span class="sxs-lookup"><span data-stu-id="68158-446">The Course entity</span></span>

![Diagram kurzu entity](intro/_static/course-entity.png)

<span data-ttu-id="68158-448">Ve složce *modely* vytvořte *Course.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="68158-448">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="68158-449">Vlastnost `Enrollments` je navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="68158-449">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="68158-450">Entita `Course` může souviset s libovolným počtem entit `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="68158-450">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="68158-451">Atribut `DatabaseGenerated` umožňuje aplikaci určit primární klíč místo toho, aby ho databáze vygenerovala.</span><span class="sxs-lookup"><span data-stu-id="68158-451">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="68158-452">Vygenerované uživatelské rozhraní modelu studenta</span><span class="sxs-lookup"><span data-stu-id="68158-452">Scaffold the student model</span></span>

<span data-ttu-id="68158-453">V této části je automaticky generovaný model studentů.</span><span class="sxs-lookup"><span data-stu-id="68158-453">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="68158-454">To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model studentů.</span><span class="sxs-lookup"><span data-stu-id="68158-454">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="68158-455">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="68158-455">Build the project.</span></span>
* <span data-ttu-id="68158-456">Vytvořte složku *stránky/studenty* .</span><span class="sxs-lookup"><span data-stu-id="68158-456">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="68158-457">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="68158-457">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="68158-458">V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *stránky/Students* > **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="68158-458">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="68158-459">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="68158-459">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="68158-460">Dokončete dialog **přidat Razor Pages pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="68158-460">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="68158-461">V rozevíracím seznamu **třída modelu** vyberte **student (ContosoUniversity. Models)** .</span><span class="sxs-lookup"><span data-stu-id="68158-461">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="68158-462">V řádku **Třída kontextu dat** vyberte symbol **+** (plus) a změňte vygenerovaný název na **ContosoUniversity. Models. SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="68158-462">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="68158-463">V rozevíracím seznamu **Třída kontextu dat** vyberte **ContosoUniversity. Models. SchoolContext.**</span><span class="sxs-lookup"><span data-stu-id="68158-463">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="68158-464">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="68158-464">Select **Add**.</span></span>

![Dialogové okno CRUD](intro/_static/s1.png)

<span data-ttu-id="68158-466">Pokud máte problém s předchozím krokem, podívejte [se do části vygenerované modely filmů](xref:tutorials/razor-pages/model#scaffold-the-movie-model) .</span><span class="sxs-lookup"><span data-stu-id="68158-466">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="68158-467">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="68158-467">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="68158-468">Spusťte následující příkazy k modelu studentů.</span><span class="sxs-lookup"><span data-stu-id="68158-468">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="68158-469">Proces vygenerované uživatelské rozhraní vytvořit a změnit následující soubory:</span><span class="sxs-lookup"><span data-stu-id="68158-469">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="68158-470">Soubory vytvořené</span><span class="sxs-lookup"><span data-stu-id="68158-470">Files created</span></span>

* <span data-ttu-id="68158-471">*Stránky/studenty* Vytvořit, odstranit, podrobnosti, upravit, index.</span><span class="sxs-lookup"><span data-stu-id="68158-471">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="68158-472">*Data/SchoolContext. cs*</span><span class="sxs-lookup"><span data-stu-id="68158-472">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="68158-473">Aktualizace souboru</span><span class="sxs-lookup"><span data-stu-id="68158-473">File updates</span></span>

* <span data-ttu-id="68158-474">*Startup.cs* : změny v tomto souboru jsou podrobně popsané v další části.</span><span class="sxs-lookup"><span data-stu-id="68158-474">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="68158-475">*appSettings. JSON* : Přidal se připojovací řetězec použitý k připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="68158-475">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="68158-476">Prozkoumání kontextu registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="68158-476">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="68158-477">ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="68158-477">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="68158-478">Služby (například kontext EF Core databáze) jsou registrované pomocí vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="68158-478">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="68158-479">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="68158-479">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="68158-480">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="68158-480">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="68158-481">Nástroj pro generování uživatelského rozhraní automaticky vytvoří kontext databáze a zaregistrovaného kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="68158-481">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="68158-482">Projděte si metodu `ConfigureServices` v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="68158-482">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="68158-483">Zvýrazněný řádek byl přidán modulem scaffolder:</span><span class="sxs-lookup"><span data-stu-id="68158-483">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="68158-484">Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="68158-484">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="68158-485">Pro místní vývoj načítá [konfigurační systém ASP.NET Core](xref:fundamentals/configuration/index) připojovací řetězec ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="68158-485">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="68158-486">Aktualizovat hlavní</span><span class="sxs-lookup"><span data-stu-id="68158-486">Update main</span></span>

<span data-ttu-id="68158-487">V *program.cs*upravte metodu `Main` a proveďte následující:</span><span class="sxs-lookup"><span data-stu-id="68158-487">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="68158-488">Instance kontextu databáze získáte z kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="68158-488">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="68158-489">Zavolejte [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="68158-489">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="68158-490">Vyřazení kontextu při dokončení metody `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="68158-490">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="68158-491">Následující kód ukazuje aktualizovaný soubor *program.cs* .</span><span class="sxs-lookup"><span data-stu-id="68158-491">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="68158-492">`EnsureCreated` zajišťuje, aby databáze pro kontext existovala.</span><span class="sxs-lookup"><span data-stu-id="68158-492">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="68158-493">Pokud existuje, nedojde k žádné akci.</span><span class="sxs-lookup"><span data-stu-id="68158-493">If it exists, no action is taken.</span></span> <span data-ttu-id="68158-494">Pokud neexistuje, pak se vytvoří databázi a její schéma.</span><span class="sxs-lookup"><span data-stu-id="68158-494">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="68158-495">`EnsureCreated` nepoužívá k vytvoření databáze migrace.</span><span class="sxs-lookup"><span data-stu-id="68158-495">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="68158-496">Databázi vytvořenou pomocí `EnsureCreated` nelze později aktualizovat pomocí migrace.</span><span class="sxs-lookup"><span data-stu-id="68158-496">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="68158-497">`EnsureCreated` se volá při spuštění aplikace, což umožňuje následující pracovní postup:</span><span class="sxs-lookup"><span data-stu-id="68158-497">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="68158-498">Odstranění databáze.</span><span class="sxs-lookup"><span data-stu-id="68158-498">Delete the DB.</span></span>
* <span data-ttu-id="68158-499">Změňte schéma databáze (například přidejte pole `EmailAddress`).</span><span class="sxs-lookup"><span data-stu-id="68158-499">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="68158-500">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="68158-500">Run the app.</span></span>
* <span data-ttu-id="68158-501">`EnsureCreated` vytvoří databázi se sloupcem`EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="68158-501">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="68158-502">`EnsureCreated` je v brzké době ve vývoji pohodlná, když se schéma rychle vyvíjí.</span><span class="sxs-lookup"><span data-stu-id="68158-502">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="68158-503">Později v tomto kurzu se odstraní databáze a používají se migrace.</span><span class="sxs-lookup"><span data-stu-id="68158-503">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="68158-504">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="68158-504">Test the app</span></span>

<span data-ttu-id="68158-505">Spusťte aplikaci a přijmout zásady souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="68158-505">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="68158-506">Tato aplikace nemá uchovává osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="68158-506">This app doesn't keep personal information.</span></span> <span data-ttu-id="68158-507">Informace o zásadách souborů cookie najdete v tématu [Podpora pro EU obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="68158-507">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="68158-508">Vyberte odkaz **Students** a pak **vytvořte nový**.</span><span class="sxs-lookup"><span data-stu-id="68158-508">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="68158-509">Otestujte upravit, podrobnosti a odkazy odstranit.</span><span class="sxs-lookup"><span data-stu-id="68158-509">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="68158-510">Prozkoumání kontextu SchoolContext DB</span><span class="sxs-lookup"><span data-stu-id="68158-510">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="68158-511">Hlavní třída, která koordinuje EF Core funkce pro daný datový model je třídy kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="68158-511">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="68158-512">Kontext dat je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="68158-512">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="68158-513">Kontext dat určuje entit, které jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="68158-513">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="68158-514">V tomto projektu je třída pojmenována `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="68158-514">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="68158-515">*SchoolContext.cs* aktualizujte pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="68158-515">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="68158-516">Zvýrazněný kód vytvoří vlastnost [negenerickými\<TEntity >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro každou sadu entit.</span><span class="sxs-lookup"><span data-stu-id="68158-516">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="68158-517">V terminologii EF Core:</span><span class="sxs-lookup"><span data-stu-id="68158-517">In EF Core terminology:</span></span>

* <span data-ttu-id="68158-518">Obvykle sadu entit odpovídá Databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="68158-518">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="68158-519">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="68158-519">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="68158-520">`DbSet<Enrollment>` a `DbSet<Course>` mohou být vynechány.</span><span class="sxs-lookup"><span data-stu-id="68158-520">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="68158-521">EF Core je implicitně obsahuje, protože entita `Student` odkazuje na entitu `Enrollment` a entita `Enrollment` odkazuje na entitu `Course`.</span><span class="sxs-lookup"><span data-stu-id="68158-521">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="68158-522">V tomto kurzu ponechte `DbSet<Enrollment>` a `DbSet<Course>` v `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="68158-522">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="68158-523">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="68158-523">SQL Server Express LocalDB</span></span>

<span data-ttu-id="68158-524">Připojovací řetězec Určuje [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="68158-524">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="68158-525">LocalDB je Odlehčená verze SQL serveru Express Database Engine a je určená pro vývoj aplikací, není použití v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="68158-525">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="68158-526">LocalDB spustí na vyžádání a běží v uživatelském režimu, takže není bez složité konfigurace.</span><span class="sxs-lookup"><span data-stu-id="68158-526">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="68158-527">Ve výchozím nastavení LocalDB vytvoří soubory *. mdf* DB v adresáři `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="68158-527">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="68158-528">Přidejte kód pro inicializaci databáze se testovací data</span><span class="sxs-lookup"><span data-stu-id="68158-528">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="68158-529">EF Core vytvoří prázdná databáze.</span><span class="sxs-lookup"><span data-stu-id="68158-529">EF Core creates an empty DB.</span></span> <span data-ttu-id="68158-530">V této části je zapsána `Initialize` metoda pro naplnění testovacích dat.</span><span class="sxs-lookup"><span data-stu-id="68158-530">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="68158-531">Ve složce *data* vytvořte nový soubor třídy s názvem *DbInitializer.cs* a přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="68158-531">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="68158-532">Poznámka: předchozí kód používá `Models` pro obor názvů (`namespace ContosoUniversity.Models`) místo `Data`.</span><span class="sxs-lookup"><span data-stu-id="68158-532">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="68158-533">`Models` je konzistentní s kódem generovaným modulem generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="68158-533">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="68158-534">Další informace najdete v [tomto problému s vygenerováním uživatelského rozhraní GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="68158-534">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="68158-535">Kód kontroluje, jestli na databáze nejsou všechny studenty.</span><span class="sxs-lookup"><span data-stu-id="68158-535">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="68158-536">Pokud v databázi nejsou žádní studenti, databáze je inicializována s testovací data.</span><span class="sxs-lookup"><span data-stu-id="68158-536">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="68158-537">Načítá testovací data do polí místo `List<T>` kolekcí za účelem optimalizace výkonu.</span><span class="sxs-lookup"><span data-stu-id="68158-537">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="68158-538">Metoda `EnsureCreated` automaticky vytvoří databázi pro kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="68158-538">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="68158-539">Pokud databáze existuje, `EnsureCreated` se vrátí beze změny databáze.</span><span class="sxs-lookup"><span data-stu-id="68158-539">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="68158-540">V *program.cs*upravte metodu `Main` pro volání `Initialize`:</span><span class="sxs-lookup"><span data-stu-id="68158-540">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="68158-541">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="68158-541">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="68158-542">Zastavte aplikaci, pokud je spuštěná, a spusťte následující příkaz v **konzole správce balíčků** (PMC):</span><span class="sxs-lookup"><span data-stu-id="68158-542">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="68158-543">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="68158-543">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="68158-544">Zastavte aplikaci, pokud je spuštěná, a odstraňte soubor *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="68158-544">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="68158-545">Zobrazení databáze</span><span class="sxs-lookup"><span data-stu-id="68158-545">View the DB</span></span>

<span data-ttu-id="68158-546">Název databáze je vygenerován z dříve zadaného názvu kontextu plus pomlčka a identifikátor GUID.</span><span class="sxs-lookup"><span data-stu-id="68158-546">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="68158-547">Proto bude název databáze "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="68158-547">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="68158-548">Identifikátor GUID bude pro každého uživatele odlišný.</span><span class="sxs-lookup"><span data-stu-id="68158-548">The GUID will be different for each user.</span></span>
<span data-ttu-id="68158-549">Otevřete **Průzkumník objektů systému SQL Server** (SSOX) z nabídky **Zobrazit** v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="68158-549">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="68158-550">V SSOX klikněte na **(LocalDB) \MSSQLLocalDB > databáze > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="68158-550">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="68158-551">Rozbalte uzel **tabulky** .</span><span class="sxs-lookup"><span data-stu-id="68158-551">Expand the **Tables** node.</span></span>

<span data-ttu-id="68158-552">Klikněte pravým tlačítkem myši na tabulku **student** a kliknutím na **Zobrazit data** Zobrazte vytvořené sloupce a řádky vložené do tabulky.</span><span class="sxs-lookup"><span data-stu-id="68158-552">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="68158-553">Asynchronní kód</span><span class="sxs-lookup"><span data-stu-id="68158-553">Asynchronous code</span></span>

<span data-ttu-id="68158-554">Asynchronní programování je výchozím režimem pro ASP.NET Core a EF Core.</span><span class="sxs-lookup"><span data-stu-id="68158-554">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="68158-555">Webový server má omezený počet vláken, které jsou k dispozici, a v situacích, vysokého zatížení všech dostupných vláken může být používán.</span><span class="sxs-lookup"><span data-stu-id="68158-555">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="68158-556">Pokud k tomu dojde, server nemůže zpracovat nové žádosti, dokud se uvolnit vlákna.</span><span class="sxs-lookup"><span data-stu-id="68158-556">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="68158-557">Přestože se nejedná skutečně každé dílo vzhledem k tomu, že čekání na vstupně-výstupních operací na dokončení, může kódem synchronní svázané několika vlákny.</span><span class="sxs-lookup"><span data-stu-id="68158-557">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="68158-558">Asynchronní kód když proces čeká na vstupně-výstupních operací na dokončení, je jeho vlákno uvolněn pro server určený pro zpracováním jiných požadavků.</span><span class="sxs-lookup"><span data-stu-id="68158-558">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="68158-559">V důsledku toho asynchronního kódu umožňuje prostředky serveru použije efektivněji a aby zvládla větší provoz bez zpoždění je povoleno na serveru.</span><span class="sxs-lookup"><span data-stu-id="68158-559">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="68158-560">Asynchronní kód zavést malé množství režie v době běhu.</span><span class="sxs-lookup"><span data-stu-id="68158-560">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="68158-561">V situacích s nízkým provozem stiskněte výkonu je zanedbatelný, při vysoké návštěvnosti situacích, potenciálních zlepšení výkonu je důležitým.</span><span class="sxs-lookup"><span data-stu-id="68158-561">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="68158-562">V následujícím kódu, klíčové slovo [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` návratová hodnota `await` klíčová slova a metoda `ToListAsync` provede asynchronní spouštění kódu.</span><span class="sxs-lookup"><span data-stu-id="68158-562">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="68158-563">Klíčové slovo `async` instruuje kompilátor na:</span><span class="sxs-lookup"><span data-stu-id="68158-563">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="68158-564">Generovat zpětná volání pro části tělo metody.</span><span class="sxs-lookup"><span data-stu-id="68158-564">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="68158-565">Automaticky vytvořte vrácený objekt [úkolu](/dotnet/api/system.threading.tasks.task) .</span><span class="sxs-lookup"><span data-stu-id="68158-565">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="68158-566">Další informace najdete v tématu [návratový typ úlohy](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="68158-566">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="68158-567">Implicitní návratový typ `Task` představuje probíhající práci.</span><span class="sxs-lookup"><span data-stu-id="68158-567">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="68158-568">Klíčové slovo `await` způsobí, že kompilátor rozdělí metodu do dvou částí.</span><span class="sxs-lookup"><span data-stu-id="68158-568">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="68158-569">První část končí operace, která se spustí asynchronně.</span><span class="sxs-lookup"><span data-stu-id="68158-569">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="68158-570">Druhá část je nepoužili metodu zpětného volání, která je volána po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="68158-570">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="68158-571">`ToListAsync` je asynchronní verze `ToList` metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="68158-571">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="68158-572">Některé co je potřeba mít na paměti při zápisu asynchronního kódu, který používá EF Core:</span><span class="sxs-lookup"><span data-stu-id="68158-572">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="68158-573">Jenom příkazy, které způsobují dotazy nebo příkazy k odeslání do databáze se provedl asynchronně.</span><span class="sxs-lookup"><span data-stu-id="68158-573">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="68158-574">To zahrnuje `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`a `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="68158-574">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="68158-575">Neobsahuje příkazy, které pouze mění `IQueryable`, například `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="68158-575">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="68158-576">Objekt context EF Core není bezpečné pro vlákna: nedoporučujeme provádět více operací paralelně.</span><span class="sxs-lookup"><span data-stu-id="68158-576">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="68158-577">Abyste mohli využívat výkony těží z asynchronní kód, ověřte, že balíčků knihovny (například pro stránkování) používat asynchronní, pokud volání metody EF Core, které odesílání dotazů do databáze.</span><span class="sxs-lookup"><span data-stu-id="68158-577">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="68158-578">Další informace o asynchronním programování v rozhraní .NET naleznete v tématu [Async Overview](/dotnet/standard/async) a [Asynchronous Programming with Async a await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="68158-578">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="68158-579">V dalším kurzu, základní CRUD (vytváření, čtení, aktualizace nebo odstranění) jsou zkoumány operace.</span><span class="sxs-lookup"><span data-stu-id="68158-579">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="68158-580">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="68158-580">Additional resources</span></span>

* [<span data-ttu-id="68158-581">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="68158-581">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="68158-582">Next</span><span class="sxs-lookup"><span data-stu-id="68158-582">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
