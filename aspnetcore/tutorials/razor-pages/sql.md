---
title: Práce s databází a ASP.NET Core
author: rick-anderson
description: Vysvětluje práci s databází a ASP.NET Core.
ms.author: riande
ms.date: 7/22/2019
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 197697f28e9faa45c1ac2b7f993bde15994957e5
ms.sourcegitcommit: 051f068c78931432e030b60094c38376d64d013e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68440378"
---
# <a name="work-with-a-database-and-aspnet-core"></a><span data-ttu-id="3690a-103">Práce s databází a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3690a-103">Work with a database and ASP.NET Core</span></span>

<span data-ttu-id="3690a-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="3690a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="3690a-105">Objekt zpracovává úlohu připojení k databázi a mapování `Movie` objektů na záznamy databáze. `RazorPagesMovieContext`</span><span class="sxs-lookup"><span data-stu-id="3690a-105">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="3690a-106">Kontext databáze je zaregistrován s kontejnerem [Injektáže závislosti](xref:fundamentals/dependency-injection) v `ConfigureServices` metodě v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="3690a-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3690a-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3690a-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="3690a-108">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3690a-108">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="3690a-109">[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="3690a-109">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="3690a-110">Pro místní vývoj získá připojovací řetězec ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="3690a-110">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3690a-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3690a-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3690a-112">Hodnota názvu pro databázi (`Database={Database name}`) bude pro vygenerovaný kód odlišná.</span><span class="sxs-lookup"><span data-stu-id="3690a-112">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="3690a-113">Hodnota name je libovolná.</span><span class="sxs-lookup"><span data-stu-id="3690a-113">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="3690a-114">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3690a-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="3690a-115">Při nasazení aplikace do testovacího nebo provozního serveru lze použít proměnnou prostředí k nastavení připojovacího řetězce na skutečný databázový server.</span><span class="sxs-lookup"><span data-stu-id="3690a-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="3690a-116">Další informace najdete v části [Konfigurace](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="3690a-116">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3690a-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3690a-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="3690a-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="3690a-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="3690a-119">LocalDB je zjednodušená verze databázového stroje SQL Server Express, který je zaměřený na vývoj programu.</span><span class="sxs-lookup"><span data-stu-id="3690a-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="3690a-120">LocalDB spustí na vyžádání a běží v uživatelském režimu, takže není bez složité konfigurace.</span><span class="sxs-lookup"><span data-stu-id="3690a-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="3690a-121">Ve výchozím nastavení vytvoří `*.mdf` databáze LocalDB soubory `C:/Users/<user/>` v adresáři.</span><span class="sxs-lookup"><span data-stu-id="3690a-121">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="3690a-122">V nabídce **Zobrazit** otevřete **Průzkumník objektů systému SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="3690a-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Nabídka Zobrazit](sql/_static/ssox.png)

* <span data-ttu-id="3690a-124">Klikněte pravým tlačítkem na  tabulkuavyberteZobrazit`Movie` návrháře:</span><span class="sxs-lookup"><span data-stu-id="3690a-124">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Kontextové nabídky otevřené v tabulce videí](sql/_static/design.png)

  ![Tabulky filmů otevřené v Návrháři](sql/_static/dv.png)

<span data-ttu-id="3690a-127">Poznamenejte si ikonu klíče vedle `ID`.</span><span class="sxs-lookup"><span data-stu-id="3690a-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="3690a-128">Ve výchozím nastavení EF vytvoří vlastnost s názvem `ID` pro primární klíč.</span><span class="sxs-lookup"><span data-stu-id="3690a-128">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="3690a-129">Klikněte pravým tlačítkem na  tabulkuavyberteZobrazit`Movie` data:</span><span class="sxs-lookup"><span data-stu-id="3690a-129">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Tabulka videa otevřená zobrazující data tabulky](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="3690a-131">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3690a-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="3690a-132">Přidání dat do databáze</span><span class="sxs-lookup"><span data-stu-id="3690a-132">Seed the database</span></span>

<span data-ttu-id="3690a-133">Vytvořte novou třídu s názvem `SeedData` ve složce *modely* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3690a-133">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="3690a-134">Pokud databáze obsahuje nějaké filmy, inicializátor počáteční hodnoty se vrátí a nepřidá se žádné filmy.</span><span class="sxs-lookup"><span data-stu-id="3690a-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="3690a-135">Přidat inicializátor počáteční hodnoty</span><span class="sxs-lookup"><span data-stu-id="3690a-135">Add the seed initializer</span></span>

<span data-ttu-id="3690a-136">V *Program.cs*, změnit `Main` metoda můžete provádět následující:</span><span class="sxs-lookup"><span data-stu-id="3690a-136">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="3690a-137">Instance kontextu databáze získáte z kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="3690a-137">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="3690a-138">Zavolejte metodu počáteční hodnoty a předejte jí kontext.</span><span class="sxs-lookup"><span data-stu-id="3690a-138">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="3690a-139">Vyřazení kontextu, když se dokončí metoda počáteční hodnoty.</span><span class="sxs-lookup"><span data-stu-id="3690a-139">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="3690a-140">Následující kód ukazuje aktualizovaný *Program.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="3690a-140">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="3690a-141">Produkční aplikace by nevolala `Database.Migrate`.</span><span class="sxs-lookup"><span data-stu-id="3690a-141">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="3690a-142">Přidá se k předchozímu kódu, aby se zabránilo následující výjimce, pokud `Update-Database` nebyla spuštěna:</span><span class="sxs-lookup"><span data-stu-id="3690a-142">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="3690a-143">SqlException: Nelze otevřít databázi "RazorPagesMovieContext-21" požadovanou přihlášením.</span><span class="sxs-lookup"><span data-stu-id="3690a-143">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="3690a-144">Přihlášení se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="3690a-144">The login failed.</span></span>
<span data-ttu-id="3690a-145">Přihlášení uživatele "uživatelské jméno" se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="3690a-145">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="3690a-146">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="3690a-146">Test the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3690a-147">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3690a-147">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3690a-148">Odstraňte všechny záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="3690a-148">Delete all the records in the DB.</span></span> <span data-ttu-id="3690a-149">Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z [SSOX](xref:tutorials/razor-pages/new-field#ssox) .</span><span class="sxs-lookup"><span data-stu-id="3690a-149">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="3690a-150">Vynuťte inicializaci aplikace (volání metod ve `Startup` třídě), takže se metoda počáteční hodnoty spustí.</span><span class="sxs-lookup"><span data-stu-id="3690a-150">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="3690a-151">Chcete-li vynutit inicializaci, IIS Express musí být zastavena a restartována.</span><span class="sxs-lookup"><span data-stu-id="3690a-151">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="3690a-152">Můžete to provést s některým z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="3690a-152">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="3690a-153">Klikněte pravým tlačítkem myši na ikonu IIS Express systému v oznamovací oblasti a klepněte na **konec** nebo **zastavení webu**:</span><span class="sxs-lookup"><span data-stu-id="3690a-153">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express ikona na hlavním panelu systému](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](sql/_static/stopIIS.png)

    * <span data-ttu-id="3690a-156">Pokud jste spustili VS v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="3690a-156">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="3690a-157">Pokud jste spustili VS v režimu ladění, ukončete ladicí program a stiskněte klávesu F5.</span><span class="sxs-lookup"><span data-stu-id="3690a-157">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="3690a-158">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3690a-158">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="3690a-159">Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí).</span><span class="sxs-lookup"><span data-stu-id="3690a-159">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="3690a-160">Zastavením a spuštěním aplikace dosadíte databázi.</span><span class="sxs-lookup"><span data-stu-id="3690a-160">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="3690a-161">Aplikace zobrazuje dosazený data.</span><span class="sxs-lookup"><span data-stu-id="3690a-161">The app shows the seeded data.</span></span>

---

<span data-ttu-id="3690a-162">V dalším kurzu dojde k vylepšení prezentace dat.</span><span class="sxs-lookup"><span data-stu-id="3690a-162">The next tutorial will improve the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3690a-163">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3690a-163">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="3690a-164">[Předchozí Razor Pages](xref:tutorials/razor-pages/page)vygenerovanéjako[další:
>  Aktualizace stránek](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="3690a-164">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="3690a-165">Objekt zpracovává úlohu připojení k databázi a mapování `Movie` objektů na záznamy databáze. `RazorPagesMovieContext`</span><span class="sxs-lookup"><span data-stu-id="3690a-165">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="3690a-166">Kontext databáze je zaregistrován s kontejnerem [Injektáže závislosti](xref:fundamentals/dependency-injection) v `ConfigureServices` metodě v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="3690a-166">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3690a-167">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3690a-167">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="3690a-168">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3690a-168">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="3690a-169">Další informace o metodách používaných v `ConfigureServices`nástroji naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="3690a-169">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="3690a-170">[Podpora GDPR (EU obecné nařízení o ochraně osobních údajů) v ASP.NET Core](xref:security/gdpr) pro `CookiePolicyOptions`.</span><span class="sxs-lookup"><span data-stu-id="3690a-170">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="3690a-171">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="3690a-171">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="3690a-172">[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="3690a-172">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="3690a-173">Pro místní vývoj získá připojovací řetězec ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="3690a-173">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3690a-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3690a-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3690a-175">Hodnota názvu pro databázi (`Database={Database name}`) bude pro vygenerovaný kód odlišná.</span><span class="sxs-lookup"><span data-stu-id="3690a-175">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="3690a-176">Hodnota name je libovolná.</span><span class="sxs-lookup"><span data-stu-id="3690a-176">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3690a-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3690a-177">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3690a-178">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3690a-178">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="3690a-179">Při nasazení aplikace do testovacího nebo provozního serveru lze použít proměnnou prostředí k nastavení připojovacího řetězce na skutečný databázový server.</span><span class="sxs-lookup"><span data-stu-id="3690a-179">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="3690a-180">Další informace najdete v části [Konfigurace](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="3690a-180">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3690a-181">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3690a-181">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="3690a-182">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="3690a-182">SQL Server Express LocalDB</span></span>

<span data-ttu-id="3690a-183">LocalDB je zjednodušená verze databázového stroje SQL Server Express, který je zaměřený na vývoj programu.</span><span class="sxs-lookup"><span data-stu-id="3690a-183">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="3690a-184">LocalDB spustí na vyžádání a běží v uživatelském režimu, takže není bez složité konfigurace.</span><span class="sxs-lookup"><span data-stu-id="3690a-184">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="3690a-185">Ve výchozím nastavení vytvoří `*.mdf` databáze LocalDB soubory `C:/Users/<user/>` v adresáři.</span><span class="sxs-lookup"><span data-stu-id="3690a-185">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="3690a-186">V nabídce **Zobrazit** otevřete **Průzkumník objektů systému SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="3690a-186">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Nabídka Zobrazit](sql/_static/ssox.png)

* <span data-ttu-id="3690a-188">Klikněte pravým tlačítkem na  tabulkuavyberteZobrazit`Movie` návrháře:</span><span class="sxs-lookup"><span data-stu-id="3690a-188">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Kontextová nabídka otevřená v tabulce videí](sql/_static/design.png)

  ![Tabulka videí otevřená v Návrháři](sql/_static/dv.png)

