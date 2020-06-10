---
title: 4. část s databází a ASP.NET Core
author: rick-anderson
description: Část 4 série kurzů na Razor stránkách.
ms.author: riande
ms.date: 7/22/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 21ae2ed4e91a0b3e52b1cdad1f4f4686c50614ba
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652984"
---
# <a name="part-4-with-a-database-and-aspnet-core"></a><span data-ttu-id="d9227-103">4. část s databází a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d9227-103">Part 4, with a database and ASP.NET Core</span></span>

<span data-ttu-id="d9227-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Jan Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="d9227-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="d9227-105">`RazorPagesMovieContext`Objekt zpracovává úlohu připojení k databázi a mapování `Movie` objektů na záznamy databáze.</span><span class="sxs-lookup"><span data-stu-id="d9227-105">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="d9227-106">Kontext databáze je zaregistrován s kontejnerem [Injektáže závislosti](xref:fundamentals/dependency-injection) v `ConfigureServices` metodě v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d9227-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d9227-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9227-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d9227-108">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d9227-108">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="d9227-109">[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="d9227-109">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="d9227-110">Pro místní vývoj získá připojovací řetězec ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="d9227-110">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d9227-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9227-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d9227-112">Hodnota názvu pro databázi ( `Database={Database name}` ) bude pro vygenerovaný kód odlišná.</span><span class="sxs-lookup"><span data-stu-id="d9227-112">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="d9227-113">Hodnota name je libovolná.</span><span class="sxs-lookup"><span data-stu-id="d9227-113">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d9227-114">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d9227-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="d9227-115">Při nasazení aplikace do testovacího nebo provozního serveru lze použít proměnnou prostředí k nastavení připojovacího řetězce na skutečný databázový server.</span><span class="sxs-lookup"><span data-stu-id="d9227-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="d9227-116">Další informace najdete v části [Konfigurace](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="d9227-116">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d9227-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9227-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="d9227-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="d9227-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="d9227-119">LocalDB je zjednodušená verze databázového stroje SQL Server Express, který je zaměřený na vývoj programu.</span><span class="sxs-lookup"><span data-stu-id="d9227-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="d9227-120">LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje složitá konfigurace.</span><span class="sxs-lookup"><span data-stu-id="d9227-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="d9227-121">Ve výchozím nastavení vytvoří databáze LocalDB `*.mdf` soubory v `C:\Users\<user>\` adresáři.</span><span class="sxs-lookup"><span data-stu-id="d9227-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="d9227-122">V nabídce **Zobrazit** otevřete **Průzkumník objektů systému SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="d9227-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Nabídka Zobrazit](sql/_static/ssox.png)

* <span data-ttu-id="d9227-124">Klikněte pravým tlačítkem na `Movie` tabulku a vyberte **Zobrazit návrháře**:</span><span class="sxs-lookup"><span data-stu-id="d9227-124">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Kontextové nabídky otevřené v tabulce videí](sql/_static/design.png)

  ![Tabulky filmů otevřené v Návrháři](sql/_static/dv.png)

<span data-ttu-id="d9227-127">Poznamenejte si ikonu klíče vedle `ID` .</span><span class="sxs-lookup"><span data-stu-id="d9227-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="d9227-128">Ve výchozím nastavení EF vytvoří vlastnost s názvem `ID` pro primární klíč.</span><span class="sxs-lookup"><span data-stu-id="d9227-128">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="d9227-129">Klikněte pravým tlačítkem na `Movie` tabulku a vyberte **Zobrazit data**:</span><span class="sxs-lookup"><span data-stu-id="d9227-129">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Tabulka videa otevřená zobrazující data tabulky](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d9227-131">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d9227-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="d9227-132">Dosazení databáze</span><span class="sxs-lookup"><span data-stu-id="d9227-132">Seed the database</span></span>

<span data-ttu-id="d9227-133">Vytvořte novou třídu s názvem `SeedData` ve složce *modely* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d9227-133">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="d9227-134">Pokud databáze obsahuje nějaké filmy, inicializátor počáteční hodnoty se vrátí a nepřidá se žádné filmy.</span><span class="sxs-lookup"><span data-stu-id="d9227-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="d9227-135">Přidat inicializátor počáteční hodnoty</span><span class="sxs-lookup"><span data-stu-id="d9227-135">Add the seed initializer</span></span>

