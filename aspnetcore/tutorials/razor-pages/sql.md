---
title: 4. část s databází a ASP.NET Core
author: rick-anderson
description: Část 4 série kurzů na Razor stránkách.
ms.author: riande
ms.date: 7/22/2019
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 6d1a93a9cab49d33181e88b7fdc0f203a1e2546c
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022417"
---
# <a name="part-4-with-a-database-and-aspnet-core"></a><span data-ttu-id="e27a2-103">4. část s databází a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e27a2-103">Part 4, with a database and ASP.NET Core</span></span>

<span data-ttu-id="e27a2-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Jan Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="e27a2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="e27a2-105">`RazorPagesMovieContext`Objekt zpracovává úlohu připojení k databázi a mapování `Movie` objektů na záznamy databáze.</span><span class="sxs-lookup"><span data-stu-id="e27a2-105">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="e27a2-106">Kontext databáze je zaregistrován s kontejnerem [Injektáže závislosti](xref:fundamentals/dependency-injection) v `ConfigureServices` metodě v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="e27a2-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e27a2-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e27a2-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e27a2-108">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e27a2-108">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="e27a2-109">[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="e27a2-109">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="e27a2-110">Pro místní vývoj získá připojovací řetězec z *appsettings.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="e27a2-110">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e27a2-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e27a2-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e27a2-112">Hodnota názvu pro databázi ( `Database={Database name}` ) bude pro vygenerovaný kód odlišná.</span><span class="sxs-lookup"><span data-stu-id="e27a2-112">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="e27a2-113">Hodnota name je libovolná.</span><span class="sxs-lookup"><span data-stu-id="e27a2-113">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e27a2-114">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e27a2-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="e27a2-115">Při nasazení aplikace do testovacího nebo provozního serveru lze použít proměnnou prostředí k nastavení připojovacího řetězce na skutečný databázový server.</span><span class="sxs-lookup"><span data-stu-id="e27a2-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="e27a2-116">Další informace najdete v části [Konfigurace](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="e27a2-116">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e27a2-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e27a2-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="e27a2-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="e27a2-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="e27a2-119">LocalDB je zjednodušená verze databázového stroje SQL Server Express, který je zaměřený na vývoj programu.</span><span class="sxs-lookup"><span data-stu-id="e27a2-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="e27a2-120">LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje složitá konfigurace.</span><span class="sxs-lookup"><span data-stu-id="e27a2-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="e27a2-121">Ve výchozím nastavení vytvoří databáze LocalDB `*.mdf` soubory v `C:\Users\<user>\` adresáři.</span><span class="sxs-lookup"><span data-stu-id="e27a2-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="e27a2-122">V nabídce **Zobrazit** otevřete **Průzkumník objektů systému SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="e27a2-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Nabídka Zobrazit](sql/_static/ssox.png)

* <span data-ttu-id="e27a2-124">Klikněte pravým tlačítkem na `Movie` tabulku a vyberte **Zobrazit návrháře**:</span><span class="sxs-lookup"><span data-stu-id="e27a2-124">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Kontextové nabídky otevřené v tabulce videí](sql/_static/design.png)

  ![Tabulky filmů otevřené v Návrháři](sql/_static/dv.png)

<span data-ttu-id="e27a2-127">Poznamenejte si ikonu klíče vedle `ID` .</span><span class="sxs-lookup"><span data-stu-id="e27a2-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="e27a2-128">Ve výchozím nastavení EF vytvoří vlastnost s názvem `ID` pro primární klíč.</span><span class="sxs-lookup"><span data-stu-id="e27a2-128">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="e27a2-129">Klikněte pravým tlačítkem na `Movie` tabulku a vyberte **Zobrazit data**:</span><span class="sxs-lookup"><span data-stu-id="e27a2-129">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Tabulka videa otevřená zobrazující data tabulky](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e27a2-131">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e27a2-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="e27a2-132">Dosazení databáze</span><span class="sxs-lookup"><span data-stu-id="e27a2-132">Seed the database</span></span>

<span data-ttu-id="e27a2-133">Vytvořte novou třídu s názvem `SeedData` ve složce *modely* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e27a2-133">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="e27a2-134">Pokud databáze obsahuje nějaké filmy, inicializátor počáteční hodnoty se vrátí a nepřidá se žádné filmy.</span><span class="sxs-lookup"><span data-stu-id="e27a2-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="e27a2-135">Přidat inicializátor počáteční hodnoty</span><span class="sxs-lookup"><span data-stu-id="e27a2-135">Add the seed initializer</span></span>

<span data-ttu-id="e27a2-136">V *program.cs*upravte `Main` metodu a proveďte následující:</span><span class="sxs-lookup"><span data-stu-id="e27a2-136">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="e27a2-137">Získá instanci kontextu databáze z kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="e27a2-137">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="e27a2-138">Zavolejte metodu počáteční hodnoty a předejte jí kontext.</span><span class="sxs-lookup"><span data-stu-id="e27a2-138">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="e27a2-139">Vyřazení kontextu, když se dokončí metoda počáteční hodnoty.</span><span class="sxs-lookup"><span data-stu-id="e27a2-139">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="e27a2-140">Následující kód ukazuje aktualizovaný soubor *program.cs* .</span><span class="sxs-lookup"><span data-stu-id="e27a2-140">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="e27a2-141">K následující výjimce dojde v případě, že nebyla `Update-Database` spuštěna:</span><span class="sxs-lookup"><span data-stu-id="e27a2-141">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="e27a2-142">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="e27a2-142">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e27a2-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e27a2-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e27a2-144">Odstraňte všechny záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="e27a2-144">Delete all the records in the DB.</span></span> <span data-ttu-id="e27a2-145">Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z [SSOX](xref:tutorials/razor-pages/new-field#ssox) .</span><span class="sxs-lookup"><span data-stu-id="e27a2-145">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="e27a2-146">Vynuťte inicializaci aplikace (volání metod ve `Startup` třídě), takže se metoda počáteční hodnoty spustí.</span><span class="sxs-lookup"><span data-stu-id="e27a2-146">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="e27a2-147">Chcete-li vynutit inicializaci, IIS Express musí být zastavena a restartována.</span><span class="sxs-lookup"><span data-stu-id="e27a2-147">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="e27a2-148">Můžete to provést s některým z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="e27a2-148">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="e27a2-149">Klikněte pravým tlačítkem myši na ikonu IIS Express systému v oznamovací oblasti a klepněte na **konec** nebo **zastavení webu**:</span><span class="sxs-lookup"><span data-stu-id="e27a2-149">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express ikona na hlavním panelu systému](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](sql/_static/stopIIS.png)

    * <span data-ttu-id="e27a2-152">Pokud jste spustili VS v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="e27a2-152">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="e27a2-153">Pokud jste spustili VS v režimu ladění, ukončete ladicí program a stiskněte klávesu F5.</span><span class="sxs-lookup"><span data-stu-id="e27a2-153">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e27a2-154">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e27a2-154">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e27a2-155">Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí).</span><span class="sxs-lookup"><span data-stu-id="e27a2-155">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="e27a2-156">Zastavením a spuštěním aplikace dosadíte databázi.</span><span class="sxs-lookup"><span data-stu-id="e27a2-156">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="e27a2-157">Aplikace zobrazuje dosazený data.</span><span class="sxs-lookup"><span data-stu-id="e27a2-157">The app shows the seeded data.</span></span>

---

<span data-ttu-id="e27a2-158">V dalším kurzu dojde k vylepšení prezentace dat.</span><span class="sxs-lookup"><span data-stu-id="e27a2-158">The next tutorial will improve the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e27a2-159">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e27a2-159">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e27a2-160">[Předchozí: vygenerované uživatelské rozhraní Razor Stránky](xref:tutorials/razor-pages/page) 
>  [Další: aktualizace stránek](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="e27a2-160">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="e27a2-161">`RazorPagesMovieContext`Objekt zpracovává úlohu připojení k databázi a mapování `Movie` objektů na záznamy databáze.</span><span class="sxs-lookup"><span data-stu-id="e27a2-161">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="e27a2-162">Kontext databáze je zaregistrován s kontejnerem [Injektáže závislosti](xref:fundamentals/dependency-injection) v `ConfigureServices` metodě v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="e27a2-162">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e27a2-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e27a2-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e27a2-164">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e27a2-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="e27a2-165">Další informace o metodách používaných v nástroji `ConfigureServices` naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="e27a2-165">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="e27a2-166">[Podpora GDPR (EU obecné nařízení o ochraně osobních údajů) v ASP.NET Core](xref:security/gdpr) pro `CookiePolicyOptions` .</span><span class="sxs-lookup"><span data-stu-id="e27a2-166">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="e27a2-167">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="e27a2-167">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="e27a2-168">[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="e27a2-168">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="e27a2-169">Pro místní vývoj získá připojovací řetězec z *appsettings.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="e27a2-169">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e27a2-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e27a2-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e27a2-171">Hodnota názvu pro databázi ( `Database={Database name}` ) bude pro vygenerovaný kód odlišná.</span><span class="sxs-lookup"><span data-stu-id="e27a2-171">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="e27a2-172">Hodnota name je libovolná.</span><span class="sxs-lookup"><span data-stu-id="e27a2-172">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="e27a2-173">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e27a2-173">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e27a2-174">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e27a2-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="e27a2-175">Při nasazení aplikace do testovacího nebo provozního serveru lze použít proměnnou prostředí k nastavení připojovacího řetězce na skutečný databázový server.</span><span class="sxs-lookup"><span data-stu-id="e27a2-175">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="e27a2-176">Další informace najdete v části [Konfigurace](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="e27a2-176">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e27a2-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e27a2-177">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="e27a2-178">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="e27a2-178">SQL Server Express LocalDB</span></span>

<span data-ttu-id="e27a2-179">LocalDB je zjednodušená verze databázového stroje SQL Server Express, který je zaměřený na vývoj programu.</span><span class="sxs-lookup"><span data-stu-id="e27a2-179">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="e27a2-180">LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje složitá konfigurace.</span><span class="sxs-lookup"><span data-stu-id="e27a2-180">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="e27a2-181">Ve výchozím nastavení vytvoří databáze LocalDB `*.mdf` soubory v `C:/Users/<user/>` adresáři.</span><span class="sxs-lookup"><span data-stu-id="e27a2-181">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="e27a2-182">V nabídce **Zobrazit** otevřete **Průzkumník objektů systému SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="e27a2-182">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Nabídka Zobrazit](sql/_static/ssox.png)

* <span data-ttu-id="e27a2-184">Klikněte pravým tlačítkem na `Movie` tabulku a vyberte **Zobrazit návrháře**:</span><span class="sxs-lookup"><span data-stu-id="e27a2-184">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Kontextová nabídka otevřená v tabulce videí](sql/_static/design.png)

  ![Tabulka videí otevřená v Návrháři](sql/_static/dv.png)

<span data-ttu-id="e27a2-187">Poznamenejte si ikonu klíče vedle `ID` .</span><span class="sxs-lookup"><span data-stu-id="e27a2-187">Note the key icon next to `ID`.</span></span> <span data-ttu-id="e27a2-188">Ve výchozím nastavení EF vytvoří vlastnost s názvem `ID` pro primární klíč.</span><span class="sxs-lookup"><span data-stu-id="e27a2-188">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="e27a2-189">Klikněte pravým tlačítkem na `Movie` tabulku a vyberte **Zobrazit data**:</span><span class="sxs-lookup"><span data-stu-id="e27a2-189">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Tabulka videa otevřená zobrazující data tabulky](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="e27a2-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e27a2-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e27a2-192">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e27a2-192">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="e27a2-193">Dosazení databáze</span><span class="sxs-lookup"><span data-stu-id="e27a2-193">Seed the database</span></span>

<span data-ttu-id="e27a2-194">Vytvořte novou třídu s názvem `SeedData` ve složce *modely* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e27a2-194">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="e27a2-195">Pokud databáze obsahuje nějaké filmy, inicializátor počáteční hodnoty se vrátí a nepřidá se žádné filmy.</span><span class="sxs-lookup"><span data-stu-id="e27a2-195">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="e27a2-196">Přidat inicializátor počáteční hodnoty</span><span class="sxs-lookup"><span data-stu-id="e27a2-196">Add the seed initializer</span></span>

<span data-ttu-id="e27a2-197">V *program.cs*upravte `Main` metodu a proveďte následující:</span><span class="sxs-lookup"><span data-stu-id="e27a2-197">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="e27a2-198">Získá instanci kontextu databáze z kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="e27a2-198">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="e27a2-199">Zavolejte metodu počáteční hodnoty a předejte jí kontext.</span><span class="sxs-lookup"><span data-stu-id="e27a2-199">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="e27a2-200">Vyřazení kontextu, když se dokončí metoda počáteční hodnoty.</span><span class="sxs-lookup"><span data-stu-id="e27a2-200">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="e27a2-201">Následující kód ukazuje aktualizovaný soubor *program.cs* .</span><span class="sxs-lookup"><span data-stu-id="e27a2-201">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="e27a2-202">Produkční aplikace by nevolala `Database.Migrate` .</span><span class="sxs-lookup"><span data-stu-id="e27a2-202">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="e27a2-203">Přidá se k předchozímu kódu, aby se zabránilo následující výjimce, pokud nebyla `Update-Database` spuštěna:</span><span class="sxs-lookup"><span data-stu-id="e27a2-203">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="e27a2-204">SqlException: Nelze otevřít databázi " Razor PagesMovieContext-21" požadovanou pro přihlášení.</span><span class="sxs-lookup"><span data-stu-id="e27a2-204">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="e27a2-205">Přihlášení se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="e27a2-205">The login failed.</span></span>
<span data-ttu-id="e27a2-206">Přihlášení uživatele "uživatelské jméno" se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="e27a2-206">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="e27a2-207">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="e27a2-207">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e27a2-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e27a2-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e27a2-209">Odstraňte všechny záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="e27a2-209">Delete all the records in the DB.</span></span> <span data-ttu-id="e27a2-210">Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z [SSOX](xref:tutorials/razor-pages/new-field#ssox) .</span><span class="sxs-lookup"><span data-stu-id="e27a2-210">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="e27a2-211">Vynuťte inicializaci aplikace (volání metod ve `Startup` třídě), takže se metoda počáteční hodnoty spustí.</span><span class="sxs-lookup"><span data-stu-id="e27a2-211">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="e27a2-212">Chcete-li vynutit inicializaci, IIS Express musí být zastavena a restartována.</span><span class="sxs-lookup"><span data-stu-id="e27a2-212">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="e27a2-213">Můžete to provést s některým z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="e27a2-213">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="e27a2-214">V oznamovací oblasti klikněte pravým tlačítkem na ikonu na hlavním panelu IIS Express systému a klepněte na **konec** nebo **zastavení webu**:</span><span class="sxs-lookup"><span data-stu-id="e27a2-214">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express ikona na hlavním panelu systému](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](sql/_static/stopIIS.png)

    * <span data-ttu-id="e27a2-217">Pokud jste spustili VS v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="e27a2-217">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="e27a2-218">Pokud jste spustili VS v režimu ladění, ukončete ladicí program a stiskněte klávesu F5.</span><span class="sxs-lookup"><span data-stu-id="e27a2-218">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e27a2-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e27a2-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e27a2-220">Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí).</span><span class="sxs-lookup"><span data-stu-id="e27a2-220">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="e27a2-221">Zastavením a spuštěním aplikace dosadíte databázi.</span><span class="sxs-lookup"><span data-stu-id="e27a2-221">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="e27a2-222">Aplikace zobrazuje dosazený data.</span><span class="sxs-lookup"><span data-stu-id="e27a2-222">The app shows the seeded data.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e27a2-223">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e27a2-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e27a2-224">Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí).</span><span class="sxs-lookup"><span data-stu-id="e27a2-224">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="e27a2-225">Zastavením a spuštěním aplikace dosadíte databázi.</span><span class="sxs-lookup"><span data-stu-id="e27a2-225">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="e27a2-226">Aplikace zobrazuje dosazený data.</span><span class="sxs-lookup"><span data-stu-id="e27a2-226">The app shows the seeded data.</span></span>

---

<span data-ttu-id="e27a2-227">Aplikace zobrazuje dosazený data:</span><span class="sxs-lookup"><span data-stu-id="e27a2-227">The app shows the seeded data:</span></span>

![Aplikace Movie otevřená v Chrome zobrazující data videa](sql/_static/m55.png)

<span data-ttu-id="e27a2-229">V dalším kurzu se vyčistí prezentace dat.</span><span class="sxs-lookup"><span data-stu-id="e27a2-229">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e27a2-230">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e27a2-230">Additional resources</span></span>

* [<span data-ttu-id="e27a2-231">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="e27a2-231">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="e27a2-232">[Předchozí: vygenerované uživatelské rozhraní Razor Stránky](xref:tutorials/razor-pages/page) 
>  [Další: aktualizace stránek](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="e27a2-232">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