<span data-ttu-id="3690a-191">Poznamenejte si ikonu klíče vedle `ID`.</span><span class="sxs-lookup"><span data-stu-id="3690a-191">Note the key icon next to `ID`.</span></span> <span data-ttu-id="3690a-192">Ve výchozím nastavení EF vytvoří vlastnost s názvem `ID` pro primární klíč.</span><span class="sxs-lookup"><span data-stu-id="3690a-192">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="3690a-193">Klikněte pravým tlačítkem na  tabulkuavyberteZobrazit`Movie` data:</span><span class="sxs-lookup"><span data-stu-id="3690a-193">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Tabulka videa otevřená zobrazující data tabulky](sql/_static/vd22.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3690a-195">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3690a-195">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3690a-196">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3690a-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="3690a-197">Přidání dat do databáze</span><span class="sxs-lookup"><span data-stu-id="3690a-197">Seed the database</span></span>

<span data-ttu-id="3690a-198">Vytvořte novou třídu s názvem `SeedData` ve složce *modely* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3690a-198">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="3690a-199">Pokud databáze obsahuje nějaké filmy, inicializátor počáteční hodnoty se vrátí a nepřidá se žádné filmy.</span><span class="sxs-lookup"><span data-stu-id="3690a-199">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="3690a-200">Přidat inicializátor počáteční hodnoty</span><span class="sxs-lookup"><span data-stu-id="3690a-200">Add the seed initializer</span></span>

<span data-ttu-id="3690a-201">V *Program.cs*, změnit `Main` metoda můžete provádět následující:</span><span class="sxs-lookup"><span data-stu-id="3690a-201">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="3690a-202">Instance kontextu databáze získáte z kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="3690a-202">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="3690a-203">Zavolejte metodu počáteční hodnoty a předejte jí kontext.</span><span class="sxs-lookup"><span data-stu-id="3690a-203">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="3690a-204">Vyřazení kontextu, když se dokončí metoda počáteční hodnoty.</span><span class="sxs-lookup"><span data-stu-id="3690a-204">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="3690a-205">Následující kód ukazuje aktualizovaný *Program.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="3690a-205">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="3690a-206">Produkční aplikace by nevolala `Database.Migrate`.</span><span class="sxs-lookup"><span data-stu-id="3690a-206">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="3690a-207">Přidá se k předchozímu kódu, aby se zabránilo následující výjimce, pokud `Update-Database` nebyla spuštěna:</span><span class="sxs-lookup"><span data-stu-id="3690a-207">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="3690a-208">SqlException: Nelze otevřít databázi "RazorPagesMovieContext-21" požadovanou přihlášením.</span><span class="sxs-lookup"><span data-stu-id="3690a-208">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="3690a-209">Přihlášení se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="3690a-209">The login failed.</span></span>
<span data-ttu-id="3690a-210">Přihlášení uživatele "uživatelské jméno" se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="3690a-210">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="3690a-211">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="3690a-211">Test the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3690a-212">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3690a-212">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3690a-213">Odstraňte všechny záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="3690a-213">Delete all the records in the DB.</span></span> <span data-ttu-id="3690a-214">Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z [SSOX](xref:tutorials/razor-pages/new-field#ssox) .</span><span class="sxs-lookup"><span data-stu-id="3690a-214">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="3690a-215">Vynuťte inicializaci aplikace (volání metod ve `Startup` třídě), takže se metoda počáteční hodnoty spustí.</span><span class="sxs-lookup"><span data-stu-id="3690a-215">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="3690a-216">Chcete-li vynutit inicializaci, IIS Express musí být zastavena a restartována.</span><span class="sxs-lookup"><span data-stu-id="3690a-216">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="3690a-217">Můžete to provést s některým z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="3690a-217">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="3690a-218">V oznamovací oblasti klikněte pravým tlačítkem na ikonu na hlavním panelu IIS Express systému a klepněte na **konec** nebo **zastavení webu**:</span><span class="sxs-lookup"><span data-stu-id="3690a-218">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express ikona na hlavním panelu systému](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](sql/_static/stopIIS.png)

    * <span data-ttu-id="3690a-221">Pokud jste spustili VS v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="3690a-221">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="3690a-222">Pokud jste spustili VS v režimu ladění, ukončete ladicí program a stiskněte klávesu F5.</span><span class="sxs-lookup"><span data-stu-id="3690a-222">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3690a-223">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3690a-223">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3690a-224">Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí).</span><span class="sxs-lookup"><span data-stu-id="3690a-224">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="3690a-225">Zastavením a spuštěním aplikace dosadíte databázi.</span><span class="sxs-lookup"><span data-stu-id="3690a-225">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="3690a-226">Aplikace zobrazuje dosazený data.</span><span class="sxs-lookup"><span data-stu-id="3690a-226">The app shows the seeded data.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3690a-227">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3690a-227">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3690a-228">Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí).</span><span class="sxs-lookup"><span data-stu-id="3690a-228">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="3690a-229">Zastavením a spuštěním aplikace dosadíte databázi.</span><span class="sxs-lookup"><span data-stu-id="3690a-229">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="3690a-230">Aplikace zobrazuje dosazený data.</span><span class="sxs-lookup"><span data-stu-id="3690a-230">The app shows the seeded data.</span></span>

---

<span data-ttu-id="3690a-231">Aplikace zobrazuje dosazený data:</span><span class="sxs-lookup"><span data-stu-id="3690a-231">The app shows the seeded data:</span></span>

![Aplikace Movie otevřená v Chrome zobrazující data videa](sql/_static/m55.png)

<span data-ttu-id="3690a-233">V dalším kurzu se vyčistí prezentace dat.</span><span class="sxs-lookup"><span data-stu-id="3690a-233">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3690a-234">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3690a-234">Additional resources</span></span>

* [<span data-ttu-id="3690a-235">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="3690a-235">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="3690a-236">[Předchozí Razor Pages](xref:tutorials/razor-pages/page)vygenerovanéjako[další:
>  Aktualizace stránek](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="3690a-236">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end