<span data-ttu-id="d9227-136">V *program.cs*upravte `Main` metodu a proveďte následující:</span><span class="sxs-lookup"><span data-stu-id="d9227-136">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="d9227-137">Získá instanci kontextu databáze z kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="d9227-137">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="d9227-138">Zavolejte metodu počáteční hodnoty a předejte jí kontext.</span><span class="sxs-lookup"><span data-stu-id="d9227-138">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="d9227-139">Vyřazení kontextu, když se dokončí metoda počáteční hodnoty.</span><span class="sxs-lookup"><span data-stu-id="d9227-139">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="d9227-140">Následující kód ukazuje aktualizovaný soubor *program.cs* .</span><span class="sxs-lookup"><span data-stu-id="d9227-140">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="d9227-141">K následující výjimce dojde v případě, že nebyla `Update-Database` spuštěna:</span><span class="sxs-lookup"><span data-stu-id="d9227-141">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="d9227-142">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="d9227-142">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d9227-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9227-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d9227-144">Odstraňte všechny záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="d9227-144">Delete all the records in the DB.</span></span> <span data-ttu-id="d9227-145">Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z [SSOX](xref:tutorials/razor-pages/new-field#ssox) .</span><span class="sxs-lookup"><span data-stu-id="d9227-145">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="d9227-146">Vynuťte inicializaci aplikace (volání metod ve `Startup` třídě), takže se metoda počáteční hodnoty spustí.</span><span class="sxs-lookup"><span data-stu-id="d9227-146">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="d9227-147">Chcete-li vynutit inicializaci, IIS Express musí být zastavena a restartována.</span><span class="sxs-lookup"><span data-stu-id="d9227-147">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="d9227-148">Můžete to provést s některým z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="d9227-148">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="d9227-149">Klikněte pravým tlačítkem myši na ikonu IIS Express systému v oznamovací oblasti a klepněte na **konec** nebo **zastavení webu**:</span><span class="sxs-lookup"><span data-stu-id="d9227-149">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express ikona na hlavním panelu systému](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](sql/_static/stopIIS.png)

    * <span data-ttu-id="d9227-152">Pokud jste spustili VS v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="d9227-152">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="d9227-153">Pokud jste spustili VS v režimu ladění, ukončete ladicí program a stiskněte klávesu F5.</span><span class="sxs-lookup"><span data-stu-id="d9227-153">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d9227-154">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d9227-154">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="d9227-155">Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí).</span><span class="sxs-lookup"><span data-stu-id="d9227-155">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="d9227-156">Zastavením a spuštěním aplikace dosadíte databázi.</span><span class="sxs-lookup"><span data-stu-id="d9227-156">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="d9227-157">Aplikace zobrazuje dosazený data.</span><span class="sxs-lookup"><span data-stu-id="d9227-157">The app shows the seeded data.</span></span>

---

