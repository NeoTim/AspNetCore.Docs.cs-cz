---
title: 4. část s databází a ASP.NET Core
author: rick-anderson
description: Část 4 série kurzů na Razor stránkách.
ms.author: riande
ms.date: 7/22/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 68764a5b8c908012cc0053aa4e3ea071f2ce423d
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400905"
---
# <a name="part-4-with-a-database-and-aspnet-core"></a><span data-ttu-id="3b2fd-103">4. část s databází a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3b2fd-103">Part 4, with a database and ASP.NET Core</span></span>

<span data-ttu-id="3b2fd-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Jan Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="3b2fd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="3b2fd-105">`RazorPagesMovieContext`Objekt zpracovává úlohu připojení k databázi a mapování `Movie` objektů na záznamy databáze.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-105">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="3b2fd-106">Kontext databáze je zaregistrován s kontejnerem [Injektáže závislosti](xref:fundamentals/dependency-injection) v `ConfigureServices` metodě v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="3b2fd-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b2fd-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b2fd-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3b2fd-108">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b2fd-108">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="3b2fd-109">[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="3b2fd-109">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="3b2fd-110">Pro místní vývoj získá připojovací řetězec z *appsettings.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-110">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b2fd-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b2fd-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3b2fd-112">Hodnota názvu pro databázi ( `Database={Database name}` ) bude pro vygenerovaný kód odlišná.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-112">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="3b2fd-113">Hodnota name je libovolná.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-113">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3b2fd-114">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b2fd-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="3b2fd-115">Při nasazení aplikace do testovacího nebo provozního serveru lze použít proměnnou prostředí k nastavení připojovacího řetězce na skutečný databázový server.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="3b2fd-116">Další informace najdete v části [Konfigurace](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="3b2fd-116">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b2fd-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b2fd-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="3b2fd-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="3b2fd-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="3b2fd-119">LocalDB je zjednodušená verze databázového stroje SQL Server Express, který je zaměřený na vývoj programu.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="3b2fd-120">LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje složitá konfigurace.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="3b2fd-121">Ve výchozím nastavení vytvoří databáze LocalDB `*.mdf` soubory v `C:\Users\<user>\` adresáři.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="3b2fd-122">V nabídce **Zobrazit** otevřete **Průzkumník objektů systému SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="3b2fd-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Nabídka Zobrazit](sql/_static/ssox.png)

* <span data-ttu-id="3b2fd-124">Klikněte pravým tlačítkem na `Movie` tabulku a vyberte **Zobrazit návrháře**:</span><span class="sxs-lookup"><span data-stu-id="3b2fd-124">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Kontextové nabídky otevřené v tabulce videí](sql/_static/design.png)

  ![Tabulky filmů otevřené v Návrháři](sql/_static/dv.png)

<span data-ttu-id="3b2fd-127">Poznamenejte si ikonu klíče vedle `ID` .</span><span class="sxs-lookup"><span data-stu-id="3b2fd-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="3b2fd-128">Ve výchozím nastavení EF vytvoří vlastnost s názvem `ID` pro primární klíč.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-128">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="3b2fd-129">Klikněte pravým tlačítkem na `Movie` tabulku a vyberte **Zobrazit data**:</span><span class="sxs-lookup"><span data-stu-id="3b2fd-129">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Tabulka videa otevřená zobrazující data tabulky](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3b2fd-131">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b2fd-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="3b2fd-132">Dosazení databáze</span><span class="sxs-lookup"><span data-stu-id="3b2fd-132">Seed the database</span></span>

<span data-ttu-id="3b2fd-133">Vytvořte novou třídu s názvem `SeedData` ve složce *modely* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3b2fd-133">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="3b2fd-134">Pokud databáze obsahuje nějaké filmy, inicializátor počáteční hodnoty se vrátí a nepřidá se žádné filmy.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="3b2fd-135">Přidat inicializátor počáteční hodnoty</span><span class="sxs-lookup"><span data-stu-id="3b2fd-135">Add the seed initializer</span></span>

<span data-ttu-id="3b2fd-136">V *program.cs*upravte `Main` metodu a proveďte následující:</span><span class="sxs-lookup"><span data-stu-id="3b2fd-136">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="3b2fd-137">Získá instanci kontextu databáze z kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-137">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="3b2fd-138">Zavolejte metodu počáteční hodnoty a předejte jí kontext.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-138">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="3b2fd-139">Vyřazení kontextu, když se dokončí metoda počáteční hodnoty.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-139">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="3b2fd-140">Následující kód ukazuje aktualizovaný soubor *program.cs* .</span><span class="sxs-lookup"><span data-stu-id="3b2fd-140">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="3b2fd-141">K následující výjimce dojde v případě, že nebyla `Update-Database` spuštěna:</span><span class="sxs-lookup"><span data-stu-id="3b2fd-141">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="3b2fd-142">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="3b2fd-142">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b2fd-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b2fd-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3b2fd-144">Odstraňte všechny záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-144">Delete all the records in the DB.</span></span> <span data-ttu-id="3b2fd-145">Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z [SSOX](xref:tutorials/razor-pages/new-field#ssox) .</span><span class="sxs-lookup"><span data-stu-id="3b2fd-145">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="3b2fd-146">Vynuťte inicializaci aplikace (volání metod ve `Startup` třídě), takže se metoda počáteční hodnoty spustí.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-146">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="3b2fd-147">Chcete-li vynutit inicializaci, IIS Express musí být zastavena a restartována.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-147">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="3b2fd-148">Můžete to provést s některým z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="3b2fd-148">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="3b2fd-149">Klikněte pravým tlačítkem myši na ikonu IIS Express systému v oznamovací oblasti a klepněte na **konec** nebo **zastavení webu**:</span><span class="sxs-lookup"><span data-stu-id="3b2fd-149">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express ikona na hlavním panelu systému](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](sql/_static/stopIIS.png)

    * <span data-ttu-id="3b2fd-152">Pokud jste spustili VS v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-152">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="3b2fd-153">Pokud jste spustili VS v režimu ladění, ukončete ladicí program a stiskněte klávesu F5.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-153">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3b2fd-154">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b2fd-154">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="3b2fd-155">Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí).</span><span class="sxs-lookup"><span data-stu-id="3b2fd-155">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="3b2fd-156">Zastavením a spuštěním aplikace dosadíte databázi.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-156">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="3b2fd-157">Aplikace zobrazuje dosazený data.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-157">The app shows the seeded data.</span></span>

