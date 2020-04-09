---
title: Práce s SQL v ASP.NET aplikaci Core MVC
author: rick-anderson
description: Další informace o používání SQL Server LocalDB nebo SQLite v aplikaci ASP.NET Core MVC.
ms.author: riande
ms.date: 8/16/2019
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: d556f07111fb2022a1c2f1a066459566e302835d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665037"
---
# <a name="work-with-sql-in-aspnet-core"></a><span data-ttu-id="7b318-103">Práce s SQL v ASP.NET core</span><span class="sxs-lookup"><span data-stu-id="7b318-103">Work with SQL in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7b318-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7b318-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7b318-105">Objekt `MvcMovieContext` zpracovává úkol připojení k databázi a `Movie` mapování objektů do databázových záznamů.</span><span class="sxs-lookup"><span data-stu-id="7b318-105">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="7b318-106">Kontext databáze je registrován s kontejnerem `ConfigureServices` [vkládání závislostí](xref:fundamentals/dependency-injection) v metodě v *souboru Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="7b318-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7b318-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7b318-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="7b318-108">Systém ASP.NET [Configuration](xref:fundamentals/configuration/index) core configuration `ConnectionString`přečte soubor .</span><span class="sxs-lookup"><span data-stu-id="7b318-108">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="7b318-109">Pro místní vývoj získá připojovací řetězec ze souboru *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="7b318-109">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7b318-110">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7b318-110">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

<span data-ttu-id="7b318-111">Systém ASP.NET [Configuration](xref:fundamentals/configuration/index) core configuration `ConnectionString`přečte soubor .</span><span class="sxs-lookup"><span data-stu-id="7b318-111">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="7b318-112">Pro místní vývoj získá připojovací řetězec ze souboru *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="7b318-112">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="7b318-113">Když je aplikace nasazena na testovací nebo produkční server, proměnná prostředí slouží k nastavení připojovacího řetězce na produkční SQL Server.</span><span class="sxs-lookup"><span data-stu-id="7b318-113">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a production SQL Server.</span></span> <span data-ttu-id="7b318-114">Další informace naleznete v [tématu Konfigurace.](xref:fundamentals/configuration/index)</span><span class="sxs-lookup"><span data-stu-id="7b318-114">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7b318-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7b318-115">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="7b318-116">Místní databáze SQL Server Express</span><span class="sxs-lookup"><span data-stu-id="7b318-116">SQL Server Express LocalDB</span></span>

<span data-ttu-id="7b318-117">LocalDB je odlehčená verze databázového stroje SQL Server Express, která je určena pro vývoj programů.</span><span class="sxs-lookup"><span data-stu-id="7b318-117">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="7b318-118">LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje žádná složitá konfigurace.</span><span class="sxs-lookup"><span data-stu-id="7b318-118">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="7b318-119">Ve výchozím nastavení databáze LocalDB vytváří soubory *MDF* v adresáři *C:/Users/{user}.*</span><span class="sxs-lookup"><span data-stu-id="7b318-119">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="7b318-120">V nabídce **Zobrazení** otevřete **Průzkumník a průzkumník objektů serveru SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="7b318-120">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Nabídka Zobrazit](working-with-sql/_static/ssox.png)

* <span data-ttu-id="7b318-122">Klikněte pravým `Movie` tlačítkem myši na tabulku **> Návrhář zobrazení**</span><span class="sxs-lookup"><span data-stu-id="7b318-122">Right click on the `Movie` table **> View Designer**</span></span>

  ![Kontextová nabídka otevřená v tabulce Film](working-with-sql/_static/design.png)

  ![Otevřese tabulka filmu v Návrháři](working-with-sql/_static/dv.png)

