---
title: 'Kurz: použití funkcí migrace – ASP.NET MVC s EF Core'
description: V tomto kurzu začnete používat funkci migrace EF Core pro správu změn datového modelu v aplikaci ASP.NET Core MVC.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/migrations
ms.openlocfilehash: 9f3e3b29d155f1024aef530bf9c55efa57d4546a
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928401"
---
# <a name="tutorial-using-the-migrations-feature---aspnet-mvc-with-ef-core"></a><span data-ttu-id="f59c8-103">Kurz: použití funkcí migrace – ASP.NET MVC s EF Core</span><span class="sxs-lookup"><span data-stu-id="f59c8-103">Tutorial: Using the migrations feature - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="f59c8-104">V tomto kurzu začnete používat funkci migrace EF Core pro správu změn datového modelu.</span><span class="sxs-lookup"><span data-stu-id="f59c8-104">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="f59c8-105">V pozdějších kurzech přidáte při změně datového modelu další migrace.</span><span class="sxs-lookup"><span data-stu-id="f59c8-105">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="f59c8-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="f59c8-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f59c8-107">Další informace o migraci</span><span class="sxs-lookup"><span data-stu-id="f59c8-107">Learn about migrations</span></span>
> * <span data-ttu-id="f59c8-108">Změna připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="f59c8-108">Change the connection string</span></span>
> * <span data-ttu-id="f59c8-109">Vytvoření prvotní migrace</span><span class="sxs-lookup"><span data-stu-id="f59c8-109">Create an initial migration</span></span>
> * <span data-ttu-id="f59c8-110">Projděte si metody směrem nahoru a dolů</span><span class="sxs-lookup"><span data-stu-id="f59c8-110">Examine Up and Down methods</span></span>
> * <span data-ttu-id="f59c8-111">Další informace o snímku datového modelu</span><span class="sxs-lookup"><span data-stu-id="f59c8-111">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="f59c8-112">Použití migrace</span><span class="sxs-lookup"><span data-stu-id="f59c8-112">Apply the migration</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f59c8-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="f59c8-113">Prerequisites</span></span>

