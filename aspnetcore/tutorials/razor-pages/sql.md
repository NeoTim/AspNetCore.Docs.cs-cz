---
title: Práce s databází a ASP.NET jádra
author: rick-anderson
description: Vysvětluje práci s databází a ASP.NET Core.
ms.author: riande
ms.date: 7/22/2019
uid: tutorials/razor-pages/sql
ms.openlocfilehash: b5acb573f8fa39e5300ecdb359113d8697d78934
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664337"
---
# <a name="work-with-a-database-and-aspnet-core"></a><span data-ttu-id="7f42a-103">Práce s databází a ASP.NET jádra</span><span class="sxs-lookup"><span data-stu-id="7f42a-103">Work with a database and ASP.NET Core</span></span>

<span data-ttu-id="7f42a-104">[Podle Rick Anderson](https://twitter.com/RickAndMSFT) a Joe [Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="7f42a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="7f42a-105">Objekt `RazorPagesMovieContext` zpracovává úkol připojení k databázi a `Movie` mapování objektů do databázových záznamů.</span><span class="sxs-lookup"><span data-stu-id="7f42a-105">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="7f42a-106">Kontext databáze je registrován s kontejnerem `ConfigureServices` vkládání [závislostí](xref:fundamentals/dependency-injection) v metodě v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="7f42a-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7f42a-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f42a-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7f42a-108">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7f42a-108">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="7f42a-109">Systém ASP.NET [Configuration](xref:fundamentals/configuration/index) core configuration `ConnectionString`přečte soubor .</span><span class="sxs-lookup"><span data-stu-id="7f42a-109">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="7f42a-110">Pro místní vývoj získá připojovací řetězec ze souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="7f42a-110">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7f42a-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f42a-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7f42a-112">Hodnota názvu databáze (`Database={Database name}`) se bude lišit pro generovaný kód.</span><span class="sxs-lookup"><span data-stu-id="7f42a-112">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="7f42a-113">Hodnota názvu je libovolná.</span><span class="sxs-lookup"><span data-stu-id="7f42a-113">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7f42a-114">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7f42a-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="7f42a-115">Když je aplikace nasazena na testovací nebo produkční server, proměnnou prostředí lze použít k nastavení připojovacího řetězce na reálný databázový server.</span><span class="sxs-lookup"><span data-stu-id="7f42a-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="7f42a-116">Další informace naleznete v [tématu Konfigurace.](xref:fundamentals/configuration/index)</span><span class="sxs-lookup"><span data-stu-id="7f42a-116">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7f42a-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f42a-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="7f42a-118">Místní databáze SQL Server Express</span><span class="sxs-lookup"><span data-stu-id="7f42a-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="7f42a-119">LocalDB je odlehčená verze databázového stroje SQL Server Express, která je určena pro vývoj programů.</span><span class="sxs-lookup"><span data-stu-id="7f42a-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="7f42a-120">LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje žádná složitá konfigurace.</span><span class="sxs-lookup"><span data-stu-id="7f42a-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="7f42a-121">Ve výchozím nastavení databáze `*.mdf` LocalDB `C:\Users\<user>\` vytváří soubory v adresáři.</span><span class="sxs-lookup"><span data-stu-id="7f42a-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="7f42a-122">V nabídce **Zobrazení** otevřete **Průzkumník a průzkumník objektů serveru SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="7f42a-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Nabídka Zobrazit](sql/_static/ssox.png)

* <span data-ttu-id="7f42a-124">Klikněte pravým `Movie` tlačítkem myši na tabulku a vyberte **Zobrazit Návrhář**:</span><span class="sxs-lookup"><span data-stu-id="7f42a-124">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Kontextové nabídky otevřené v tabulce Film](sql/_static/design.png)

  ![Filmové tabulky otevřené v Návrháři](sql/_static/dv.png)

<span data-ttu-id="7f42a-127">Poznamenejte si `ID`ikonu klíče vedle aplikace .</span><span class="sxs-lookup"><span data-stu-id="7f42a-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="7f42a-128">Ve výchozím nastavení EF `ID` vytvoří vlastnost pojmenovanou pro primární klíč.</span><span class="sxs-lookup"><span data-stu-id="7f42a-128">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="7f42a-129">Klikněte pravým `Movie` tlačítkem myši na tabulku a vyberte **zobrazit data**:</span><span class="sxs-lookup"><span data-stu-id="7f42a-129">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Otevřená tabulka filmu zobrazující data tabulky](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7f42a-131">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7f42a-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="7f42a-132">Osivo databáze</span><span class="sxs-lookup"><span data-stu-id="7f42a-132">Seed the database</span></span>