<span data-ttu-id="d9227-158">V dalším kurzu dojde k vylepšení prezentace dat.</span><span class="sxs-lookup"><span data-stu-id="d9227-158">The next tutorial will improve the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d9227-159">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d9227-159">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="d9227-160">[Předchozí: vygenerované uživatelské rozhraní Razor Stránky](xref:tutorials/razor-pages/page) 
>  [Další: aktualizace stránek](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="d9227-160">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="d9227-161">`RazorPagesMovieContext`Objekt zpracovává úlohu připojení k databázi a mapování `Movie` objektů na záznamy databáze.</span><span class="sxs-lookup"><span data-stu-id="d9227-161">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="d9227-162">Kontext databáze je zaregistrován s kontejnerem [Injektáže závislosti](xref:fundamentals/dependency-injection) v `ConfigureServices` metodě v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d9227-162">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d9227-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9227-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d9227-164">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d9227-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="d9227-165">Další informace o metodách používaných v nástroji `ConfigureServices` naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="d9227-165">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="d9227-166">[Podpora GDPR (EU obecné nařízení o ochraně osobních údajů) v ASP.NET Core](xref:security/gdpr) pro `CookiePolicyOptions` .</span><span class="sxs-lookup"><span data-stu-id="d9227-166">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="d9227-167">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="d9227-167">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="d9227-168">[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="d9227-168">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="d9227-169">Pro místní vývoj získá připojovací řetězec ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="d9227-169">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d9227-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9227-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d9227-171">Hodnota názvu pro databázi ( `Database={Database name}` ) bude pro vygenerovaný kód odlišná.</span><span class="sxs-lookup"><span data-stu-id="d9227-171">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="d9227-172">Hodnota name je libovolná.</span><span class="sxs-lookup"><span data-stu-id="d9227-172">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d9227-173">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d9227-173">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d9227-174">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d9227-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="d9227-175">Při nasazení aplikace do testovacího nebo provozního serveru lze použít proměnnou prostředí k nastavení připojovacího řetězce na skutečný databázový server.</span><span class="sxs-lookup"><span data-stu-id="d9227-175">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="d9227-176">Další informace najdete v části [Konfigurace](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="d9227-176">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d9227-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9227-177">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="d9227-178">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="d9227-178">SQL Server Express LocalDB</span></span>

<span data-ttu-id="d9227-179">LocalDB je zjednodušená verze databázového stroje SQL Server Express, který je zaměřený na vývoj programu.</span><span class="sxs-lookup"><span data-stu-id="d9227-179">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="d9227-180">LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje složitá konfigurace.</span><span class="sxs-lookup"><span data-stu-id="d9227-180">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="d9227-181">Ve výchozím nastavení vytvoří databáze LocalDB `*.mdf` soubory v `C:/Users/<user/>` adresáři.</span><span class="sxs-lookup"><span data-stu-id="d9227-181">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="d9227-182">V nabídce **Zobrazit** otevřete **Průzkumník objektů systému SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="d9227-182">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Nabídka Zobrazit](sql/_static/ssox.png)

* <span data-ttu-id="d9227-184">Klikněte pravým tlačítkem na `Movie` tabulku a vyberte **Zobrazit návrháře**:</span><span class="sxs-lookup"><span data-stu-id="d9227-184">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Kontextová nabídka otevřená v tabulce videí](sql/_static/design.png)

  ![Tabulka videí otevřená v Návrháři](sql/_static/dv.png)

<span data-ttu-id="d9227-187">Poznamenejte si ikonu klíče vedle `ID` .</span><span class="sxs-lookup"><span data-stu-id="d9227-187">Note the key icon next to `ID`.</span></span> <span data-ttu-id="d9227-188">Ve výchozím nastavení EF vytvoří vlastnost s názvem `ID` pro primární klíč.</span><span class="sxs-lookup"><span data-stu-id="d9227-188">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="d9227-189">Klikněte pravým tlačítkem na `Movie` tabulku a vyberte **Zobrazit data**:</span><span class="sxs-lookup"><span data-stu-id="d9227-189">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Tabulka videa otevřená zobrazující data tabulky](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d9227-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d9227-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d9227-192">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d9227-192">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="d9227-193">Dosazení databáze</span><span class="sxs-lookup"><span data-stu-id="d9227-193">Seed the database</span></span>

<span data-ttu-id="d9227-194">Vytvořte novou třídu s názvem `SeedData` ve složce *modely* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d9227-194">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="d9227-195">Pokud databáze obsahuje nějaké filmy, inicializátor počáteční hodnoty se vrátí a nepřidá se žádné filmy.</span><span class="sxs-lookup"><span data-stu-id="d9227-195">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="d9227-196">Přidat inicializátor počáteční hodnoty</span><span class="sxs-lookup"><span data-stu-id="d9227-196">Add the seed initializer</span></span>

<span data-ttu-id="d9227-197">V *program.cs*upravte `Main` metodu a proveďte následující:</span><span class="sxs-lookup"><span data-stu-id="d9227-197">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="d9227-198">Získá instanci kontextu databáze z kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="d9227-198">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="d9227-199">Zavolejte metodu počáteční hodnoty a předejte jí kontext.</span><span class="sxs-lookup"><span data-stu-id="d9227-199">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="d9227-200">Vyřazení kontextu, když se dokončí metoda počáteční hodnoty.</span><span class="sxs-lookup"><span data-stu-id="d9227-200">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="d9227-201">Následující kód ukazuje aktualizovaný soubor *program.cs* .</span><span class="sxs-lookup"><span data-stu-id="d9227-201">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="d9227-202">Produkční aplikace by nevolala `Database.Migrate` .</span><span class="sxs-lookup"><span data-stu-id="d9227-202">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="d9227-203">Přidá se k předchozímu kódu, aby se zabránilo následující výjimce, pokud nebyla `Update-Database` spuštěna:</span><span class="sxs-lookup"><span data-stu-id="d9227-203">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="d9227-204">SqlException: Nelze otevřít databázi "RazorPagesMovieContext-21" požadovanou pro přihlášení.</span><span class="sxs-lookup"><span data-stu-id="d9227-204">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="d9227-205">Přihlášení se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="d9227-205">The login failed.</span></span>
<span data-ttu-id="d9227-206">Přihlášení uživatele "uživatelské jméno" se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="d9227-206">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="d9227-207">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="d9227-207">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d9227-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9227-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d9227-209">Odstraňte všechny záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="d9227-209">Delete all the records in the DB.</span></span> <span data-ttu-id="d9227-210">Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z [SSOX](xref:tutorials/razor-pages/new-field#ssox) .</span><span class="sxs-lookup"><span data-stu-id="d9227-210">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="d9227-211">Vynuťte inicializaci aplikace (volání metod ve `Startup` třídě), takže se metoda počáteční hodnoty spustí.</span><span class="sxs-lookup"><span data-stu-id="d9227-211">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="d9227-212">Chcete-li vynutit inicializaci, IIS Express musí být zastavena a restartována.</span><span class="sxs-lookup"><span data-stu-id="d9227-212">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="d9227-213">Můžete to provést s některým z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="d9227-213">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="d9227-214">V oznamovací oblasti klikněte pravým tlačítkem na ikonu na hlavním panelu IIS Express systému a klepněte na **konec** nebo **zastavení webu**:</span><span class="sxs-lookup"><span data-stu-id="d9227-214">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express ikona na hlavním panelu systému](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](sql/_static/stopIIS.png)

    * <span data-ttu-id="d9227-217">Pokud jste spustili VS v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="d9227-217">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="d9227-218">Pokud jste spustili VS v režimu ladění, ukončete ladicí program a stiskněte klávesu F5.</span><span class="sxs-lookup"><span data-stu-id="d9227-218">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d9227-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d9227-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d9227-220">Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí).</span><span class="sxs-lookup"><span data-stu-id="d9227-220">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="d9227-221">Zastavením a spuštěním aplikace dosadíte databázi.</span><span class="sxs-lookup"><span data-stu-id="d9227-221">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="d9227-222">Aplikace zobrazuje dosazený data.</span><span class="sxs-lookup"><span data-stu-id="d9227-222">The app shows the seeded data.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d9227-223">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d9227-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d9227-224">Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí).</span><span class="sxs-lookup"><span data-stu-id="d9227-224">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="d9227-225">Zastavením a spuštěním aplikace dosadíte databázi.</span><span class="sxs-lookup"><span data-stu-id="d9227-225">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="d9227-226">Aplikace zobrazuje dosazený data.</span><span class="sxs-lookup"><span data-stu-id="d9227-226">The app shows the seeded data.</span></span>

---

<span data-ttu-id="d9227-227">Aplikace zobrazuje dosazený data:</span><span class="sxs-lookup"><span data-stu-id="d9227-227">The app shows the seeded data:</span></span>

![Aplikace Movie otevřená v Chrome zobrazující data videa](sql/_static/m55.png)

<span data-ttu-id="d9227-229">V dalším kurzu se vyčistí prezentace dat.</span><span class="sxs-lookup"><span data-stu-id="d9227-229">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d9227-230">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d9227-230">Additional resources</span></span>

* [<span data-ttu-id="d9227-231">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="d9227-231">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="d9227-232">[Předchozí: vygenerované uživatelské rozhraní Razor Stránky](xref:tutorials/razor-pages/page) 
>  [Další: aktualizace stránek](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="d9227-232">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
