---
title: Razor stránky s jádrem entity framework u ASP.NET jádra - výuka 1 z 8
author: rick-anderson
description: Ukazuje, jak vytvořit aplikaci Razor Pages pomocí entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: data/ef-rp/intro
ms.openlocfilehash: 07faf5e596e7ea8b134d13caa0259c1e9d74ff1b
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661618"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="593ab-103">Razor stránky s jádrem entity framework u ASP.NET jádra - výuka 1 z 8</span><span class="sxs-lookup"><span data-stu-id="593ab-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="593ab-104">Tom [Dykstra](https://github.com/tdykstra) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="593ab-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="593ab-105">Toto je první z řady kurzů, které ukazují, jak používat Core entity Framework (EF) v [aplikaci ASP.NET Core Razor Pages.](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="593ab-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="593ab-106">Výukové programy vytvořit webové stránky pro fiktivní Contoso University.</span><span class="sxs-lookup"><span data-stu-id="593ab-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="593ab-107">Stránka obsahuje funkce, jako je přijetí studentů, tvorba kurzů a úkoly instruktora.</span><span class="sxs-lookup"><span data-stu-id="593ab-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="593ab-108">Kurz používá první přístup kódu.</span><span class="sxs-lookup"><span data-stu-id="593ab-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="593ab-109">Informace o následujícím kurzu pomocí prvního přístupu databáze naleznete v [tomto problému Github](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="593ab-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="593ab-110">Stáhněte si nebo zobrazte dokončenou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="593ab-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="593ab-111">[Stáhnout pokyny](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="593ab-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="593ab-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="593ab-112">Prerequisites</span></span>

* <span data-ttu-id="593ab-113">Pokud jste s Razor Pages nováčkem, projděte si před spuštěním této série série [Začínáme s břitvou stránek.](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="593ab-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="593ab-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="593ab-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="593ab-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="593ab-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="593ab-116">Databázové stroje</span><span class="sxs-lookup"><span data-stu-id="593ab-116">Database engines</span></span>

<span data-ttu-id="593ab-117">Pokyny sady Visual Studio používají [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), což je verze sql serveru Express, která je spuštěna pouze v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="593ab-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="593ab-118">Visual Studio kód pokyny použít [SQLite](https://www.sqlite.org/), databázový stroj pro více platforem.</span><span class="sxs-lookup"><span data-stu-id="593ab-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="593ab-119">Pokud se rozhodnete použít SQLite, stáhněte a nainstalujte nástroj třetí strany pro správu a prohlížení databáze SQLite, například [DB Browser pro SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="593ab-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="593ab-120">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="593ab-120">Troubleshooting</span></span>

<span data-ttu-id="593ab-121">Pokud narazíte na problém, který nelze vyřešit, porovnejte kód s [dokončeným projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="593ab-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="593ab-122">Dobrým způsobem, jak získat pomoc, je odeslání otázky do StackOverflow.com pomocí [značky ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [značky EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="593ab-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="593ab-123">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="593ab-123">The sample app</span></span>

<span data-ttu-id="593ab-124">Aplikace postavená v těchto kurzech je základní univerzitní web.</span><span class="sxs-lookup"><span data-stu-id="593ab-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="593ab-125">Uživatelé mohou zobrazit a aktualizovat informace o studentovi, kurzu a instruktorovi.</span><span class="sxs-lookup"><span data-stu-id="593ab-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="593ab-126">Zde je několik obrazovek vytvořených v tutoriálu.</span><span class="sxs-lookup"><span data-stu-id="593ab-126">Here are a few of the screens created in the tutorial.</span></span>

![Stránka Studentský index](intro/_static/students-index30.png)

![Stránka Úpravy studentů](intro/_static/student-edit30.png)

<span data-ttu-id="593ab-129">Styl uživatelského uživatelského nastavení tohoto webu je založen na předdefinovaných šablonách projektu.</span><span class="sxs-lookup"><span data-stu-id="593ab-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="593ab-130">Kurz se zaměřuje na to, jak používat EF Core, ne jak přizpůsobit ui.</span><span class="sxs-lookup"><span data-stu-id="593ab-130">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="593ab-131">Chcete-li získat zdrojový kód dokončeného projektu, postupujte podle odkazu v horní části stránky.</span><span class="sxs-lookup"><span data-stu-id="593ab-131">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="593ab-132">Složka *cu30* má kód pro ASP.NET verzi výukového programu Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="593ab-132">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="593ab-133">Soubory, které odrážejí stav kódu pro výukové programy 1-7 lze nalézt ve složce *cu30snapshots.*</span><span class="sxs-lookup"><span data-stu-id="593ab-133">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="593ab-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="593ab-134">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="593ab-135">Spuštění aplikace po stažení dokončeného projektu:</span><span class="sxs-lookup"><span data-stu-id="593ab-135">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="593ab-136">Odstraňte tři soubory a jednu složku, které mají *SQLite* v názvu.</span><span class="sxs-lookup"><span data-stu-id="593ab-136">Delete three files and one folder that have *SQLite* in the name.</span></span>
* <span data-ttu-id="593ab-137">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="593ab-137">Build the project.</span></span>
* <span data-ttu-id="593ab-138">V konzole Správce balíčků (PMC) spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="593ab-138">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="593ab-139">Spusťte projekt osiva databáze.</span><span class="sxs-lookup"><span data-stu-id="593ab-139">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="593ab-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="593ab-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="593ab-141">Spuštění aplikace po stažení dokončeného projektu:</span><span class="sxs-lookup"><span data-stu-id="593ab-141">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="593ab-142">Odstraňte *ContosoUniversity.csproj*a přejmenujte *ContosoUniversitySQLite.csproj* na *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="593ab-142">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="593ab-143">Odstraňte *Startup.cs*a přejmenujte *StartupSQLite.cs* na *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="593ab-143">Delete *Startup.cs*, and rename *StartupSQLite.cs* to *Startup.cs*.</span></span>
* <span data-ttu-id="593ab-144">Odstraňte *soubor appSettings.json*a přejmenujte *aplikaci SettingsSQLite.json* na *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="593ab-144">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="593ab-145">Odstraňte složku *Migrace* a přejmenujte *aplikaci MigrationsSQL* na *Migrace*.</span><span class="sxs-lookup"><span data-stu-id="593ab-145">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="593ab-146">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="593ab-146">Build the project.</span></span>
* <span data-ttu-id="593ab-147">Na příkazovém řádku ve složce projektu spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="593ab-147">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* <span data-ttu-id="593ab-148">V nástroji SQLite spusťte následující příkaz SQL:</span><span class="sxs-lookup"><span data-stu-id="593ab-148">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="593ab-149">Spusťte projekt osiva databáze.</span><span class="sxs-lookup"><span data-stu-id="593ab-149">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="593ab-150">Vytvoření projektu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="593ab-150">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="593ab-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="593ab-151">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="593ab-152">V nabídce **Soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="593ab-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="593ab-153">Vyberte **ASP.NET základní webovou aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="593ab-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="593ab-154">Název projektu *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="593ab-154">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="593ab-155">Je důležité použít tento přesný název včetně velkých písmen, takže obory názvů odpovídají kopírování a vložení kódu.</span><span class="sxs-lookup"><span data-stu-id="593ab-155">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="593ab-156">V rozevíracích souborech vyberte položku **.NET Core** a **ASP.NET Core 3.0** a pak vyberte **možnost Webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="593ab-156">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="593ab-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="593ab-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="593ab-158">V terminálu přejděte do složky, ve které má být vytvořena složka projektu.</span><span class="sxs-lookup"><span data-stu-id="593ab-158">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="593ab-159">Spusťte následující příkazy a vytvořte projekt Razor Pages a `cd` do nové složky projektu:</span><span class="sxs-lookup"><span data-stu-id="593ab-159">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="593ab-160">Nastavení stylu webu</span><span class="sxs-lookup"><span data-stu-id="593ab-160">Set up the site style</span></span>

<span data-ttu-id="593ab-161">Nastavení záhlaví webu, zápatí a nabídky můžete nastavit aktualizací *stránek/Sdílené/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="593ab-161">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="593ab-162">Změňte každý výskyt "ContosoUniversity" na "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="593ab-162">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="593ab-163">Existují tři výskyty.</span><span class="sxs-lookup"><span data-stu-id="593ab-163">There are three occurrences.</span></span>

* <span data-ttu-id="593ab-164">Odstraňte položky nabídky **Domů** a **Soukromí** a přidejte položky **pro informace**, **studenty**, **kurzy**, **instruktory**a **oddělení**.</span><span class="sxs-lookup"><span data-stu-id="593ab-164">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="593ab-165">Změny jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="593ab-165">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="593ab-166">V *pages/Index.cshtml*nahraďte obsah souboru následujícím kódem, který nahradí text o ASP.NET Core textem o této aplikaci:</span><span class="sxs-lookup"><span data-stu-id="593ab-166">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="593ab-167">Spusťte aplikaci a ověřte, zda se zobrazí domovská stránka.</span><span class="sxs-lookup"><span data-stu-id="593ab-167">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="593ab-168">Datový model</span><span class="sxs-lookup"><span data-stu-id="593ab-168">The data model</span></span>

<span data-ttu-id="593ab-169">V následujících částech se vytvoří datový model:</span><span class="sxs-lookup"><span data-stu-id="593ab-169">The following sections create a data model:</span></span>

![Diagram datového modelu zápisu do kurzu-studenta](intro/_static/data-model-diagram.png)

<span data-ttu-id="593ab-171">Student se může zapsat do libovolného počtu kurzů a v kurzu může být zapsán libovolný počet studentů.</span><span class="sxs-lookup"><span data-stu-id="593ab-171">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="593ab-172">Studentská entita</span><span class="sxs-lookup"><span data-stu-id="593ab-172">The Student entity</span></span>

![Diagram studentských entit](intro/_static/student-entity.png)

* <span data-ttu-id="593ab-174">Vytvořte složku *Modely* ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="593ab-174">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="593ab-175">Vytvořit *modely/Student.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="593ab-175">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="593ab-176">Vlastnost `ID` se stane sloupcem primárního klíče databázové tabulky, který odpovídá této třídě.</span><span class="sxs-lookup"><span data-stu-id="593ab-176">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="593ab-177">Ve výchozím nastavení EF Core interpretuje `ID` `classnameID` vlastnost, která je pojmenována nebo jako primární klíč.</span><span class="sxs-lookup"><span data-stu-id="593ab-177">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="593ab-178">Alternativní automaticky rozpoznaný `Student` název primárního klíče třídy je `StudentID`tedy .</span><span class="sxs-lookup"><span data-stu-id="593ab-178">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="593ab-179">Další informace naleznete v tématu [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="593ab-179">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="593ab-180">Vlastnost `Enrollments` je [navigační vlastnost](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="593ab-180">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="593ab-181">Navigační vlastnosti mají další entity, které souvisejí s touto entitou.</span><span class="sxs-lookup"><span data-stu-id="593ab-181">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="593ab-182">V tomto případě `Enrollments` vlastnost `Student` entity obsahuje všechny `Enrollment` entity, které souvisejí s tímto studentem.</span><span class="sxs-lookup"><span data-stu-id="593ab-182">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="593ab-183">Například pokud Student řádek v databázi obsahuje dva `Enrollments` související Zápis řádky, navigační vlastnost obsahuje tyto dvě entity Zápisu.</span><span class="sxs-lookup"><span data-stu-id="593ab-183">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="593ab-184">V databázi řádek Registrace souvisí s řádkem Student, pokud jeho sloupec ID studenta obsahuje hodnotu ID studenta.</span><span class="sxs-lookup"><span data-stu-id="593ab-184">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="593ab-185">Předpokládejme například, že řádek Student má ID=1.</span><span class="sxs-lookup"><span data-stu-id="593ab-185">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="593ab-186">Související řádky zápisu budou mít ID studenta = 1.</span><span class="sxs-lookup"><span data-stu-id="593ab-186">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="593ab-187">StudentID je *cizí klíč* v tabulce Registrace.</span><span class="sxs-lookup"><span data-stu-id="593ab-187">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="593ab-188">Vlastnost `Enrollments` je definována jako, `ICollection<Enrollment>` protože může existovat více souvisejících entit zápisu.</span><span class="sxs-lookup"><span data-stu-id="593ab-188">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="593ab-189">Můžete použít jiné typy kolekcí, například `List<Enrollment>` nebo `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="593ab-189">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="593ab-190">Při `ICollection<Enrollment>` použití EF Core `HashSet<Enrollment>` vytvoří kolekci ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="593ab-190">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="593ab-191">Entita Zápis</span><span class="sxs-lookup"><span data-stu-id="593ab-191">The Enrollment entity</span></span>

![Diagram entit zápisu](intro/_static/enrollment-entity.png)

<span data-ttu-id="593ab-193">Vytvořte *model/enrollment.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="593ab-193">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="593ab-194">Vlastnost `EnrollmentID` je primární klíč; tato entita `classnameID` používá `ID` vzor místo sebe.</span><span class="sxs-lookup"><span data-stu-id="593ab-194">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="593ab-195">Pro model produkčních dat zvolte jeden vzor a používejte jej konzistentně.</span><span class="sxs-lookup"><span data-stu-id="593ab-195">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="593ab-196">Tento kurz používá jak jen pro ilustraci, že obě práce.</span><span class="sxs-lookup"><span data-stu-id="593ab-196">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="593ab-197">Použití `ID` `classname` bez usnadňuje implementaci některé druhy změn datového modelu.</span><span class="sxs-lookup"><span data-stu-id="593ab-197">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="593ab-198">Ubytovací `Grade` zařízení `enum`je .</span><span class="sxs-lookup"><span data-stu-id="593ab-198">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="593ab-199">Otazník za `Grade` deklarací typu `Grade` označuje, že vlastnost je [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="593ab-199">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="593ab-200">Stupeň, který je null se&mdash;liší od nulové třídy null znamená, že platová třída není známa nebo ještě nebyla přiřazena.</span><span class="sxs-lookup"><span data-stu-id="593ab-200">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="593ab-201">Vlastnost `StudentID` je cizí klíč a odpovídající navigační `Student`vlastnost je .</span><span class="sxs-lookup"><span data-stu-id="593ab-201">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="593ab-202">Entita `Enrollment` je `Student` přidružena k jedné entitě, takže vlastnost obsahuje jednu `Student` entitu.</span><span class="sxs-lookup"><span data-stu-id="593ab-202">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="593ab-203">Vlastnost `CourseID` je cizí klíč a odpovídající navigační `Course`vlastnost je .</span><span class="sxs-lookup"><span data-stu-id="593ab-203">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="593ab-204">Entita `Enrollment` je `Course` přidružena k jedné entitě.</span><span class="sxs-lookup"><span data-stu-id="593ab-204">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="593ab-205">EF Core interpretuje vlastnost jako cizí klíč, pokud je pojmenována `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="593ab-205">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="593ab-206">Například`StudentID` je cizí klíč `Student` pro navigační vlastnost, protože je primární klíč `Student` entity `ID`.</span><span class="sxs-lookup"><span data-stu-id="593ab-206">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="593ab-207">Vlastnosti cizího klíče `<primary key property name>`lze také pojmenovat .</span><span class="sxs-lookup"><span data-stu-id="593ab-207">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="593ab-208">Například `CourseID` vzhledem `Course` k tomu, `CourseID`že primární klíč entity je .</span><span class="sxs-lookup"><span data-stu-id="593ab-208">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="593ab-209">Entita Kurzu</span><span class="sxs-lookup"><span data-stu-id="593ab-209">The Course entity</span></span>

![Diagram entity kurzu](intro/_static/course-entity.png)

<span data-ttu-id="593ab-211">Vytvořit *modely / Course.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="593ab-211">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="593ab-212">Vlastnost `Enrollments` je navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="593ab-212">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="593ab-213">Entita `Course` může souviset `Enrollment` s libovolným počtem entit.</span><span class="sxs-lookup"><span data-stu-id="593ab-213">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="593ab-214">Atribut `DatabaseGenerated` umožňuje aplikaci určit primární klíč, nikoli databázi, která jej generuje.</span><span class="sxs-lookup"><span data-stu-id="593ab-214">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="593ab-215">Sestavení projektu k ověření, že neexistují žádné chyby kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="593ab-215">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="593ab-216">Stránky studentů lešení</span><span class="sxs-lookup"><span data-stu-id="593ab-216">Scaffold Student pages</span></span>

<span data-ttu-id="593ab-217">V této části použijete nástroj ASP.NET generování generování pomocí nástroje generování pomocí ASP.NET základního lešení:</span><span class="sxs-lookup"><span data-stu-id="593ab-217">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="593ab-218">Ef Core *kontextové* třídy.</span><span class="sxs-lookup"><span data-stu-id="593ab-218">An EF Core *context* class.</span></span> <span data-ttu-id="593ab-219">Kontext je hlavní třída, která koordinuje entity framework funkce pro daný datový model.</span><span class="sxs-lookup"><span data-stu-id="593ab-219">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="593ab-220">Je odvozen z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="593ab-220">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="593ab-221">Holicí strojek stránky, které zpracovávají vytvořit, číst, aktualizovat a odstranit (CRUD) operace pro entitu. `Student`</span><span class="sxs-lookup"><span data-stu-id="593ab-221">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="593ab-222">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="593ab-222">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="593ab-223">Vytvořte složku *Studenti* ve složce *Stránky.*</span><span class="sxs-lookup"><span data-stu-id="593ab-223">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="593ab-224">V **Průzkumníku řešení**klepněte pravým tlačítkem myši na složku *Stránky/Studenti* a vyberte **přidat** > **novou scaffolded item**.</span><span class="sxs-lookup"><span data-stu-id="593ab-224">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="593ab-225">V dialogovém okně **Přidat písmo** vyberte **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span><span class="sxs-lookup"><span data-stu-id="593ab-225">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="593ab-226">V dialogovém **okně Přidat žiletky pomocí entity frameworku (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="593ab-226">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="593ab-227">V rozevíracím seznamu **Model třídy** vyberte **Student (ContosoUniversity.Models)**.</span><span class="sxs-lookup"><span data-stu-id="593ab-227">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="593ab-228">V řádku **třídy Kontext** **+** dat vyberte znaménko (plus).</span><span class="sxs-lookup"><span data-stu-id="593ab-228">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="593ab-229">Změňte název kontextu dat z *ContosoUniversity.Models.ContosoUniversityContext* na *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="593ab-229">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="593ab-230">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="593ab-230">Select **Add**.</span></span>

<span data-ttu-id="593ab-231">Automaticky se instalují následující balíčky:</span><span class="sxs-lookup"><span data-stu-id="593ab-231">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="593ab-232">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="593ab-232">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="593ab-233">Spusťte následující příkazy rozhraní PŘÍKAZOVÉHO PŘÍKAZU .NET Core k instalaci požadovaných balíčků NuGet:</span><span class="sxs-lookup"><span data-stu-id="593ab-233">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="593ab-234">Balíček Microsoft.VisualStudio.Web.CodeGeneration.Design je vyžadován pro generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="593ab-234">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="593ab-235">Přestože aplikace nebude používat SQL Server, nástroj generování uživatelského terminálu potřebuje balíček SQL Server.</span><span class="sxs-lookup"><span data-stu-id="593ab-235">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="593ab-236">Vytvořte složku *Stránky/Studenti.*</span><span class="sxs-lookup"><span data-stu-id="593ab-236">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="593ab-237">Spuštěním následujícího příkazu nainstalujte [nástroj lešení generátoru aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="593ab-237">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="593ab-238">Spusťte následující příkaz pro studentské stránky uživatelského lístí.</span><span class="sxs-lookup"><span data-stu-id="593ab-238">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="593ab-239">**V systému Windows**</span><span class="sxs-lookup"><span data-stu-id="593ab-239">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="593ab-240">**Na macOS nebo Linuxu**</span><span class="sxs-lookup"><span data-stu-id="593ab-240">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="593ab-241">Pokud máte problém s předchozím krokem, vytvořte projekt a opakujte krok sešitu.</span><span class="sxs-lookup"><span data-stu-id="593ab-241">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="593ab-242">Proces lešení:</span><span class="sxs-lookup"><span data-stu-id="593ab-242">The scaffolding process:</span></span>

* <span data-ttu-id="593ab-243">Vytvoří stránky Razor ve složce *Stránky/Studenti:*</span><span class="sxs-lookup"><span data-stu-id="593ab-243">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="593ab-244">*Create.cshtml* a *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="593ab-244">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="593ab-245">*Odstranit.cshtml* a *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="593ab-245">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="593ab-246">*Details.cshtml* a *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="593ab-246">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="593ab-247">*Edit.cshtml* a *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="593ab-247">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="593ab-248">*Index.cshtml* a *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="593ab-248">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="593ab-249">Vytvoří *data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="593ab-249">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="593ab-250">Přidá kontext vkládání závislostí v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="593ab-250">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="593ab-251">Přidá připojovací řetězec databáze do *souboru appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="593ab-251">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="593ab-252">Připojovací řetězec databáze</span><span class="sxs-lookup"><span data-stu-id="593ab-252">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="593ab-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="593ab-253">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="593ab-254">Připojovací řetězec určuje [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="593ab-254">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="593ab-255">LocalDB je odlehčená verze sql server express databázový stroj a je určen pro vývoj aplikací, nikoli produkční použití.</span><span class="sxs-lookup"><span data-stu-id="593ab-255">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="593ab-256">Ve výchozím nastavení localdb vytvoří soubory `C:/Users/<user>` *MDF* v adresáři.</span><span class="sxs-lookup"><span data-stu-id="593ab-256">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="593ab-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="593ab-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="593ab-258">Změňte připojovací řetězec tak, aby přectol na databázový soubor SQLite s názvem *CU.db*:</span><span class="sxs-lookup"><span data-stu-id="593ab-258">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="593ab-259">Aktualizace třídy kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="593ab-259">Update the database context class</span></span>

<span data-ttu-id="593ab-260">Hlavní třída, která koordinuje funkce EF Core pro daný datový model, je třída kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="593ab-260">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="593ab-261">Kontext je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="593ab-261">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="593ab-262">Kontext určuje, které entity jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="593ab-262">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="593ab-263">V tomto projektu je `SchoolContext`třída pojmenována .</span><span class="sxs-lookup"><span data-stu-id="593ab-263">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="593ab-264">Aktualizujte *SchoolContext.cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="593ab-264">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="593ab-265">Zvýrazněný kód vytvoří [vlastnost\<DbSet TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro každou sadu entit.</span><span class="sxs-lookup"><span data-stu-id="593ab-265">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="593ab-266">V ef core terminologii:</span><span class="sxs-lookup"><span data-stu-id="593ab-266">In EF Core terminology:</span></span>

* <span data-ttu-id="593ab-267">Sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="593ab-267">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="593ab-268">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="593ab-268">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="593ab-269">Vzhledem k tomu, že sada entit obsahuje více entit, měly by být vlastnosti DBSet plurálním jménem.</span><span class="sxs-lookup"><span data-stu-id="593ab-269">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="593ab-270">Vzhledem k tomu, že`Student` nástroj lešení vytvořil `Students`sadu DBSet, tento krok jej změní na množné číslo .</span><span class="sxs-lookup"><span data-stu-id="593ab-270">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="593ab-271">Chcete-li, aby kód Razor Pages odpovídal novému názvu DBSet, proveďte globální změnu v celém projektu `_context.Student` na . `_context.Students`</span><span class="sxs-lookup"><span data-stu-id="593ab-271">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="593ab-272">Existuje 8 výskytů.</span><span class="sxs-lookup"><span data-stu-id="593ab-272">There are 8 occurrences.</span></span>

<span data-ttu-id="593ab-273">Sestavení projektu k ověření, že neexistují žádné chyby kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="593ab-273">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="593ab-274">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="593ab-274">Startup.cs</span></span>

<span data-ttu-id="593ab-275">ASP.NET Core je postaven s [vstřikování závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="593ab-275">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="593ab-276">Služby (například kontext databáze EF Core) jsou registrovány s vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="593ab-276">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="593ab-277">Součásti, které vyžadují tyto služby (například Razor Pages) jsou poskytovány tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="593ab-277">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="593ab-278">Kód konstruktoru, který získá instanci kontextu databáze je zobrazen dále v kurzu.</span><span class="sxs-lookup"><span data-stu-id="593ab-278">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="593ab-279">Nástroj pro vytváření uživatelského funkce automaticky zaregistroval třídu kontextu s kontejnerem vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="593ab-279">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="593ab-280">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="593ab-280">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="593ab-281">V `ConfigureServices`aplikace byly zvýrazněné řádky přidány lešením:</span><span class="sxs-lookup"><span data-stu-id="593ab-281">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="593ab-282">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="593ab-282">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="593ab-283">V `ConfigureServices`oblasti se ujistěte, že kód `UseSqlite`přidaný voláním lešení .</span><span class="sxs-lookup"><span data-stu-id="593ab-283">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="593ab-284">Název připojovacího řetězce je předán do kontextu voláním metody na [dbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="593ab-284">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="593ab-285">Pro místní vývoj [ASP.NET konfiguračnísystém Core](xref:fundamentals/configuration/index) přečte připojovací řetězec ze souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="593ab-285">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="593ab-286">Vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="593ab-286">Create the database</span></span>

<span data-ttu-id="593ab-287">Aktualizace *Program.cs* a vytvořte databázi, pokud neexistuje:</span><span class="sxs-lookup"><span data-stu-id="593ab-287">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="593ab-288">[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) Metoda neprovede žádnou akci, pokud existuje databáze pro kontext.</span><span class="sxs-lookup"><span data-stu-id="593ab-288">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="593ab-289">Pokud neexistuje žádná databáze, vytvoří databázi a schéma.</span><span class="sxs-lookup"><span data-stu-id="593ab-289">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="593ab-290">`EnsureCreated`umožňuje následující pracovní postup pro zpracování změn datového modelu:</span><span class="sxs-lookup"><span data-stu-id="593ab-290">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="593ab-291">Odstraňte databázi.</span><span class="sxs-lookup"><span data-stu-id="593ab-291">Delete the database.</span></span> <span data-ttu-id="593ab-292">Všechna existující data budou ztracena.</span><span class="sxs-lookup"><span data-stu-id="593ab-292">Any existing data is lost.</span></span>
* <span data-ttu-id="593ab-293">Změňte datový model.</span><span class="sxs-lookup"><span data-stu-id="593ab-293">Change the data model.</span></span> <span data-ttu-id="593ab-294">Přidejte například `EmailAddress` pole.</span><span class="sxs-lookup"><span data-stu-id="593ab-294">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="593ab-295">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="593ab-295">Run the app.</span></span>
* <span data-ttu-id="593ab-296">`EnsureCreated`vytvoří databázi s novým schématem.</span><span class="sxs-lookup"><span data-stu-id="593ab-296">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="593ab-297">Tento pracovní postup funguje dobře brzy ve vývoji, když se schéma rychle vyvíjí, pokud nepotřebujete zachovat data.</span><span class="sxs-lookup"><span data-stu-id="593ab-297">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="593ab-298">Situace se liší, když je třeba zachovat data, která byla zadána do databáze.</span><span class="sxs-lookup"><span data-stu-id="593ab-298">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="593ab-299">V takovém případě použijte migrace.</span><span class="sxs-lookup"><span data-stu-id="593ab-299">When that is the case, use migrations.</span></span>

<span data-ttu-id="593ab-300">Později v sérii kurzů odstraníte databázi, která byla `EnsureCreated` vytvořena, a místo toho použijete migrace.</span><span class="sxs-lookup"><span data-stu-id="593ab-300">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="593ab-301">Databázi, která `EnsureCreated` je vytvořena, nelze aktualizovat pomocí migrace.</span><span class="sxs-lookup"><span data-stu-id="593ab-301">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="593ab-302">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="593ab-302">Test the app</span></span>

* <span data-ttu-id="593ab-303">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="593ab-303">Run the app.</span></span>
* <span data-ttu-id="593ab-304">Vyberte odkaz **Studenti** a pak **Vytvořit nový**.</span><span class="sxs-lookup"><span data-stu-id="593ab-304">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="593ab-305">Otestujte odkazy Upravit, Podrobnosti a Odstranit.</span><span class="sxs-lookup"><span data-stu-id="593ab-305">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="593ab-306">Osivo databáze</span><span class="sxs-lookup"><span data-stu-id="593ab-306">Seed the database</span></span>

<span data-ttu-id="593ab-307">Metoda `EnsureCreated` vytvoří prázdnou databázi.</span><span class="sxs-lookup"><span data-stu-id="593ab-307">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="593ab-308">Tato část přidá kód, který naplní databázi testovacími daty.</span><span class="sxs-lookup"><span data-stu-id="593ab-308">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="593ab-309">Vytvořte *data/DbInitializer.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="593ab-309">Create *Data/DbInitializer.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="593ab-310">Kód zkontroluje, zda jsou v databázi nějaké studenty.</span><span class="sxs-lookup"><span data-stu-id="593ab-310">The code checks if there are any students in the database.</span></span> <span data-ttu-id="593ab-311">Pokud neexistují žádné studenty, přidá testovací data do databáze.</span><span class="sxs-lookup"><span data-stu-id="593ab-311">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="593ab-312">Vytvoří testovací data v polích, nikoli `List<T>` v kolekcích pro optimalizaci výkonu.</span><span class="sxs-lookup"><span data-stu-id="593ab-312">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="593ab-313">V *Program.cs*nahraďte `EnsureCreated` hovor voláním: `DbInitializer.Initialize`</span><span class="sxs-lookup"><span data-stu-id="593ab-313">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="593ab-314">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="593ab-314">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="593ab-315">Zastavte aplikaci, pokud je spuštěná, a spusťte následující příkaz v **konzole Správce balíčků** (PMC):</span><span class="sxs-lookup"><span data-stu-id="593ab-315">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="593ab-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="593ab-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="593ab-317">Zastavte aplikaci, pokud je spuštěná, a odstraňte soubor *CU.db.*</span><span class="sxs-lookup"><span data-stu-id="593ab-317">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="593ab-318">Restartujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="593ab-318">Restart the app.</span></span>

* <span data-ttu-id="593ab-319">Výběrem stránky Studenti zobrazíte osývaná data.</span><span class="sxs-lookup"><span data-stu-id="593ab-319">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="593ab-320">Zobrazit databázi</span><span class="sxs-lookup"><span data-stu-id="593ab-320">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="593ab-321">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="593ab-321">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="593ab-322">Otevřete **Průzkumníka objektů SERVERU SQL Server** (SSOX) z nabídky **Zobrazení** v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="593ab-322">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="593ab-323">Ve SSOX vyberte **(localdb)\MSSQLLocalDB > databáze > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="593ab-323">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="593ab-324">Název databáze je generován z názvu kontextu, který jste zadali dříve plus pomlčka a GUID.</span><span class="sxs-lookup"><span data-stu-id="593ab-324">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="593ab-325">Rozbalte uzel **Tabulky.**</span><span class="sxs-lookup"><span data-stu-id="593ab-325">Expand the **Tables** node.</span></span>
* <span data-ttu-id="593ab-326">Klikněte pravým tlačítkem myši na tabulku **Student** a kliknutím na **Zobrazit data** zobrazte vytvořené sloupce a řádky vložené do tabulky.</span><span class="sxs-lookup"><span data-stu-id="593ab-326">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="593ab-327">Klikněte pravým tlačítkem myši na tabulku **Student** a `Student` kliknutím na Zobrazit **kód** zobrazíte, `Student` jak se model mapuje na schéma tabulky.</span><span class="sxs-lookup"><span data-stu-id="593ab-327">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="593ab-328">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="593ab-328">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="593ab-329">Pomocí nástroje SQLite můžete zobrazit schéma databáze a osiva dat.</span><span class="sxs-lookup"><span data-stu-id="593ab-329">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="593ab-330">Databázový soubor má název *CU.db* a je umístěn ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="593ab-330">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="593ab-331">Asynchronní kód</span><span class="sxs-lookup"><span data-stu-id="593ab-331">Asynchronous code</span></span>

<span data-ttu-id="593ab-332">Asynchronní programování je výchozí režim pro ASP.NET Core a EF Core.</span><span class="sxs-lookup"><span data-stu-id="593ab-332">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="593ab-333">Webový server má omezený počet vláken k dispozici a v situacích s vysokým zatížením všechny dostupné podprocesy mohou být používány.</span><span class="sxs-lookup"><span data-stu-id="593ab-333">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="593ab-334">V takovém případě server nemůže zpracovat nové požadavky, dokud vlákna jsou uvolněny.</span><span class="sxs-lookup"><span data-stu-id="593ab-334">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="593ab-335">Pomocí synchronního kódu může být mnoho vláken svázáno, zatímco ve skutečnosti nepracují, protože čekají na dokončení vstupně-up.</span><span class="sxs-lookup"><span data-stu-id="593ab-335">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="593ab-336">S asynchronním kódem, když proces čeká na dokončení vstupně-va, jeho vlákno je uvolněno pro server pro zpracování jiných požadavků.</span><span class="sxs-lookup"><span data-stu-id="593ab-336">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="593ab-337">V důsledku toho asynchronní kód umožňuje efektivnější použití prostředků serveru a server může zpracovat větší provoz bez zpoždění.</span><span class="sxs-lookup"><span data-stu-id="593ab-337">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="593ab-338">Asynchronní kód zavést malé množství režie v době běhu.</span><span class="sxs-lookup"><span data-stu-id="593ab-338">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="593ab-339">V situacích s nízkým provozem je zásah ový výkon zanedbatelný, zatímco v situacích s vysokým provozem je potenciální zlepšení výkonu značné.</span><span class="sxs-lookup"><span data-stu-id="593ab-339">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="593ab-340">V následujícím kódu [asynchronní](/dotnet/csharp/language-reference/keywords/async) `Task<T>` klíčové slovo, vrácená hodnota, `await` klíčové slovo a `ToListAsync` metoda provést spuštění kódu asynchronně.</span><span class="sxs-lookup"><span data-stu-id="593ab-340">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="593ab-341">Klíčové `async` slovo říká kompilátoru:</span><span class="sxs-lookup"><span data-stu-id="593ab-341">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="593ab-342">Generovat zpětná volání pro části těla metody.</span><span class="sxs-lookup"><span data-stu-id="593ab-342">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="593ab-343">Vytvořte [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) objekt, který je vrácen.</span><span class="sxs-lookup"><span data-stu-id="593ab-343">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="593ab-344">Návratový `Task<T>` typ představuje probíhající práci.</span><span class="sxs-lookup"><span data-stu-id="593ab-344">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="593ab-345">Klíčové `await` slovo způsobí, že kompilátor rozdělit metodu do dvou částí.</span><span class="sxs-lookup"><span data-stu-id="593ab-345">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="593ab-346">První část končí operací, která byla spuštěna asynchronně.</span><span class="sxs-lookup"><span data-stu-id="593ab-346">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="593ab-347">Druhá část je umístěn a callback metoda, která je volána po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="593ab-347">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="593ab-348">`ToListAsync`je asynchronní verze metody `ToList` rozšíření.</span><span class="sxs-lookup"><span data-stu-id="593ab-348">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="593ab-349">Některé věci, které je třeba si uvědomit při psaní asynchronního kódu, který používá EF Core:</span><span class="sxs-lookup"><span data-stu-id="593ab-349">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="593ab-350">Pouze příkazy, které způsobují dotazy nebo příkazy, které mají být odeslány do databáze jsou prováděny asynchronně.</span><span class="sxs-lookup"><span data-stu-id="593ab-350">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="593ab-351">To `ToListAsync`zahrnuje `SingleOrDefaultAsync` `FirstOrDefaultAsync`, `SaveChangesAsync`, , a .</span><span class="sxs-lookup"><span data-stu-id="593ab-351">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="593ab-352">Neobsahuje příkazy, které pouze `IQueryable`změnit `var students = context.Students.Where(s => s.LastName == "Davolio")`, například .</span><span class="sxs-lookup"><span data-stu-id="593ab-352">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="593ab-353">Kontext EF Core není bezpečný pro přístup z více vláken: nepokoušejte se provést více operací paralelně.</span><span class="sxs-lookup"><span data-stu-id="593ab-353">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="593ab-354">Chcete-li využít výhod výkonu asynchronního kódu, ověřte, zda balíčky knihovny (například pro stránkování) používají asynchronní, pokud volají metody EF Core, které odesílají dotazy do databáze.</span><span class="sxs-lookup"><span data-stu-id="593ab-354">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="593ab-355">Další informace o asynchronním programování v rozhraní .NET naleznete v [tématu Async Overview](/dotnet/standard/async) a [Asynchronní programování s async a await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="593ab-355">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="593ab-356">Další kroky</span><span class="sxs-lookup"><span data-stu-id="593ab-356">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="593ab-357">Další kurz</span><span class="sxs-lookup"><span data-stu-id="593ab-357">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="593ab-358">Ukázková webová aplikace Contoso University ukazuje, jak vytvořit aplikaci ASP.NET Core Razor Pages pomocí core frameworku entit.Contoso University ukazuje, jak vytvořit aplikaci ASP.NET Core Razor Pages pomocí core entity framework (EF).</span><span class="sxs-lookup"><span data-stu-id="593ab-358">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="593ab-359">Ukázková aplikace je webová stránka fiktivní univerzity Contoso.</span><span class="sxs-lookup"><span data-stu-id="593ab-359">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="593ab-360">Zahrnuje funkce, jako je přijetí studentů, tvorba kurzů a úkoly instruktora.</span><span class="sxs-lookup"><span data-stu-id="593ab-360">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="593ab-361">Tato stránka je první z řady kurzů, které vysvětlují, jak vytvořit ukázkovou aplikaci Contoso University.</span><span class="sxs-lookup"><span data-stu-id="593ab-361">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="593ab-362">Stáhněte si nebo zobrazte dokončenou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="593ab-362">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="593ab-363">[Stáhnout pokyny](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="593ab-363">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="593ab-364">Požadavky</span><span class="sxs-lookup"><span data-stu-id="593ab-364">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="593ab-365">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="593ab-365">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="593ab-366">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="593ab-366">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="593ab-367">Znalost razor [stránek](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="593ab-367">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="593ab-368">Noví programátoři by měli před zahájením této série dokončit [začínáme se stránkami Razor Pages.](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="593ab-368">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="593ab-369">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="593ab-369">Troubleshooting</span></span>

<span data-ttu-id="593ab-370">Pokud narazíte na problém, který nelze vyřešit, můžete obecně najít řešení porovnáním kódu s [dokončeným projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="593ab-370">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="593ab-371">Dobrým způsobem, jak získat pomoc, je zveřejnění matné otázky [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) pro [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="593ab-371">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="593ab-372">Webová aplikace Univerzity Contoso</span><span class="sxs-lookup"><span data-stu-id="593ab-372">The Contoso University web app</span></span>

<span data-ttu-id="593ab-373">Aplikace postavená v těchto kurzech je základní univerzitní web.</span><span class="sxs-lookup"><span data-stu-id="593ab-373">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="593ab-374">Uživatelé mohou zobrazit a aktualizovat informace o studentovi, kurzu a instruktorovi.</span><span class="sxs-lookup"><span data-stu-id="593ab-374">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="593ab-375">Zde je několik obrazovek vytvořených v tutoriálu.</span><span class="sxs-lookup"><span data-stu-id="593ab-375">Here are a few of the screens created in the tutorial.</span></span>

![Stránka Studentský index](intro/_static/students-index.png)

![Stránka Úpravy studentů](intro/_static/student-edit.png)

<span data-ttu-id="593ab-378">Styl uživatelského uživatelského nastavení tohoto webu se blíží tomu, co je generováno vestavěné šablony.</span><span class="sxs-lookup"><span data-stu-id="593ab-378">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="593ab-379">Zaměření kurzu je na EF Core s Razor Pages, ne na ui.</span><span class="sxs-lookup"><span data-stu-id="593ab-379">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-razor-pages-web-app"></a><span data-ttu-id="593ab-380">Vytvořte webovou aplikaci ContosoUniversity Razor Pages</span><span class="sxs-lookup"><span data-stu-id="593ab-380">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="593ab-381">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="593ab-381">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="593ab-382">V nabídce **Soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="593ab-382">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="593ab-383">Vytvořte novou ASP.NET základní webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="593ab-383">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="593ab-384">Název projektu **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="593ab-384">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="593ab-385">Je důležité pojmenovat projekt *ContosoUniversity,* aby se obory názvů shodovaly s kopírováním/vložením kódu.</span><span class="sxs-lookup"><span data-stu-id="593ab-385">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="593ab-386">V rozevíracím souboru vyberte **ASP.NET Core 2.1** a pak vyberte **možnost Webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="593ab-386">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="593ab-387">Obrázky z předchozích kroků najdete v [tématu Vytvoření webové aplikace Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="593ab-387">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="593ab-388">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="593ab-388">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="593ab-389">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="593ab-389">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="593ab-390">Nastavení stylu webu</span><span class="sxs-lookup"><span data-stu-id="593ab-390">Set up the site style</span></span>

<span data-ttu-id="593ab-391">Několik změn nastavilo nabídku webu, rozložení a domovskou stránku.</span><span class="sxs-lookup"><span data-stu-id="593ab-391">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="593ab-392">Aktualizovat *stránky/Sdílené/_Layout.cshtml* s následujícími změnami:</span><span class="sxs-lookup"><span data-stu-id="593ab-392">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="593ab-393">Změňte každý výskyt "ContosoUniversity" na "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="593ab-393">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="593ab-394">Existují tři výskyty.</span><span class="sxs-lookup"><span data-stu-id="593ab-394">There are three occurrences.</span></span>

* <span data-ttu-id="593ab-395">Přidejte položky nabídky pro **studenty**, **kurzy**, **instruktory**a **oddělení**a odstraňte položku nabídky **Kontakt.**</span><span class="sxs-lookup"><span data-stu-id="593ab-395">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="593ab-396">Změny jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="593ab-396">The changes are highlighted.</span></span> <span data-ttu-id="593ab-397">(Všechny značky *se* nezobrazí.)</span><span class="sxs-lookup"><span data-stu-id="593ab-397">(All the markup is *not* displayed.)</span></span>

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="593ab-398">V *pages/Index.cshtml*nahraďte obsah souboru následujícím kódem, který nahradí text o ASP.NET a MVC textem o této aplikaci:</span><span class="sxs-lookup"><span data-stu-id="593ab-398">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="593ab-399">Vytvoření datového modelu</span><span class="sxs-lookup"><span data-stu-id="593ab-399">Create the data model</span></span>

<span data-ttu-id="593ab-400">Vytvořte třídy entit pro aplikaci Contoso University.</span><span class="sxs-lookup"><span data-stu-id="593ab-400">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="593ab-401">Začněte s následujícími třemi entitami:</span><span class="sxs-lookup"><span data-stu-id="593ab-401">Start with the following three entities:</span></span>

![Diagram datového modelu zápisu do kurzu-studenta](intro/_static/data-model-diagram.png)

<span data-ttu-id="593ab-403">Existuje vztah 1:N mezi `Student` entitami. `Enrollment`</span><span class="sxs-lookup"><span data-stu-id="593ab-403">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="593ab-404">Existuje vztah 1:N mezi `Course` entitami. `Enrollment`</span><span class="sxs-lookup"><span data-stu-id="593ab-404">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="593ab-405">Student se může zapsat do libovolného počtu kurzů.</span><span class="sxs-lookup"><span data-stu-id="593ab-405">A student can enroll in any number of courses.</span></span> <span data-ttu-id="593ab-406">Kurz může mít libovolný počet studentů zapsaných v něm.</span><span class="sxs-lookup"><span data-stu-id="593ab-406">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="593ab-407">V následujících částech je vytvořena třída pro každou z těchto entit.</span><span class="sxs-lookup"><span data-stu-id="593ab-407">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="593ab-408">Studentská entita</span><span class="sxs-lookup"><span data-stu-id="593ab-408">The Student entity</span></span>

![Diagram studentských entit](intro/_static/student-entity.png)

<span data-ttu-id="593ab-410">Vytvořte složku *Modely.*</span><span class="sxs-lookup"><span data-stu-id="593ab-410">Create a *Models* folder.</span></span> <span data-ttu-id="593ab-411">Ve složce *Modely* vytvořte soubor třídy s názvem *Student.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="593ab-411">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="593ab-412">Vlastnost `ID` se stane sloupcem primárního klíče tabulky databáze (DB), která odpovídá této třídě.</span><span class="sxs-lookup"><span data-stu-id="593ab-412">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="593ab-413">Ve výchozím nastavení EF Core interpretuje `ID` `classnameID` vlastnost, která je pojmenována nebo jako primární klíč.</span><span class="sxs-lookup"><span data-stu-id="593ab-413">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="593ab-414">V `classnameID` `classname` , je název třídy.</span><span class="sxs-lookup"><span data-stu-id="593ab-414">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="593ab-415">Alternativní automaticky rozpoznaný primární klíč je `StudentID` v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="593ab-415">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="593ab-416">Vlastnost `Enrollments` je [navigační vlastnost](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="593ab-416">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="593ab-417">Navigační vlastnosti odkazují na jiné entity, které souvisejí s touto entitou.</span><span class="sxs-lookup"><span data-stu-id="593ab-417">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="593ab-418">V tomto případě `Enrollments` vlastnost `Student entity` a obsahuje `Enrollment` všechny entity, které `Student`s tím souvisejí .</span><span class="sxs-lookup"><span data-stu-id="593ab-418">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="593ab-419">Například pokud Student řádek v DB má dva `Enrollments` související Zápis řádky, navigační vlastnost obsahuje tyto dvě `Enrollment` entity.</span><span class="sxs-lookup"><span data-stu-id="593ab-419">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="593ab-420">Související `Enrollment` řádek je řádek, který obsahuje hodnotu primárního klíče studenta ve sloupci. `StudentID`</span><span class="sxs-lookup"><span data-stu-id="593ab-420">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="593ab-421">Předpokládejme například, že student s ID= `Enrollment` 1 má v tabulce dva řádky.</span><span class="sxs-lookup"><span data-stu-id="593ab-421">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="593ab-422">Tabulka `Enrollment` má dva `StudentID` řádky s = 1.</span><span class="sxs-lookup"><span data-stu-id="593ab-422">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="593ab-423">`StudentID`je cizí klíč `Enrollment` v tabulce, který určuje `Student` studenta v tabulce.</span><span class="sxs-lookup"><span data-stu-id="593ab-423">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="593ab-424">Pokud vlastnost navigace může obsahovat více entit, vlastnost navigace `ICollection<T>`musí být typ seznamu, například .</span><span class="sxs-lookup"><span data-stu-id="593ab-424">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="593ab-425">`ICollection<T>`nebo typ, jako je `List<T>` nebo `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="593ab-425">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="593ab-426">Při `ICollection<T>` použití EF Core `HashSet<T>` vytvoří kolekci ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="593ab-426">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="593ab-427">Navigační vlastnosti, které mají více entit, pocházejí ze vztahů N:N a 1:N.</span><span class="sxs-lookup"><span data-stu-id="593ab-427">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="593ab-428">Entita Zápis</span><span class="sxs-lookup"><span data-stu-id="593ab-428">The Enrollment entity</span></span>

![Diagram entit zápisu](intro/_static/enrollment-entity.png)

<span data-ttu-id="593ab-430">Ve složce *Modely* vytvořte *Enrollment.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="593ab-430">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="593ab-431">Vlastnost `EnrollmentID` je primární klíč.</span><span class="sxs-lookup"><span data-stu-id="593ab-431">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="593ab-432">Tato entita `classnameID` používá `ID` vzor `Student` místo jako entita.</span><span class="sxs-lookup"><span data-stu-id="593ab-432">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="593ab-433">Vývojáři obvykle zvolit jeden vzor a použít jej v celém datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="593ab-433">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="593ab-434">V pozdějším kurzu pomocí ID bez názvu třídy je zobrazeno usnadnit implementaci dědičnosti v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="593ab-434">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="593ab-435">Ubytovací `Grade` zařízení `enum`je .</span><span class="sxs-lookup"><span data-stu-id="593ab-435">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="593ab-436">Otazník za `Grade` deklarací typu `Grade` označuje, že vlastnost je nullable.</span><span class="sxs-lookup"><span data-stu-id="593ab-436">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="593ab-437">Stupeň, který je null se liší od nulové třídy - null znamená, že platová třída není známa nebo ještě nebyla přiřazena.</span><span class="sxs-lookup"><span data-stu-id="593ab-437">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="593ab-438">Vlastnost `StudentID` je cizí klíč a odpovídající navigační `Student`vlastnost je .</span><span class="sxs-lookup"><span data-stu-id="593ab-438">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="593ab-439">Entita `Enrollment` je `Student` přidružena k jedné entitě, takže vlastnost obsahuje jednu `Student` entitu.</span><span class="sxs-lookup"><span data-stu-id="593ab-439">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="593ab-440">Entita `Student` se `Student.Enrollments` liší od navigační `Enrollment` vlastnosti, která obsahuje více entit.</span><span class="sxs-lookup"><span data-stu-id="593ab-440">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="593ab-441">Vlastnost `CourseID` je cizí klíč a odpovídající navigační `Course`vlastnost je .</span><span class="sxs-lookup"><span data-stu-id="593ab-441">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="593ab-442">Entita `Enrollment` je `Course` přidružena k jedné entitě.</span><span class="sxs-lookup"><span data-stu-id="593ab-442">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="593ab-443">EF Core interpretuje vlastnost jako cizí klíč, pokud je pojmenována `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="593ab-443">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="593ab-444">Například`StudentID` pro `Student` navigační vlastnost, `Student` protože je primární `ID`klíč entity .</span><span class="sxs-lookup"><span data-stu-id="593ab-444">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="593ab-445">Vlastnosti cizího klíče `<primary key property name>`lze také pojmenovat .</span><span class="sxs-lookup"><span data-stu-id="593ab-445">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="593ab-446">Například `CourseID` vzhledem `Course` k tomu, `CourseID`že primární klíč entity je .</span><span class="sxs-lookup"><span data-stu-id="593ab-446">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="593ab-447">Entita Kurzu</span><span class="sxs-lookup"><span data-stu-id="593ab-447">The Course entity</span></span>

![Diagram entity kurzu](intro/_static/course-entity.png)

<span data-ttu-id="593ab-449">Ve složce *Modely* vytvořte *Course.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="593ab-449">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="593ab-450">Vlastnost `Enrollments` je navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="593ab-450">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="593ab-451">Entita `Course` může souviset `Enrollment` s libovolným počtem entit.</span><span class="sxs-lookup"><span data-stu-id="593ab-451">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="593ab-452">Atribut `DatabaseGenerated` umožňuje aplikaci určit primární klíč, nikoli ho vygenerovat DB.</span><span class="sxs-lookup"><span data-stu-id="593ab-452">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="593ab-453">Lešení studentský model</span><span class="sxs-lookup"><span data-stu-id="593ab-453">Scaffold the student model</span></span>

<span data-ttu-id="593ab-454">V této části je model studenta lešený.</span><span class="sxs-lookup"><span data-stu-id="593ab-454">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="593ab-455">To znamená, že nástroj lešení vytváří stránky pro operace Vytvořit, Číst, Aktualizovat a Odstranit (CRUD) pro model studenta.</span><span class="sxs-lookup"><span data-stu-id="593ab-455">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="593ab-456">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="593ab-456">Build the project.</span></span>
* <span data-ttu-id="593ab-457">Vytvořte složku *Stránky/Studenti.*</span><span class="sxs-lookup"><span data-stu-id="593ab-457">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="593ab-458">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="593ab-458">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="593ab-459">V **Průzkumníku řešení**klikněte pravým tlačítkem myši na složku *Stránky/Studenti* > **Přidat** > **novou scaffolded položku**.</span><span class="sxs-lookup"><span data-stu-id="593ab-459">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="593ab-460">V dialogovém okně **Přidat písmo** vyberte **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span><span class="sxs-lookup"><span data-stu-id="593ab-460">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="593ab-461">Dokončete dialogové okno **Přidat žiletky pomocí entity frameworku (CRUD):**</span><span class="sxs-lookup"><span data-stu-id="593ab-461">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="593ab-462">V rozevíracím seznamu **Model třídy** vyberte **Student (ContosoUniversity.Models)**.</span><span class="sxs-lookup"><span data-stu-id="593ab-462">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="593ab-463">V řádku **třídy Kontext** **+** dat vyberte znaménko (plus) a změňte generovaný název na **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="593ab-463">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="593ab-464">V rozevíracím souboru **třídy Kontext dat** vyberte **ContosoUniversity.Models.SchoolContext.**</span><span class="sxs-lookup"><span data-stu-id="593ab-464">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="593ab-465">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="593ab-465">Select **Add**.</span></span>

![DIALOGOVÉ OKNO CRUD](intro/_static/s1.png)

<span data-ttu-id="593ab-467">Pokud máte problém s předchozím krokem, přečtěte si zobrazit scénář [k saffold filmového modelu.](xref:tutorials/razor-pages/model#scaffold-the-movie-model)</span><span class="sxs-lookup"><span data-stu-id="593ab-467">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="593ab-468">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="593ab-468">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="593ab-469">Spusťte následující příkazy pro zasouvací vzor studenta.</span><span class="sxs-lookup"><span data-stu-id="593ab-469">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="593ab-470">Proces vytváření uživatelského příkazu vytvořil a změnil následující soubory:</span><span class="sxs-lookup"><span data-stu-id="593ab-470">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="593ab-471">Vytvořené soubory</span><span class="sxs-lookup"><span data-stu-id="593ab-471">Files created</span></span>

* <span data-ttu-id="593ab-472">*Stránky/Studenti* Vytvořit, odstranit, podrobnosti, upravit, indexovat.</span><span class="sxs-lookup"><span data-stu-id="593ab-472">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="593ab-473">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="593ab-473">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="593ab-474">Aktualizace souborů</span><span class="sxs-lookup"><span data-stu-id="593ab-474">File updates</span></span>

* <span data-ttu-id="593ab-475">*Startup.cs* : Změny tohoto souboru jsou podrobně popsány v další části.</span><span class="sxs-lookup"><span data-stu-id="593ab-475">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="593ab-476">*appsettings.json* : Připojovací řetězec používaný pro připojení k místní databázi je přidán.</span><span class="sxs-lookup"><span data-stu-id="593ab-476">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="593ab-477">Prozkoumejte kontext registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="593ab-477">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="593ab-478">ASP.NET Core je postaven s [vstřikování závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="593ab-478">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="593ab-479">Služby (například kontext EF Core DB) jsou registrovány s vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="593ab-479">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="593ab-480">Součásti, které vyžadují tyto služby (například Razor Pages) jsou poskytovány tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="593ab-480">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="593ab-481">Kód konstruktoru, který získá instanci kontextu db, je zobrazen dále v kurzu.</span><span class="sxs-lookup"><span data-stu-id="593ab-481">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="593ab-482">Nástroj pro vytváření uživatelského funkce automaticky vytvořil kontext DB a zaregistroval jej do kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="593ab-482">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="593ab-483">Zkontrolujte `ConfigureServices` metodu v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="593ab-483">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="593ab-484">Zvýrazněný řádek byl přidán lešení:</span><span class="sxs-lookup"><span data-stu-id="593ab-484">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="593ab-485">Název připojovacího řetězce je předán do kontextu voláním metody na [dbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="593ab-485">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="593ab-486">Pro místní vývoj [ASP.NET konfiguračnísystém Core](xref:fundamentals/configuration/index) přečte připojovací řetězec ze souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="593ab-486">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="593ab-487">Aktualizovat hlavní</span><span class="sxs-lookup"><span data-stu-id="593ab-487">Update main</span></span>

<span data-ttu-id="593ab-488">V *Program.cs*upravte metodu `Main` takto:</span><span class="sxs-lookup"><span data-stu-id="593ab-488">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="593ab-489">Získejte kontextové instance DB z kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="593ab-489">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="593ab-490">Volání [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="593ab-490">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="593ab-491">Po dokončení `EnsureCreated` metody zlikvidujte kontext.</span><span class="sxs-lookup"><span data-stu-id="593ab-491">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="593ab-492">Následující kód zobrazuje aktualizovaný *soubor Program.cs.*</span><span class="sxs-lookup"><span data-stu-id="593ab-492">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="593ab-493">`EnsureCreated`zajišťuje, že databáze pro kontext existuje.</span><span class="sxs-lookup"><span data-stu-id="593ab-493">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="593ab-494">Pokud existuje, není provedena žádná akce.</span><span class="sxs-lookup"><span data-stu-id="593ab-494">If it exists, no action is taken.</span></span> <span data-ttu-id="593ab-495">Pokud neexistuje, jsou vytvořeny databáze a všechny jeho schéma.</span><span class="sxs-lookup"><span data-stu-id="593ab-495">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="593ab-496">`EnsureCreated`nepoužívá migrace k vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="593ab-496">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="593ab-497">Databázi, která `EnsureCreated` je vytvořena pomocí aplikace, nelze později aktualizovat pomocí migrace.</span><span class="sxs-lookup"><span data-stu-id="593ab-497">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="593ab-498">`EnsureCreated`se nazývá spuštění aplikace, což umožňuje následující pracovní tok:</span><span class="sxs-lookup"><span data-stu-id="593ab-498">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="593ab-499">Odstraňte db.</span><span class="sxs-lookup"><span data-stu-id="593ab-499">Delete the DB.</span></span>
* <span data-ttu-id="593ab-500">Změňte schéma DB (například přidejte `EmailAddress` pole).</span><span class="sxs-lookup"><span data-stu-id="593ab-500">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="593ab-501">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="593ab-501">Run the app.</span></span>
* <span data-ttu-id="593ab-502">`EnsureCreated`vytvoří DB se`EmailAddress` sloupcem.</span><span class="sxs-lookup"><span data-stu-id="593ab-502">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="593ab-503">`EnsureCreated`je vhodný na počátku vývoje, kdy se schéma rychle vyvíjí.</span><span class="sxs-lookup"><span data-stu-id="593ab-503">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="593ab-504">Později v kurzu db se odstraní a migrace se používají.</span><span class="sxs-lookup"><span data-stu-id="593ab-504">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="593ab-505">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="593ab-505">Test the app</span></span>

<span data-ttu-id="593ab-506">Spusťte aplikaci a přijměte zásady používání souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="593ab-506">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="593ab-507">Tato aplikace neuchovává osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="593ab-507">This app doesn't keep personal information.</span></span> <span data-ttu-id="593ab-508">O zásadách používání souborů cookie si můžete přečíst na [adrese OBECNÁ PODPORA NAŘÍZENÍ EU o ochraně osobních údajů (GDPR).](xref:security/gdpr)</span><span class="sxs-lookup"><span data-stu-id="593ab-508">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="593ab-509">Vyberte odkaz **Studenti** a pak **Vytvořit nový**.</span><span class="sxs-lookup"><span data-stu-id="593ab-509">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="593ab-510">Otestujte odkazy Upravit, Podrobnosti a Odstranit.</span><span class="sxs-lookup"><span data-stu-id="593ab-510">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="593ab-511">Prozkoumání kontextu SchoolContext DB</span><span class="sxs-lookup"><span data-stu-id="593ab-511">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="593ab-512">Hlavní třída, která koordinuje funkce EF Core pro daný datový model, je kontextová třída DB.</span><span class="sxs-lookup"><span data-stu-id="593ab-512">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="593ab-513">Kontext dat je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="593ab-513">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="593ab-514">Kontext dat určuje, které entity jsou zahrnuty do datového modelu.</span><span class="sxs-lookup"><span data-stu-id="593ab-514">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="593ab-515">V tomto projektu je `SchoolContext`třída pojmenována .</span><span class="sxs-lookup"><span data-stu-id="593ab-515">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="593ab-516">Aktualizujte *SchoolContext.cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="593ab-516">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="593ab-517">Zvýrazněný kód vytvoří [vlastnost\<DbSet TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro každou sadu entit.</span><span class="sxs-lookup"><span data-stu-id="593ab-517">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="593ab-518">V ef core terminologii:</span><span class="sxs-lookup"><span data-stu-id="593ab-518">In EF Core terminology:</span></span>

* <span data-ttu-id="593ab-519">Sada entit obvykle odpovídá tabulce DB.</span><span class="sxs-lookup"><span data-stu-id="593ab-519">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="593ab-520">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="593ab-520">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="593ab-521">`DbSet<Enrollment>`a `DbSet<Course>` může být vynechán.</span><span class="sxs-lookup"><span data-stu-id="593ab-521">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="593ab-522">EF Core je implicitně `Student` zahrnuje, `Enrollment` protože entita odkazuje na entitu a entita `Enrollment` odkazuje na entitu. `Course`</span><span class="sxs-lookup"><span data-stu-id="593ab-522">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="593ab-523">Pro účely tohoto `DbSet<Enrollment>` `DbSet<Course>` kurzu `SchoolContext`uchovávejte a v .</span><span class="sxs-lookup"><span data-stu-id="593ab-523">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="593ab-524">Místní databáze SQL Server Express</span><span class="sxs-lookup"><span data-stu-id="593ab-524">SQL Server Express LocalDB</span></span>

<span data-ttu-id="593ab-525">Připojovací řetězec určuje [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="593ab-525">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="593ab-526">LocalDB je odlehčená verze sql server express databázový stroj a je určen pro vývoj aplikací, nikoli produkční použití.</span><span class="sxs-lookup"><span data-stu-id="593ab-526">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="593ab-527">LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje žádná složitá konfigurace.</span><span class="sxs-lookup"><span data-stu-id="593ab-527">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="593ab-528">Ve výchozím nastavení localdb vytvoří *.mdf* DB soubory v adresáři. `C:/Users/<user>`</span><span class="sxs-lookup"><span data-stu-id="593ab-528">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="593ab-529">Přidání kódu pro inicializaci DATABÁZE s testovacími daty</span><span class="sxs-lookup"><span data-stu-id="593ab-529">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="593ab-530">EF Core vytvoří prázdnou DB.</span><span class="sxs-lookup"><span data-stu-id="593ab-530">EF Core creates an empty DB.</span></span> <span data-ttu-id="593ab-531">V této části `Initialize` je metoda zapsána k naplnění s testovací data.</span><span class="sxs-lookup"><span data-stu-id="593ab-531">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="593ab-532">Ve složce *Data* vytvořte nový soubor třídy s názvem *DbInitializer.cs* a přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="593ab-532">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="593ab-533">Poznámka: Předchozí kód `Models` používá pro obor`namespace ContosoUniversity.Models`názvů `Data`( ) spíše než .</span><span class="sxs-lookup"><span data-stu-id="593ab-533">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="593ab-534">`Models`je konzistentní s kódem generovaným generováním šaše.</span><span class="sxs-lookup"><span data-stu-id="593ab-534">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="593ab-535">Další informace naleznete v [tomto problému s lešením GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="593ab-535">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="593ab-536">Kód zkontroluje, zda jsou v DB nějaké studenty.</span><span class="sxs-lookup"><span data-stu-id="593ab-536">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="593ab-537">Pokud nejsou žádné studenty v DB, DB je inicializována s testovací data.</span><span class="sxs-lookup"><span data-stu-id="593ab-537">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="593ab-538">Načte testovací data do `List<T>` polí, nikoli do kolekcí pro optimalizaci výkonu.</span><span class="sxs-lookup"><span data-stu-id="593ab-538">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="593ab-539">Metoda `EnsureCreated` automaticky vytvoří DB pro kontext DB.</span><span class="sxs-lookup"><span data-stu-id="593ab-539">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="593ab-540">Pokud DB existuje, `EnsureCreated` vrátí bez úpravdb.</span><span class="sxs-lookup"><span data-stu-id="593ab-540">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="593ab-541">V *Program.cs*upravte metodu `Main` volání `Initialize`:</span><span class="sxs-lookup"><span data-stu-id="593ab-541">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="593ab-542">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="593ab-542">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="593ab-543">Zastavte aplikaci, pokud je spuštěná, a spusťte následující příkaz v **konzole Správce balíčků** (PMC):</span><span class="sxs-lookup"><span data-stu-id="593ab-543">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="593ab-544">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="593ab-544">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="593ab-545">Zastavte aplikaci, pokud je spuštěná, a odstraňte soubor *CU.db.*</span><span class="sxs-lookup"><span data-stu-id="593ab-545">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="593ab-546">Zobrazit DATABÁZI</span><span class="sxs-lookup"><span data-stu-id="593ab-546">View the DB</span></span>

<span data-ttu-id="593ab-547">Název databáze je generován z názvu kontextu, který jste zadali dříve plus pomlčka a GUID.</span><span class="sxs-lookup"><span data-stu-id="593ab-547">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="593ab-548">Název databáze tedy bude "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="593ab-548">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="593ab-549">Identifikátor GUID se bude lišit pro každého uživatele.</span><span class="sxs-lookup"><span data-stu-id="593ab-549">The GUID will be different for each user.</span></span>
<span data-ttu-id="593ab-550">Otevřete **Průzkumníka objektů SERVERU SQL Server** (SSOX) z nabídky **Zobrazení** v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="593ab-550">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="593ab-551">Ve ssoxu klikněte na **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="593ab-551">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="593ab-552">Rozbalte uzel **Tabulky.**</span><span class="sxs-lookup"><span data-stu-id="593ab-552">Expand the **Tables** node.</span></span>

<span data-ttu-id="593ab-553">Klikněte pravým tlačítkem myši na tabulku **Student** a kliknutím na **Zobrazit data** zobrazte vytvořené sloupce a řádky vložené do tabulky.</span><span class="sxs-lookup"><span data-stu-id="593ab-553">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="593ab-554">Asynchronní kód</span><span class="sxs-lookup"><span data-stu-id="593ab-554">Asynchronous code</span></span>

<span data-ttu-id="593ab-555">Asynchronní programování je výchozí režim pro ASP.NET Core a EF Core.</span><span class="sxs-lookup"><span data-stu-id="593ab-555">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="593ab-556">Webový server má omezený počet vláken k dispozici a v situacích s vysokým zatížením všechny dostupné podprocesy mohou být používány.</span><span class="sxs-lookup"><span data-stu-id="593ab-556">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="593ab-557">V takovém případě server nemůže zpracovat nové požadavky, dokud vlákna jsou uvolněny.</span><span class="sxs-lookup"><span data-stu-id="593ab-557">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="593ab-558">Pomocí synchronního kódu může být mnoho vláken svázáno, zatímco ve skutečnosti nepracují, protože čekají na dokončení vstupně-up.</span><span class="sxs-lookup"><span data-stu-id="593ab-558">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="593ab-559">S asynchronním kódem, když proces čeká na dokončení vstupně-va, jeho vlákno je uvolněno pro server pro zpracování jiných požadavků.</span><span class="sxs-lookup"><span data-stu-id="593ab-559">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="593ab-560">V důsledku toho asynchronní kód umožňuje efektivnější použití prostředků serveru a server je povolen pro zpracování většího provozu bez zpoždění.</span><span class="sxs-lookup"><span data-stu-id="593ab-560">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="593ab-561">Asynchronní kód zavést malé množství režie v době běhu.</span><span class="sxs-lookup"><span data-stu-id="593ab-561">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="593ab-562">V situacích s nízkým provozem je zásah ový výkon zanedbatelný, zatímco v situacích s vysokým provozem je potenciální zlepšení výkonu značné.</span><span class="sxs-lookup"><span data-stu-id="593ab-562">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="593ab-563">V následujícím kódu [asynchronní](/dotnet/csharp/language-reference/keywords/async) `Task<T>` klíčové slovo, vrácená hodnota, `await` klíčové slovo a `ToListAsync` metoda provést spuštění kódu asynchronně.</span><span class="sxs-lookup"><span data-stu-id="593ab-563">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="593ab-564">Klíčové `async` slovo říká kompilátoru:</span><span class="sxs-lookup"><span data-stu-id="593ab-564">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="593ab-565">Generovat zpětná volání pro části těla metody.</span><span class="sxs-lookup"><span data-stu-id="593ab-565">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="593ab-566">Automaticky vytvořte vrácený objekt [Task.](/dotnet/api/system.threading.tasks.task)</span><span class="sxs-lookup"><span data-stu-id="593ab-566">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="593ab-567">Další informace naleznete v [tématu Typ vrácení úkolu](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="593ab-567">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="593ab-568">Implicitní návratový typ `Task` představuje probíhající práci.</span><span class="sxs-lookup"><span data-stu-id="593ab-568">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="593ab-569">Klíčové `await` slovo způsobí, že kompilátor rozdělit metodu do dvou částí.</span><span class="sxs-lookup"><span data-stu-id="593ab-569">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="593ab-570">První část končí operací, která byla spuštěna asynchronně.</span><span class="sxs-lookup"><span data-stu-id="593ab-570">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="593ab-571">Druhá část je umístěn a callback metoda, která je volána po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="593ab-571">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="593ab-572">`ToListAsync`je asynchronní verze metody `ToList` rozšíření.</span><span class="sxs-lookup"><span data-stu-id="593ab-572">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="593ab-573">Některé věci, které je třeba si uvědomit při psaní asynchronního kódu, který používá EF Core:</span><span class="sxs-lookup"><span data-stu-id="593ab-573">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="593ab-574">Pouze příkazy, které způsobují dotazy nebo příkazy, které mají být odeslány do DB jsou prováděny asynchronně.</span><span class="sxs-lookup"><span data-stu-id="593ab-574">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="593ab-575">To zahrnuje `ToListAsync` `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, `SaveChangesAsync`, a .</span><span class="sxs-lookup"><span data-stu-id="593ab-575">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="593ab-576">Neobsahuje příkazy, které pouze `IQueryable`změnit `var students = context.Students.Where(s => s.LastName == "Davolio")`, například .</span><span class="sxs-lookup"><span data-stu-id="593ab-576">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="593ab-577">Kontext EF Core není bezpečný pro přístup z více vláken: nepokoušejte se provést více operací paralelně.</span><span class="sxs-lookup"><span data-stu-id="593ab-577">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="593ab-578">Chcete-li využít výhod výkonu asynchronního kódu, ověřte, zda balíčky knihovny (například pro stránkování) používají asynchronní, pokud volají metody EF Core, které odesílají dotazy do DB.</span><span class="sxs-lookup"><span data-stu-id="593ab-578">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="593ab-579">Další informace o asynchronním programování v rozhraní .NET naleznete v [tématu Async Overview](/dotnet/standard/async) a [Asynchronní programování s async a await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="593ab-579">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="593ab-580">V dalším kurzu jsou zkoumány základní operace CRUD (vytváření, čtení, aktualizace, odstranění).</span><span class="sxs-lookup"><span data-stu-id="593ab-580">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="593ab-581">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="593ab-581">Additional resources</span></span>

* [<span data-ttu-id="593ab-582">Verze tohoto kurzu pro YouTube</span><span class="sxs-lookup"><span data-stu-id="593ab-582">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="593ab-583">Další</span><span class="sxs-lookup"><span data-stu-id="593ab-583">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