<span data-ttu-id="7f42a-133">Vytvořte novou `SeedData` třídu pojmenovanou ve složce *Modely* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="7f42a-133">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="7f42a-134">Pokud jsou nějaké filmy v DB, inicializátor osiva vrátí a žádné filmy jsou přidány.</span><span class="sxs-lookup"><span data-stu-id="7f42a-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="7f42a-135">Přidejte inicializátor osiva</span><span class="sxs-lookup"><span data-stu-id="7f42a-135">Add the seed initializer</span></span>

<span data-ttu-id="7f42a-136">V *Program.cs*upravte metodu `Main` takto:</span><span class="sxs-lookup"><span data-stu-id="7f42a-136">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="7f42a-137">Získejte kontextové instance DB z kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="7f42a-137">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="7f42a-138">Volání metody osiva, předávání k ní kontextu.</span><span class="sxs-lookup"><span data-stu-id="7f42a-138">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="7f42a-139">Po dokončení metody osiva zlikvidujte kontext.</span><span class="sxs-lookup"><span data-stu-id="7f42a-139">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="7f42a-140">Následující kód zobrazuje aktualizovaný *soubor Program.cs.*</span><span class="sxs-lookup"><span data-stu-id="7f42a-140">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="7f42a-141">Následující výjimka nastane, když `Update-Database` nebylspuštěn:</span><span class="sxs-lookup"><span data-stu-id="7f42a-141">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="7f42a-142">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="7f42a-142">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7f42a-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f42a-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7f42a-144">Odstraňte všechny záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="7f42a-144">Delete all the records in the DB.</span></span> <span data-ttu-id="7f42a-145">Můžete to udělat s odstranit odkazy v prohlížeči nebo od [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="7f42a-145">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="7f42a-146">Vynutit aplikaci inicializovat (volání metod ve `Startup` třídě), takže metoda osiva spustí.</span><span class="sxs-lookup"><span data-stu-id="7f42a-146">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="7f42a-147">Chcete-li vynutit inicializaci, musí být iis Express zastaven a restartován.</span><span class="sxs-lookup"><span data-stu-id="7f42a-147">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="7f42a-148">Můžete to provést s některou z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="7f42a-148">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="7f42a-149">Klikněte pravým tlačítkem myši na ikonu na hlavním panelu systému IIS Express v oznamovací oblasti a klepněte na **Ukončit** nebo **zastavit web**:</span><span class="sxs-lookup"><span data-stu-id="7f42a-149">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Ikona systémové lišty IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](sql/_static/stopIIS.png)

    * <span data-ttu-id="7f42a-152">Pokud jste používali VS v režimu bez ladění, stiskněte klávesu F5 a spusťte v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="7f42a-152">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="7f42a-153">Pokud jste v režimu ladění používali VS, zastavte ladicí program a stiskněte klávesu F5.</span><span class="sxs-lookup"><span data-stu-id="7f42a-153">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7f42a-154">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7f42a-154">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="7f42a-155">Odstranit všechny záznamy v DB (Tak bude spuštěna metoda osiva).</span><span class="sxs-lookup"><span data-stu-id="7f42a-155">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="7f42a-156">Zastavit a spustit aplikaci pro osílku databáze.</span><span class="sxs-lookup"><span data-stu-id="7f42a-156">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="7f42a-157">Aplikace zobrazuje osývaná data.</span><span class="sxs-lookup"><span data-stu-id="7f42a-157">The app shows the seeded data.</span></span>

---