<span data-ttu-id="7b318-125">Poznamenejte si `ID`ikonu klíče vedle aplikace .</span><span class="sxs-lookup"><span data-stu-id="7b318-125">Note the key icon next to `ID`.</span></span> <span data-ttu-id="7b318-126">Ve výchozím nastavení ef vytvoří `ID` vlastnost s názvem primární klíč.</span><span class="sxs-lookup"><span data-stu-id="7b318-126">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="7b318-127">Klikněte pravým `Movie` tlačítkem myši na tabulku **> Zobrazit data**</span><span class="sxs-lookup"><span data-stu-id="7b318-127">Right click on the `Movie` table **> View Data**</span></span>

  ![Kontextová nabídka otevřená v tabulce Film](working-with-sql/_static/ssox2.png)

  ![Otevřená tabulka filmu zobrazující data tabulky](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7b318-130">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7b318-130">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="7b318-131">Osivo databáze</span><span class="sxs-lookup"><span data-stu-id="7b318-131">Seed the database</span></span>

<span data-ttu-id="7b318-132">Vytvořte novou `SeedData` třídu pojmenovanou ve složce *Modely.*</span><span class="sxs-lookup"><span data-stu-id="7b318-132">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="7b318-133">Nahraďte generovaný kód následujícím:</span><span class="sxs-lookup"><span data-stu-id="7b318-133">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="7b318-134">Pokud jsou nějaké filmy v DB, inicializátor osiva vrátí a žádné filmy jsou přidány.</span><span class="sxs-lookup"><span data-stu-id="7b318-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="7b318-135">Přidejte inicializátor osiva</span><span class="sxs-lookup"><span data-stu-id="7b318-135">Add the seed initializer</span></span>

<span data-ttu-id="7b318-136">Nahraďte obsah *Program.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="7b318-136">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

<span data-ttu-id="7b318-137">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="7b318-137">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7b318-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7b318-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7b318-139">Odstraňte všechny záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="7b318-139">Delete all the records in the DB.</span></span> <span data-ttu-id="7b318-140">Můžete to udělat s odstranit odkazy v prohlížeči nebo od SSOX.</span><span class="sxs-lookup"><span data-stu-id="7b318-140">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="7b318-141">Vynutit aplikaci inicializovat (volání metod ve `Startup` třídě), takže metoda osiva spustí.</span><span class="sxs-lookup"><span data-stu-id="7b318-141">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="7b318-142">Chcete-li vynutit inicializaci, musí být iis Express zastaven a restartován.</span><span class="sxs-lookup"><span data-stu-id="7b318-142">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="7b318-143">Můžete to provést s některou z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="7b318-143">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="7b318-144">Klikněte pravým tlačítkem myši na ikonu systémové liště IIS Express v oznamovací oblasti a klepněte na **Ukončit** nebo **zastavit web.**</span><span class="sxs-lookup"><span data-stu-id="7b318-144">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![Ikona systémové lišty IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="7b318-147">Pokud jste spouštěli VS v režimu bez ladění, spusťte stisknutím klávesy F5 v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="7b318-147">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="7b318-148">Pokud jste v režimu ladění používali VS, zastavte ladicí program a stiskněte klávesu F5.</span><span class="sxs-lookup"><span data-stu-id="7b318-148">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7b318-149">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7b318-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="7b318-150">Odstranit všechny záznamy v DB (Tak bude spuštěna metoda osiva).</span><span class="sxs-lookup"><span data-stu-id="7b318-150">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="7b318-151">Zastavit a spustit aplikaci pro osílku databáze.</span><span class="sxs-lookup"><span data-stu-id="7b318-151">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="7b318-152">Aplikace zobrazuje osývaná data.</span><span class="sxs-lookup"><span data-stu-id="7b318-152">The app shows the seeded data.</span></span>

![Aplikace MVC Movie otevřená v microsoft edge zobrazující filmová data](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="7b318-154">[Předchozí](adding-model.md)
> [další](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="7b318-154">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7b318-155">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7b318-155">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7b318-156">Objekt `MvcMovieContext` zpracovává úkol připojení k databázi a `Movie` mapování objektů do databázových záznamů.</span><span class="sxs-lookup"><span data-stu-id="7b318-156">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="7b318-157">Kontext databáze je registrován s kontejnerem `ConfigureServices` [vkládání závislostí](xref:fundamentals/dependency-injection) v metodě v *souboru Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="7b318-157">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7b318-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7b318-158">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="7b318-159">Systém ASP.NET [Configuration](xref:fundamentals/configuration/index) core configuration `ConnectionString`přečte soubor .</span><span class="sxs-lookup"><span data-stu-id="7b318-159">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="7b318-160">Pro místní vývoj získá připojovací řetězec ze souboru *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="7b318-160">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7b318-161">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7b318-161">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="7b318-162">Systém ASP.NET [Configuration](xref:fundamentals/configuration/index) core configuration `ConnectionString`přečte soubor .</span><span class="sxs-lookup"><span data-stu-id="7b318-162">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="7b318-163">Pro místní vývoj získá připojovací řetězec ze souboru *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="7b318-163">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="7b318-164">Při nasazení aplikace na testovací nebo produkční server můžete použít proměnnou prostředí nebo jiný přístup k nastavení připojovacího řetězce na skutečný SQL Server.</span><span class="sxs-lookup"><span data-stu-id="7b318-164">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="7b318-165">Další informace naleznete v [tématu Konfigurace.](xref:fundamentals/configuration/index)</span><span class="sxs-lookup"><span data-stu-id="7b318-165">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7b318-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7b318-166">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="7b318-167">Místní databáze SQL Server Express</span><span class="sxs-lookup"><span data-stu-id="7b318-167">SQL Server Express LocalDB</span></span>

<span data-ttu-id="7b318-168">LocalDB je odlehčená verze databázového stroje SQL Server Express, která je určena pro vývoj programů.</span><span class="sxs-lookup"><span data-stu-id="7b318-168">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="7b318-169">LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje žádná složitá konfigurace.</span><span class="sxs-lookup"><span data-stu-id="7b318-169">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="7b318-170">Ve výchozím nastavení databáze LocalDB vytváří soubory *MDF* v adresáři *C:/Users/{user}.*</span><span class="sxs-lookup"><span data-stu-id="7b318-170">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="7b318-171">V nabídce **Zobrazení** otevřete **Průzkumník a průzkumník objektů serveru SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="7b318-171">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Nabídka Zobrazit](working-with-sql/_static/ssox.png)

* <span data-ttu-id="7b318-173">Klikněte pravým `Movie` tlačítkem myši na tabulku **> Návrhář zobrazení**</span><span class="sxs-lookup"><span data-stu-id="7b318-173">Right click on the `Movie` table **> View Designer**</span></span>

  ![Kontextová nabídka otevřená v tabulce Film](working-with-sql/_static/design.png)

  ![Otevřese tabulka filmu v Návrháři](working-with-sql/_static/dv.png)

<span data-ttu-id="7b318-176">Poznamenejte si `ID`ikonu klíče vedle aplikace .</span><span class="sxs-lookup"><span data-stu-id="7b318-176">Note the key icon next to `ID`.</span></span> <span data-ttu-id="7b318-177">Ve výchozím nastavení ef vytvoří `ID` vlastnost s názvem primární klíč.</span><span class="sxs-lookup"><span data-stu-id="7b318-177">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="7b318-178">Klikněte pravým `Movie` tlačítkem myši na tabulku **> Zobrazit data**</span><span class="sxs-lookup"><span data-stu-id="7b318-178">Right click on the `Movie` table **> View Data**</span></span>

  ![Kontextová nabídka otevřená v tabulce Film](working-with-sql/_static/ssox2.png)

  ![Otevřená tabulka filmu zobrazující data tabulky](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7b318-181">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7b318-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="7b318-182">Osivo databáze</span><span class="sxs-lookup"><span data-stu-id="7b318-182">Seed the database</span></span>

<span data-ttu-id="7b318-183">Vytvořte novou `SeedData` třídu pojmenovanou ve složce *Modely.*</span><span class="sxs-lookup"><span data-stu-id="7b318-183">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="7b318-184">Nahraďte generovaný kód následujícím:</span><span class="sxs-lookup"><span data-stu-id="7b318-184">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="7b318-185">Pokud jsou nějaké filmy v DB, inicializátor osiva vrátí a žádné filmy jsou přidány.</span><span class="sxs-lookup"><span data-stu-id="7b318-185">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="7b318-186">Přidejte inicializátor osiva</span><span class="sxs-lookup"><span data-stu-id="7b318-186">Add the seed initializer</span></span>

<span data-ttu-id="7b318-187">Nahraďte obsah *Program.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="7b318-187">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

<span data-ttu-id="7b318-188">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="7b318-188">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7b318-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7b318-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7b318-190">Odstraňte všechny záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="7b318-190">Delete all the records in the DB.</span></span> <span data-ttu-id="7b318-191">Můžete to udělat s odstranit odkazy v prohlížeči nebo od SSOX.</span><span class="sxs-lookup"><span data-stu-id="7b318-191">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="7b318-192">Vynutit aplikaci inicializovat (volání metod ve `Startup` třídě), takže metoda osiva spustí.</span><span class="sxs-lookup"><span data-stu-id="7b318-192">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="7b318-193">Chcete-li vynutit inicializaci, musí být iis Express zastaven a restartován.</span><span class="sxs-lookup"><span data-stu-id="7b318-193">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="7b318-194">Můžete to provést s některou z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="7b318-194">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="7b318-195">Klikněte pravým tlačítkem myši na ikonu systémové liště IIS Express v oznamovací oblasti a klepněte na **Ukončit** nebo **zastavit web.**</span><span class="sxs-lookup"><span data-stu-id="7b318-195">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![Ikona systémové lišty IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="7b318-198">Pokud jste spouštěli VS v režimu bez ladění, spusťte stisknutím klávesy F5 v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="7b318-198">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="7b318-199">Pokud jste v režimu ladění používali VS, zastavte ladicí program a stiskněte klávesu F5.</span><span class="sxs-lookup"><span data-stu-id="7b318-199">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7b318-200">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7b318-200">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="7b318-201">Odstranit všechny záznamy v DB (Tak bude spuštěna metoda osiva).</span><span class="sxs-lookup"><span data-stu-id="7b318-201">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="7b318-202">Zastavit a spustit aplikaci pro osílku databáze.</span><span class="sxs-lookup"><span data-stu-id="7b318-202">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="7b318-203">Aplikace zobrazuje osývaná data.</span><span class="sxs-lookup"><span data-stu-id="7b318-203">The app shows the seeded data.</span></span>

![Aplikace MVC Movie otevřená v microsoft edge zobrazující filmová data](working-with-sql/_static/m55_mac.png)

> [!div class="step-by-step"]
> <span data-ttu-id="7b318-205">[Předchozí](adding-model.md)
> [další](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="7b318-205">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>

::: moniker-end