---

<span data-ttu-id="3b2fd-158">V dalším kurzu dojde k vylepšení prezentace dat.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-158">The next tutorial will improve the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3b2fd-159">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3b2fd-159">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="3b2fd-160">[Předchozí: vygenerované uživatelské rozhraní Razor Stránky](xref:tutorials/razor-pages/page) 
>  [Další: aktualizace stránek](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="3b2fd-160">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="3b2fd-161">`RazorPagesMovieContext`Objekt zpracovává úlohu připojení k databázi a mapování `Movie` objektů na záznamy databáze.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-161">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="3b2fd-162">Kontext databáze je zaregistrován s kontejnerem [Injektáže závislosti](xref:fundamentals/dependency-injection) v `ConfigureServices` metodě v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="3b2fd-162">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b2fd-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b2fd-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3b2fd-164">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b2fd-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="3b2fd-165">Další informace o metodách používaných v nástroji `ConfigureServices` naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="3b2fd-165">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="3b2fd-166">[Podpora GDPR (EU obecné nařízení o ochraně osobních údajů) v ASP.NET Core](xref:security/gdpr) pro `CookiePolicyOptions` .</span><span class="sxs-lookup"><span data-stu-id="3b2fd-166">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="3b2fd-167">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="3b2fd-167">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="3b2fd-168">[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="3b2fd-168">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="3b2fd-169">Pro místní vývoj získá připojovací řetězec z *appsettings.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-169">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b2fd-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b2fd-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3b2fd-171">Hodnota názvu pro databázi ( `Database={Database name}` ) bude pro vygenerovaný kód odlišná.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-171">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="3b2fd-172">Hodnota name je libovolná.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-172">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b2fd-173">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b2fd-173">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b2fd-174">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b2fd-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="3b2fd-175">Při nasazení aplikace do testovacího nebo provozního serveru lze použít proměnnou prostředí k nastavení připojovacího řetězce na skutečný databázový server.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-175">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="3b2fd-176">Další informace najdete v části [Konfigurace](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="3b2fd-176">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b2fd-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b2fd-177">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="3b2fd-178">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="3b2fd-178">SQL Server Express LocalDB</span></span>

<span data-ttu-id="3b2fd-179">LocalDB je zjednodušená verze databázového stroje SQL Server Express, který je zaměřený na vývoj programu.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-179">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="3b2fd-180">LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje složitá konfigurace.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-180">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="3b2fd-181">Ve výchozím nastavení vytvoří databáze LocalDB `*.mdf` soubory v `C:/Users/<user/>` adresáři.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-181">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="3b2fd-182">V nabídce **Zobrazit** otevřete **Průzkumník objektů systému SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="3b2fd-182">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Nabídka Zobrazit](sql/_static/ssox.png)

* <span data-ttu-id="3b2fd-184">Klikněte pravým tlačítkem na `Movie` tabulku a vyberte **Zobrazit návrháře**:</span><span class="sxs-lookup"><span data-stu-id="3b2fd-184">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Kontextová nabídka otevřená v tabulce videí](sql/_static/design.png)

  ![Tabulka videí otevřená v Návrháři](sql/_static/dv.png)

<span data-ttu-id="3b2fd-187">Poznamenejte si ikonu klíče vedle `ID` .</span><span class="sxs-lookup"><span data-stu-id="3b2fd-187">Note the key icon next to `ID`.</span></span> <span data-ttu-id="3b2fd-188">Ve výchozím nastavení EF vytvoří vlastnost s názvem `ID` pro primární klíč.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-188">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="3b2fd-189">Klikněte pravým tlačítkem na `Movie` tabulku a vyberte **Zobrazit data**:</span><span class="sxs-lookup"><span data-stu-id="3b2fd-189">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Tabulka videa otevřená zobrazující data tabulky](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b2fd-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b2fd-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b2fd-192">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b2fd-192">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="3b2fd-193">Dosazení databáze</span><span class="sxs-lookup"><span data-stu-id="3b2fd-193">Seed the database</span></span>

<span data-ttu-id="3b2fd-194">Vytvořte novou třídu s názvem `SeedData` ve složce *modely* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3b2fd-194">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="3b2fd-195">Pokud databáze obsahuje nějaké filmy, inicializátor počáteční hodnoty se vrátí a nepřidá se žádné filmy.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-195">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="3b2fd-196">Přidat inicializátor počáteční hodnoty</span><span class="sxs-lookup"><span data-stu-id="3b2fd-196">Add the seed initializer</span></span>

<span data-ttu-id="3b2fd-197">V *program.cs*upravte `Main` metodu a proveďte následující:</span><span class="sxs-lookup"><span data-stu-id="3b2fd-197">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="3b2fd-198">Získá instanci kontextu databáze z kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-198">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="3b2fd-199">Zavolejte metodu počáteční hodnoty a předejte jí kontext.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-199">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="3b2fd-200">Vyřazení kontextu, když se dokončí metoda počáteční hodnoty.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-200">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="3b2fd-201">Následující kód ukazuje aktualizovaný soubor *program.cs* .</span><span class="sxs-lookup"><span data-stu-id="3b2fd-201">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="3b2fd-202">Produkční aplikace by nevolala `Database.Migrate` .</span><span class="sxs-lookup"><span data-stu-id="3b2fd-202">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="3b2fd-203">Přidá se k předchozímu kódu, aby se zabránilo následující výjimce, pokud nebyla `Update-Database` spuštěna:</span><span class="sxs-lookup"><span data-stu-id="3b2fd-203">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="3b2fd-204">SqlException: Nelze otevřít databázi "RazorPagesMovieContext-21" požadovanou pro přihlášení.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-204">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="3b2fd-205">Přihlášení se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-205">The login failed.</span></span>
<span data-ttu-id="3b2fd-206">Přihlášení uživatele "uživatelské jméno" se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-206">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="3b2fd-207">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="3b2fd-207">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b2fd-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b2fd-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3b2fd-209">Odstraňte všechny záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-209">Delete all the records in the DB.</span></span> <span data-ttu-id="3b2fd-210">Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z [SSOX](xref:tutorials/razor-pages/new-field#ssox) .</span><span class="sxs-lookup"><span data-stu-id="3b2fd-210">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="3b2fd-211">Vynuťte inicializaci aplikace (volání metod ve `Startup` třídě), takže se metoda počáteční hodnoty spustí.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-211">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="3b2fd-212">Chcete-li vynutit inicializaci, IIS Express musí být zastavena a restartována.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-212">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="3b2fd-213">Můžete to provést s některým z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="3b2fd-213">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="3b2fd-214">V oznamovací oblasti klikněte pravým tlačítkem na ikonu na hlavním panelu IIS Express systému a klepněte na **konec** nebo **zastavení webu**:</span><span class="sxs-lookup"><span data-stu-id="3b2fd-214">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express ikona na hlavním panelu systému](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](sql/_static/stopIIS.png)

    * <span data-ttu-id="3b2fd-217">Pokud jste spustili VS v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-217">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="3b2fd-218">Pokud jste spustili VS v režimu ladění, ukončete ladicí program a stiskněte klávesu F5.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-218">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b2fd-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b2fd-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3b2fd-220">Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí).</span><span class="sxs-lookup"><span data-stu-id="3b2fd-220">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="3b2fd-221">Zastavením a spuštěním aplikace dosadíte databázi.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-221">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="3b2fd-222">Aplikace zobrazuje dosazený data.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-222">The app shows the seeded data.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b2fd-223">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b2fd-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3b2fd-224">Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí).</span><span class="sxs-lookup"><span data-stu-id="3b2fd-224">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="3b2fd-225">Zastavením a spuštěním aplikace dosadíte databázi.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-225">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="3b2fd-226">Aplikace zobrazuje dosazený data.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-226">The app shows the seeded data.</span></span>

---

<span data-ttu-id="3b2fd-227">Aplikace zobrazuje dosazený data:</span><span class="sxs-lookup"><span data-stu-id="3b2fd-227">The app shows the seeded data:</span></span>

![Aplikace Movie otevřená v Chrome zobrazující data videa](sql/_static/m55.png)

<span data-ttu-id="3b2fd-229">V dalším kurzu se vyčistí prezentace dat.</span><span class="sxs-lookup"><span data-stu-id="3b2fd-229">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3b2fd-230">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3b2fd-230">Additional resources</span></span>

* [<span data-ttu-id="3b2fd-231">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="3b2fd-231">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="3b2fd-232">[Předchozí: vygenerované uživatelské rozhraní Razor Stránky](xref:tutorials/razor-pages/page) 
>  [Další: aktualizace stránek](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="3b2fd-232">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