<span data-ttu-id="7f42a-158">Další kurz zlepší prezentaci dat.</span><span class="sxs-lookup"><span data-stu-id="7f42a-158">The next tutorial will improve the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7f42a-159">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="7f42a-159">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="7f42a-160">[Předchozí: Scaffolded Razor Stránky](xref:tutorials/razor-pages/page)
> [Další: Aktualizace stránek](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="7f42a-160">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="7f42a-161">Objekt `RazorPagesMovieContext` zpracovává úkol připojení k databázi a `Movie` mapování objektů do databázových záznamů.</span><span class="sxs-lookup"><span data-stu-id="7f42a-161">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="7f42a-162">Kontext databáze je registrován s kontejnerem `ConfigureServices` vkládání [závislostí](xref:fundamentals/dependency-injection) v metodě v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="7f42a-162">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7f42a-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f42a-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7f42a-164">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7f42a-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="7f42a-165">Další informace o metodách `ConfigureServices`používaných v písmenech a), r.</span><span class="sxs-lookup"><span data-stu-id="7f42a-165">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="7f42a-166">[Obecné nařízení EU o ochraně osobních údajů (GDPR) v ASP.NET jádru](xref:security/gdpr) pro `CookiePolicyOptions`.</span><span class="sxs-lookup"><span data-stu-id="7f42a-166">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="7f42a-167">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="7f42a-167">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="7f42a-168">Systém ASP.NET [Configuration](xref:fundamentals/configuration/index) core configuration `ConnectionString`přečte soubor .</span><span class="sxs-lookup"><span data-stu-id="7f42a-168">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="7f42a-169">Pro místní vývoj získá připojovací řetězec ze souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="7f42a-169">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7f42a-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f42a-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7f42a-171">Hodnota názvu databáze (`Database={Database name}`) se bude lišit pro generovaný kód.</span><span class="sxs-lookup"><span data-stu-id="7f42a-171">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="7f42a-172">Hodnota názvu je libovolná.</span><span class="sxs-lookup"><span data-stu-id="7f42a-172">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7f42a-173">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f42a-173">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7f42a-174">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7f42a-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="7f42a-175">Když je aplikace nasazena na testovací nebo produkční server, proměnnou prostředí lze použít k nastavení připojovacího řetězce na reálný databázový server.</span><span class="sxs-lookup"><span data-stu-id="7f42a-175">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="7f42a-176">Další informace naleznete v [tématu Konfigurace.](xref:fundamentals/configuration/index)</span><span class="sxs-lookup"><span data-stu-id="7f42a-176">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7f42a-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f42a-177">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="7f42a-178">Místní databáze SQL Server Express</span><span class="sxs-lookup"><span data-stu-id="7f42a-178">SQL Server Express LocalDB</span></span>

<span data-ttu-id="7f42a-179">LocalDB je odlehčená verze databázového stroje SQL Server Express, která je určena pro vývoj programů.</span><span class="sxs-lookup"><span data-stu-id="7f42a-179">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="7f42a-180">LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje žádná složitá konfigurace.</span><span class="sxs-lookup"><span data-stu-id="7f42a-180">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="7f42a-181">Ve výchozím nastavení databáze `*.mdf` LocalDB `C:/Users/<user/>` vytváří soubory v adresáři.</span><span class="sxs-lookup"><span data-stu-id="7f42a-181">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="7f42a-182">V nabídce **Zobrazení** otevřete **Průzkumník a průzkumník objektů serveru SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="7f42a-182">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Nabídka Zobrazit](sql/_static/ssox.png)

* <span data-ttu-id="7f42a-184">Klikněte pravým `Movie` tlačítkem myši na tabulku a vyberte **Zobrazit Návrhář**:</span><span class="sxs-lookup"><span data-stu-id="7f42a-184">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Kontextová nabídka otevřená v tabulce Film](sql/_static/design.png)

  ![Otevřese tabulka filmu v Návrháři](sql/_static/dv.png)

