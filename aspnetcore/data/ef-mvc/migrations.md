---
title: 'Kurz: Použití funkce migrace - ASP.NET MVC s EF Core'
description: V tomto kurzu začnete používat funkci migrace EF Core pro správu změn datového modelu v ASP.NET základní aplikace MVC.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/migrations
ms.openlocfilehash: 8b3417205457a5ce5fa16994701a06e2a4d7d350
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665723"
---
# <a name="tutorial-using-the-migrations-feature---aspnet-mvc-with-ef-core"></a><span data-ttu-id="65184-103">Kurz: Použití funkce migrace - ASP.NET MVC s EF Core</span><span class="sxs-lookup"><span data-stu-id="65184-103">Tutorial: Using the migrations feature - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="65184-104">V tomto kurzu začnete používat funkci migrace EF Core pro správu změn datového modelu.</span><span class="sxs-lookup"><span data-stu-id="65184-104">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="65184-105">V pozdějších kurzech přidáte další migrace při změně datového modelu.</span><span class="sxs-lookup"><span data-stu-id="65184-105">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="65184-106">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="65184-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="65184-107">Další informace o migracích</span><span class="sxs-lookup"><span data-stu-id="65184-107">Learn about migrations</span></span>
> * <span data-ttu-id="65184-108">Změna připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="65184-108">Change the connection string</span></span>
> * <span data-ttu-id="65184-109">Vytvoření počáteční migrace</span><span class="sxs-lookup"><span data-stu-id="65184-109">Create an initial migration</span></span>
> * <span data-ttu-id="65184-110">Zkoumat metody nahoru a dolů</span><span class="sxs-lookup"><span data-stu-id="65184-110">Examine Up and Down methods</span></span>
> * <span data-ttu-id="65184-111">Informace o snímku datového modelu</span><span class="sxs-lookup"><span data-stu-id="65184-111">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="65184-112">Použití migrace</span><span class="sxs-lookup"><span data-stu-id="65184-112">Apply the migration</span></span>

## <a name="prerequisites"></a><span data-ttu-id="65184-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="65184-113">Prerequisites</span></span>

