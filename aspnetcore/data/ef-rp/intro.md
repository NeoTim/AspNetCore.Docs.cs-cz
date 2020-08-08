---
title: RazorStránky s Entity Framework Core v ASP.NET Core – kurz 1 z 8
author: rick-anderson
description: Ukazuje, jak vytvořit Razor aplikaci Pages pomocí Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
no-loc:
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
ms.openlocfilehash: cd6624d107fb19da92a7e58a747cc85e876a6ba4
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018634"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="9bd43-103">RazorStránky s Entity Framework Core v ASP.NET Core – kurz 1 z 8</span><span class="sxs-lookup"><span data-stu-id="9bd43-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="9bd43-104">[Dykstra](https://github.com/tdykstra) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9bd43-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9bd43-105">Toto je první v sérii kurzů, které ukazují, jak používat Entity Framework (EF) jádro v aplikaci [ASP.NET Core Razor Pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="9bd43-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="9bd43-106">Kurzy vytvářejí web pro fiktivní univerzitě společnosti Contoso.</span><span class="sxs-lookup"><span data-stu-id="9bd43-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="9bd43-107">Tato lokalita obsahuje funkce, jako je například využití studenta, vytváření kurzů a přiřazení instruktorů.</span><span class="sxs-lookup"><span data-stu-id="9bd43-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="9bd43-108">Kurz používá první přístup ke kódu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="9bd43-109">Informace o tomto kurzu s použitím databáze prvního přístupu najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="9bd43-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="9bd43-110">Stažení nebo zobrazení dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="9bd43-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="9bd43-111">[Pokyny ke stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="9bd43-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9bd43-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="9bd43-112">Prerequisites</span></span>

* <span data-ttu-id="9bd43-113">Pokud se Razor stránkami teprve začínáte, Projděte si řadu kurzů [Začínáme se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start) , než začnete s tímto.</span><span class="sxs-lookup"><span data-stu-id="9bd43-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9bd43-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd43-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="9bd43-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9bd43-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="9bd43-116">Databázové moduly</span><span class="sxs-lookup"><span data-stu-id="9bd43-116">Database engines</span></span>

<span data-ttu-id="9bd43-117">Pokyny pro Visual Studio používají [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), verzi SQL Server Express, která se spouští jenom v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="9bd43-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="9bd43-118">Pokyny pro [Visual Studio Code používají nástroj](https://www.sqlite.org/)pro databázový stroj pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="9bd43-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="9bd43-119">Pokud se rozhodnete použít SQLite, Stáhněte a nainstalujte nástroj třetí strany pro správu a zobrazení databáze SQLite, jako je například [prohlížeč databáze pro SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="9bd43-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="9bd43-120">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="9bd43-120">Troubleshooting</span></span>

<span data-ttu-id="9bd43-121">Pokud narazíte na problém, který nemůžete vyřešit, porovnejte kód s [dokončeným projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="9bd43-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="9bd43-122">Dobrý způsob, jak získat pomoc, je odeslání otázky do StackOverflow.com s použitím [značky ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [značky EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="9bd43-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="9bd43-123">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="9bd43-123">The sample app</span></span>

<span data-ttu-id="9bd43-124">Aplikace sestavená v těchto kurzech je základním webem na univerzitě.</span><span class="sxs-lookup"><span data-stu-id="9bd43-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="9bd43-125">Uživatelé můžou zobrazit a aktualizovat informace o studentech, kurzech a instruktorech.</span><span class="sxs-lookup"><span data-stu-id="9bd43-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="9bd43-126">Tady je několik obrazovek vytvořených v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-126">Here are a few of the screens created in the tutorial.</span></span>

![Stránka indexu studentů](intro/_static/students-index30.png)

![Stránka pro úpravy studentů](intro/_static/student-edit30.png)

<span data-ttu-id="9bd43-129">Styl uživatelského rozhraní tohoto webu je založen na předdefinovaných šablonách projektů.</span><span class="sxs-lookup"><span data-stu-id="9bd43-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="9bd43-130">V tomto kurzu se naučíte, jak používat EF Core, nikoli způsob přizpůsobení uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="9bd43-130">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="9bd43-131">Pomocí odkazu v horní části stránky Získejte zdrojový kód dokončeného projektu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-131">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="9bd43-132">Složka *cu30* obsahuje kód pro verzi kurzu ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="9bd43-132">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="9bd43-133">Soubory, které reflektují stav kódu pro kurzy 1-7, najdete ve složce *cu30snapshots* .</span><span class="sxs-lookup"><span data-stu-id="9bd43-133">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9bd43-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd43-134">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9bd43-135">Spuštění aplikace po stažení dokončeného projektu:</span><span class="sxs-lookup"><span data-stu-id="9bd43-135">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="9bd43-136">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="9bd43-136">Build the project.</span></span>
* <span data-ttu-id="9bd43-137">V konzole správce balíčků (PMC) spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="9bd43-137">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="9bd43-138">Spusťte projekt k osazení databáze.</span><span class="sxs-lookup"><span data-stu-id="9bd43-138">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9bd43-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9bd43-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9bd43-140">Spuštění aplikace po stažení dokončeného projektu:</span><span class="sxs-lookup"><span data-stu-id="9bd43-140">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="9bd43-141">Odstraňte *ContosoUniversity. csproj*a přejmenujte *ContosoUniversitySQLite. csproj* na *ContosoUniversity. csproj*.</span><span class="sxs-lookup"><span data-stu-id="9bd43-141">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="9bd43-142">Odstraňte *Startup.cs*a přejmenujte *StartupSQLite.cs* na *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="9bd43-142">Delete *Startup.cs*, and rename *StartupSQLite.cs* to *Startup.cs*.</span></span>
* <span data-ttu-id="9bd43-143">Odstraňte *appSettings.jsna*a přejmenujte *appSettingsSQLite.jsna* na *appSettings.jszapnuto*.</span><span class="sxs-lookup"><span data-stu-id="9bd43-143">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="9bd43-144">Odstraňte složku *migrace* a přejmenujte *MigrationsSQL* na *migrace*.</span><span class="sxs-lookup"><span data-stu-id="9bd43-144">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="9bd43-145">Proveďte globální hledání `#if SQLiteVersion` a odeberte `#if SQLiteVersion` příkaz a přidružený `#endif` příkaz.</span><span class="sxs-lookup"><span data-stu-id="9bd43-145">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="9bd43-146">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="9bd43-146">Build the project.</span></span>
* <span data-ttu-id="9bd43-147">Na příkazovém řádku ve složce projektu spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="9bd43-147">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* <span data-ttu-id="9bd43-148">V nástroji SQLite spusťte následující příkaz SQL:</span><span class="sxs-lookup"><span data-stu-id="9bd43-148">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="9bd43-149">Spusťte projekt k osazení databáze.</span><span class="sxs-lookup"><span data-stu-id="9bd43-149">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="9bd43-150">Vytvoření projektu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="9bd43-150">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9bd43-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd43-151">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9bd43-152">V nabídce **soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="9bd43-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="9bd43-153">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="9bd43-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="9bd43-154">Pojmenujte projekt *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="9bd43-154">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="9bd43-155">Je důležité použít tento přesný název, včetně velkých a malých písmen, aby se obory názvů shodovaly při zkopírování a vložení kódu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-155">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="9bd43-156">V rozevíracích seznamech vyberte **.NET Core** a **ASP.NET Core 3,0** a potom vyberte **Webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="9bd43-156">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9bd43-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9bd43-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9bd43-158">V terminálu přejděte do složky, ve které by měla být vytvořena složka projektu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-158">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="9bd43-159">Spuštěním následujících příkazů vytvořte Razor projekt stránky a `cd` do nové složky projektu:</span><span class="sxs-lookup"><span data-stu-id="9bd43-159">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="9bd43-160">Nastavení stylu webu</span><span class="sxs-lookup"><span data-stu-id="9bd43-160">Set up the site style</span></span>

<span data-ttu-id="9bd43-161">Pomocí aktualizace *stránek/Shared/_Layout. cshtml*nastavte záhlaví webu, zápatí a nabídku. cshtml:</span><span class="sxs-lookup"><span data-stu-id="9bd43-161">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="9bd43-162">Změňte všechny výskyty "ContosoUniversity" na "contoso University".</span><span class="sxs-lookup"><span data-stu-id="9bd43-162">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="9bd43-163">Existují tři výskyty.</span><span class="sxs-lookup"><span data-stu-id="9bd43-163">There are three occurrences.</span></span>

* <span data-ttu-id="9bd43-164">Odstraňte položky nabídky **Domů** a **Ochrana osobních údajů** a přidejte záznamy **o o** **studentech**, **kurzech**, **instruktorech**a **odděleních**.</span><span class="sxs-lookup"><span data-stu-id="9bd43-164">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="9bd43-165">Změny jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="9bd43-165">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="9bd43-166">Na *stránce pages/index. cshtml*nahraďte obsah souboru následujícím kódem, který nahradí text o ASP.NET Core textem této aplikace:</span><span class="sxs-lookup"><span data-stu-id="9bd43-166">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="9bd43-167">Spusťte aplikaci, abyste ověřili, že se zobrazí domovská stránka.</span><span class="sxs-lookup"><span data-stu-id="9bd43-167">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="9bd43-168">Datový model</span><span class="sxs-lookup"><span data-stu-id="9bd43-168">The data model</span></span>

<span data-ttu-id="9bd43-169">V následujících částech se vytvoří datový model:</span><span class="sxs-lookup"><span data-stu-id="9bd43-169">The following sections create a data model:</span></span>

![Kurz – registrace – diagram datového modelu studenta](intro/_static/data-model-diagram.png)

<span data-ttu-id="9bd43-171">Student se může zaregistrovat v jakémkoli počtu kurzů a kurz může mít zaregistrovaný libovolný počet studentů.</span><span class="sxs-lookup"><span data-stu-id="9bd43-171">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="9bd43-172">Entita studenta</span><span class="sxs-lookup"><span data-stu-id="9bd43-172">The Student entity</span></span>

![Diagram entity studenta](intro/_static/student-entity.png)

* <span data-ttu-id="9bd43-174">Vytvořte složku *modely* ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-174">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="9bd43-175">Vytvořte *modely/studenta. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="9bd43-175">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="9bd43-176">`ID`Vlastnost se zobrazí jako sloupec primárního klíče tabulky databáze, která odpovídá této třídě.</span><span class="sxs-lookup"><span data-stu-id="9bd43-176">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="9bd43-177">Ve výchozím nastavení EF Core interpretuje vlastnost s názvem `ID` nebo `classnameID` jako primární klíč.</span><span class="sxs-lookup"><span data-stu-id="9bd43-177">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="9bd43-178">Proto je alternativní automaticky rozpoznaný název pro `Student` primární klíč třídy `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-178">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="9bd43-179">Další informace najdete v tématu [EF Core-Keys](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="9bd43-179">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="9bd43-180">`Enrollments`Vlastnost je [navigační vlastnost](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="9bd43-180">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="9bd43-181">Navigační vlastnosti obsahují další entity, které se vztahují k této entitě.</span><span class="sxs-lookup"><span data-stu-id="9bd43-181">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="9bd43-182">V takovém případě `Enrollments` vlastnost `Student` entity obsahuje všechny `Enrollment` entity, které se vztahují k danému studentovi.</span><span class="sxs-lookup"><span data-stu-id="9bd43-182">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="9bd43-183">Například pokud má řádek studenta v databázi dva související řádky registrace, `Enrollments` navigační vlastnost obsahuje tyto dvě entity registrace.</span><span class="sxs-lookup"><span data-stu-id="9bd43-183">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="9bd43-184">V databázi se řádek registrace vztahuje k řádku studenta, pokud jeho sloupec StudentID obsahuje hodnotu ID studenta.</span><span class="sxs-lookup"><span data-stu-id="9bd43-184">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="9bd43-185">Předpokládejme například, že řádek studenta má ID = 1.</span><span class="sxs-lookup"><span data-stu-id="9bd43-185">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="9bd43-186">Související řádky registrace budou mít StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="9bd43-186">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="9bd43-187">StudentID je *cizí klíč* v tabulce zápisu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-187">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="9bd43-188">`Enrollments`Vlastnost je definována tak, `ICollection<Enrollment>` že může existovat více souvisejících entit zápisu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-188">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="9bd43-189">Můžete použít jiné typy kolekce, například `List<Enrollment>` nebo `HashSet<Enrollment>` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-189">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="9bd43-190">Při `ICollection<Enrollment>` použití EF Core vytvoří `HashSet<Enrollment>` ve výchozím nastavení kolekci.</span><span class="sxs-lookup"><span data-stu-id="9bd43-190">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="9bd43-191">Entita registrace</span><span class="sxs-lookup"><span data-stu-id="9bd43-191">The Enrollment entity</span></span>

![Diagram entity registrace](intro/_static/enrollment-entity.png)

<span data-ttu-id="9bd43-193">Vytvořte *modely/registrace. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="9bd43-193">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="9bd43-194">`EnrollmentID`Vlastnost je primární klíč. Tato entita používá `classnameID` vzor namísto `ID` samotného.</span><span class="sxs-lookup"><span data-stu-id="9bd43-194">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="9bd43-195">Pro model produkčních dat vyberte jeden vzor a používejte ho konzistentně.</span><span class="sxs-lookup"><span data-stu-id="9bd43-195">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="9bd43-196">Tento kurz používá pouze k ilustraci toho, jak funguje.</span><span class="sxs-lookup"><span data-stu-id="9bd43-196">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="9bd43-197">Použití `ID` bez toho usnadňuje `classname` implementaci některých druhů změn datového modelu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-197">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="9bd43-198">`Grade`Vlastnost je `enum` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-198">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="9bd43-199">Otazník po `Grade` deklaraci typu označuje, že vlastnost může `Grade` [mít hodnotu null](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="9bd43-199">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="9bd43-200">Třídu, která má hodnotu null, se liší od nulové třídy &mdash; null znamená, že známka není známa nebo ještě nebyla přiřazena.</span><span class="sxs-lookup"><span data-stu-id="9bd43-200">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="9bd43-201">`StudentID`Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Student` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-201">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="9bd43-202">`Enrollment`Entita je přidružená k jedné `Student` entitě, takže vlastnost obsahuje jednu `Student` entitu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-202">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="9bd43-203">`CourseID`Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Course` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-203">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="9bd43-204">`Enrollment`Entita je přidružená k jedné `Course` entitě.</span><span class="sxs-lookup"><span data-stu-id="9bd43-204">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="9bd43-205">EF Core interpretuje vlastnost jako cizí klíč, pokud má název `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-205">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="9bd43-206">Například `StudentID` je cizí klíč pro `Student` navigační vlastnost, protože `Student` primární klíč entity je `ID` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-206">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="9bd43-207">Lze také pojmenovat vlastnosti cizího klíče `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-207">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="9bd43-208">Například `CourseID` vzhledem k tomu, že `Course` primární klíč entity je `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-208">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="9bd43-209">Entita kurzu</span><span class="sxs-lookup"><span data-stu-id="9bd43-209">The Course entity</span></span>

![Diagram entity kurzu](intro/_static/course-entity.png)

<span data-ttu-id="9bd43-211">Vytvořte *modely/Course. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="9bd43-211">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="9bd43-212">`Enrollments`Vlastnost je navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="9bd43-212">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="9bd43-213">`Course`Entita může souviset s libovolným počtem `Enrollment` entit.</span><span class="sxs-lookup"><span data-stu-id="9bd43-213">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="9bd43-214">`DatabaseGenerated`Atribut umožňuje aplikaci určit primární klíč místo toho, aby ho databáze vygenerovala.</span><span class="sxs-lookup"><span data-stu-id="9bd43-214">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="9bd43-215">Sestavte projekt, aby se ověřilo, že nejsou k dispozici žádné chyby kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="9bd43-215">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="9bd43-216">Stránky studenta pro generování uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="9bd43-216">Scaffold Student pages</span></span>

<span data-ttu-id="9bd43-217">V této části použijete nástroj ASP.NET Core pro generování uživatelského rozhraní k vygenerování:</span><span class="sxs-lookup"><span data-stu-id="9bd43-217">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="9bd43-218">Třída *kontextu* EF Core.</span><span class="sxs-lookup"><span data-stu-id="9bd43-218">An EF Core *context* class.</span></span> <span data-ttu-id="9bd43-219">Kontext je hlavní třída, která koordinuje funkce Entity Framework pro daný datový model.</span><span class="sxs-lookup"><span data-stu-id="9bd43-219">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="9bd43-220">Je odvozen z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="9bd43-220">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="9bd43-221">Razorstránky, které zpracovávají operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro danou `Student` entitu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-221">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9bd43-222">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd43-222">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9bd43-223">Vytvořte složku *Students* ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="9bd43-223">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="9bd43-224">V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *Pages/Students* a vyberte **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="9bd43-224">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="9bd43-225">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte \*\* Razor stránky pomocí Entity Framework (CRUD)\*\* > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="9bd43-225">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="9bd43-226">V dialogovém okně **Přidat Razor stránky pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="9bd43-226">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="9bd43-227">V rozevíracím seznamu **třída modelu** vyberte **student (ContosoUniversity. Models)**.</span><span class="sxs-lookup"><span data-stu-id="9bd43-227">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="9bd43-228">V řádku **třídy kontextu dat** vyberte **+** znaménko (plus).</span><span class="sxs-lookup"><span data-stu-id="9bd43-228">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="9bd43-229">Změňte název kontextu dat z *ContosoUniversity. Models. ContosoUniversityContext* na *ContosoUniversity. data. SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="9bd43-229">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="9bd43-230">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="9bd43-230">Select **Add**.</span></span>

<span data-ttu-id="9bd43-231">Automaticky se nainstalují tyto balíčky:</span><span class="sxs-lookup"><span data-stu-id="9bd43-231">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="9bd43-232">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9bd43-232">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9bd43-233">Spuštěním následujících příkazů .NET Core CLI nainstalujte požadované balíčky NuGet:</span><span class="sxs-lookup"><span data-stu-id="9bd43-233">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="9bd43-234">Pro generování uživatelského rozhraní je vyžadován balíček Microsoft. VisualStudio. Web. strategii. Design.</span><span class="sxs-lookup"><span data-stu-id="9bd43-234">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="9bd43-235">I když aplikace nebude používat SQL Server, nástroj pro generování uživatelského rozhraní potřebuje balíček SQL Server.</span><span class="sxs-lookup"><span data-stu-id="9bd43-235">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="9bd43-236">Vytvořte složku *stránky/studenty* .</span><span class="sxs-lookup"><span data-stu-id="9bd43-236">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="9bd43-237">Spusťte následující příkaz pro instalaci nástroje pro [generování uživatelského rozhraní ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="9bd43-237">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="9bd43-238">Spusťte následující příkaz pro generování uživatelského rozhraní stránek studenta.</span><span class="sxs-lookup"><span data-stu-id="9bd43-238">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="9bd43-239">**V systému Windows**</span><span class="sxs-lookup"><span data-stu-id="9bd43-239">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="9bd43-240">**V systému macOS nebo Linux**</span><span class="sxs-lookup"><span data-stu-id="9bd43-240">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="9bd43-241">Pokud máte problém s předchozím krokem, sestavte projekt a opakujte krok generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="9bd43-241">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="9bd43-242">Proces generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="9bd43-242">The scaffolding process:</span></span>

* <span data-ttu-id="9bd43-243">Vytvoří Razor stránky ve složce *Pages/Students* :</span><span class="sxs-lookup"><span data-stu-id="9bd43-243">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="9bd43-244">*Vytvoření. cshtml* a *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="9bd43-244">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="9bd43-245">*Odstranění. cshtml* a *DELETE.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="9bd43-245">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="9bd43-246">*Podrobnosti. cshtml* a *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="9bd43-246">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="9bd43-247">*Upravit. cshtml* a *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="9bd43-247">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="9bd43-248">*Index. cshtml* a *index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="9bd43-248">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="9bd43-249">Vytvoří *data/SchoolContext. cs*.</span><span class="sxs-lookup"><span data-stu-id="9bd43-249">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="9bd43-250">Přidá kontext do injektáže závislosti v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="9bd43-250">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="9bd43-251">Přidá připojovací řetězec databáze, do kterého se má *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="9bd43-251">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="9bd43-252">Připojovací řetězec databáze</span><span class="sxs-lookup"><span data-stu-id="9bd43-252">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9bd43-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd43-253">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9bd43-254">Připojovací řetězec Určuje [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="9bd43-254">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="9bd43-255">LocalDB je zjednodušená verze databázového stroje SQL Server Express a je určena pro vývoj aplikací, nikoli za použití v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="9bd43-255">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="9bd43-256">Ve výchozím nastavení LocalDB vytvoří soubory *. mdf* v `C:/Users/<user>` adresáři.</span><span class="sxs-lookup"><span data-stu-id="9bd43-256">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9bd43-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9bd43-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9bd43-258">Změňte připojovací řetězec tak, aby odkazoval na soubor databáze SQLite s názvem *cu. DB*:</span><span class="sxs-lookup"><span data-stu-id="9bd43-258">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="9bd43-259">Aktualizuje třídu kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="9bd43-259">Update the database context class</span></span>

<span data-ttu-id="9bd43-260">Hlavní třída, která koordinuje funkce EF Core pro daný datový model, je třída kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="9bd43-260">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="9bd43-261">Kontext je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="9bd43-261">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="9bd43-262">Kontext určuje, které entity jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-262">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="9bd43-263">V tomto projektu je třída pojmenována `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-263">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="9bd43-264">*SchoolContext.cs* aktualizujte pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="9bd43-264">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="9bd43-265">Zvýrazněný kód vytvoří vlastnost [negenerickými \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro každou sadu entit.</span><span class="sxs-lookup"><span data-stu-id="9bd43-265">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="9bd43-266">V terminologii EF Core:</span><span class="sxs-lookup"><span data-stu-id="9bd43-266">In EF Core terminology:</span></span>

* <span data-ttu-id="9bd43-267">Sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="9bd43-267">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="9bd43-268">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="9bd43-268">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="9bd43-269">Vzhledem k tomu, že sada entit obsahuje více entit, musí mít vlastnosti Negenerickými plurální názvy.</span><span class="sxs-lookup"><span data-stu-id="9bd43-269">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="9bd43-270">Vzhledem k tomu, že nástroj pro generování uživatelského rozhraní vytvořil `Student` negenerickými, tento krok změny změní na plural `Students` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-270">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="9bd43-271">Chcete-li Razor , aby kód stránky odpovídal novému negenerickými názvu, proveďte globální změnu v celém projektu `_context.Student` na `_context.Students` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-271">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="9bd43-272">K dispozici je 8 výskytů.</span><span class="sxs-lookup"><span data-stu-id="9bd43-272">There are 8 occurrences.</span></span>

<span data-ttu-id="9bd43-273">Sestavte projekt, aby se ověřilo, že nedošlo k chybám kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="9bd43-273">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="9bd43-274">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="9bd43-274">Startup.cs</span></span>

<span data-ttu-id="9bd43-275">ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9bd43-275">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="9bd43-276">Služby (například kontext databáze EF Core) jsou registrovány pomocí injektáže závislosti při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="9bd43-276">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="9bd43-277">Komponenty, které vyžadují tyto služby (například Razor stránky), poskytují tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="9bd43-277">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="9bd43-278">Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-278">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="9bd43-279">Nástroj pro generování uživatelského rozhraní automaticky zaregistroval třídu kontextu pomocí kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="9bd43-279">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9bd43-280">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd43-280">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9bd43-281">V `ConfigureServices` byly zvýrazněné řádky přidány pomocí generátoru:</span><span class="sxs-lookup"><span data-stu-id="9bd43-281">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="9bd43-282">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9bd43-282">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9bd43-283">V nástroji se ujistěte, `ConfigureServices` že kód přidaný voláním uživatelského rozhraní `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-283">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="9bd43-284">Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="9bd43-284">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="9bd43-285">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) přečte připojovací řetězec z *appsettings.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="9bd43-285">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="9bd43-286">Vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="9bd43-286">Create the database</span></span>

<span data-ttu-id="9bd43-287">Aktualizujte *program.cs* , aby se vytvořila databáze, pokud neexistuje:</span><span class="sxs-lookup"><span data-stu-id="9bd43-287">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="9bd43-288">Metoda [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) neprovede žádnou akci, pokud existuje databáze pro kontext.</span><span class="sxs-lookup"><span data-stu-id="9bd43-288">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="9bd43-289">Pokud žádná databáze neexistuje, vytvoří databázi a schéma.</span><span class="sxs-lookup"><span data-stu-id="9bd43-289">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="9bd43-290">`EnsureCreated`povolí následující pracovní postup pro zpracování změn datového modelu:</span><span class="sxs-lookup"><span data-stu-id="9bd43-290">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="9bd43-291">Odstraňte databázi.</span><span class="sxs-lookup"><span data-stu-id="9bd43-291">Delete the database.</span></span> <span data-ttu-id="9bd43-292">Všechna existující data budou ztracena.</span><span class="sxs-lookup"><span data-stu-id="9bd43-292">Any existing data is lost.</span></span>
* <span data-ttu-id="9bd43-293">Změňte datový model.</span><span class="sxs-lookup"><span data-stu-id="9bd43-293">Change the data model.</span></span> <span data-ttu-id="9bd43-294">Například přidejte `EmailAddress` pole.</span><span class="sxs-lookup"><span data-stu-id="9bd43-294">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="9bd43-295">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9bd43-295">Run the app.</span></span>
* <span data-ttu-id="9bd43-296">`EnsureCreated`vytvoří databázi s novým schématem.</span><span class="sxs-lookup"><span data-stu-id="9bd43-296">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="9bd43-297">Tento pracovní postup funguje dobře v rané fázi vývoje, když se schéma rychle vyvíjí, pokud nepotřebujete zachovat data.</span><span class="sxs-lookup"><span data-stu-id="9bd43-297">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="9bd43-298">Tato situace se liší v případě, že data, která byla zadána do databáze, musí být zachována.</span><span class="sxs-lookup"><span data-stu-id="9bd43-298">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="9bd43-299">V takovém případě použijte migrace.</span><span class="sxs-lookup"><span data-stu-id="9bd43-299">When that is the case, use migrations.</span></span>

<span data-ttu-id="9bd43-300">Později v rámci série kurzů odstraníte databázi, kterou vytvořil, `EnsureCreated` a místo toho použijete migrace.</span><span class="sxs-lookup"><span data-stu-id="9bd43-300">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="9bd43-301">Databázi vytvořenou nástrojem `EnsureCreated` nelze aktualizovat pomocí migrací.</span><span class="sxs-lookup"><span data-stu-id="9bd43-301">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="9bd43-302">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="9bd43-302">Test the app</span></span>

* <span data-ttu-id="9bd43-303">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9bd43-303">Run the app.</span></span>
* <span data-ttu-id="9bd43-304">Vyberte odkaz **Students** a pak **vytvořte nový**.</span><span class="sxs-lookup"><span data-stu-id="9bd43-304">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="9bd43-305">Otestujte odkazy Upravit, Podrobnosti a Odstranit.</span><span class="sxs-lookup"><span data-stu-id="9bd43-305">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="9bd43-306">Dosazení databáze</span><span class="sxs-lookup"><span data-stu-id="9bd43-306">Seed the database</span></span>

<span data-ttu-id="9bd43-307">`EnsureCreated`Metoda vytvoří prázdnou databázi.</span><span class="sxs-lookup"><span data-stu-id="9bd43-307">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="9bd43-308">V této části se přidá kód, který naplní databázi testovacími daty.</span><span class="sxs-lookup"><span data-stu-id="9bd43-308">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="9bd43-309">Vytvořte *data/DbInitializer. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="9bd43-309">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="9bd43-310">Kód kontroluje, zda v databázi existují studenti.</span><span class="sxs-lookup"><span data-stu-id="9bd43-310">The code checks if there are any students in the database.</span></span> <span data-ttu-id="9bd43-311">Pokud neexistují studenti, přidá testovací data do databáze.</span><span class="sxs-lookup"><span data-stu-id="9bd43-311">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="9bd43-312">Vytvoří testovací data v polích, nikoli `List<T>` kolekce pro optimalizaci výkonu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-312">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="9bd43-313">V *program.cs*nahraďte `EnsureCreated` volání `DbInitializer.Initialize` voláním:</span><span class="sxs-lookup"><span data-stu-id="9bd43-313">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="9bd43-314">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd43-314">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9bd43-315">Zastavte aplikaci, pokud je spuštěná, a spusťte následující příkaz v **konzole správce balíčků** (PMC):</span><span class="sxs-lookup"><span data-stu-id="9bd43-315">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="9bd43-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9bd43-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9bd43-317">Zastavte aplikaci, pokud je spuštěná, a odstraňte soubor *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="9bd43-317">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="9bd43-318">Restartujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9bd43-318">Restart the app.</span></span>

* <span data-ttu-id="9bd43-319">Vyberte stránku Students a zobrazte si dosazený údaj.</span><span class="sxs-lookup"><span data-stu-id="9bd43-319">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="9bd43-320">Zobrazení databáze</span><span class="sxs-lookup"><span data-stu-id="9bd43-320">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9bd43-321">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd43-321">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9bd43-322">Otevřete **Průzkumník objektů systému SQL Server** (SSOX) z nabídky **Zobrazit** v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9bd43-322">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="9bd43-323">V SSOX vyberte **(LocalDB) \MSSQLLocalDB > databáze > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="9bd43-323">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="9bd43-324">Název databáze je vygenerován z dříve zadaného názvu kontextu plus pomlčka a identifikátor GUID.</span><span class="sxs-lookup"><span data-stu-id="9bd43-324">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="9bd43-325">Rozbalte uzel **tabulky** .</span><span class="sxs-lookup"><span data-stu-id="9bd43-325">Expand the **Tables** node.</span></span>
* <span data-ttu-id="9bd43-326">Klikněte pravým tlačítkem myši na tabulku **student** a kliknutím na **Zobrazit data** Zobrazte vytvořené sloupce a řádky vložené do tabulky.</span><span class="sxs-lookup"><span data-stu-id="9bd43-326">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="9bd43-327">Kliknutím pravým tlačítkem na tabulku **student** a kliknutím na **Zobrazit kód** zjistíte, jak se `Student` model mapuje na `Student` schéma tabulky.</span><span class="sxs-lookup"><span data-stu-id="9bd43-327">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9bd43-328">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9bd43-328">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9bd43-329">Použijte nástroj SQLite k zobrazení schématu databáze a dat osazených daty.</span><span class="sxs-lookup"><span data-stu-id="9bd43-329">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="9bd43-330">Databázový soubor má název *cu. DB* a je umístěn ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-330">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="9bd43-331">Asynchronní kód</span><span class="sxs-lookup"><span data-stu-id="9bd43-331">Asynchronous code</span></span>

<span data-ttu-id="9bd43-332">Asynchronní programování je výchozí režim pro ASP.NET Core a EF Core.</span><span class="sxs-lookup"><span data-stu-id="9bd43-332">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="9bd43-333">Na webovém serveru je k dispozici omezený počet vláken a v situacích vysokého zatížení se mohou používat všechna dostupná vlákna.</span><span class="sxs-lookup"><span data-stu-id="9bd43-333">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="9bd43-334">Pokud k tomu dojde, server nemůže zpracovat nové požadavky, dokud nebudou vlákna uvolněna.</span><span class="sxs-lookup"><span data-stu-id="9bd43-334">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="9bd43-335">Pomocí synchronního kódu může být mnoho vláken svázáno s tím, že ve skutečnosti neprovádí žádnou práci, protože čeká na dokončení vstupně-výstupních operací.</span><span class="sxs-lookup"><span data-stu-id="9bd43-335">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="9bd43-336">V případě asynchronního kódu, když proces čeká na dokončení vstupně-výstupních operací, je jeho vlákno uvolněno na server, který bude použit pro zpracování jiných požadavků.</span><span class="sxs-lookup"><span data-stu-id="9bd43-336">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="9bd43-337">Výsledkem je, že asynchronní kód umožňuje efektivnější použití prostředků serveru a server může zpracovávat více provozu bez prodlev.</span><span class="sxs-lookup"><span data-stu-id="9bd43-337">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="9bd43-338">Asynchronní kód zavádí v době běhu malé množství režie.</span><span class="sxs-lookup"><span data-stu-id="9bd43-338">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="9bd43-339">V situacích s nízkým provozem je dosaženo zanedbatelného výkonu, zatímco v situacích vysokého provozu je potenciální zlepšení výkonu značné.</span><span class="sxs-lookup"><span data-stu-id="9bd43-339">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="9bd43-340">V následujícím kódu, klíčové slovo [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` návratová hodnota, `await` klíčové slovo a `ToListAsync` Metoda provede asynchronní spouštění kódu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-340">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="9bd43-341">`async`Klíčové slovo dává kompilátoru následující informace:</span><span class="sxs-lookup"><span data-stu-id="9bd43-341">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="9bd43-342">Vygenerujte zpětná volání pro části těla metody.</span><span class="sxs-lookup"><span data-stu-id="9bd43-342">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="9bd43-343">Vytvořte vrácený objekt [úkolu](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) .</span><span class="sxs-lookup"><span data-stu-id="9bd43-343">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="9bd43-344">`Task<T>`Návratový typ představuje probíhající práci.</span><span class="sxs-lookup"><span data-stu-id="9bd43-344">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="9bd43-345">`await`Klíčové slovo způsobí, že kompilátor rozdělí metodu do dvou částí.</span><span class="sxs-lookup"><span data-stu-id="9bd43-345">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="9bd43-346">První část končí asynchronně spuštěnou operací.</span><span class="sxs-lookup"><span data-stu-id="9bd43-346">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="9bd43-347">Druhá část je vložena do metody zpětného volání, která je volána po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="9bd43-347">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="9bd43-348">`ToListAsync`je asynchronní verze `ToList` metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="9bd43-348">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="9bd43-349">Některé věci, které je potřeba znát při psaní asynchronního kódu, který používá EF Core:</span><span class="sxs-lookup"><span data-stu-id="9bd43-349">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="9bd43-350">Asynchronně jsou spouštěny pouze příkazy, které způsobují odeslání dotazů nebo příkazů do databáze.</span><span class="sxs-lookup"><span data-stu-id="9bd43-350">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="9bd43-351">To zahrnuje `ToListAsync` , `SingleOrDefaultAsync` , `FirstOrDefaultAsync` a `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-351">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="9bd43-352">Neobsahuje příkazy, které mění pouze `IQueryable` , například `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-352">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="9bd43-353">Kontext EF Core není bezpečný pro přístup z více vláken: Nepokoušejte se současně provést více operací.</span><span class="sxs-lookup"><span data-stu-id="9bd43-353">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="9bd43-354">Chcete-li využít výhody z hlediska výkonu asynchronního kódu, ověřte, že balíčky knihoven (například pro stránkování) používají async, pokud volají EF Core metody, které odesílají dotazy do databáze.</span><span class="sxs-lookup"><span data-stu-id="9bd43-354">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="9bd43-355">Další informace o asynchronním programování v rozhraní .NET naleznete v tématu [Async Overview](/dotnet/standard/async) a [Asynchronous Programming with Async a await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="9bd43-355">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="9bd43-356">Další kroky</span><span class="sxs-lookup"><span data-stu-id="9bd43-356">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="9bd43-357">Další kurz</span><span class="sxs-lookup"><span data-stu-id="9bd43-357">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9bd43-358">Ukázková webová aplikace společnosti Contoso University ukazuje, jak vytvořit Razor aplikaci ASP.NET Core Pages s využitím jádra Entity Framework (EF).</span><span class="sxs-lookup"><span data-stu-id="9bd43-358">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="9bd43-359">Ukázková aplikace je web pro fiktivní univerzitě společnosti Contoso.</span><span class="sxs-lookup"><span data-stu-id="9bd43-359">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="9bd43-360">Zahrnuje funkce, jako je například využití studenta, vytváření kurzu a přiřazení instruktora.</span><span class="sxs-lookup"><span data-stu-id="9bd43-360">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="9bd43-361">Tato stránka je první v sérii kurzů, které vysvětlují, jak vytvořit ukázkovou aplikaci Contoso University.</span><span class="sxs-lookup"><span data-stu-id="9bd43-361">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="9bd43-362">Stažení nebo zobrazení dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="9bd43-362">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="9bd43-363">[Pokyny ke stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="9bd43-363">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9bd43-364">Požadavky</span><span class="sxs-lookup"><span data-stu-id="9bd43-364">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9bd43-365">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd43-365">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="9bd43-366">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9bd43-366">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="9bd43-367">Znalost [ Razor stránek](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="9bd43-367">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="9bd43-368">Noví Programátori by měli před spuštěním této série dokončit [Začínáme se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="9bd43-368">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="9bd43-369">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="9bd43-369">Troubleshooting</span></span>

<span data-ttu-id="9bd43-370">Pokud narazíte na problém, který nelze vyřešit, můžete řešení obecně najít porovnáním kódu s [dokončeným projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="9bd43-370">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="9bd43-371">Dobrým způsobem, jak získat pomoc, je odeslání otázky do [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) pro [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="9bd43-371">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="9bd43-372">Webová aplikace společnosti Contoso University</span><span class="sxs-lookup"><span data-stu-id="9bd43-372">The Contoso University web app</span></span>

<span data-ttu-id="9bd43-373">Aplikace sestavená v těchto kurzech je základním webem na univerzitě.</span><span class="sxs-lookup"><span data-stu-id="9bd43-373">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="9bd43-374">Uživatelé můžou zobrazit a aktualizovat informace o studentech, kurzech a instruktorech.</span><span class="sxs-lookup"><span data-stu-id="9bd43-374">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="9bd43-375">Tady je několik obrazovek vytvořených v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-375">Here are a few of the screens created in the tutorial.</span></span>

![Stránka indexu studentů](intro/_static/students-index.png)

![Stránka pro úpravy studentů](intro/_static/student-edit.png)

<span data-ttu-id="9bd43-378">Styl uživatelského rozhraní tohoto webu je blízko toho, co vygenerovaly předdefinované šablony.</span><span class="sxs-lookup"><span data-stu-id="9bd43-378">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="9bd43-379">Tento kurz se zaměřuje na EF Core se Razor stránkami, nikoli v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="9bd43-379">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="9bd43-380">Vytvoření Razor webové aplikace ContosoUniversity Pages</span><span class="sxs-lookup"><span data-stu-id="9bd43-380">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9bd43-381">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd43-381">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9bd43-382">V nabídce **soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="9bd43-382">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="9bd43-383">Vytvořte novou ASP.NET Core webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9bd43-383">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="9bd43-384">Pojmenujte projekt **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="9bd43-384">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="9bd43-385">Je důležité pojmenovat projekt *ContosoUniversity* , aby se obory názvů shodovaly, když je kód zkopírován/vložen.</span><span class="sxs-lookup"><span data-stu-id="9bd43-385">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="9bd43-386">V rozevíracím seznamu vyberte **ASP.NET Core 2,1** a potom vyberte možnost **Webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="9bd43-386">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="9bd43-387">Obrázky předchozích kroků najdete v tématu [vytvoření Razor webové aplikace](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="9bd43-387">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="9bd43-388">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9bd43-388">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9bd43-389">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9bd43-389">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="9bd43-390">Nastavení stylu webu</span><span class="sxs-lookup"><span data-stu-id="9bd43-390">Set up the site style</span></span>

<span data-ttu-id="9bd43-391">Několik změn nastaví nabídku webu, rozložení a domovskou stránku.</span><span class="sxs-lookup"><span data-stu-id="9bd43-391">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="9bd43-392">Update *Pages/Shared/_Layout. cshtml* s následujícími změnami:</span><span class="sxs-lookup"><span data-stu-id="9bd43-392">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="9bd43-393">Změňte všechny výskyty "ContosoUniversity" na "contoso University".</span><span class="sxs-lookup"><span data-stu-id="9bd43-393">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="9bd43-394">Existují tři výskyty.</span><span class="sxs-lookup"><span data-stu-id="9bd43-394">There are three occurrences.</span></span>

* <span data-ttu-id="9bd43-395">Přidejte položky nabídky pro **studenty**, **kurzy**, **instruktory**a **oddělení**a odstraňte položku nabídky **kontakt** .</span><span class="sxs-lookup"><span data-stu-id="9bd43-395">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="9bd43-396">Změny jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="9bd43-396">The changes are highlighted.</span></span> <span data-ttu-id="9bd43-397">(Všechny *značky se nezobrazí* .)</span><span class="sxs-lookup"><span data-stu-id="9bd43-397">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="9bd43-398">Na *stránce pages/index. cshtml*nahraďte obsah souboru následujícím kódem, který nahradí text o ASP.NET a MVC textem o této aplikaci:</span><span class="sxs-lookup"><span data-stu-id="9bd43-398">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="9bd43-399">Vytvoření datového modelu</span><span class="sxs-lookup"><span data-stu-id="9bd43-399">Create the data model</span></span>

<span data-ttu-id="9bd43-400">Vytvořte třídy entit pro aplikaci Contoso University.</span><span class="sxs-lookup"><span data-stu-id="9bd43-400">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="9bd43-401">Začněte následujícími třemi entitami:</span><span class="sxs-lookup"><span data-stu-id="9bd43-401">Start with the following three entities:</span></span>

![Kurz – registrace – diagram datového modelu studenta](intro/_static/data-model-diagram.png)

<span data-ttu-id="9bd43-403">Mezi `Student` entitami a entitami je vztah 1: n `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-403">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="9bd43-404">Mezi `Course` entitami a entitami je vztah 1: n `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-404">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="9bd43-405">Student se může zaregistrovat v jakémkoli počtu kurzů.</span><span class="sxs-lookup"><span data-stu-id="9bd43-405">A student can enroll in any number of courses.</span></span> <span data-ttu-id="9bd43-406">Kurz může mít zaregistrovaný libovolný počet studentů.</span><span class="sxs-lookup"><span data-stu-id="9bd43-406">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="9bd43-407">V následujících oddílech je vytvořena třída pro každou z těchto entit.</span><span class="sxs-lookup"><span data-stu-id="9bd43-407">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="9bd43-408">Entita studenta</span><span class="sxs-lookup"><span data-stu-id="9bd43-408">The Student entity</span></span>

![Diagram entity studenta](intro/_static/student-entity.png)

<span data-ttu-id="9bd43-410">Vytvořte složku *modelů* .</span><span class="sxs-lookup"><span data-stu-id="9bd43-410">Create a *Models* folder.</span></span> <span data-ttu-id="9bd43-411">Ve složce *modely* vytvořte soubor třídy s názvem *student.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="9bd43-411">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="9bd43-412">`ID`Vlastnost se zobrazí jako sloupec primárního klíče tabulky databáze (DB), která odpovídá této třídě.</span><span class="sxs-lookup"><span data-stu-id="9bd43-412">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="9bd43-413">Ve výchozím nastavení EF Core interpretuje vlastnost s názvem `ID` nebo `classnameID` jako primární klíč.</span><span class="sxs-lookup"><span data-stu-id="9bd43-413">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="9bd43-414">V `classnameID` `classname` je název třídy.</span><span class="sxs-lookup"><span data-stu-id="9bd43-414">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="9bd43-415">Alternativní automaticky rozpoznaný primární klíč je `StudentID` v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-415">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="9bd43-416">`Enrollments`Vlastnost je [navigační vlastnost](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="9bd43-416">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="9bd43-417">Vlastnosti navigace odkazují na další entity, které se vztahují k této entitě.</span><span class="sxs-lookup"><span data-stu-id="9bd43-417">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="9bd43-418">V tomto případě `Enrollments` vlastnost `Student entity` obsahuje všechny `Enrollment` entity, které jsou v relaci `Student` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-418">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="9bd43-419">Například pokud má řádek studenta v databázi dva související řádky registrace, `Enrollments` navigační vlastnost obsahuje tyto dvě `Enrollment` entity.</span><span class="sxs-lookup"><span data-stu-id="9bd43-419">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="9bd43-420">Související `Enrollment` řádek je řádek, který obsahuje hodnotu primárního klíče tohoto studenta ve `StudentID` sloupci.</span><span class="sxs-lookup"><span data-stu-id="9bd43-420">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="9bd43-421">Předpokládejme například, že student s ID = 1 má dva řádky v `Enrollment` tabulce.</span><span class="sxs-lookup"><span data-stu-id="9bd43-421">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="9bd43-422">`Enrollment`Tabulka má dva řádky s `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="9bd43-422">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="9bd43-423">`StudentID`je cizí klíč v `Enrollment` tabulce, který určuje studenta v `Student` tabulce.</span><span class="sxs-lookup"><span data-stu-id="9bd43-423">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="9bd43-424">Pokud navigační vlastnost může obsahovat více entit, musí být vlastnost navigace typu seznam, například `ICollection<T>` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-424">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="9bd43-425">`ICollection<T>`lze zadat nebo typ jako `List<T>` nebo `HashSet<T>` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-425">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="9bd43-426">Při `ICollection<T>` použití EF Core vytvoří `HashSet<T>` ve výchozím nastavení kolekci.</span><span class="sxs-lookup"><span data-stu-id="9bd43-426">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="9bd43-427">Navigační vlastnosti, které obsahují více entit, přicházejí ze vztahů m:n a 1: n.</span><span class="sxs-lookup"><span data-stu-id="9bd43-427">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="9bd43-428">Entita registrace</span><span class="sxs-lookup"><span data-stu-id="9bd43-428">The Enrollment entity</span></span>

![Diagram entity registrace](intro/_static/enrollment-entity.png)

<span data-ttu-id="9bd43-430">Ve složce *modely* vytvořte *Enrollment.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="9bd43-430">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="9bd43-431">`EnrollmentID`Vlastnost je primární klíč.</span><span class="sxs-lookup"><span data-stu-id="9bd43-431">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="9bd43-432">Tato entita používá `classnameID` vzor místo `ID` podobně jako `Student` entita.</span><span class="sxs-lookup"><span data-stu-id="9bd43-432">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="9bd43-433">Vývojáři obvykle volí jeden model a používají ho v rámci datového modelu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-433">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="9bd43-434">V pozdějším kurzu se zobrazuje použití ID bez ClassName, které usnadňuje implementaci dědičnosti v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-434">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="9bd43-435">`Grade`Vlastnost je `enum` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-435">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="9bd43-436">Otazník po `Grade` deklaraci typu označuje, že vlastnost může `Grade` mít hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="9bd43-436">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="9bd43-437">Hodnota, která je null, se liší od nulové třídy – hodnota null znamená, že se nejedná o známku nebo ještě není přiřazená.</span><span class="sxs-lookup"><span data-stu-id="9bd43-437">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="9bd43-438">`StudentID`Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Student` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-438">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="9bd43-439">`Enrollment`Entita je přidružená k jedné `Student` entitě, takže vlastnost obsahuje jednu `Student` entitu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-439">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="9bd43-440">`Student`Entita se liší od `Student.Enrollments` navigační vlastnosti, která obsahuje více `Enrollment` entit.</span><span class="sxs-lookup"><span data-stu-id="9bd43-440">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="9bd43-441">`CourseID`Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Course` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-441">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="9bd43-442">`Enrollment`Entita je přidružená k jedné `Course` entitě.</span><span class="sxs-lookup"><span data-stu-id="9bd43-442">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="9bd43-443">EF Core interpretuje vlastnost jako cizí klíč, pokud má název `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-443">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="9bd43-444">Například `StudentID` pro `Student` vlastnost navigace, protože `Student` primární klíč entity je `ID` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-444">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="9bd43-445">Lze také pojmenovat vlastnosti cizího klíče `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-445">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="9bd43-446">Například `CourseID` vzhledem k tomu, že `Course` primární klíč entity je `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-446">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="9bd43-447">Entita kurzu</span><span class="sxs-lookup"><span data-stu-id="9bd43-447">The Course entity</span></span>

![Diagram entity kurzu](intro/_static/course-entity.png)

<span data-ttu-id="9bd43-449">Ve složce *modely* vytvořte *Course.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="9bd43-449">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="9bd43-450">`Enrollments`Vlastnost je navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="9bd43-450">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="9bd43-451">`Course`Entita může souviset s libovolným počtem `Enrollment` entit.</span><span class="sxs-lookup"><span data-stu-id="9bd43-451">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="9bd43-452">`DatabaseGenerated`Atribut umožňuje aplikaci zadat primární klíč, a ne vytvořit databázi.</span><span class="sxs-lookup"><span data-stu-id="9bd43-452">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="9bd43-453">Generování uživatelského rozhraní modelu studenta</span><span class="sxs-lookup"><span data-stu-id="9bd43-453">Scaffold the student model</span></span>

<span data-ttu-id="9bd43-454">V této části je model studenta vygenerovaný jako generátor.</span><span class="sxs-lookup"><span data-stu-id="9bd43-454">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="9bd43-455">To znamená, že nástroj pro generování uživatelského rozhraní vytváří stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model studenta.</span><span class="sxs-lookup"><span data-stu-id="9bd43-455">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="9bd43-456">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="9bd43-456">Build the project.</span></span>
* <span data-ttu-id="9bd43-457">Vytvořte složku *stránky/studenty* .</span><span class="sxs-lookup"><span data-stu-id="9bd43-457">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9bd43-458">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd43-458">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9bd43-459">V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *Pages/Students* > **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="9bd43-459">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="9bd43-460">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte \*\* Razor stránky pomocí Entity Framework (CRUD)\*\* > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="9bd43-460">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="9bd43-461">Dokončete dialog **Přidat Razor stránky pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="9bd43-461">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="9bd43-462">V rozevíracím seznamu **třída modelu** vyberte **student (ContosoUniversity. Models)**.</span><span class="sxs-lookup"><span data-stu-id="9bd43-462">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="9bd43-463">V řádku **třídy kontextu dat** vyberte **+** znaménko (plus) a změňte vygenerovaný název na **ContosoUniversity. Models. SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="9bd43-463">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="9bd43-464">V rozevíracím seznamu **Třída kontextu dat** vyberte **ContosoUniversity. Models. SchoolContext.**</span><span class="sxs-lookup"><span data-stu-id="9bd43-464">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="9bd43-465">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="9bd43-465">Select **Add**.</span></span>

![Dialogové okno CRUD](intro/_static/s1.png)

<span data-ttu-id="9bd43-467">Pokud máte problém s předchozím krokem, podívejte [se do části vygenerované modely filmů](xref:tutorials/razor-pages/model#scaffold-the-movie-model) .</span><span class="sxs-lookup"><span data-stu-id="9bd43-467">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9bd43-468">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9bd43-468">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9bd43-469">Spusťte následující příkazy pro generování uživatelského rozhraní modelu studenta.</span><span class="sxs-lookup"><span data-stu-id="9bd43-469">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="9bd43-470">Proces generování uživatelského rozhraní byl vytvořen a změnil následující soubory:</span><span class="sxs-lookup"><span data-stu-id="9bd43-470">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="9bd43-471">Vytvořené soubory</span><span class="sxs-lookup"><span data-stu-id="9bd43-471">Files created</span></span>

* <span data-ttu-id="9bd43-472">*Stránky/studenty* Vytvořit, odstranit, podrobnosti, upravit, index.</span><span class="sxs-lookup"><span data-stu-id="9bd43-472">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="9bd43-473">*Data/SchoolContext. cs*</span><span class="sxs-lookup"><span data-stu-id="9bd43-473">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="9bd43-474">Aktualizace souborů</span><span class="sxs-lookup"><span data-stu-id="9bd43-474">File updates</span></span>

* <span data-ttu-id="9bd43-475">*Startup.cs* : změny v tomto souboru jsou podrobně popsané v další části.</span><span class="sxs-lookup"><span data-stu-id="9bd43-475">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="9bd43-476">*appsettings.js* : připojovací řetězec použitý k připojení k místní databázi je přidán.</span><span class="sxs-lookup"><span data-stu-id="9bd43-476">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="9bd43-477">Kontrola kontextu zaregistrovaného vkládáním závislostí</span><span class="sxs-lookup"><span data-stu-id="9bd43-477">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="9bd43-478">ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9bd43-478">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="9bd43-479">Služby (například kontext EF Core DB) jsou registrovány pomocí injektáže závislosti při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="9bd43-479">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="9bd43-480">Komponenty, které vyžadují tyto služby (například Razor stránky), poskytují tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="9bd43-480">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="9bd43-481">Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-481">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="9bd43-482">Nástroj pro generování uživatelského rozhraní automaticky vytvořil kontext databáze a zaregistroval ho pomocí kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="9bd43-482">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="9bd43-483">Projděte si `ConfigureServices` metodu v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="9bd43-483">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="9bd43-484">Zvýrazněný řádek byl přidán do modulu generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="9bd43-484">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="9bd43-485">Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="9bd43-485">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="9bd43-486">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) přečte připojovací řetězec z *appsettings.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="9bd43-486">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="9bd43-487">Aktualizace Main</span><span class="sxs-lookup"><span data-stu-id="9bd43-487">Update main</span></span>

<span data-ttu-id="9bd43-488">V *program.cs*upravte `Main` metodu a proveďte následující:</span><span class="sxs-lookup"><span data-stu-id="9bd43-488">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="9bd43-489">Získá instanci kontextu databáze z kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="9bd43-489">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="9bd43-490">Zavolejte [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="9bd43-490">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="9bd43-491">Vyřazení kontextu po `EnsureCreated` dokončení metody.</span><span class="sxs-lookup"><span data-stu-id="9bd43-491">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="9bd43-492">Následující kód ukazuje aktualizovaný soubor *program.cs* .</span><span class="sxs-lookup"><span data-stu-id="9bd43-492">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="9bd43-493">`EnsureCreated`zajišťuje, aby databáze pro kontext existovala.</span><span class="sxs-lookup"><span data-stu-id="9bd43-493">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="9bd43-494">Pokud existuje, není provedena žádná akce.</span><span class="sxs-lookup"><span data-stu-id="9bd43-494">If it exists, no action is taken.</span></span> <span data-ttu-id="9bd43-495">Pokud neexistuje, vytvoří se databáze a všechny její schéma.</span><span class="sxs-lookup"><span data-stu-id="9bd43-495">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="9bd43-496">`EnsureCreated`nepoužívá k vytvoření databáze migrace.</span><span class="sxs-lookup"><span data-stu-id="9bd43-496">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="9bd43-497">Databázi, která je vytvořená pomocí, se `EnsureCreated` nedá později aktualizovat pomocí migrace.</span><span class="sxs-lookup"><span data-stu-id="9bd43-497">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="9bd43-498">`EnsureCreated`se volá při spuštění aplikace, což umožňuje následující pracovní postup:</span><span class="sxs-lookup"><span data-stu-id="9bd43-498">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="9bd43-499">Odstraňte databázi.</span><span class="sxs-lookup"><span data-stu-id="9bd43-499">Delete the DB.</span></span>
* <span data-ttu-id="9bd43-500">Změňte schéma databáze (například přidat `EmailAddress` pole).</span><span class="sxs-lookup"><span data-stu-id="9bd43-500">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="9bd43-501">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9bd43-501">Run the app.</span></span>
* <span data-ttu-id="9bd43-502">`EnsureCreated`vytvoří databázi se `EmailAddress` sloupcem.</span><span class="sxs-lookup"><span data-stu-id="9bd43-502">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="9bd43-503">`EnsureCreated`je pohodlná v brzké fázi vývoje, když se schéma rychle vyvíjí.</span><span class="sxs-lookup"><span data-stu-id="9bd43-503">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="9bd43-504">Později v kurzu se databáze odstraní a použijí se migrace.</span><span class="sxs-lookup"><span data-stu-id="9bd43-504">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="9bd43-505">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="9bd43-505">Test the app</span></span>

<span data-ttu-id="9bd43-506">Spusťte aplikaci a přijměte tuto cookie zásadu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-506">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="9bd43-507">Tato aplikace neuchovává osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="9bd43-507">This app doesn't keep personal information.</span></span> <span data-ttu-id="9bd43-508">Informace o cookie zásadách najdete v tématu Podpora pro [EU obecné nařízení O ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="9bd43-508">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="9bd43-509">Vyberte odkaz **Students** a pak **vytvořte nový**.</span><span class="sxs-lookup"><span data-stu-id="9bd43-509">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="9bd43-510">Otestujte odkazy Upravit, Podrobnosti a Odstranit.</span><span class="sxs-lookup"><span data-stu-id="9bd43-510">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="9bd43-511">Projděte si kontext SchoolContext DB.</span><span class="sxs-lookup"><span data-stu-id="9bd43-511">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="9bd43-512">Hlavní třída, která koordinuje funkce EF Core pro daný datový model, je třída kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="9bd43-512">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="9bd43-513">Kontext dat je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="9bd43-513">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="9bd43-514">Kontext dat určuje, které entity jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-514">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="9bd43-515">V tomto projektu je třída pojmenována `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-515">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="9bd43-516">*SchoolContext.cs* aktualizujte pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="9bd43-516">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="9bd43-517">Zvýrazněný kód vytvoří vlastnost [negenerickými \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro každou sadu entit.</span><span class="sxs-lookup"><span data-stu-id="9bd43-517">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="9bd43-518">V terminologii EF Core:</span><span class="sxs-lookup"><span data-stu-id="9bd43-518">In EF Core terminology:</span></span>

* <span data-ttu-id="9bd43-519">Sada entit obvykle odpovídá tabulce databáze.</span><span class="sxs-lookup"><span data-stu-id="9bd43-519">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="9bd43-520">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="9bd43-520">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="9bd43-521">`DbSet<Enrollment>`a `DbSet<Course>` může být vynecháno.</span><span class="sxs-lookup"><span data-stu-id="9bd43-521">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="9bd43-522">EF Core je implicitně obsahuje, protože `Student` entita odkazuje na `Enrollment` entitu a `Enrollment` entita odkazuje na `Course` entitu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-522">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="9bd43-523">V tomto kurzu ponechejte `DbSet<Enrollment>` a `DbSet<Course>` v `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-523">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="9bd43-524">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="9bd43-524">SQL Server Express LocalDB</span></span>

<span data-ttu-id="9bd43-525">Připojovací řetězec Určuje [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="9bd43-525">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="9bd43-526">LocalDB je zjednodušená verze databázového stroje SQL Server Express a je určena pro vývoj aplikací, nikoli za použití v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="9bd43-526">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="9bd43-527">LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje složitá konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9bd43-527">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="9bd43-528">Ve výchozím nastavení LocalDB vytvoří soubory *. mdf* DB v `C:/Users/<user>` adresáři.</span><span class="sxs-lookup"><span data-stu-id="9bd43-528">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="9bd43-529">Přidejte kód pro inicializaci databáze s testovacími daty</span><span class="sxs-lookup"><span data-stu-id="9bd43-529">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="9bd43-530">EF Core vytvoří prázdnou databázi.</span><span class="sxs-lookup"><span data-stu-id="9bd43-530">EF Core creates an empty DB.</span></span> <span data-ttu-id="9bd43-531">V této části `Initialize` je zapsána metoda pro naplnění testovacích dat.</span><span class="sxs-lookup"><span data-stu-id="9bd43-531">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="9bd43-532">Ve složce *data* vytvořte nový soubor třídy s názvem *DbInitializer.cs* a přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="9bd43-532">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="9bd43-533">Poznámka: předchozí kód používá `Models` pro obor názvů ( `namespace ContosoUniversity.Models` ) spíše než `Data` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-533">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="9bd43-534">`Models`je konzistentní s kódem generovaným pomocí generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="9bd43-534">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="9bd43-535">Další informace najdete v [tomto problému s vygenerováním uživatelského rozhraní GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="9bd43-535">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="9bd43-536">Kód zkontroluje, jestli v databázi existují studenti.</span><span class="sxs-lookup"><span data-stu-id="9bd43-536">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="9bd43-537">Pokud databáze neobsahuje žádné studenty, databáze se inicializuje s testovacími daty.</span><span class="sxs-lookup"><span data-stu-id="9bd43-537">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="9bd43-538">Načte testovací data do polí `List<T>` , nikoli kolekce pro optimalizaci výkonu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-538">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="9bd43-539">`EnsureCreated`Metoda automaticky vytvoří databázi pro kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="9bd43-539">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="9bd43-540">Pokud databáze existuje, `EnsureCreated` vrátí se beze změny databáze.</span><span class="sxs-lookup"><span data-stu-id="9bd43-540">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="9bd43-541">V *program.cs*upravte `Main` metodu pro volání `Initialize` :</span><span class="sxs-lookup"><span data-stu-id="9bd43-541">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="9bd43-542">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd43-542">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9bd43-543">Zastavte aplikaci, pokud je spuštěná, a spusťte následující příkaz v **konzole správce balíčků** (PMC):</span><span class="sxs-lookup"><span data-stu-id="9bd43-543">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="9bd43-544">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9bd43-544">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9bd43-545">Zastavte aplikaci, pokud je spuštěná, a odstraňte soubor *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="9bd43-545">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="9bd43-546">Zobrazení databáze</span><span class="sxs-lookup"><span data-stu-id="9bd43-546">View the DB</span></span>

<span data-ttu-id="9bd43-547">Název databáze je vygenerován z dříve zadaného názvu kontextu plus pomlčka a identifikátor GUID.</span><span class="sxs-lookup"><span data-stu-id="9bd43-547">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="9bd43-548">Proto bude název databáze "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="9bd43-548">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="9bd43-549">Identifikátor GUID bude pro každého uživatele odlišný.</span><span class="sxs-lookup"><span data-stu-id="9bd43-549">The GUID will be different for each user.</span></span>
<span data-ttu-id="9bd43-550">Otevřete **Průzkumník objektů systému SQL Server** (SSOX) z nabídky **Zobrazit** v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9bd43-550">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="9bd43-551">V SSOX klikněte na **(LocalDB) \MSSQLLocalDB > databáze > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="9bd43-551">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="9bd43-552">Rozbalte uzel **tabulky** .</span><span class="sxs-lookup"><span data-stu-id="9bd43-552">Expand the **Tables** node.</span></span>

<span data-ttu-id="9bd43-553">Klikněte pravým tlačítkem myši na tabulku **student** a kliknutím na **Zobrazit data** Zobrazte vytvořené sloupce a řádky vložené do tabulky.</span><span class="sxs-lookup"><span data-stu-id="9bd43-553">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="9bd43-554">Asynchronní kód</span><span class="sxs-lookup"><span data-stu-id="9bd43-554">Asynchronous code</span></span>

<span data-ttu-id="9bd43-555">Asynchronní programování je výchozí režim pro ASP.NET Core a EF Core.</span><span class="sxs-lookup"><span data-stu-id="9bd43-555">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="9bd43-556">Na webovém serveru je k dispozici omezený počet vláken a v situacích vysokého zatížení se mohou používat všechna dostupná vlákna.</span><span class="sxs-lookup"><span data-stu-id="9bd43-556">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="9bd43-557">Pokud k tomu dojde, server nemůže zpracovat nové požadavky, dokud nebudou vlákna uvolněna.</span><span class="sxs-lookup"><span data-stu-id="9bd43-557">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="9bd43-558">Pomocí synchronního kódu může být mnoho vláken svázáno s tím, že ve skutečnosti neprovádí žádnou práci, protože čeká na dokončení vstupně-výstupních operací.</span><span class="sxs-lookup"><span data-stu-id="9bd43-558">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="9bd43-559">V případě asynchronního kódu, když proces čeká na dokončení vstupně-výstupních operací, je jeho vlákno uvolněno na server, který bude použit pro zpracování jiných požadavků.</span><span class="sxs-lookup"><span data-stu-id="9bd43-559">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="9bd43-560">Výsledkem je, že asynchronní kód umožňuje efektivnější použití prostředků serveru a server je povolen pro zpracování většího objemu dat bez prodlev.</span><span class="sxs-lookup"><span data-stu-id="9bd43-560">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="9bd43-561">Asynchronní kód zavádí v době běhu malé množství režie.</span><span class="sxs-lookup"><span data-stu-id="9bd43-561">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="9bd43-562">V situacích s nízkým provozem je dosaženo zanedbatelného výkonu, zatímco v situacích vysokého provozu je potenciální zlepšení výkonu značné.</span><span class="sxs-lookup"><span data-stu-id="9bd43-562">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="9bd43-563">V následujícím kódu, klíčové slovo [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` návratová hodnota, `await` klíčové slovo a `ToListAsync` Metoda provede asynchronní spouštění kódu.</span><span class="sxs-lookup"><span data-stu-id="9bd43-563">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="9bd43-564">`async`Klíčové slovo dává kompilátoru následující informace:</span><span class="sxs-lookup"><span data-stu-id="9bd43-564">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="9bd43-565">Vygenerujte zpětná volání pro části těla metody.</span><span class="sxs-lookup"><span data-stu-id="9bd43-565">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="9bd43-566">Automaticky vytvořte vrácený objekt [úkolu](/dotnet/api/system.threading.tasks.task) .</span><span class="sxs-lookup"><span data-stu-id="9bd43-566">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="9bd43-567">Další informace najdete v tématu [návratový typ úlohy](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="9bd43-567">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="9bd43-568">Implicitní návratový typ `Task` představuje probíhající práci.</span><span class="sxs-lookup"><span data-stu-id="9bd43-568">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="9bd43-569">`await`Klíčové slovo způsobí, že kompilátor rozdělí metodu do dvou částí.</span><span class="sxs-lookup"><span data-stu-id="9bd43-569">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="9bd43-570">První část končí asynchronně spuštěnou operací.</span><span class="sxs-lookup"><span data-stu-id="9bd43-570">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="9bd43-571">Druhá část je vložena do metody zpětného volání, která je volána po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="9bd43-571">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="9bd43-572">`ToListAsync`je asynchronní verze `ToList` metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="9bd43-572">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="9bd43-573">Některé věci, které je potřeba znát při psaní asynchronního kódu, který používá EF Core:</span><span class="sxs-lookup"><span data-stu-id="9bd43-573">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="9bd43-574">Asynchronně jsou spouštěny pouze příkazy, které způsobují odeslání dotazů nebo příkazů do databáze.</span><span class="sxs-lookup"><span data-stu-id="9bd43-574">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="9bd43-575">To zahrnuje, `ToListAsync` , `SingleOrDefaultAsync` , `FirstOrDefaultAsync` a `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-575">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="9bd43-576">Neobsahuje příkazy, které mění pouze `IQueryable` , například `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="9bd43-576">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="9bd43-577">Kontext EF Core není bezpečný pro přístup z více vláken: Nepokoušejte se současně provést více operací.</span><span class="sxs-lookup"><span data-stu-id="9bd43-577">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="9bd43-578">Chcete-li využít výhody z hlediska výkonu asynchronního kódu, ověřte, že balíčky knihoven (například pro stránkování) používají async, pokud volají EF Core metody, které odesílají dotazy do databáze.</span><span class="sxs-lookup"><span data-stu-id="9bd43-578">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="9bd43-579">Další informace o asynchronním programování v rozhraní .NET naleznete v tématu [Async Overview](/dotnet/standard/async) a [Asynchronous Programming with Async a await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="9bd43-579">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="9bd43-580">V dalším kurzu se přezkoumávají základní operace CRUD (vytváření, čtení, aktualizace, odstranění).</span><span class="sxs-lookup"><span data-stu-id="9bd43-580">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="9bd43-581">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="9bd43-581">Additional resources</span></span>

* [<span data-ttu-id="9bd43-582">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="9bd43-582">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="9bd43-583">Další</span><span class="sxs-lookup"><span data-stu-id="9bd43-583">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