<span data-ttu-id="7f42a-187">Poznamenejte si `ID`ikonu klíče vedle aplikace .</span><span class="sxs-lookup"><span data-stu-id="7f42a-187">Note the key icon next to `ID`.</span></span> <span data-ttu-id="7f42a-188">Ve výchozím nastavení EF `ID` vytvoří vlastnost pojmenovanou pro primární klíč.</span><span class="sxs-lookup"><span data-stu-id="7f42a-188">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="7f42a-189">Klikněte pravým `Movie` tlačítkem myši na tabulku a vyberte **zobrazit data**:</span><span class="sxs-lookup"><span data-stu-id="7f42a-189">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Otevřená tabulka filmu zobrazující data tabulky](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="7f42a-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f42a-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7f42a-192">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7f42a-192">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="7f42a-193">Osivo databáze</span><span class="sxs-lookup"><span data-stu-id="7f42a-193">Seed the database</span></span>

<span data-ttu-id="7f42a-194">Vytvořte novou `SeedData` třídu pojmenovanou ve složce *Modely* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="7f42a-194">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="7f42a-195">Pokud jsou nějaké filmy v DB, inicializátor osiva vrátí a žádné filmy jsou přidány.</span><span class="sxs-lookup"><span data-stu-id="7f42a-195">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="7f42a-196">Přidejte inicializátor osiva</span><span class="sxs-lookup"><span data-stu-id="7f42a-196">Add the seed initializer</span></span>

<span data-ttu-id="7f42a-197">V *Program.cs*upravte metodu `Main` takto:</span><span class="sxs-lookup"><span data-stu-id="7f42a-197">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="7f42a-198">Získejte kontextové instance DB z kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="7f42a-198">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="7f42a-199">Volání metody osiva, předávání k ní kontextu.</span><span class="sxs-lookup"><span data-stu-id="7f42a-199">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="7f42a-200">Po dokončení metody osiva zlikvidujte kontext.</span><span class="sxs-lookup"><span data-stu-id="7f42a-200">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="7f42a-201">Následující kód zobrazuje aktualizovaný *soubor Program.cs.*</span><span class="sxs-lookup"><span data-stu-id="7f42a-201">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="7f42a-202">Produkční aplikace nebude `Database.Migrate`volat .</span><span class="sxs-lookup"><span data-stu-id="7f42a-202">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="7f42a-203">Je přidán do předchozího kódu, aby se `Update-Database` zabránilo následující výjimku, když nebyla spuštěna:</span><span class="sxs-lookup"><span data-stu-id="7f42a-203">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="7f42a-204">SqlException: Nelze otevřít databázi "RazorPagesMovieContext-21" požadované přihlášení.</span><span class="sxs-lookup"><span data-stu-id="7f42a-204">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="7f42a-205">Přihlášení se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="7f42a-205">The login failed.</span></span>
<span data-ttu-id="7f42a-206">Přihlášení se nezdařilo pro uživatelské jméno uživatele.</span><span class="sxs-lookup"><span data-stu-id="7f42a-206">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="7f42a-207">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="7f42a-207">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7f42a-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f42a-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7f42a-209">Odstraňte všechny záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="7f42a-209">Delete all the records in the DB.</span></span> <span data-ttu-id="7f42a-210">Můžete to udělat s odstranit odkazy v prohlížeči nebo od [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="7f42a-210">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="7f42a-211">Vynutit aplikaci inicializovat (volání metod ve `Startup` třídě), takže metoda osiva spustí.</span><span class="sxs-lookup"><span data-stu-id="7f42a-211">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="7f42a-212">Chcete-li vynutit inicializaci, musí být iis Express zastaven a restartován.</span><span class="sxs-lookup"><span data-stu-id="7f42a-212">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="7f42a-213">Můžete to provést s některou z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="7f42a-213">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="7f42a-214">Klepněte pravým tlačítkem myši na ikonu systémové lišty IIS Express v oznamovací oblasti a klepněte na **Ukončit** nebo **zastavit web**:</span><span class="sxs-lookup"><span data-stu-id="7f42a-214">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Ikona systémové lišty IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](sql/_static/stopIIS.png)

    * <span data-ttu-id="7f42a-217">Pokud jste používali VS v režimu bez ladění, stiskněte klávesu F5 a spusťte v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="7f42a-217">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="7f42a-218">Pokud jste v režimu ladění používali VS, zastavte ladicí program a stiskněte klávesu F5.</span><span class="sxs-lookup"><span data-stu-id="7f42a-218">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7f42a-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f42a-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7f42a-220">Odstranit všechny záznamy v DB (Tak bude spuštěna metoda osiva).</span><span class="sxs-lookup"><span data-stu-id="7f42a-220">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="7f42a-221">Zastavit a spustit aplikaci pro osílku databáze.</span><span class="sxs-lookup"><span data-stu-id="7f42a-221">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="7f42a-222">Aplikace zobrazuje osývaná data.</span><span class="sxs-lookup"><span data-stu-id="7f42a-222">The app shows the seeded data.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7f42a-223">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7f42a-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7f42a-224">Odstranit všechny záznamy v DB (Tak bude spuštěna metoda osiva).</span><span class="sxs-lookup"><span data-stu-id="7f42a-224">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="7f42a-225">Zastavit a spustit aplikaci pro osílku databáze.</span><span class="sxs-lookup"><span data-stu-id="7f42a-225">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="7f42a-226">Aplikace zobrazuje osývaná data.</span><span class="sxs-lookup"><span data-stu-id="7f42a-226">The app shows the seeded data.</span></span>

---

<span data-ttu-id="7f42a-227">Aplikace zobrazuje osývaná data:</span><span class="sxs-lookup"><span data-stu-id="7f42a-227">The app shows the seeded data:</span></span>

![Filmová aplikace otevřená v Chromu zobrazující data filmu](sql/_static/m55.png)

<span data-ttu-id="7f42a-229">Další kurz vyčistí prezentaci dat.</span><span class="sxs-lookup"><span data-stu-id="7f42a-229">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7f42a-230">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="7f42a-230">Additional resources</span></span>

* [<span data-ttu-id="7f42a-231">Verze tohoto kurzu pro YouTube</span><span class="sxs-lookup"><span data-stu-id="7f42a-231">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="7f42a-232">[Předchozí: Scaffolded Razor Stránky](xref:tutorials/razor-pages/page)
> [Další: Aktualizace stránek](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="7f42a-232">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