* [<span data-ttu-id="65184-114">Řazení, filtrování a stránkování</span><span class="sxs-lookup"><span data-stu-id="65184-114">Sorting, filtering, and paging</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="65184-115">O migracích</span><span class="sxs-lookup"><span data-stu-id="65184-115">About migrations</span></span>

<span data-ttu-id="65184-116">Při vývoji nové aplikace, datový model se často mění a pokaždé, když se změní model, se synchronizuje s databází.</span><span class="sxs-lookup"><span data-stu-id="65184-116">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="65184-117">Tyto kurzy jste spustili tak, že jste nakonfigurovali rozhraní Entity Framework k vytvoření databáze, pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="65184-117">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="65184-118">Potom pokaždé, když změníte datový model – přidat, odebrat nebo změnit třídy entit nebo změnit třídu DbContext -- můžete odstranit databázi a EF vytvoří nový model, který odpovídá modelu a semena s testovací data.</span><span class="sxs-lookup"><span data-stu-id="65184-118">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="65184-119">Tento způsob udržování databáze v synchronizaci s datovým modelem funguje dobře, dokud nenasadíte aplikaci do produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="65184-119">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="65184-120">Když je aplikace spuštěna v produkčním prostředí, obvykle ukládá data, která chcete zachovat, a nechcete přijít o vše při každé změně, jako je například přidání nového sloupce.</span><span class="sxs-lookup"><span data-stu-id="65184-120">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="65184-121">Funkce EF Core Migrations tento problém řeší povolením EF k aktualizaci schématu databáze namísto vytvoření nové databáze.</span><span class="sxs-lookup"><span data-stu-id="65184-121">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

<span data-ttu-id="65184-122">Chcete-li pracovat s migracemi, můžete použít **konzolu Správce balíčků** (PMC) nebo CLI.</span><span class="sxs-lookup"><span data-stu-id="65184-122">To work with migrations, you can use the **Package Manager Console** (PMC) or the CLI.</span></span>  <span data-ttu-id="65184-123">Tyto kurzy ukazují, jak používat příkazy příkazového příkazu.</span><span class="sxs-lookup"><span data-stu-id="65184-123">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="65184-124">Informace o PMC je [na konci tohoto kurzu](#pmc).</span><span class="sxs-lookup"><span data-stu-id="65184-124">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="65184-125">Změna připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="65184-125">Change the connection string</span></span>

<span data-ttu-id="65184-126">V souboru *appsettings.json* změňte název databáze v připojovacím řetězci na ContosoUniversity2 nebo jiný název, který jste v počítači, který používáte, nepoužili.</span><span class="sxs-lookup"><span data-stu-id="65184-126">In the *appsettings.json* file, change the name of the database in the connection string to ContosoUniversity2 or some other name that you haven't used on the computer you're using.</span></span>

[!code-json[](intro/samples/cu/appsettings2.json?range=1-4)]

<span data-ttu-id="65184-127">Tato změna nastaví projekt tak, aby první migrace vytvoří novou databázi.</span><span class="sxs-lookup"><span data-stu-id="65184-127">This change sets up the project so that the first migration will create a new database.</span></span> <span data-ttu-id="65184-128">To není nutné začít s migrací, ale uvidíte později, proč je to dobrý nápad.</span><span class="sxs-lookup"><span data-stu-id="65184-128">This isn't required to get started with migrations, but you'll see later why it's a good idea.</span></span>

> [!NOTE]
> <span data-ttu-id="65184-129">Jako alternativu ke změně názvu databáze můžete databázi odstranit.</span><span class="sxs-lookup"><span data-stu-id="65184-129">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="65184-130">Použijte **Průzkumník objektů SERVERU SQL** Server `database drop` (SSOX) nebo příkaz cli:</span><span class="sxs-lookup"><span data-stu-id="65184-130">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```dotnetcli
> dotnet ef database drop
> ```
>
> <span data-ttu-id="65184-131">V následující části je vysvětleno, jak spustit příkazy příkazového příkazu.</span><span class="sxs-lookup"><span data-stu-id="65184-131">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="65184-132">Vytvoření počáteční migrace</span><span class="sxs-lookup"><span data-stu-id="65184-132">Create an initial migration</span></span>

<span data-ttu-id="65184-133">Uložte změny a vytvořte projekt.</span><span class="sxs-lookup"><span data-stu-id="65184-133">Save your changes and build the project.</span></span> <span data-ttu-id="65184-134">Potom otevřete příkazové okno a přejděte do složky projektu.</span><span class="sxs-lookup"><span data-stu-id="65184-134">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="65184-135">Zde je rychlý způsob, jak to udělat:</span><span class="sxs-lookup"><span data-stu-id="65184-135">Here's a quick way to do that:</span></span>

* <span data-ttu-id="65184-136">V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt a z kontextové nabídky zvolte **Otevřít složku v Průzkumníkovi souborů.**</span><span class="sxs-lookup"><span data-stu-id="65184-136">In **Solution Explorer**, right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![Otevřít v položce nabídky Průzkumníksouborů](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="65184-138">Do adresního řádku zadejte "cmd" a stiskněte Enter.</span><span class="sxs-lookup"><span data-stu-id="65184-138">Enter "cmd" in the address bar and press Enter.</span></span>

  ![Otevřít příkazové okno](migrations/_static/open-command-window.png)

<span data-ttu-id="65184-140">Do příkazového okna zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="65184-140">Enter the following command in the command window:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="65184-141">Výstup se zobrazí jako následující v příkazovém okně:</span><span class="sxs-lookup"><span data-stu-id="65184-141">You see output like the following in the command window:</span></span>

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> <span data-ttu-id="65184-142">Pokud se zobrazí chybová zpráva *Žádný spustitelný soubor nenašel odpovídající příkaz "dotnet-ef"*, podívejte se na [tento příspěvek blogu,](https://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) kde naleznete nápovědu k řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="65184-142">If you see an error message *No executable found matching command "dotnet-ef"*, see [this blog post](https://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) for help troubleshooting.</span></span>

<span data-ttu-id="65184-143">Pokud se zobrazí chybová zpráva "*nelze získat přístup k souboru ... Soubor ContosoUniversity.dll, protože je používán jiným procesem.*", vyhledejte ikonu IIS Express v systémové liště systému Windows a klepněte pravým tlačítkem myši na něj a klepněte na příkaz **ContosoUniversity > Stop Site**.</span><span class="sxs-lookup"><span data-stu-id="65184-143">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="65184-144">Zkoumat metody nahoru a dolů</span><span class="sxs-lookup"><span data-stu-id="65184-144">Examine Up and Down methods</span></span>

<span data-ttu-id="65184-145">Při spuštění příkazu `migrations add` EF vygeneroval kód, který vytvoří databázi od začátku.</span><span class="sxs-lookup"><span data-stu-id="65184-145">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="65184-146">Tento kód je ve složce *Migrace* v souboru s názvem \* \<timestamp>_InitialCreate.cs\*.</span><span class="sxs-lookup"><span data-stu-id="65184-146">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="65184-147">Metoda `Up` `InitialCreate` třídy vytvoří databázové tabulky, které odpovídají sady entit datového modelu a `Down` metoda je odstraní, jak je znázorněno v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="65184-147">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="65184-148">Migrace volá `Up` metodu k implementaci změn datového modelu pro migraci.</span><span class="sxs-lookup"><span data-stu-id="65184-148">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="65184-149">Když zadáte příkaz pro vrácení aktualizace zpět, `Down` migrace volá metodu.</span><span class="sxs-lookup"><span data-stu-id="65184-149">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="65184-150">Tento kód je určen pro počáteční migraci, která byla vytvořena při zadání příkazu. `migrations add InitialCreate`</span><span class="sxs-lookup"><span data-stu-id="65184-150">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="65184-151">Parametr názvu migrace ("InitialCreate" v příkladu) se používá pro název souboru a může být libovolné.</span><span class="sxs-lookup"><span data-stu-id="65184-151">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="65184-152">Je nejlepší zvolit slovo nebo frázi, která shrnuje, co se v migraci děje.</span><span class="sxs-lookup"><span data-stu-id="65184-152">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="65184-153">Můžete například pojmenovat pozdější migraci "AddDepartmentTable".</span><span class="sxs-lookup"><span data-stu-id="65184-153">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="65184-154">Pokud jste vytvořili počáteční migraci, když databáze již existuje, je generován kód vytvoření databáze, ale nemusí být spuštěn, protože databáze již odpovídá datovému modelu.</span><span class="sxs-lookup"><span data-stu-id="65184-154">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="65184-155">Když nasadíte aplikaci do jiného prostředí, kde databáze ještě neexistuje, tento kód se spustí k vytvoření databáze, takže je vhodné ji nejprve otestovat.</span><span class="sxs-lookup"><span data-stu-id="65184-155">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="65184-156">To je důvod, proč jste změnili název databáze v připojovacím řetězci dříve - tak, aby migrace můžete vytvořit nový od začátku.</span><span class="sxs-lookup"><span data-stu-id="65184-156">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="65184-157">Snímek datového modelu</span><span class="sxs-lookup"><span data-stu-id="65184-157">The data model snapshot</span></span>

<span data-ttu-id="65184-158">Migrace vytvoří *snímek* aktuálního schématu databáze v *migrace/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="65184-158">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="65184-159">Když přidáte migraci, EF určuje, co se změnilo porovnáním datového modelu se souborem snímku.</span><span class="sxs-lookup"><span data-stu-id="65184-159">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="65184-160">K odebrání migrace použijte příkaz [remove migrace dotnet ef.](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove)</span><span class="sxs-lookup"><span data-stu-id="65184-160">Use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command to remove a migration.</span></span> <span data-ttu-id="65184-161">`dotnet ef migrations remove`odstraní migraci a zajistí správné resetování snímku.</span><span class="sxs-lookup"><span data-stu-id="65184-161">`dotnet ef migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="65184-162">Pokud `dotnet ef migrations remove` se `dotnet ef migrations remove -v` nezdaří, použijte k získání dalších informací o selhání.</span><span class="sxs-lookup"><span data-stu-id="65184-162">If `dotnet ef migrations remove` fails, use `dotnet ef migrations remove -v` to get more information on the failure.</span></span>

<span data-ttu-id="65184-163">Další informace o tom, jak se soubor snímku používá, najdete [v tématu migrace jádra EF v týmových prostředích.](/ef/core/managing-schemas/migrations/teams)</span><span class="sxs-lookup"><span data-stu-id="65184-163">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="65184-164">Použití migrace</span><span class="sxs-lookup"><span data-stu-id="65184-164">Apply the migration</span></span>

<span data-ttu-id="65184-165">V příkazovém okně zadejte následující příkaz pro vytvoření databáze a tabulek v něm.</span><span class="sxs-lookup"><span data-stu-id="65184-165">In the command window, enter the following command to create the database and tables in it.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="65184-166">Výstup z příkazu je `migrations add` podobný příkazu, s tím rozdílem, že se zobrazí protokoly pro příkazy SQL, které nastavují databázi.</span><span class="sxs-lookup"><span data-stu-id="65184-166">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="65184-167">Většina protokolů jsou vynechány v následujícím ukázkovém výstupu.</span><span class="sxs-lookup"><span data-stu-id="65184-167">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="65184-168">Pokud nechcete vidět tuto úroveň podrobností ve zprávách protokolu, můžete změnit úroveň protokolu v *nastavení aplikace. Soubor Development.json.*</span><span class="sxs-lookup"><span data-stu-id="65184-168">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="65184-169">Další informace naleznete v tématu <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="65184-169">For more information, see <xref:fundamentals/logging/index>.</span></span>

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (274ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (60ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      IF SERVERPROPERTY('EngineEdition') <> 5
      BEGIN
          ALTER DATABASE [ContosoUniversity2] SET READ_COMMITTED_SNAPSHOT ON;
      END;
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (15ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20190327172701_InitialCreate', N'2.2.0-rtm-35687');
Done.
```

<span data-ttu-id="65184-170">Pomocí **Průzkumníka objektů SERVERU SQL Server** zkontrolujte databázi stejně jako v prvním kurzu.</span><span class="sxs-lookup"><span data-stu-id="65184-170">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="65184-171">Všimněte si přidání \_ \_EFMigrationsHistory tabulka, která sleduje, které migrace byly použity do databáze.</span><span class="sxs-lookup"><span data-stu-id="65184-171">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="65184-172">Zobrazte data v této tabulce a uvidíte jeden řádek pro první migraci.</span><span class="sxs-lookup"><span data-stu-id="65184-172">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="65184-173">(Poslední protokol v předchozím příkladu výstupu příkazového řádku zobrazuje příkaz INSERT, který vytvoří tento řádek.)</span><span class="sxs-lookup"><span data-stu-id="65184-173">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="65184-174">Spusťte aplikaci a ověřte, zda vše stále funguje stejně jako dříve.</span><span class="sxs-lookup"><span data-stu-id="65184-174">Run the application to verify that everything still works the same as before.</span></span>

![Stránka Studentský index](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="65184-176">Porovnání CLI a PMC</span><span class="sxs-lookup"><span data-stu-id="65184-176">Compare CLI and PMC</span></span>

<span data-ttu-id="65184-177">Nástroj EF pro správu migrací je k dispozici z příkazů rozhraní PŘÍKAZOvého příkazu .NET Core nebo z rutin prostředí PowerShell v okně PMC (Visual Studio **Package Manager Console).**</span><span class="sxs-lookup"><span data-stu-id="65184-177">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="65184-178">Tento kurz ukazuje, jak používat cli, ale můžete použít PMC, pokud dáváte přednost.</span><span class="sxs-lookup"><span data-stu-id="65184-178">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="65184-179">Příkazy EF pro příkazy PMC jsou v balíčku [Microsoft.EntityFrameworkCore.Tools.](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)</span><span class="sxs-lookup"><span data-stu-id="65184-179">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="65184-180">Tento balíček je součástí [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), takže není nutné přidávat odkaz na balíček, pokud vaše aplikace obsahuje odkaz na balíček pro `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="65184-180">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="65184-181">**Důležité:** Nejedná se o stejný balíček jako ten, který nainstalujete pro příkaz cli úpravou souboru *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="65184-181">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="65184-182">Název tohoto končí na `Tools`, na rozdíl od názvu `Tools.DotNet`balíčku CLI, který končí v .</span><span class="sxs-lookup"><span data-stu-id="65184-182">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="65184-183">Další informace o příkazech rozhraní příkazového příkazu naleznete [v tématu rozhraní příkazového příkazu .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="65184-183">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="65184-184">Další informace o příkazech PMC naleznete v [tématu Package Manager Console (Visual Studio).](/ef/core/miscellaneous/cli/powershell)</span><span class="sxs-lookup"><span data-stu-id="65184-184">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="65184-185">Získání kódu</span><span class="sxs-lookup"><span data-stu-id="65184-185">Get the code</span></span>

[<span data-ttu-id="65184-186">Stáhněte nebo zobrazte dokončenou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="65184-186">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-step"></a><span data-ttu-id="65184-187">Další krok</span><span class="sxs-lookup"><span data-stu-id="65184-187">Next step</span></span>

<span data-ttu-id="65184-188">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="65184-188">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="65184-189">Informace o migracích</span><span class="sxs-lookup"><span data-stu-id="65184-189">Learned about migrations</span></span>
> * <span data-ttu-id="65184-190">Informace o balíčcích migrace NuGet</span><span class="sxs-lookup"><span data-stu-id="65184-190">Learned about NuGet migration packages</span></span>
> * <span data-ttu-id="65184-191">Změna připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="65184-191">Changed the connection string</span></span>
> * <span data-ttu-id="65184-192">Vytvoření počáteční migrace</span><span class="sxs-lookup"><span data-stu-id="65184-192">Created an initial migration</span></span>
> * <span data-ttu-id="65184-193">Zkoumané metody nahoru a dolů</span><span class="sxs-lookup"><span data-stu-id="65184-193">Examined Up and Down methods</span></span>
> * <span data-ttu-id="65184-194">Informace o snímku datového modelu</span><span class="sxs-lookup"><span data-stu-id="65184-194">Learned about the data model snapshot</span></span>
> * <span data-ttu-id="65184-195">Použitá migrace</span><span class="sxs-lookup"><span data-stu-id="65184-195">Applied the migration</span></span>

<span data-ttu-id="65184-196">Přejde mekontější kurz a začněte se zabývat pokročilejšími tématy o rozšíření datového modelu.</span><span class="sxs-lookup"><span data-stu-id="65184-196">Advance to the next tutorial to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="65184-197">Cestou budete vytvářet a používat další migrace.</span><span class="sxs-lookup"><span data-stu-id="65184-197">Along the way you'll create and apply additional migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="65184-198">Vytvoření a použití dalších migrací</span><span class="sxs-lookup"><span data-stu-id="65184-198">Create and apply additional migrations</span></span>](complex-data-model.md)
