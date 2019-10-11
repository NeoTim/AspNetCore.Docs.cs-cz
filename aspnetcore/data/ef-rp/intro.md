---
title: Razor Pages s Entity Framework Core v ASP.NET Core – kurz 1 z 8
author: rick-anderson
description: Ukazuje, jak vytvořit aplikaci Razor Pages pomocí Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: data/ef-rp/intro
ms.openlocfilehash: 01e507326ddd57057aa178ad3909fd4027a013fd
ms.sourcegitcommit: 7d3c6565dda6241eb13f9a8e1e1fd89b1cfe4d18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72259371"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="5094c-103">Razor Pages s Entity Framework Core v ASP.NET Core – kurz 1 z 8</span><span class="sxs-lookup"><span data-stu-id="5094c-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="5094c-104">[Dykstra](https://github.com/tdykstra) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5094c-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5094c-105">Toto je první v sérii kurzů, které ukazují, jak používat Entity Framework (EF) jádro v aplikaci [ASP.NET Core Razor Pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="5094c-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="5094c-106">Kurzy vytvářejí web pro fiktivní univerzitě společnosti Contoso.</span><span class="sxs-lookup"><span data-stu-id="5094c-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="5094c-107">Tato lokalita obsahuje funkce, jako je například využití studenta, vytváření kurzů a přiřazení instruktorů.</span><span class="sxs-lookup"><span data-stu-id="5094c-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span>

[<span data-ttu-id="5094c-108">Stažení nebo zobrazení dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="5094c-108">Download or view the completed app.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="5094c-109">[Pokyny ke stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="5094c-109">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5094c-110">Požadované součásti</span><span class="sxs-lookup"><span data-stu-id="5094c-110">Prerequisites</span></span>

* <span data-ttu-id="5094c-111">Pokud s Razor Pages teprve začínáte, Projděte si kurz [Začínáme s Razor Pagesm](xref:tutorials/razor-pages/razor-pages-start) , než začnete s tímto.</span><span class="sxs-lookup"><span data-stu-id="5094c-111">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5094c-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5094c-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5094c-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5094c-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="5094c-114">Databázové moduly</span><span class="sxs-lookup"><span data-stu-id="5094c-114">Database engines</span></span>

<span data-ttu-id="5094c-115">Pokyny pro Visual Studio používají [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), verzi SQL Server Express, která se spouští jenom v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="5094c-115">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="5094c-116">Pokyny pro [Visual Studio Code používají nástroj](https://www.sqlite.org/)pro databázový stroj pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="5094c-116">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="5094c-117">Pokud se rozhodnete použít SQLite, Stáhněte a nainstalujte nástroj třetí strany pro správu a zobrazení databáze SQLite, jako je například [prohlížeč databáze pro SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="5094c-117">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="5094c-118">Odstraňování potíží</span><span class="sxs-lookup"><span data-stu-id="5094c-118">Troubleshooting</span></span>

<span data-ttu-id="5094c-119">Pokud narazíte na problém, který nemůžete vyřešit, porovnejte kód s [dokončeným projektem](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="5094c-119">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="5094c-120">Dobrý způsob, jak získat pomoc, je odeslání otázky do StackOverflow.com s použitím [značky ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [značky EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="5094c-120">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="5094c-121">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="5094c-121">The sample app</span></span>

<span data-ttu-id="5094c-122">Aplikace sestavená v těchto kurzech je základním webem na univerzitě.</span><span class="sxs-lookup"><span data-stu-id="5094c-122">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="5094c-123">Uživatelé můžou zobrazit a aktualizovat informace o studentech, kurzech a instruktorech.</span><span class="sxs-lookup"><span data-stu-id="5094c-123">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="5094c-124">Tady je několik obrazovek vytvořených v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="5094c-124">Here are a few of the screens created in the tutorial.</span></span>

![Stránka indexu studentů](intro/_static/students-index30.png)

![Stránka pro úpravy studentů](intro/_static/student-edit30.png)

<span data-ttu-id="5094c-127">Styl uživatelského rozhraní tohoto webu je založen na předdefinovaných šablonách projektů.</span><span class="sxs-lookup"><span data-stu-id="5094c-127">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="5094c-128">V tomto kurzu se naučíte, jak používat EF Core, nikoli způsob přizpůsobení uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5094c-128">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="5094c-129">Pomocí odkazu v horní části stránky Získejte zdrojový kód dokončeného projektu.</span><span class="sxs-lookup"><span data-stu-id="5094c-129">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="5094c-130">Složka *cu30* obsahuje kód pro verzi kurzu ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="5094c-130">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="5094c-131">Soubory, které reflektují stav kódu pro kurzy 1-7, najdete ve složce *cu30snapshots* .</span><span class="sxs-lookup"><span data-stu-id="5094c-131">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5094c-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5094c-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5094c-133">Spuštění aplikace po stažení dokončeného projektu:</span><span class="sxs-lookup"><span data-stu-id="5094c-133">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="5094c-134">Odstraňte tři soubory a jednu složku, která má v názvu název *SQLite* .</span><span class="sxs-lookup"><span data-stu-id="5094c-134">Delete three files and one folder that have *SQLite* in the name.</span></span>
* <span data-ttu-id="5094c-135">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="5094c-135">Build the project.</span></span>
* <span data-ttu-id="5094c-136">V konzole správce balíčků (PMC) spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5094c-136">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="5094c-137">Spusťte projekt k osazení databáze.</span><span class="sxs-lookup"><span data-stu-id="5094c-137">Run the project to seed the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5094c-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5094c-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5094c-139">Spuštění aplikace po stažení dokončeného projektu:</span><span class="sxs-lookup"><span data-stu-id="5094c-139">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="5094c-140">Odstraňte *ContosoUniversity. csproj*a přejmenujte *ContosoUniversitySQLite. csproj* na *ContosoUniversity. csproj*.</span><span class="sxs-lookup"><span data-stu-id="5094c-140">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="5094c-141">Odstraňte *Startup.cs*a přejmenujte *StartupSQLite.cs* na *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5094c-141">Delete *Startup.cs*, and rename *StartupSQLite.cs* to *Startup.cs*.</span></span>
* <span data-ttu-id="5094c-142">Odstraňte *appSettings. JSON*a přejmenujte *appSettingsSQLite.* JSON na *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="5094c-142">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="5094c-143">Odstraňte složku *migrace* a přejmenujte *MigrationsSQL* na *migrace*.</span><span class="sxs-lookup"><span data-stu-id="5094c-143">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="5094c-144">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="5094c-144">Build the project.</span></span>
* <span data-ttu-id="5094c-145">Na příkazovém řádku ve složce projektu spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="5094c-145">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* <span data-ttu-id="5094c-146">V nástroji SQLite spusťte následující příkaz SQL:</span><span class="sxs-lookup"><span data-stu-id="5094c-146">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="5094c-147">Spusťte projekt k osazení databáze.</span><span class="sxs-lookup"><span data-stu-id="5094c-147">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="5094c-148">Vytvoření projektu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="5094c-148">Create the web app project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5094c-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5094c-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5094c-150">V nabídce **soubor** v aplikaci Visual Studio vyberte **Nový** **projekt**>.</span><span class="sxs-lookup"><span data-stu-id="5094c-150">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="5094c-151">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="5094c-151">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="5094c-152">Pojmenujte projekt *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="5094c-152">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="5094c-153">Je důležité použít tento přesný název, včetně velkých a malých písmen, aby se obory názvů shodovaly při zkopírování a vložení kódu.</span><span class="sxs-lookup"><span data-stu-id="5094c-153">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="5094c-154">V rozevíracích seznamech vyberte **.NET Core** a **ASP.NET Core 3,0** a potom vyberte **Webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="5094c-154">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5094c-155">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5094c-155">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5094c-156">V terminálu přejděte do složky, ve které by měla být vytvořena složka projektu.</span><span class="sxs-lookup"><span data-stu-id="5094c-156">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="5094c-157">Spuštěním následujících příkazů vytvořte projekt Razor Pages a `cd` do složky nového projektu:</span><span class="sxs-lookup"><span data-stu-id="5094c-157">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="5094c-158">Nastavení stylu webu</span><span class="sxs-lookup"><span data-stu-id="5094c-158">Set up the site style</span></span>

<span data-ttu-id="5094c-159">Pomocí aktualizace *stránek/Shared/_Layout. cshtml*nastavte záhlaví webu, zápatí a nabídku.</span><span class="sxs-lookup"><span data-stu-id="5094c-159">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="5094c-160">Změňte všechny výskyty "ContosoUniversity" na "contoso University".</span><span class="sxs-lookup"><span data-stu-id="5094c-160">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="5094c-161">Existují tři výskyty.</span><span class="sxs-lookup"><span data-stu-id="5094c-161">There are three occurrences.</span></span>

* <span data-ttu-id="5094c-162">Odstraňte položky nabídky **Domů** a **Ochrana osobních údajů** a přidejte záznamy **o o** **studentech**, **kurzech**, **instruktorech**a **odděleních**.</span><span class="sxs-lookup"><span data-stu-id="5094c-162">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="5094c-163">Změny jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="5094c-163">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="5094c-164">Na *stránce pages/index. cshtml*nahraďte obsah souboru následujícím kódem, který nahradí text o ASP.NET Core textem této aplikace:</span><span class="sxs-lookup"><span data-stu-id="5094c-164">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="5094c-165">Spusťte aplikaci, abyste ověřili, že se zobrazí domovská stránka.</span><span class="sxs-lookup"><span data-stu-id="5094c-165">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="5094c-166">Datový model</span><span class="sxs-lookup"><span data-stu-id="5094c-166">The data model</span></span>

<span data-ttu-id="5094c-167">V následujících částech se vytvoří datový model:</span><span class="sxs-lookup"><span data-stu-id="5094c-167">The following sections create a data model:</span></span>

![Kurz – registrace – diagram datového modelu studenta](intro/_static/data-model-diagram.png)

<span data-ttu-id="5094c-169">Student se může zaregistrovat v jakémkoli počtu kurzů a kurz může mít zaregistrovaný libovolný počet studentů.</span><span class="sxs-lookup"><span data-stu-id="5094c-169">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="5094c-170">Entita studenta</span><span class="sxs-lookup"><span data-stu-id="5094c-170">The Student entity</span></span>

![Diagram entity studenta](intro/_static/student-entity.png)

* <span data-ttu-id="5094c-172">Vytvořte složku *modely* ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="5094c-172">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="5094c-173">Vytvořte *modely/studenta. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="5094c-173">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="5094c-174">Vlastnost `ID` se zobrazí jako sloupec primárního klíče tabulky databáze, která odpovídá této třídě.</span><span class="sxs-lookup"><span data-stu-id="5094c-174">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="5094c-175">Ve výchozím nastavení EF Core interpretuje vlastnost s názvem `ID` nebo `classnameID` jako primární klíč.</span><span class="sxs-lookup"><span data-stu-id="5094c-175">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="5094c-176">Proto je jako primární klíč třídy `Student` alternativní automaticky rozpoznaný název `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="5094c-176">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span>

<span data-ttu-id="5094c-177">Vlastnost `Enrollments` je [navigační vlastnost](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="5094c-177">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="5094c-178">Navigační vlastnosti obsahují další entity, které se vztahují k této entitě.</span><span class="sxs-lookup"><span data-stu-id="5094c-178">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="5094c-179">V tomto případě obsahuje vlastnost `Enrollments` entity `Student` všechny entity `Enrollment`, které se vztahují k tomuto studentovi.</span><span class="sxs-lookup"><span data-stu-id="5094c-179">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="5094c-180">Například pokud má řádek studenta v databázi dva související řádky registrace, navigační vlastnost `Enrollments` obsahuje tyto dvě entity registrace.</span><span class="sxs-lookup"><span data-stu-id="5094c-180">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="5094c-181">V databázi se řádek registrace vztahuje k řádku studenta, pokud jeho sloupec StudentID obsahuje hodnotu ID studenta.</span><span class="sxs-lookup"><span data-stu-id="5094c-181">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="5094c-182">Předpokládejme například, že řádek studenta má ID = 1.</span><span class="sxs-lookup"><span data-stu-id="5094c-182">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="5094c-183">Související řádky registrace budou mít StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="5094c-183">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="5094c-184">StudentID je *cizí klíč* v tabulce zápisu.</span><span class="sxs-lookup"><span data-stu-id="5094c-184">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="5094c-185">Vlastnost `Enrollments` je definována jako `ICollection<Enrollment>`, protože může existovat více souvisejících entit zápisu.</span><span class="sxs-lookup"><span data-stu-id="5094c-185">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="5094c-186">Můžete použít jiné typy kolekce, například `List<Enrollment>` nebo `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="5094c-186">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="5094c-187">Když se použije `ICollection<Enrollment>`, EF Core ve výchozím nastavení vytvoří kolekci `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="5094c-187">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="5094c-188">Entita registrace</span><span class="sxs-lookup"><span data-stu-id="5094c-188">The Enrollment entity</span></span>

![Diagram entity registrace](intro/_static/enrollment-entity.png)

<span data-ttu-id="5094c-190">Vytvořte *modely/registrace. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5094c-190">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="5094c-191">Vlastnost `EnrollmentID` je primárním klíčem; Tato entita používá ke vzorci `classnameID` místo `ID` sám sebe.</span><span class="sxs-lookup"><span data-stu-id="5094c-191">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="5094c-192">Pro model produkčních dat vyberte jeden vzor a používejte ho konzistentně.</span><span class="sxs-lookup"><span data-stu-id="5094c-192">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="5094c-193">Tento kurz používá pouze k ilustraci toho, jak funguje.</span><span class="sxs-lookup"><span data-stu-id="5094c-193">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="5094c-194">Použití `ID` bez `classname` usnadňuje implementaci některých druhů změn datového modelu.</span><span class="sxs-lookup"><span data-stu-id="5094c-194">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="5094c-195">Vlastnost `Grade` je `enum`.</span><span class="sxs-lookup"><span data-stu-id="5094c-195">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="5094c-196">Otazník po deklaraci typu `Grade` značí, že vlastnost `Grade` má [hodnotu null](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="5094c-196">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="5094c-197">Hodnota, která je null, se liší od nulové třídy @ no__t-0null znamená, že se nejedná o známou třídu nebo ještě nebyla přiřazena.</span><span class="sxs-lookup"><span data-stu-id="5094c-197">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="5094c-198">Vlastnost `StudentID` je cizí klíč a odpovídající navigační vlastnost je `Student`.</span><span class="sxs-lookup"><span data-stu-id="5094c-198">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="5094c-199">Entita `Enrollment` je přidružená k jedné entitě `Student`, takže tato vlastnost obsahuje jednu entitu `Student`.</span><span class="sxs-lookup"><span data-stu-id="5094c-199">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="5094c-200">Vlastnost `CourseID` je cizí klíč a odpovídající navigační vlastnost je `Course`.</span><span class="sxs-lookup"><span data-stu-id="5094c-200">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="5094c-201">Entita `Enrollment` je přidružená k jedné entitě `Course`.</span><span class="sxs-lookup"><span data-stu-id="5094c-201">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="5094c-202">EF Core interpretuje vlastnost jako cizí klíč, pokud má název `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="5094c-202">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="5094c-203">Například `StudentID` je cizí klíč pro navigační vlastnost `Student`, protože primární klíč entity `Student` je `ID`.</span><span class="sxs-lookup"><span data-stu-id="5094c-203">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="5094c-204">Vlastnosti cizího klíče mohou být také pojmenovány `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="5094c-204">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="5094c-205">Například `CourseID`, protože primární klíč entity `Course` je `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="5094c-205">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="5094c-206">Entita kurzu</span><span class="sxs-lookup"><span data-stu-id="5094c-206">The Course entity</span></span>

![Diagram entity kurzu](intro/_static/course-entity.png)

<span data-ttu-id="5094c-208">Vytvořte *modely/Course. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5094c-208">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="5094c-209">Vlastnost `Enrollments` je navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="5094c-209">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="5094c-210">Entita `Course` může souviset s libovolným počtem entit `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="5094c-210">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="5094c-211">Atribut `DatabaseGenerated` umožňuje aplikaci určit primární klíč místo toho, aby ho databáze vygenerovala.</span><span class="sxs-lookup"><span data-stu-id="5094c-211">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="5094c-212">Sestavte projekt, aby se ověřilo, že nejsou k dispozici žádné chyby kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="5094c-212">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="5094c-213">Stránky studenta pro generování uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="5094c-213">Scaffold Student pages</span></span>

<span data-ttu-id="5094c-214">V této části použijete nástroj ASP.NET Core pro generování uživatelského rozhraní k vygenerování:</span><span class="sxs-lookup"><span data-stu-id="5094c-214">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="5094c-215">Třída *kontextu* EF Core.</span><span class="sxs-lookup"><span data-stu-id="5094c-215">An EF Core *context* class.</span></span> <span data-ttu-id="5094c-216">Kontext je hlavní třída, která koordinuje funkce Entity Framework pro daný datový model.</span><span class="sxs-lookup"><span data-stu-id="5094c-216">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="5094c-217">Je odvozena z třídy `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="5094c-217">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="5094c-218">Stránky Razor, které zpracovávají operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro entitu `Student`</span><span class="sxs-lookup"><span data-stu-id="5094c-218">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5094c-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5094c-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5094c-220">Vytvořte složku *Students* ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="5094c-220">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="5094c-221">V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *Pages/Students* a vyberte **Přidat** > **Nová vygenerovaná položka**.</span><span class="sxs-lookup"><span data-stu-id="5094c-221">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="5094c-222">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="5094c-222">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="5094c-223">V dialogovém okně **přidat Razor Pages pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="5094c-223">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="5094c-224">V rozevíracím seznamu **třída modelu** vyberte **student (ContosoUniversity. Models)** .</span><span class="sxs-lookup"><span data-stu-id="5094c-224">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="5094c-225">V řádku **Třída kontextu dat** vyberte symbol **+** (plus).</span><span class="sxs-lookup"><span data-stu-id="5094c-225">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="5094c-226">Změňte název kontextu dat z *ContosoUniversity. Models. ContosoUniversityContext* na *ContosoUniversity. data. SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="5094c-226">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="5094c-227">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="5094c-227">Select **Add**.</span></span>

<span data-ttu-id="5094c-228">Automaticky se nainstalují tyto balíčky:</span><span class="sxs-lookup"><span data-stu-id="5094c-228">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5094c-229">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5094c-229">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5094c-230">Spuštěním následujících příkazů .NET Core CLI nainstalujte požadované balíčky NuGet:</span><span class="sxs-lookup"><span data-stu-id="5094c-230">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="5094c-231">Pro generování uživatelského rozhraní je vyžadován balíček Microsoft. VisualStudio. Web. strategii. Design.</span><span class="sxs-lookup"><span data-stu-id="5094c-231">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="5094c-232">I když aplikace nebude používat SQL Server, nástroj pro generování uživatelského rozhraní potřebuje balíček SQL Server.</span><span class="sxs-lookup"><span data-stu-id="5094c-232">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="5094c-233">Vytvořte složku *stránky/studenty* .</span><span class="sxs-lookup"><span data-stu-id="5094c-233">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="5094c-234">Spusťte následující příkaz pro instalaci nástroje pro [generování uživatelského rozhraní ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="5094c-234">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="5094c-235">Spusťte následující příkaz pro generování uživatelského rozhraní stránek studenta.</span><span class="sxs-lookup"><span data-stu-id="5094c-235">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="5094c-236">**Ve Windows**</span><span class="sxs-lookup"><span data-stu-id="5094c-236">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="5094c-237">**V systému macOS nebo Linux**</span><span class="sxs-lookup"><span data-stu-id="5094c-237">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="5094c-238">Pokud máte problém s předchozím krokem, sestavte projekt a opakujte krok generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5094c-238">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="5094c-239">Proces generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="5094c-239">The scaffolding process:</span></span>

* <span data-ttu-id="5094c-240">Vytvoří stránky Razor ve složce *Pages/Students* :</span><span class="sxs-lookup"><span data-stu-id="5094c-240">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="5094c-241">*Vytvoření. cshtml* a *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="5094c-241">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="5094c-242">*Odstranění. cshtml* a *DELETE.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="5094c-242">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="5094c-243">*Podrobnosti. cshtml* a *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="5094c-243">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="5094c-244">*Upravit. cshtml* a *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="5094c-244">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="5094c-245">*Index. cshtml* a *index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="5094c-245">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="5094c-246">Vytvoří *data/SchoolContext. cs*.</span><span class="sxs-lookup"><span data-stu-id="5094c-246">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="5094c-247">Přidá kontext do injektáže závislosti v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5094c-247">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="5094c-248">Přidá připojovací řetězec databáze do souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="5094c-248">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="5094c-249">Připojovací řetězec databáze</span><span class="sxs-lookup"><span data-stu-id="5094c-249">Database connection string</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5094c-250">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5094c-250">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5094c-251">Připojovací řetězec Určuje [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="5094c-251">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="5094c-252">LocalDB je zjednodušená verze databázového stroje SQL Server Express a je určena pro vývoj aplikací, nikoli za použití v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="5094c-252">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="5094c-253">Ve výchozím nastavení LocalDB vytvoří soubory *. mdf* v adresáři `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="5094c-253">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5094c-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5094c-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5094c-255">Změňte připojovací řetězec tak, aby odkazoval na soubor databáze SQLite s názvem *cu. DB*:</span><span class="sxs-lookup"><span data-stu-id="5094c-255">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="5094c-256">Aktualizuje třídu kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="5094c-256">Update the database context class</span></span>

<span data-ttu-id="5094c-257">Hlavní třída, která koordinuje funkce EF Core pro daný datový model, je třída kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="5094c-257">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="5094c-258">Kontext je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="5094c-258">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="5094c-259">Kontext určuje, které entity jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="5094c-259">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="5094c-260">V tomto projektu je třída pojmenována `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="5094c-260">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="5094c-261">*SchoolContext.cs* aktualizujte pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="5094c-261">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="5094c-262">Zvýrazněný kód vytvoří vlastnost [negenerickými @ no__t-> 1TEntity](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro každou sadu entit.</span><span class="sxs-lookup"><span data-stu-id="5094c-262">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="5094c-263">V terminologii EF Core:</span><span class="sxs-lookup"><span data-stu-id="5094c-263">In EF Core terminology:</span></span>

* <span data-ttu-id="5094c-264">Sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="5094c-264">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="5094c-265">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="5094c-265">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="5094c-266">Vzhledem k tomu, že sada entit obsahuje více entit, musí mít vlastnosti Negenerickými plurální názvy.</span><span class="sxs-lookup"><span data-stu-id="5094c-266">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="5094c-267">Vzhledem k tomu, že nástroj pro generování uživatelského rozhraní vytvořil Negenerickými @ no__t-0, tento krok změny změní na plural `Students`.</span><span class="sxs-lookup"><span data-stu-id="5094c-267">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="5094c-268">Chcete-li, aby kód Razor Pages odpovídal novému názvu Negenerickými, proveďte globální změnu v celém projektu `_context.Student` na `_context.Students`.</span><span class="sxs-lookup"><span data-stu-id="5094c-268">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="5094c-269">K dispozici je 8 výskytů.</span><span class="sxs-lookup"><span data-stu-id="5094c-269">There are 8 occurrences.</span></span>

<span data-ttu-id="5094c-270">Sestavte projekt, aby se ověřilo, že nedošlo k chybám kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="5094c-270">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="5094c-271">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="5094c-271">Startup.cs</span></span>

<span data-ttu-id="5094c-272">ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5094c-272">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5094c-273">Služby (například kontext databáze EF Core) jsou registrovány pomocí injektáže závislosti při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="5094c-273">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="5094c-274">Komponenty, které vyžadují tyto služby (například Razor Pages), poskytují tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="5094c-274">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="5094c-275">Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="5094c-275">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="5094c-276">Nástroj pro generování uživatelského rozhraní automaticky zaregistroval třídu kontextu pomocí kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="5094c-276">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5094c-277">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5094c-277">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5094c-278">V `ConfigureServices` byly zvýrazněné řádky přidány pomocí nástroje pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="5094c-278">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5094c-279">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5094c-279">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5094c-280">V `ConfigureServices` se ujistěte, že kód přidaný modulem pro generování uživatelského rozhraní volá `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="5094c-280">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="5094c-281">Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="5094c-281">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="5094c-282">Pro místní vývoj načítá [konfigurační systém ASP.NET Core](xref:fundamentals/configuration/index) připojovací řetězec ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="5094c-282">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="5094c-283">Vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="5094c-283">Create the database</span></span>

<span data-ttu-id="5094c-284">Aktualizujte *program.cs* , aby se vytvořila databáze, pokud neexistuje:</span><span class="sxs-lookup"><span data-stu-id="5094c-284">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="5094c-285">Metoda [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) neprovede žádnou akci, pokud existuje databáze pro kontext.</span><span class="sxs-lookup"><span data-stu-id="5094c-285">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="5094c-286">Pokud žádná databáze neexistuje, vytvoří databázi a schéma.</span><span class="sxs-lookup"><span data-stu-id="5094c-286">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="5094c-287">`EnsureCreated` povolí následující pracovní postup pro zpracování změn datového modelu:</span><span class="sxs-lookup"><span data-stu-id="5094c-287">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="5094c-288">Odstraňte databázi.</span><span class="sxs-lookup"><span data-stu-id="5094c-288">Delete the database.</span></span> <span data-ttu-id="5094c-289">Všechna existující data budou ztracena.</span><span class="sxs-lookup"><span data-stu-id="5094c-289">Any existing data is lost.</span></span>
* <span data-ttu-id="5094c-290">Změňte datový model.</span><span class="sxs-lookup"><span data-stu-id="5094c-290">Change the data model.</span></span> <span data-ttu-id="5094c-291">Přidejte například pole `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="5094c-291">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="5094c-292">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5094c-292">Run the app.</span></span>
* <span data-ttu-id="5094c-293">`EnsureCreated` vytvoří databázi s novým schématem.</span><span class="sxs-lookup"><span data-stu-id="5094c-293">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="5094c-294">Tento pracovní postup funguje dobře v rané fázi vývoje, když se schéma rychle vyvíjí, pokud nepotřebujete zachovat data.</span><span class="sxs-lookup"><span data-stu-id="5094c-294">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="5094c-295">Tato situace se liší v případě, že data, která byla zadána do databáze, musí být zachována.</span><span class="sxs-lookup"><span data-stu-id="5094c-295">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="5094c-296">V takovém případě použijte migrace.</span><span class="sxs-lookup"><span data-stu-id="5094c-296">When that is the case, use migrations.</span></span>

<span data-ttu-id="5094c-297">Později v rámci série kurzů odstraníte databázi vytvořenou `EnsureCreated` a místo toho použít migrace.</span><span class="sxs-lookup"><span data-stu-id="5094c-297">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="5094c-298">Databázi vytvořenou pomocí `EnsureCreated` nelze aktualizovat pomocí migrace.</span><span class="sxs-lookup"><span data-stu-id="5094c-298">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="5094c-299">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="5094c-299">Test the app</span></span>

* <span data-ttu-id="5094c-300">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5094c-300">Run the app.</span></span>
* <span data-ttu-id="5094c-301">Vyberte odkaz **Students** a pak **vytvořte nový**.</span><span class="sxs-lookup"><span data-stu-id="5094c-301">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="5094c-302">Otestujte odkazy upravit, podrobnosti a odstranit.</span><span class="sxs-lookup"><span data-stu-id="5094c-302">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="5094c-303">Dosazení databáze</span><span class="sxs-lookup"><span data-stu-id="5094c-303">Seed the database</span></span>

<span data-ttu-id="5094c-304">Metoda `EnsureCreated` vytvoří prázdnou databázi.</span><span class="sxs-lookup"><span data-stu-id="5094c-304">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="5094c-305">V této části se přidá kód, který naplní databázi testovacími daty.</span><span class="sxs-lookup"><span data-stu-id="5094c-305">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="5094c-306">Vytvořte *data/DbInitializer. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="5094c-306">Create *Data/DbInitializer.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="5094c-307">Kód kontroluje, zda v databázi existují studenti.</span><span class="sxs-lookup"><span data-stu-id="5094c-307">The code checks if there are any students in the database.</span></span> <span data-ttu-id="5094c-308">Pokud neexistují studenti, přidá testovací data do databáze.</span><span class="sxs-lookup"><span data-stu-id="5094c-308">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="5094c-309">Vytvoří testovací data v polích, nikoli kolekce `List<T>` pro optimalizaci výkonu.</span><span class="sxs-lookup"><span data-stu-id="5094c-309">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="5094c-310">V *program.cs*nahraďte volání `EnsureCreated` voláním `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="5094c-310">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ````

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5094c-311">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5094c-311">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5094c-312">Zastavte aplikaci, pokud je spuštěná, a spusťte následující příkaz v **konzole správce balíčků** (PMC):</span><span class="sxs-lookup"><span data-stu-id="5094c-312">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5094c-313">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5094c-313">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5094c-314">Zastavte aplikaci, pokud je spuštěná, a odstraňte soubor *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="5094c-314">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="5094c-315">Restartujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5094c-315">Restart the app.</span></span>

* <span data-ttu-id="5094c-316">Vyberte stránku Students a zobrazte si dosazený údaj.</span><span class="sxs-lookup"><span data-stu-id="5094c-316">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="5094c-317">Zobrazení databáze</span><span class="sxs-lookup"><span data-stu-id="5094c-317">View the database</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5094c-318">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5094c-318">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5094c-319">Otevřete **Průzkumník objektů systému SQL Server** (SSOX) z nabídky **Zobrazit** v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5094c-319">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="5094c-320">V SSOX vyberte **(LocalDB) \MSSQLLocalDB > databáze > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="5094c-320">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="5094c-321">Název databáze je vygenerován z dříve zadaného názvu kontextu plus pomlčka a identifikátor GUID.</span><span class="sxs-lookup"><span data-stu-id="5094c-321">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="5094c-322">Rozbalte uzel **tabulky** .</span><span class="sxs-lookup"><span data-stu-id="5094c-322">Expand the **Tables** node.</span></span>
* <span data-ttu-id="5094c-323">Klikněte pravým tlačítkem myši na tabulku **student** a kliknutím na **Zobrazit data** Zobrazte vytvořené sloupce a řádky vložené do tabulky.</span><span class="sxs-lookup"><span data-stu-id="5094c-323">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="5094c-324">Kliknutím pravým tlačítkem myši na tabulku **student** a kliknutím na **Zobrazit kód** zjistíte, jak se model `Student` mapuje na schéma tabulky `Student`.</span><span class="sxs-lookup"><span data-stu-id="5094c-324">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5094c-325">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5094c-325">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5094c-326">Použijte nástroj SQLite k zobrazení schématu databáze a dat osazených daty.</span><span class="sxs-lookup"><span data-stu-id="5094c-326">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="5094c-327">Databázový soubor má název *cu. DB* a je umístěn ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="5094c-327">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="5094c-328">Asynchronní kód</span><span class="sxs-lookup"><span data-stu-id="5094c-328">Asynchronous code</span></span>

<span data-ttu-id="5094c-329">Asynchronní programování je výchozí režim pro ASP.NET Core a EF Core.</span><span class="sxs-lookup"><span data-stu-id="5094c-329">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="5094c-330">Na webovém serveru je k dispozici omezený počet vláken a v situacích vysokého zatížení se mohou používat všechna dostupná vlákna.</span><span class="sxs-lookup"><span data-stu-id="5094c-330">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="5094c-331">Pokud k tomu dojde, server nemůže zpracovat nové požadavky, dokud nebudou vlákna uvolněna.</span><span class="sxs-lookup"><span data-stu-id="5094c-331">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="5094c-332">Pomocí synchronního kódu může být mnoho vláken svázáno s tím, že ve skutečnosti neprovádí žádnou práci, protože čeká na dokončení vstupně-výstupních operací.</span><span class="sxs-lookup"><span data-stu-id="5094c-332">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="5094c-333">V případě asynchronního kódu, když proces čeká na dokončení vstupně-výstupních operací, je jeho vlákno uvolněno na server, který bude použit pro zpracování jiných požadavků.</span><span class="sxs-lookup"><span data-stu-id="5094c-333">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="5094c-334">Výsledkem je, že asynchronní kód umožňuje efektivnější použití prostředků serveru a server může zpracovávat více provozu bez prodlev.</span><span class="sxs-lookup"><span data-stu-id="5094c-334">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="5094c-335">Asynchronní kód zavádí v době běhu malé množství režie.</span><span class="sxs-lookup"><span data-stu-id="5094c-335">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="5094c-336">V situacích s nízkým provozem je dosaženo zanedbatelného výkonu, zatímco v situacích vysokého provozu je potenciální zlepšení výkonu značné.</span><span class="sxs-lookup"><span data-stu-id="5094c-336">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="5094c-337">V následujícím kódu, klíčové slovo [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` návratová hodnota, `await` a metoda `ToListAsync` provede asynchronní spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="5094c-337">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="5094c-338">Klíčové slovo `async` instruuje kompilátor na:</span><span class="sxs-lookup"><span data-stu-id="5094c-338">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="5094c-339">Vygenerujte zpětná volání pro části těla metody.</span><span class="sxs-lookup"><span data-stu-id="5094c-339">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="5094c-340">Vytvořte vrácený objekt [úkolu](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) .</span><span class="sxs-lookup"><span data-stu-id="5094c-340">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="5094c-341">Návratový typ `Task<T>` představuje probíhající práci.</span><span class="sxs-lookup"><span data-stu-id="5094c-341">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="5094c-342">Klíčové slovo `await` způsobí, že kompilátor rozdělí metodu do dvou částí.</span><span class="sxs-lookup"><span data-stu-id="5094c-342">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="5094c-343">První část končí asynchronně spuštěnou operací.</span><span class="sxs-lookup"><span data-stu-id="5094c-343">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="5094c-344">Druhá část je vložena do metody zpětného volání, která je volána po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="5094c-344">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="5094c-345">`ToListAsync` je asynchronní verze metody rozšíření `ToList`.</span><span class="sxs-lookup"><span data-stu-id="5094c-345">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="5094c-346">Některé věci, které je potřeba znát při psaní asynchronního kódu, který používá EF Core:</span><span class="sxs-lookup"><span data-stu-id="5094c-346">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="5094c-347">Asynchronně jsou spouštěny pouze příkazy, které způsobují odeslání dotazů nebo příkazů do databáze.</span><span class="sxs-lookup"><span data-stu-id="5094c-347">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="5094c-348">To zahrnuje `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` a `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="5094c-348">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="5094c-349">Neobsahuje příkazy, které pouze mění `IQueryable`, například `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="5094c-349">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="5094c-350">Kontext EF Core není bezpečný pro přístup z více vláken: Nepokoušejte se současně provést více operací.</span><span class="sxs-lookup"><span data-stu-id="5094c-350">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="5094c-351">Chcete-li využít výhody z hlediska výkonu asynchronního kódu, ověřte, že balíčky knihoven (například pro stránkování) používají async, pokud volají EF Core metody, které odesílají dotazy do databáze.</span><span class="sxs-lookup"><span data-stu-id="5094c-351">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="5094c-352">Další informace o asynchronním programování v rozhraní .NET naleznete v tématu [Async Overview](/dotnet/standard/async) a [Asynchronous Programming with Async a await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="5094c-352">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="5094c-353">Další kroky</span><span class="sxs-lookup"><span data-stu-id="5094c-353">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="5094c-354">Další kurz</span><span class="sxs-lookup"><span data-stu-id="5094c-354">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5094c-355">Ukázková webová aplikace společnosti Contoso University ukazuje, jak vytvořit aplikaci ASP.NET Core Razor Pages pomocí jádra Entity Framework (EF).</span><span class="sxs-lookup"><span data-stu-id="5094c-355">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="5094c-356">Ukázková aplikace je web pro fiktivní univerzitě společnosti Contoso.</span><span class="sxs-lookup"><span data-stu-id="5094c-356">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="5094c-357">Zahrnuje funkce, jako je například využití studenta, vytváření kurzu a přiřazení instruktora.</span><span class="sxs-lookup"><span data-stu-id="5094c-357">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="5094c-358">Tato stránka je první v sérii kurzů, které vysvětlují, jak vytvořit ukázkovou aplikaci Contoso University.</span><span class="sxs-lookup"><span data-stu-id="5094c-358">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="5094c-359">Stažení nebo zobrazení dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="5094c-359">Download or view the completed app.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="5094c-360">[Pokyny ke stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="5094c-360">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5094c-361">Požadované součásti</span><span class="sxs-lookup"><span data-stu-id="5094c-361">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5094c-362">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5094c-362">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5094c-363">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5094c-363">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="5094c-364">Znalost [Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="5094c-364">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="5094c-365">Noví Programátori by měli před spuštěním této série dokončit [Začínáme s Razor Pages](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="5094c-365">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="5094c-366">Odstraňování potíží</span><span class="sxs-lookup"><span data-stu-id="5094c-366">Troubleshooting</span></span>

<span data-ttu-id="5094c-367">Pokud narazíte na problém, který nelze vyřešit, můžete řešení obecně najít porovnáním kódu s [dokončeným projektem](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="5094c-367">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="5094c-368">Dobrým způsobem, jak získat pomoc, je odeslání otázky do [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) pro [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="5094c-368">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="5094c-369">Webová aplikace společnosti Contoso University</span><span class="sxs-lookup"><span data-stu-id="5094c-369">The Contoso University web app</span></span>

<span data-ttu-id="5094c-370">Aplikace sestavená v těchto kurzech je základním webem na univerzitě.</span><span class="sxs-lookup"><span data-stu-id="5094c-370">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="5094c-371">Uživatelé můžou zobrazit a aktualizovat informace o studentech, kurzech a instruktorech.</span><span class="sxs-lookup"><span data-stu-id="5094c-371">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="5094c-372">Tady je několik obrazovek vytvořených v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="5094c-372">Here are a few of the screens created in the tutorial.</span></span>

![Stránka indexu studentů](intro/_static/students-index.png)

![Stránka pro úpravy studentů](intro/_static/student-edit.png)

<span data-ttu-id="5094c-375">Styl uživatelského rozhraní tohoto webu je blízko toho, co vygenerovaly předdefinované šablony.</span><span class="sxs-lookup"><span data-stu-id="5094c-375">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="5094c-376">Tento kurz se zaměřuje na EF Core s Razor Pages, ne s uživatelským rozhraním.</span><span class="sxs-lookup"><span data-stu-id="5094c-376">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-razor-pages-web-app"></a><span data-ttu-id="5094c-377">Vytvoření webové aplikace ContosoUniversity Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5094c-377">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5094c-378">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5094c-378">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5094c-379">V nabídce **soubor** v aplikaci Visual Studio vyberte **Nový** **projekt**>.</span><span class="sxs-lookup"><span data-stu-id="5094c-379">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="5094c-380">Vytvořte novou ASP.NET Core webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5094c-380">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="5094c-381">Pojmenujte projekt **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="5094c-381">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="5094c-382">Je důležité pojmenovat projekt *ContosoUniversity* , aby se obory názvů shodovaly, když je kód zkopírován/vložen.</span><span class="sxs-lookup"><span data-stu-id="5094c-382">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="5094c-383">V rozevíracím seznamu vyberte **ASP.NET Core 2,1** a potom vyberte možnost **Webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="5094c-383">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="5094c-384">Obrázky předchozích kroků najdete v tématu [Vytvoření webové aplikace Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="5094c-384">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="5094c-385">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5094c-385">Run the app.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5094c-386">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5094c-386">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="5094c-387">Nastavení stylu webu</span><span class="sxs-lookup"><span data-stu-id="5094c-387">Set up the site style</span></span>

<span data-ttu-id="5094c-388">Několik změn nastaví nabídku webu, rozložení a domovskou stránku.</span><span class="sxs-lookup"><span data-stu-id="5094c-388">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="5094c-389">Update *Pages/Shared/_Layout. cshtml* s následujícími změnami:</span><span class="sxs-lookup"><span data-stu-id="5094c-389">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="5094c-390">Změňte všechny výskyty "ContosoUniversity" na "contoso University".</span><span class="sxs-lookup"><span data-stu-id="5094c-390">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="5094c-391">Existují tři výskyty.</span><span class="sxs-lookup"><span data-stu-id="5094c-391">There are three occurrences.</span></span>

* <span data-ttu-id="5094c-392">Přidejte položky nabídky pro **studenty**, **kurzy**, **instruktory**a **oddělení**a odstraňte položku nabídky **kontakt** .</span><span class="sxs-lookup"><span data-stu-id="5094c-392">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="5094c-393">Změny jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="5094c-393">The changes are highlighted.</span></span> <span data-ttu-id="5094c-394">(Všechny *značky se nezobrazí* .)</span><span class="sxs-lookup"><span data-stu-id="5094c-394">(All the markup is *not* displayed.)</span></span>

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="5094c-395">Na *stránce pages/index. cshtml*nahraďte obsah souboru následujícím kódem, který nahradí text o ASP.NET a MVC textem o této aplikaci:</span><span class="sxs-lookup"><span data-stu-id="5094c-395">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="5094c-396">Vytvoření datového modelu</span><span class="sxs-lookup"><span data-stu-id="5094c-396">Create the data model</span></span>

<span data-ttu-id="5094c-397">Vytvořte třídy entit pro aplikaci Contoso University.</span><span class="sxs-lookup"><span data-stu-id="5094c-397">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="5094c-398">Začněte následujícími třemi entitami:</span><span class="sxs-lookup"><span data-stu-id="5094c-398">Start with the following three entities:</span></span>

![Kurz – registrace – diagram datového modelu studenta](intro/_static/data-model-diagram.png)

<span data-ttu-id="5094c-400">Mezi entitami `Student` a `Enrollment` existuje vztah 1: n.</span><span class="sxs-lookup"><span data-stu-id="5094c-400">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="5094c-401">Mezi entitami `Course` a `Enrollment` existuje vztah 1: n.</span><span class="sxs-lookup"><span data-stu-id="5094c-401">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="5094c-402">Student se může zaregistrovat v jakémkoli počtu kurzů.</span><span class="sxs-lookup"><span data-stu-id="5094c-402">A student can enroll in any number of courses.</span></span> <span data-ttu-id="5094c-403">Kurz může mít zaregistrovaný libovolný počet studentů.</span><span class="sxs-lookup"><span data-stu-id="5094c-403">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="5094c-404">V následujících oddílech je vytvořena třída pro každou z těchto entit.</span><span class="sxs-lookup"><span data-stu-id="5094c-404">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="5094c-405">Entita studenta</span><span class="sxs-lookup"><span data-stu-id="5094c-405">The Student entity</span></span>

![Diagram entity studenta](intro/_static/student-entity.png)

<span data-ttu-id="5094c-407">Vytvořte složku *modelů* .</span><span class="sxs-lookup"><span data-stu-id="5094c-407">Create a *Models* folder.</span></span> <span data-ttu-id="5094c-408">Ve složce *modely* vytvořte soubor třídy s názvem *student.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5094c-408">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="5094c-409">Vlastnost `ID` se zobrazí jako sloupec primárního klíče tabulky databáze (DB), která odpovídá této třídě.</span><span class="sxs-lookup"><span data-stu-id="5094c-409">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="5094c-410">Ve výchozím nastavení EF Core interpretuje vlastnost s názvem `ID` nebo `classnameID` jako primární klíč.</span><span class="sxs-lookup"><span data-stu-id="5094c-410">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="5094c-411">V `classnameID` `classname` je název třídy.</span><span class="sxs-lookup"><span data-stu-id="5094c-411">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="5094c-412">Alternativní automaticky rozpoznaný primární klíč je `StudentID` v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="5094c-412">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="5094c-413">Vlastnost `Enrollments` je [navigační vlastnost](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="5094c-413">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="5094c-414">Vlastnosti navigace odkazují na další entity, které se vztahují k této entitě.</span><span class="sxs-lookup"><span data-stu-id="5094c-414">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="5094c-415">V tomto případě obsahuje vlastnost `Enrollments` `Student entity` všechny entity `Enrollment`, které se vztahují k danému `Student`.</span><span class="sxs-lookup"><span data-stu-id="5094c-415">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="5094c-416">Například pokud má řádek studenta v databázi dva související řádky registrace, navigační vlastnost `Enrollments` obsahuje tyto dvě entity `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="5094c-416">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="5094c-417">Související řádek `Enrollment` je řádek, který obsahuje hodnotu primárního klíče studenta ve sloupci `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="5094c-417">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="5094c-418">Předpokládejme například, že student s ID = 1 má dva řádky v tabulce `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="5094c-418">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="5094c-419">Tabulka `Enrollment` má dva řádky s `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="5094c-419">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="5094c-420">`StudentID` je cizí klíč v tabulce `Enrollment`, který určuje studenta v tabulce `Student`.</span><span class="sxs-lookup"><span data-stu-id="5094c-420">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="5094c-421">Pokud navigační vlastnost může obsahovat více entit, musí být vlastnost navigace typu seznam, například `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="5094c-421">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="5094c-422">lze zadat `ICollection<T>` nebo typ, jako je například `List<T>` nebo `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="5094c-422">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="5094c-423">Když se použije `ICollection<T>`, EF Core ve výchozím nastavení vytvoří kolekci `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="5094c-423">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="5094c-424">Navigační vlastnosti, které obsahují více entit, přicházejí ze vztahů m:n a 1: n.</span><span class="sxs-lookup"><span data-stu-id="5094c-424">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="5094c-425">Entita registrace</span><span class="sxs-lookup"><span data-stu-id="5094c-425">The Enrollment entity</span></span>

![Diagram entity registrace](intro/_static/enrollment-entity.png)

<span data-ttu-id="5094c-427">Ve složce *modely* vytvořte *Enrollment.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5094c-427">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="5094c-428">Vlastnost `EnrollmentID` je primární klíč.</span><span class="sxs-lookup"><span data-stu-id="5094c-428">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="5094c-429">Tato entita používá vzor `classnameID` místo `ID` jako entita `Student`.</span><span class="sxs-lookup"><span data-stu-id="5094c-429">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="5094c-430">Vývojáři obvykle volí jeden model a používají ho v rámci datového modelu.</span><span class="sxs-lookup"><span data-stu-id="5094c-430">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="5094c-431">V pozdějším kurzu se zobrazuje použití ID bez ClassName, které usnadňuje implementaci dědičnosti v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="5094c-431">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="5094c-432">Vlastnost `Grade` je `enum`.</span><span class="sxs-lookup"><span data-stu-id="5094c-432">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="5094c-433">Otazník po deklaraci typu `Grade` značí, že vlastnost `Grade` má hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="5094c-433">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="5094c-434">Hodnota, která je null, se liší od nulové třídy – hodnota null znamená, že se nejedná o známku nebo ještě není přiřazená.</span><span class="sxs-lookup"><span data-stu-id="5094c-434">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="5094c-435">Vlastnost `StudentID` je cizí klíč a odpovídající navigační vlastnost je `Student`.</span><span class="sxs-lookup"><span data-stu-id="5094c-435">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="5094c-436">Entita `Enrollment` je přidružená k jedné entitě `Student`, takže tato vlastnost obsahuje jednu entitu `Student`.</span><span class="sxs-lookup"><span data-stu-id="5094c-436">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="5094c-437">Entita `Student` se liší od navigační vlastnosti `Student.Enrollments`, která obsahuje více entit `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="5094c-437">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="5094c-438">Vlastnost `CourseID` je cizí klíč a odpovídající navigační vlastnost je `Course`.</span><span class="sxs-lookup"><span data-stu-id="5094c-438">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="5094c-439">Entita `Enrollment` je přidružená k jedné entitě `Course`.</span><span class="sxs-lookup"><span data-stu-id="5094c-439">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="5094c-440">EF Core interpretuje vlastnost jako cizí klíč, pokud má název `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="5094c-440">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="5094c-441">Například `StudentID` pro navigační vlastnost `Student`, protože primární klíč entity `Student` je `ID`.</span><span class="sxs-lookup"><span data-stu-id="5094c-441">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="5094c-442">Vlastnosti cizího klíče mohou být také pojmenovány `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="5094c-442">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="5094c-443">Například `CourseID`, protože primární klíč entity `Course` je `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="5094c-443">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="5094c-444">Entita kurzu</span><span class="sxs-lookup"><span data-stu-id="5094c-444">The Course entity</span></span>

![Diagram entity kurzu](intro/_static/course-entity.png)

<span data-ttu-id="5094c-446">Ve složce *modely* vytvořte *Course.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5094c-446">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="5094c-447">Vlastnost `Enrollments` je navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="5094c-447">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="5094c-448">Entita `Course` může souviset s libovolným počtem entit `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="5094c-448">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="5094c-449">Atribut `DatabaseGenerated` umožňuje aplikaci určit primární klíč, místo aby ho databáze vygenerovala.</span><span class="sxs-lookup"><span data-stu-id="5094c-449">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="5094c-450">Generování uživatelského rozhraní modelu studenta</span><span class="sxs-lookup"><span data-stu-id="5094c-450">Scaffold the student model</span></span>

<span data-ttu-id="5094c-451">V této části je model studenta vygenerovaný jako generátor.</span><span class="sxs-lookup"><span data-stu-id="5094c-451">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="5094c-452">To znamená, že nástroj pro generování uživatelského rozhraní vytváří stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model studenta.</span><span class="sxs-lookup"><span data-stu-id="5094c-452">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="5094c-453">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="5094c-453">Build the project.</span></span>
* <span data-ttu-id="5094c-454">Vytvořte složku *stránky/studenty* .</span><span class="sxs-lookup"><span data-stu-id="5094c-454">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5094c-455">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5094c-455">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5094c-456">V **Průzkumník řešení**klikněte pravým tlačítkem na složku *stránky/Students* > **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="5094c-456">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="5094c-457">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="5094c-457">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="5094c-458">Dokončete dialog **přidat Razor Pages pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="5094c-458">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="5094c-459">V rozevíracím seznamu **třída modelu** vyberte **student (ContosoUniversity. Models)** .</span><span class="sxs-lookup"><span data-stu-id="5094c-459">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="5094c-460">V řádku **Třída kontextu dat** vyberte znaménko **+** (plus) a změňte vygenerovaný název na **ContosoUniversity. Models. SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="5094c-460">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="5094c-461">V rozevíracím seznamu **Třída kontextu dat** vyberte **ContosoUniversity. Models. SchoolContext.**</span><span class="sxs-lookup"><span data-stu-id="5094c-461">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="5094c-462">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="5094c-462">Select **Add**.</span></span>

![Dialogové okno CRUD](intro/_static/s1.png)

<span data-ttu-id="5094c-464">Pokud máte problém s předchozím krokem, podívejte [se do části vygenerované modely filmů](xref:tutorials/razor-pages/model#scaffold-the-movie-model) .</span><span class="sxs-lookup"><span data-stu-id="5094c-464">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5094c-465">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5094c-465">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5094c-466">Spusťte následující příkazy pro generování uživatelského rozhraní modelu studenta.</span><span class="sxs-lookup"><span data-stu-id="5094c-466">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="5094c-467">Proces generování uživatelského rozhraní byl vytvořen a změnil následující soubory:</span><span class="sxs-lookup"><span data-stu-id="5094c-467">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="5094c-468">Vytvořené soubory</span><span class="sxs-lookup"><span data-stu-id="5094c-468">Files created</span></span>

* <span data-ttu-id="5094c-469">*Stránky/studenty* Vytvořit, odstranit, podrobnosti, upravit, index.</span><span class="sxs-lookup"><span data-stu-id="5094c-469">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="5094c-470">*Data/SchoolContext. cs*</span><span class="sxs-lookup"><span data-stu-id="5094c-470">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="5094c-471">Aktualizace souborů</span><span class="sxs-lookup"><span data-stu-id="5094c-471">File updates</span></span>

* <span data-ttu-id="5094c-472">*Startup.cs* : změny v tomto souboru jsou podrobně popsané v další části.</span><span class="sxs-lookup"><span data-stu-id="5094c-472">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="5094c-473">*appSettings. JSON* : Přidal se připojovací řetězec použitý k připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="5094c-473">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="5094c-474">Kontrola kontextu zaregistrovaného vkládáním závislostí</span><span class="sxs-lookup"><span data-stu-id="5094c-474">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="5094c-475">ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5094c-475">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5094c-476">Služby (například kontext EF Core DB) jsou registrovány pomocí injektáže závislosti při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="5094c-476">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="5094c-477">Komponenty, které vyžadují tyto služby (například Razor Pages), poskytují tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="5094c-477">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="5094c-478">Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="5094c-478">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="5094c-479">Nástroj pro generování uživatelského rozhraní automaticky vytvořil kontext databáze a zaregistroval ho pomocí kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="5094c-479">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="5094c-480">Projděte si metodu `ConfigureServices` v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5094c-480">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="5094c-481">Zvýrazněný řádek byl přidán do modulu generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="5094c-481">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="5094c-482">Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="5094c-482">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="5094c-483">Pro místní vývoj načítá [konfigurační systém ASP.NET Core](xref:fundamentals/configuration/index) připojovací řetězec ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="5094c-483">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="5094c-484">Aktualizace Main</span><span class="sxs-lookup"><span data-stu-id="5094c-484">Update main</span></span>

<span data-ttu-id="5094c-485">V *program.cs*upravte metodu `Main` a proveďte následující:</span><span class="sxs-lookup"><span data-stu-id="5094c-485">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="5094c-486">Získá instanci kontextu databáze z kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="5094c-486">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="5094c-487">Zavolejte [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="5094c-487">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="5094c-488">Vyřazení kontextu při dokončení metody `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="5094c-488">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="5094c-489">Následující kód ukazuje aktualizovaný soubor *program.cs* .</span><span class="sxs-lookup"><span data-stu-id="5094c-489">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="5094c-490">`EnsureCreated` zajistí, že databáze pro kontext existuje.</span><span class="sxs-lookup"><span data-stu-id="5094c-490">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="5094c-491">Pokud existuje, není provedena žádná akce.</span><span class="sxs-lookup"><span data-stu-id="5094c-491">If it exists, no action is taken.</span></span> <span data-ttu-id="5094c-492">Pokud neexistuje, vytvoří se databáze a všechny její schéma.</span><span class="sxs-lookup"><span data-stu-id="5094c-492">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="5094c-493">`EnsureCreated` nepoužívá migrace k vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="5094c-493">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="5094c-494">Databázi vytvořenou pomocí `EnsureCreated` nelze později aktualizovat pomocí migrace.</span><span class="sxs-lookup"><span data-stu-id="5094c-494">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="5094c-495">`EnsureCreated` se volá při spuštění aplikace, což umožňuje následující pracovní postup:</span><span class="sxs-lookup"><span data-stu-id="5094c-495">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="5094c-496">Odstraňte databázi.</span><span class="sxs-lookup"><span data-stu-id="5094c-496">Delete the DB.</span></span>
* <span data-ttu-id="5094c-497">Změňte schéma databáze (například přidejte pole `EmailAddress`).</span><span class="sxs-lookup"><span data-stu-id="5094c-497">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="5094c-498">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5094c-498">Run the app.</span></span>
* <span data-ttu-id="5094c-499">`EnsureCreated` vytvoří databázi se sloupcem @ no__t-1.</span><span class="sxs-lookup"><span data-stu-id="5094c-499">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="5094c-500">@no__t – 0 je praktické v brzké době vývoje, když se schéma rychle vyvíjí.</span><span class="sxs-lookup"><span data-stu-id="5094c-500">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="5094c-501">Později v kurzu se databáze odstraní a použijí se migrace.</span><span class="sxs-lookup"><span data-stu-id="5094c-501">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="5094c-502">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="5094c-502">Test the app</span></span>

<span data-ttu-id="5094c-503">Spusťte aplikaci a přijměte zásady souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="5094c-503">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="5094c-504">Tato aplikace neuchovává osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="5094c-504">This app doesn't keep personal information.</span></span> <span data-ttu-id="5094c-505">Informace o zásadách souborů cookie najdete v tématu [Podpora pro EU obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="5094c-505">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="5094c-506">Vyberte odkaz **Students** a pak **vytvořte nový**.</span><span class="sxs-lookup"><span data-stu-id="5094c-506">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="5094c-507">Otestujte odkazy upravit, podrobnosti a odstranit.</span><span class="sxs-lookup"><span data-stu-id="5094c-507">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="5094c-508">Projděte si kontext SchoolContext DB.</span><span class="sxs-lookup"><span data-stu-id="5094c-508">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="5094c-509">Hlavní třída, která koordinuje funkce EF Core pro daný datový model, je třída kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="5094c-509">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="5094c-510">Kontext dat je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="5094c-510">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="5094c-511">Kontext dat určuje, které entity jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="5094c-511">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="5094c-512">V tomto projektu je třída pojmenována `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="5094c-512">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="5094c-513">*SchoolContext.cs* aktualizujte pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="5094c-513">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="5094c-514">Zvýrazněný kód vytvoří vlastnost [negenerickými @ no__t-> 1TEntity](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro každou sadu entit.</span><span class="sxs-lookup"><span data-stu-id="5094c-514">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="5094c-515">V terminologii EF Core:</span><span class="sxs-lookup"><span data-stu-id="5094c-515">In EF Core terminology:</span></span>

* <span data-ttu-id="5094c-516">Sada entit obvykle odpovídá tabulce databáze.</span><span class="sxs-lookup"><span data-stu-id="5094c-516">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="5094c-517">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="5094c-517">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="5094c-518">`DbSet<Enrollment>` a `DbSet<Course>` by bylo možné vynechat.</span><span class="sxs-lookup"><span data-stu-id="5094c-518">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="5094c-519">EF Core je implicitně obsahuje, protože entita `Student` odkazuje na entitu `Enrollment` a entita `Enrollment` odkazuje na entitu `Course`.</span><span class="sxs-lookup"><span data-stu-id="5094c-519">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="5094c-520">V tomto kurzu ponechte v `SchoolContext` `DbSet<Enrollment>` a `DbSet<Course>`.</span><span class="sxs-lookup"><span data-stu-id="5094c-520">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="5094c-521">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="5094c-521">SQL Server Express LocalDB</span></span>

<span data-ttu-id="5094c-522">Připojovací řetězec Určuje [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="5094c-522">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="5094c-523">LocalDB je zjednodušená verze databázového stroje SQL Server Express a je určena pro vývoj aplikací, nikoli za použití v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="5094c-523">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="5094c-524">LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje složitá konfigurace.</span><span class="sxs-lookup"><span data-stu-id="5094c-524">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="5094c-525">Ve výchozím nastavení LocalDB vytvoří soubory *. mdf* DB v adresáři `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="5094c-525">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="5094c-526">Přidejte kód pro inicializaci databáze s testovacími daty</span><span class="sxs-lookup"><span data-stu-id="5094c-526">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="5094c-527">EF Core vytvoří prázdnou databázi.</span><span class="sxs-lookup"><span data-stu-id="5094c-527">EF Core creates an empty DB.</span></span> <span data-ttu-id="5094c-528">V této části je metoda `Initialize` zapsána k naplnění testovacích dat.</span><span class="sxs-lookup"><span data-stu-id="5094c-528">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="5094c-529">Ve složce *data* vytvořte nový soubor třídy s názvem *DbInitializer.cs* a přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="5094c-529">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="5094c-530">Poznámka: předchozí kód používá `Models` pro obor názvů (`namespace ContosoUniversity.Models`) místo `Data`.</span><span class="sxs-lookup"><span data-stu-id="5094c-530">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="5094c-531">`Models` je konzistentní s kódem generovaným modulem generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5094c-531">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="5094c-532">Další informace najdete v [tomto problému s vygenerováním uživatelského rozhraní GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="5094c-532">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="5094c-533">Kód zkontroluje, jestli v databázi existují studenti.</span><span class="sxs-lookup"><span data-stu-id="5094c-533">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="5094c-534">Pokud databáze neobsahuje žádné studenty, databáze se inicializuje s testovacími daty.</span><span class="sxs-lookup"><span data-stu-id="5094c-534">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="5094c-535">Načte testovací data do polí, nikoli kolekce `List<T>` pro optimalizaci výkonu.</span><span class="sxs-lookup"><span data-stu-id="5094c-535">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="5094c-536">Metoda `EnsureCreated` automaticky vytvoří databázi pro kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="5094c-536">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="5094c-537">Pokud databáze existuje, `EnsureCreated` vrátí beze změny databáze.</span><span class="sxs-lookup"><span data-stu-id="5094c-537">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="5094c-538">V *program.cs*upravte metodu `Main` pro volání `Initialize`:</span><span class="sxs-lookup"><span data-stu-id="5094c-538">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5094c-539">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5094c-539">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5094c-540">Zastavte aplikaci, pokud je spuštěná, a spusťte následující příkaz v **konzole správce balíčků** (PMC):</span><span class="sxs-lookup"><span data-stu-id="5094c-540">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5094c-541">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5094c-541">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5094c-542">Zastavte aplikaci, pokud je spuštěná, a odstraňte soubor *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="5094c-542">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="5094c-543">Zobrazení databáze</span><span class="sxs-lookup"><span data-stu-id="5094c-543">View the DB</span></span>

<span data-ttu-id="5094c-544">Název databáze je vygenerován z dříve zadaného názvu kontextu plus pomlčka a identifikátor GUID.</span><span class="sxs-lookup"><span data-stu-id="5094c-544">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="5094c-545">Proto bude název databáze "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="5094c-545">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="5094c-546">Identifikátor GUID bude pro každého uživatele odlišný.</span><span class="sxs-lookup"><span data-stu-id="5094c-546">The GUID will be different for each user.</span></span>
<span data-ttu-id="5094c-547">Otevřete **Průzkumník objektů systému SQL Server** (SSOX) z nabídky **Zobrazit** v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5094c-547">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="5094c-548">V SSOX klikněte na **(LocalDB) \MSSQLLocalDB > databáze > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="5094c-548">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="5094c-549">Rozbalte uzel **tabulky** .</span><span class="sxs-lookup"><span data-stu-id="5094c-549">Expand the **Tables** node.</span></span>

<span data-ttu-id="5094c-550">Klikněte pravým tlačítkem myši na tabulku **student** a kliknutím na **Zobrazit data** Zobrazte vytvořené sloupce a řádky vložené do tabulky.</span><span class="sxs-lookup"><span data-stu-id="5094c-550">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="5094c-551">Asynchronní kód</span><span class="sxs-lookup"><span data-stu-id="5094c-551">Asynchronous code</span></span>

<span data-ttu-id="5094c-552">Asynchronní programování je výchozí režim pro ASP.NET Core a EF Core.</span><span class="sxs-lookup"><span data-stu-id="5094c-552">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="5094c-553">Na webovém serveru je k dispozici omezený počet vláken a v situacích vysokého zatížení se mohou používat všechna dostupná vlákna.</span><span class="sxs-lookup"><span data-stu-id="5094c-553">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="5094c-554">Pokud k tomu dojde, server nemůže zpracovat nové požadavky, dokud nebudou vlákna uvolněna.</span><span class="sxs-lookup"><span data-stu-id="5094c-554">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="5094c-555">Pomocí synchronního kódu může být mnoho vláken svázáno s tím, že ve skutečnosti neprovádí žádnou práci, protože čeká na dokončení vstupně-výstupních operací.</span><span class="sxs-lookup"><span data-stu-id="5094c-555">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="5094c-556">V případě asynchronního kódu, když proces čeká na dokončení vstupně-výstupních operací, je jeho vlákno uvolněno na server, který bude použit pro zpracování jiných požadavků.</span><span class="sxs-lookup"><span data-stu-id="5094c-556">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="5094c-557">Výsledkem je, že asynchronní kód umožňuje efektivnější použití prostředků serveru a server je povolen pro zpracování většího objemu dat bez prodlev.</span><span class="sxs-lookup"><span data-stu-id="5094c-557">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="5094c-558">Asynchronní kód zavádí v době běhu malé množství režie.</span><span class="sxs-lookup"><span data-stu-id="5094c-558">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="5094c-559">V situacích s nízkým provozem je dosaženo zanedbatelného výkonu, zatímco v situacích vysokého provozu je potenciální zlepšení výkonu značné.</span><span class="sxs-lookup"><span data-stu-id="5094c-559">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="5094c-560">V následujícím kódu, klíčové slovo [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` návratová hodnota, `await` a metoda `ToListAsync` provede asynchronní spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="5094c-560">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="5094c-561">Klíčové slovo `async` instruuje kompilátor na:</span><span class="sxs-lookup"><span data-stu-id="5094c-561">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="5094c-562">Vygenerujte zpětná volání pro části těla metody.</span><span class="sxs-lookup"><span data-stu-id="5094c-562">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="5094c-563">Automaticky vytvořte vrácený objekt [úkolu](/dotnet/api/system.threading.tasks.task) .</span><span class="sxs-lookup"><span data-stu-id="5094c-563">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="5094c-564">Další informace najdete v tématu [návratový typ úlohy](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="5094c-564">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="5094c-565">Implicitní návratový typ `Task` představuje probíhající práci.</span><span class="sxs-lookup"><span data-stu-id="5094c-565">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="5094c-566">Klíčové slovo `await` způsobí, že kompilátor rozdělí metodu do dvou částí.</span><span class="sxs-lookup"><span data-stu-id="5094c-566">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="5094c-567">První část končí asynchronně spuštěnou operací.</span><span class="sxs-lookup"><span data-stu-id="5094c-567">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="5094c-568">Druhá část je vložena do metody zpětného volání, která je volána po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="5094c-568">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="5094c-569">`ToListAsync` je asynchronní verze metody rozšíření `ToList`.</span><span class="sxs-lookup"><span data-stu-id="5094c-569">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="5094c-570">Některé věci, které je potřeba znát při psaní asynchronního kódu, který používá EF Core:</span><span class="sxs-lookup"><span data-stu-id="5094c-570">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="5094c-571">Asynchronně jsou spouštěny pouze příkazy, které způsobují odeslání dotazů nebo příkazů do databáze.</span><span class="sxs-lookup"><span data-stu-id="5094c-571">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="5094c-572">To zahrnuje `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` a `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="5094c-572">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="5094c-573">Neobsahuje příkazy, které pouze mění `IQueryable`, například `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="5094c-573">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="5094c-574">Kontext EF Core není bezpečný pro přístup z více vláken: Nepokoušejte se současně provést více operací.</span><span class="sxs-lookup"><span data-stu-id="5094c-574">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="5094c-575">Chcete-li využít výhody z hlediska výkonu asynchronního kódu, ověřte, že balíčky knihoven (například pro stránkování) používají async, pokud volají EF Core metody, které odesílají dotazy do databáze.</span><span class="sxs-lookup"><span data-stu-id="5094c-575">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="5094c-576">Další informace o asynchronním programování v rozhraní .NET naleznete v tématu [Async Overview](/dotnet/standard/async) a [Asynchronous Programming with Async a await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="5094c-576">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="5094c-577">V dalším kurzu se přezkoumávají základní operace CRUD (vytváření, čtení, aktualizace, odstranění).</span><span class="sxs-lookup"><span data-stu-id="5094c-577">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="5094c-578">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="5094c-578">Additional resources</span></span>

* [<span data-ttu-id="5094c-579">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="5094c-579">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="5094c-580">Generace</span><span class="sxs-lookup"><span data-stu-id="5094c-580">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