* [<span data-ttu-id="f59c8-114">Řazení, filtrování a stránkování</span><span class="sxs-lookup"><span data-stu-id="f59c8-114">Sorting, filtering, and paging</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="f59c8-115">O migracích</span><span class="sxs-lookup"><span data-stu-id="f59c8-115">About migrations</span></span>

<span data-ttu-id="f59c8-116">Při vývoji nové aplikace se datový model často mění a pokaždé, když se model změní, se nesynchronizuje s databází.</span><span class="sxs-lookup"><span data-stu-id="f59c8-116">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="f59c8-117">Tyto kurzy jste začali konfigurací Entity Framework vytvořit databázi, pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="f59c8-117">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="f59c8-118">Pak pokaždé, když změníte datový model – můžete přidat, odebrat nebo změnit třídy entit nebo změnit třídu DbContext – můžete odstranit databázi a EF vytvoří novou, která odpovídá modelu, a vyhodnotit jeho semena s testovacími daty.</span><span class="sxs-lookup"><span data-stu-id="f59c8-118">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="f59c8-119">Tato metoda uchování databáze v synchronizaci s datovým modelem funguje dobře, dokud aplikaci nenainstalujete do produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="f59c8-119">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="f59c8-120">Když je aplikace spuštěná v produkčním prostředí, obvykle ukládá data, která chcete zachovat, a nechcete přijít o všechny pokaždé, když uděláte změnu, jako je přidání nového sloupce.</span><span class="sxs-lookup"><span data-stu-id="f59c8-120">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="f59c8-121">Funkce migrace EF Core vyřeší tento problém tím, že umožňuje EF aktualizovat schéma databáze místo vytvoření nové databáze.</span><span class="sxs-lookup"><span data-stu-id="f59c8-121">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

<span data-ttu-id="f59c8-122">Pro práci s migrací můžete použít **konzolu Správce balíčků** (PMC) nebo rozhraní příkazového řádku (CLI).</span><span class="sxs-lookup"><span data-stu-id="f59c8-122">To work with migrations, you can use the **Package Manager Console** (PMC) or the CLI.</span></span>  <span data-ttu-id="f59c8-123">Tyto kurzy ukazují, jak používat příkazy rozhraní příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="f59c8-123">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="f59c8-124">Informace o PMC jsou na [konci tohoto kurzu](#pmc).</span><span class="sxs-lookup"><span data-stu-id="f59c8-124">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="f59c8-125">Změna připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="f59c8-125">Change the connection string</span></span>

<span data-ttu-id="f59c8-126">V souboru *appSettings. JSON* změňte název databáze v připojovacím řetězci na ContosoUniversity2 nebo jiný název, který jste nepoužili v počítači, který používáte.</span><span class="sxs-lookup"><span data-stu-id="f59c8-126">In the *appsettings.json* file, change the name of the database in the connection string to ContosoUniversity2 or some other name that you haven't used on the computer you're using.</span></span>

[!code-json[](intro/samples/cu/appsettings2.json?range=1-4)]

<span data-ttu-id="f59c8-127">Tato změna nastaví projekt tak, že první migrace vytvoří novou databázi.</span><span class="sxs-lookup"><span data-stu-id="f59c8-127">This change sets up the project so that the first migration will create a new database.</span></span> <span data-ttu-id="f59c8-128">Tento postup není potřebný k tomu, aby bylo možné začít s migracemi, ale později se dozvíte, proč je to dobrý nápad.</span><span class="sxs-lookup"><span data-stu-id="f59c8-128">This isn't required to get started with migrations, but you'll see later why it's a good idea.</span></span>

> [!NOTE]
> <span data-ttu-id="f59c8-129">Jako alternativu ke změně názvu databáze můžete databázi odstranit.</span><span class="sxs-lookup"><span data-stu-id="f59c8-129">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="f59c8-130">Použijte **Průzkumník objektů systému SQL Server** (SSOX) nebo příkaz `database drop` CLI:</span><span class="sxs-lookup"><span data-stu-id="f59c8-130">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```dotnetcli
> dotnet ef database drop
> ```
>
> <span data-ttu-id="f59c8-131">V následující části je vysvětleno, jak spustit příkazy rozhraní příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="f59c8-131">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="f59c8-132">Vytvoření prvotní migrace</span><span class="sxs-lookup"><span data-stu-id="f59c8-132">Create an initial migration</span></span>

<span data-ttu-id="f59c8-133">Uložte změny a sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="f59c8-133">Save your changes and build the project.</span></span> <span data-ttu-id="f59c8-134">Pak otevřete příkazové okno a přejděte do složky projektu.</span><span class="sxs-lookup"><span data-stu-id="f59c8-134">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="f59c8-135">Tady je rychlý způsob, jak to udělat:</span><span class="sxs-lookup"><span data-stu-id="f59c8-135">Here's a quick way to do that:</span></span>

* <span data-ttu-id="f59c8-136">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a v místní nabídce vyberte možnost **Otevřít složku v Průzkumníku souborů** .</span><span class="sxs-lookup"><span data-stu-id="f59c8-136">In **Solution Explorer**, right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![Otevřít v Průzkumníkovi souborů – položka nabídky](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="f59c8-138">Do adresního řádku zadejte "cmd" a stiskněte klávesu ENTER.</span><span class="sxs-lookup"><span data-stu-id="f59c8-138">Enter "cmd" in the address bar and press Enter.</span></span>

  ![Otevřít příkazové okno](migrations/_static/open-command-window.png)

<span data-ttu-id="f59c8-140">V příkazovém okně zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="f59c8-140">Enter the following command in the command window:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="f59c8-141">V příkazovém okně se zobrazí výstup podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="f59c8-141">You see output like the following in the command window:</span></span>

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> <span data-ttu-id="f59c8-142">Pokud se zobrazí chybová zpráva *bez nalezeného spustitelného souboru, který odpovídá příkazu "dotnet-EF"* , přečtěte si [Tento Blogový příspěvek](https://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) , kde najdete pomoc při řešení potíží</span><span class="sxs-lookup"><span data-stu-id="f59c8-142">If you see an error message *No executable found matching command "dotnet-ef"*, see [this blog post](https://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) for help troubleshooting.</span></span>

<span data-ttu-id="f59c8-143">Pokud se zobrazí chybová zpráva "*nelze získat přístup k souboru... ContosoUniversity. dll, protože ho používá jiný proces.* ", najděte ikonu IIS Express v hlavním panelu systému Windows a klikněte na ni pravým tlačítkem myši a potom klikněte na **ContosoUniversity > zastavit web**.</span><span class="sxs-lookup"><span data-stu-id="f59c8-143">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="f59c8-144">Projděte si metody směrem nahoru a dolů</span><span class="sxs-lookup"><span data-stu-id="f59c8-144">Examine Up and Down methods</span></span>

<span data-ttu-id="f59c8-145">Když jste provedli příkaz `migrations add`, EF vygeneroval kód, který vytvoří databázi zcela od začátku.</span><span class="sxs-lookup"><span data-stu-id="f59c8-145">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="f59c8-146">Tento kód je ve složce *migraces* v souboru s názvem *\<časové razítko > _InitialCreate. cs*.</span><span class="sxs-lookup"><span data-stu-id="f59c8-146">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="f59c8-147">Metoda `Up` třídy `InitialCreate` vytvoří tabulky databáze, které odpovídají sadám entit datového modelu, a metoda `Down` je odstraní, jak je znázorněno v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="f59c8-147">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="f59c8-148">Migrace zavolá metodu `Up` pro implementaci změn datového modelu pro migraci.</span><span class="sxs-lookup"><span data-stu-id="f59c8-148">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="f59c8-149">Když zadáte příkaz pro vrácení aktualizace, migrace zavolá metodu `Down`.</span><span class="sxs-lookup"><span data-stu-id="f59c8-149">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="f59c8-150">Tento kód je určen pro počáteční migraci, která byla vytvořena při zadání příkazu `migrations add InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="f59c8-150">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="f59c8-151">Parametr názvu migrace (v příkladu "InitialCreate") se používá pro název souboru a může být libovolný, který chcete.</span><span class="sxs-lookup"><span data-stu-id="f59c8-151">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="f59c8-152">Nejlepší je zvolit slovo nebo frázi, která shrnuje, co se v migraci provádí.</span><span class="sxs-lookup"><span data-stu-id="f59c8-152">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="f59c8-153">Můžete třeba pojmenovat pozdější migraci "AddDepartmentTable".</span><span class="sxs-lookup"><span data-stu-id="f59c8-153">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="f59c8-154">Pokud jste vytvořili počáteční migraci i v případě, že databáze již existuje, je vytvořen kód pro vytvoření databáze, ale nemusí být spuštěn, protože databáze již odpovídá datovému modelu.</span><span class="sxs-lookup"><span data-stu-id="f59c8-154">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="f59c8-155">Když nasadíte aplikaci do jiného prostředí, kde databáze ještě neexistuje, tento kód se spustí, aby se vytvořila vaše databáze, takže je dobré ho nejdřív otestovat.</span><span class="sxs-lookup"><span data-stu-id="f59c8-155">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="f59c8-156">To je důvod, proč jste změnili název databáze v připojovacím řetězci dříve, takže migrace mohou vytvořit nové od začátku.</span><span class="sxs-lookup"><span data-stu-id="f59c8-156">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="f59c8-157">Snímek datového modelu</span><span class="sxs-lookup"><span data-stu-id="f59c8-157">The data model snapshot</span></span>

<span data-ttu-id="f59c8-158">Migrace vytvoří *snímek* aktuálního schématu databáze v části *migrations/SchoolContextModelSnapshot. cs*.</span><span class="sxs-lookup"><span data-stu-id="f59c8-158">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="f59c8-159">Když přidáte migraci, EF určí, co se změnilo, porovnáním datového modelu se souborem snímku.</span><span class="sxs-lookup"><span data-stu-id="f59c8-159">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="f59c8-160">K odebrání migrace použijte příkaz [dotnet EF migrations Remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) .</span><span class="sxs-lookup"><span data-stu-id="f59c8-160">Use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command to remove a migration.</span></span> <span data-ttu-id="f59c8-161">`dotnet ef migrations remove` odstraní migraci a zajistí správné resetování snímku.</span><span class="sxs-lookup"><span data-stu-id="f59c8-161">`dotnet ef migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="f59c8-162">Pokud se `dotnet ef migrations remove` nezdaří, získáte další informace o selhání pomocí `dotnet ef migrations remove -v`.</span><span class="sxs-lookup"><span data-stu-id="f59c8-162">If `dotnet ef migrations remove` fails, use `dotnet ef migrations remove -v` to get more information on the failure.</span></span>

<span data-ttu-id="f59c8-163">Další informace o tom, jak se soubor snímku používá, najdete [v tématu EF Core migrace v týmových prostředích](/ef/core/managing-schemas/migrations/teams) .</span><span class="sxs-lookup"><span data-stu-id="f59c8-163">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="f59c8-164">Použití migrace</span><span class="sxs-lookup"><span data-stu-id="f59c8-164">Apply the migration</span></span>

<span data-ttu-id="f59c8-165">V okně příkazového řádku zadejte následující příkaz, který v něm vytvoří databázi a tabulky.</span><span class="sxs-lookup"><span data-stu-id="f59c8-165">In the command window, enter the following command to create the database and tables in it.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="f59c8-166">Výstup příkazu je podobný příkazu `migrations add`, s tím rozdílem, že se zobrazí protokoly pro příkazy SQL, které nastaví databázi.</span><span class="sxs-lookup"><span data-stu-id="f59c8-166">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="f59c8-167">Většina protokolů je vynechána v následujícím ukázkovém výstupu.</span><span class="sxs-lookup"><span data-stu-id="f59c8-167">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="f59c8-168">Pokud nechcete, aby se tato úroveň podrobností zobrazovala v protokolových zprávách, můžete změnit úroveň protokolu v souboru *appSettings. Soubor Development. JSON* .</span><span class="sxs-lookup"><span data-stu-id="f59c8-168">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="f59c8-169">Další informace najdete v tématu <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="f59c8-169">For more information, see <xref:fundamentals/logging/index>.</span></span>

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

<span data-ttu-id="f59c8-170">Pomocí **Průzkumník objektů systému SQL Server** můžete zkontrolovat databázi jako v prvním kurzu.</span><span class="sxs-lookup"><span data-stu-id="f59c8-170">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="f59c8-171">Všimněte si přidání \_\_tabulce EFMigrationsHistory, která sleduje, které migrace byly pro databázi aplikovány.</span><span class="sxs-lookup"><span data-stu-id="f59c8-171">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="f59c8-172">Zobrazit data v této tabulce a pro první migraci se zobrazí jeden řádek.</span><span class="sxs-lookup"><span data-stu-id="f59c8-172">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="f59c8-173">(Poslední protokol v předchozím příkladu výstupu rozhraní příkazového řádku ukazuje příkaz INSERT, který tento řádek vytvoří.)</span><span class="sxs-lookup"><span data-stu-id="f59c8-173">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="f59c8-174">Spusťte aplikaci, abyste ověřili, že všechno pořád funguje stejně jako předtím.</span><span class="sxs-lookup"><span data-stu-id="f59c8-174">Run the application to verify that everything still works the same as before.</span></span>

![Studenti indexová stránka](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="f59c8-176">Porovnání CLI a PMC</span><span class="sxs-lookup"><span data-stu-id="f59c8-176">Compare CLI and PMC</span></span>

<span data-ttu-id="f59c8-177">Nástroje EF pro správu migrací jsou dostupné z .NET Core CLI příkazy nebo z rutin prostředí PowerShell v okně **konzoly Správce balíčků** sady Visual Studio (PMC).</span><span class="sxs-lookup"><span data-stu-id="f59c8-177">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="f59c8-178">V tomto kurzu se dozvíte, jak používat rozhraní příkazového řádku, ale pokud chcete, můžete použít PMC.</span><span class="sxs-lookup"><span data-stu-id="f59c8-178">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="f59c8-179">Příkazy EF pro příkazy PMC jsou v balíčku [Microsoft. EntityFrameworkCore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) .</span><span class="sxs-lookup"><span data-stu-id="f59c8-179">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="f59c8-180">Tento balíček je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), takže nemusíte přidávat odkaz na balíček, pokud vaše aplikace obsahuje odkaz na balíček pro `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="f59c8-180">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="f59c8-181">**Důležité informace:** Nejedná se o stejný balíček jako ten, který nainstalujete pro rozhraní příkazového řádku úpravou souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="f59c8-181">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="f59c8-182">Název tohoto typu končí v `Tools`, na rozdíl od názvu balíčku CLI, který končí v `Tools.DotNet`.</span><span class="sxs-lookup"><span data-stu-id="f59c8-182">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="f59c8-183">Další informace o příkazech rozhraní příkazového řádku naleznete v tématu [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="f59c8-183">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="f59c8-184">Další informace o příkazech PMC naleznete v tématu [Konzola správce balíčků (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="f59c8-184">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="f59c8-185">Získat kód</span><span class="sxs-lookup"><span data-stu-id="f59c8-185">Get the code</span></span>

[<span data-ttu-id="f59c8-186">Stažení nebo zobrazení dokončené aplikace.</span><span class="sxs-lookup"><span data-stu-id="f59c8-186">Download or view the completed application.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-step"></a><span data-ttu-id="f59c8-187">Další krok</span><span class="sxs-lookup"><span data-stu-id="f59c8-187">Next step</span></span>

<span data-ttu-id="f59c8-188">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="f59c8-188">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f59c8-189">Seznámili jste se s migracemi</span><span class="sxs-lookup"><span data-stu-id="f59c8-189">Learned about migrations</span></span>
> * <span data-ttu-id="f59c8-190">Seznámili jste se s balíčky migrace NuGet.</span><span class="sxs-lookup"><span data-stu-id="f59c8-190">Learned about NuGet migration packages</span></span>
> * <span data-ttu-id="f59c8-191">Změna připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="f59c8-191">Changed the connection string</span></span>
> * <span data-ttu-id="f59c8-192">Vytvořila se počáteční migrace.</span><span class="sxs-lookup"><span data-stu-id="f59c8-192">Created an initial migration</span></span>
> * <span data-ttu-id="f59c8-193">Prověření a snížení metod</span><span class="sxs-lookup"><span data-stu-id="f59c8-193">Examined Up and Down methods</span></span>
> * <span data-ttu-id="f59c8-194">Dozvěděli jste se o snímku datového modelu.</span><span class="sxs-lookup"><span data-stu-id="f59c8-194">Learned about the data model snapshot</span></span>
> * <span data-ttu-id="f59c8-195">Migrace se používala</span><span class="sxs-lookup"><span data-stu-id="f59c8-195">Applied the migration</span></span>

<span data-ttu-id="f59c8-196">Přejděte k dalšímu kurzu a začněte hledat v pokročilejších tématech o rozšiřování datového modelu.</span><span class="sxs-lookup"><span data-stu-id="f59c8-196">Advance to the next tutorial to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="f59c8-197">Podél toho, jak vytvoříte a použijete další migrace.</span><span class="sxs-lookup"><span data-stu-id="f59c8-197">Along the way you'll create and apply additional migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="f59c8-198">Vytvoření a použití dalších migrací</span><span class="sxs-lookup"><span data-stu-id="f59c8-198">Create and apply additional migrations</span></span>](complex-data-model.md)
