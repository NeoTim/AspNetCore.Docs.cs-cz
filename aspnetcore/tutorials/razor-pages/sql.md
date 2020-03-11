---
title: Práce s databází a ASP.NET Core
author: rick-anderson
description: Vysvětluje práci s databází a ASP.NET Core.
ms.author: riande
ms.date: 7/22/2019
uid: tutorials/razor-pages/sql
ms.openlocfilehash: b5acb573f8fa39e5300ecdb359113d8697d78934
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664337"
---
# <a name="work-with-a-database-and-aspnet-core"></a><span data-ttu-id="c2313-103">Práce s databází a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c2313-103">Work with a database and ASP.NET Core</span></span>

<span data-ttu-id="c2313-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Jan Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="c2313-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="c2313-105">Objekt `RazorPagesMovieContext` zpracovává úlohu připojení k databázi a mapování objektů `Movie` na záznamy databáze.</span><span class="sxs-lookup"><span data-stu-id="c2313-105">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="c2313-106">Kontext databáze je zaregistrován s kontejnerem [vkládání závislostí](xref:fundamentals/dependency-injection) v metodě `ConfigureServices` v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="c2313-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c2313-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2313-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c2313-108">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="c2313-108">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="c2313-109">[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="c2313-109">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="c2313-110">Pro místní vývoj získá připojovací řetězec ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="c2313-110">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c2313-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2313-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c2313-112">Hodnota názvu pro databázi (`Database={Database name}`) bude pro vygenerovaný kód odlišná.</span><span class="sxs-lookup"><span data-stu-id="c2313-112">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="c2313-113">Hodnota name je libovolná.</span><span class="sxs-lookup"><span data-stu-id="c2313-113">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c2313-114">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="c2313-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="c2313-115">Při nasazení aplikace do testovacího nebo provozního serveru lze použít proměnnou prostředí k nastavení připojovacího řetězce na skutečný databázový server.</span><span class="sxs-lookup"><span data-stu-id="c2313-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="c2313-116">Další informace najdete v části [Konfigurace](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="c2313-116">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c2313-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2313-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="c2313-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="c2313-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="c2313-119">LocalDB je zjednodušená verze databázového stroje SQL Server Express, který je zaměřený na vývoj programu.</span><span class="sxs-lookup"><span data-stu-id="c2313-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="c2313-120">LocalDB spustí na vyžádání a běží v uživatelském režimu, takže není bez složité konfigurace.</span><span class="sxs-lookup"><span data-stu-id="c2313-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="c2313-121">Ve výchozím nastavení vytvoří databáze LocalDB soubory `*.mdf` v adresáři `C:\Users\<user>\`.</span><span class="sxs-lookup"><span data-stu-id="c2313-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="c2313-122">V nabídce **Zobrazit** otevřete **Průzkumník objektů systému SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="c2313-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Nabídka Zobrazit](sql/_static/ssox.png)

* <span data-ttu-id="c2313-124">Klikněte pravým tlačítkem na tabulku `Movie` a vyberte **Zobrazit návrháře**:</span><span class="sxs-lookup"><span data-stu-id="c2313-124">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Kontextové nabídky otevřené v tabulce videí](sql/_static/design.png)

  ![Tabulky filmů otevřené v Návrháři](sql/_static/dv.png)

<span data-ttu-id="c2313-127">Poznamenejte si ikonu klíče vedle `ID`.</span><span class="sxs-lookup"><span data-stu-id="c2313-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="c2313-128">Ve výchozím nastavení EF vytvoří vlastnost s názvem `ID` pro primární klíč.</span><span class="sxs-lookup"><span data-stu-id="c2313-128">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="c2313-129">Klikněte pravým tlačítkem na tabulku `Movie` a vyberte **Zobrazit data**:</span><span class="sxs-lookup"><span data-stu-id="c2313-129">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Tabulka videa otevřená zobrazující data tabulky](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c2313-131">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="c2313-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="c2313-132">Přidání dat do databáze</span><span class="sxs-lookup"><span data-stu-id="c2313-132">Seed the database</span></span>

<span data-ttu-id="c2313-133">Vytvořte novou třídu s názvem `SeedData` ve složce *modely* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c2313-133">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="c2313-134">Pokud databáze obsahuje nějaké filmy, inicializátor počáteční hodnoty se vrátí a nepřidá se žádné filmy.</span><span class="sxs-lookup"><span data-stu-id="c2313-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="c2313-135">Přidat inicializátor počáteční hodnoty</span><span class="sxs-lookup"><span data-stu-id="c2313-135">Add the seed initializer</span></span>

<span data-ttu-id="c2313-136">V *program.cs*upravte metodu `Main` a proveďte následující:</span><span class="sxs-lookup"><span data-stu-id="c2313-136">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="c2313-137">Instance kontextu databáze získáte z kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="c2313-137">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="c2313-138">Zavolejte metodu počáteční hodnoty a předejte jí kontext.</span><span class="sxs-lookup"><span data-stu-id="c2313-138">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="c2313-139">Vyřazení kontextu, když se dokončí metoda počáteční hodnoty.</span><span class="sxs-lookup"><span data-stu-id="c2313-139">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="c2313-140">Následující kód ukazuje aktualizovaný soubor *program.cs* .</span><span class="sxs-lookup"><span data-stu-id="c2313-140">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="c2313-141">Pokud `Update-Database` nebyl spuštěn, dojde k následující výjimce:</span><span class="sxs-lookup"><span data-stu-id="c2313-141">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="c2313-142">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="c2313-142">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c2313-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2313-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c2313-144">Odstraňte všechny záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="c2313-144">Delete all the records in the DB.</span></span> <span data-ttu-id="c2313-145">Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z [SSOX](xref:tutorials/razor-pages/new-field#ssox) .</span><span class="sxs-lookup"><span data-stu-id="c2313-145">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="c2313-146">Vynuťte inicializaci aplikace (voláním metod ve třídě `Startup`), takže se metoda počáteční hodnoty spustí.</span><span class="sxs-lookup"><span data-stu-id="c2313-146">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="c2313-147">Chcete-li vynutit inicializaci, IIS Express musí být zastavena a restartována.</span><span class="sxs-lookup"><span data-stu-id="c2313-147">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="c2313-148">Můžete to provést s některým z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="c2313-148">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="c2313-149">Klikněte pravým tlačítkem myši na ikonu IIS Express systému v oznamovací oblasti a klepněte na **konec** nebo **zastavení webu**:</span><span class="sxs-lookup"><span data-stu-id="c2313-149">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express ikona na hlavním panelu systému](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](sql/_static/stopIIS.png)

    * <span data-ttu-id="c2313-152">Pokud jste spustili VS v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="c2313-152">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="c2313-153">Pokud jste spustili VS v režimu ladění, ukončete ladicí program a stiskněte klávesu F5.</span><span class="sxs-lookup"><span data-stu-id="c2313-153">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c2313-154">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="c2313-154">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="c2313-155">Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí).</span><span class="sxs-lookup"><span data-stu-id="c2313-155">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="c2313-156">Zastavením a spuštěním aplikace dosadíte databázi.</span><span class="sxs-lookup"><span data-stu-id="c2313-156">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="c2313-157">Aplikace zobrazuje dosazený data.</span><span class="sxs-lookup"><span data-stu-id="c2313-157">The app shows the seeded data.</span></span>

---

<span data-ttu-id="c2313-158">V dalším kurzu dojde k vylepšení prezentace dat.</span><span class="sxs-lookup"><span data-stu-id="c2313-158">The next tutorial will improve the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c2313-159">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c2313-159">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="c2313-160">[Předchozí: vygenerované Razor Pages](xref:tutorials/razor-pages/page)
> [Další: aktualizace stránek](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="c2313-160">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="c2313-161">Objekt `RazorPagesMovieContext` zpracovává úlohu připojení k databázi a mapování objektů `Movie` na záznamy databáze.</span><span class="sxs-lookup"><span data-stu-id="c2313-161">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="c2313-162">Kontext databáze je zaregistrován s kontejnerem [vkládání závislostí](xref:fundamentals/dependency-injection) v metodě `ConfigureServices` v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="c2313-162">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c2313-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2313-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c2313-164">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="c2313-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="c2313-165">Další informace o metodách používaných v `ConfigureServices`najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="c2313-165">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="c2313-166">[Podpora GDPR (EU obecné nařízení o ochraně osobních údajů) v ASP.NET Core](xref:security/gdpr) pro `CookiePolicyOptions`.</span><span class="sxs-lookup"><span data-stu-id="c2313-166">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="c2313-167">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="c2313-167">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="c2313-168">[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="c2313-168">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="c2313-169">Pro místní vývoj získá připojovací řetězec ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="c2313-169">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c2313-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2313-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c2313-171">Hodnota názvu pro databázi (`Database={Database name}`) bude pro vygenerovaný kód odlišná.</span><span class="sxs-lookup"><span data-stu-id="c2313-171">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="c2313-172">Hodnota name je libovolná.</span><span class="sxs-lookup"><span data-stu-id="c2313-172">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c2313-173">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c2313-173">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c2313-174">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="c2313-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="c2313-175">Při nasazení aplikace do testovacího nebo provozního serveru lze použít proměnnou prostředí k nastavení připojovacího řetězce na skutečný databázový server.</span><span class="sxs-lookup"><span data-stu-id="c2313-175">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="c2313-176">Další informace najdete v části [Konfigurace](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="c2313-176">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c2313-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2313-177">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="c2313-178">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="c2313-178">SQL Server Express LocalDB</span></span>

<span data-ttu-id="c2313-179">LocalDB je zjednodušená verze databázového stroje SQL Server Express, který je zaměřený na vývoj programu.</span><span class="sxs-lookup"><span data-stu-id="c2313-179">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="c2313-180">LocalDB spustí na vyžádání a běží v uživatelském režimu, takže není bez složité konfigurace.</span><span class="sxs-lookup"><span data-stu-id="c2313-180">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="c2313-181">Ve výchozím nastavení vytvoří databáze LocalDB soubory `*.mdf` v adresáři `C:/Users/<user/>`.</span><span class="sxs-lookup"><span data-stu-id="c2313-181">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="c2313-182">V nabídce **Zobrazit** otevřete **Průzkumník objektů systému SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="c2313-182">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Nabídka Zobrazit](sql/_static/ssox.png)

* <span data-ttu-id="c2313-184">Klikněte pravým tlačítkem na tabulku `Movie` a vyberte **Zobrazit návrháře**:</span><span class="sxs-lookup"><span data-stu-id="c2313-184">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Kontextová nabídka otevřená v tabulce videí](sql/_static/design.png)

  ![Tabulka videí otevřená v Návrháři](sql/_static/dv.png)

<span data-ttu-id="c2313-187">Poznamenejte si ikonu klíče vedle `ID`.</span><span class="sxs-lookup"><span data-stu-id="c2313-187">Note the key icon next to `ID`.</span></span> <span data-ttu-id="c2313-188">Ve výchozím nastavení EF vytvoří vlastnost s názvem `ID` pro primární klíč.</span><span class="sxs-lookup"><span data-stu-id="c2313-188">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="c2313-189">Klikněte pravým tlačítkem na tabulku `Movie` a vyberte **Zobrazit data**:</span><span class="sxs-lookup"><span data-stu-id="c2313-189">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Tabulka videa otevřená zobrazující data tabulky](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c2313-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c2313-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c2313-192">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="c2313-192">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="c2313-193">Přidání dat do databáze</span><span class="sxs-lookup"><span data-stu-id="c2313-193">Seed the database</span></span>

<span data-ttu-id="c2313-194">Vytvořte novou třídu s názvem `SeedData` ve složce *modely* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c2313-194">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="c2313-195">Pokud databáze obsahuje nějaké filmy, inicializátor počáteční hodnoty se vrátí a nepřidá se žádné filmy.</span><span class="sxs-lookup"><span data-stu-id="c2313-195">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="c2313-196">Přidat inicializátor počáteční hodnoty</span><span class="sxs-lookup"><span data-stu-id="c2313-196">Add the seed initializer</span></span>

<span data-ttu-id="c2313-197">V *program.cs*upravte metodu `Main` a proveďte následující:</span><span class="sxs-lookup"><span data-stu-id="c2313-197">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="c2313-198">Instance kontextu databáze získáte z kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="c2313-198">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="c2313-199">Zavolejte metodu počáteční hodnoty a předejte jí kontext.</span><span class="sxs-lookup"><span data-stu-id="c2313-199">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="c2313-200">Vyřazení kontextu, když se dokončí metoda počáteční hodnoty.</span><span class="sxs-lookup"><span data-stu-id="c2313-200">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="c2313-201">Následující kód ukazuje aktualizovaný soubor *program.cs* .</span><span class="sxs-lookup"><span data-stu-id="c2313-201">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="c2313-202">Produkční aplikace by nevolala `Database.Migrate`.</span><span class="sxs-lookup"><span data-stu-id="c2313-202">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="c2313-203">Přidá se k předchozímu kódu, aby se zabránilo následující výjimce, když `Update-Database` nebyl spuštěn:</span><span class="sxs-lookup"><span data-stu-id="c2313-203">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="c2313-204">SqlException: Nelze otevřít databázi "RazorPagesMovieContext-21" požadovanou pro přihlášení.</span><span class="sxs-lookup"><span data-stu-id="c2313-204">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="c2313-205">Přihlášení se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="c2313-205">The login failed.</span></span>
<span data-ttu-id="c2313-206">Přihlášení uživatele "uživatelské jméno" se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="c2313-206">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="c2313-207">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="c2313-207">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c2313-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2313-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c2313-209">Odstraňte všechny záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="c2313-209">Delete all the records in the DB.</span></span> <span data-ttu-id="c2313-210">Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z [SSOX](xref:tutorials/razor-pages/new-field#ssox) .</span><span class="sxs-lookup"><span data-stu-id="c2313-210">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="c2313-211">Vynuťte inicializaci aplikace (voláním metod ve třídě `Startup`), takže se metoda počáteční hodnoty spustí.</span><span class="sxs-lookup"><span data-stu-id="c2313-211">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="c2313-212">Chcete-li vynutit inicializaci, IIS Express musí být zastavena a restartována.</span><span class="sxs-lookup"><span data-stu-id="c2313-212">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="c2313-213">Můžete to provést s některým z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="c2313-213">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="c2313-214">V oznamovací oblasti klikněte pravým tlačítkem na ikonu na hlavním panelu IIS Express systému a klepněte na **konec** nebo **zastavení webu**:</span><span class="sxs-lookup"><span data-stu-id="c2313-214">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express ikona na hlavním panelu systému](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](sql/_static/stopIIS.png)

    * <span data-ttu-id="c2313-217">Pokud jste spustili VS v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="c2313-217">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="c2313-218">Pokud jste spustili VS v režimu ladění, ukončete ladicí program a stiskněte klávesu F5.</span><span class="sxs-lookup"><span data-stu-id="c2313-218">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c2313-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c2313-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c2313-220">Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí).</span><span class="sxs-lookup"><span data-stu-id="c2313-220">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="c2313-221">Zastavením a spuštěním aplikace dosadíte databázi.</span><span class="sxs-lookup"><span data-stu-id="c2313-221">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="c2313-222">Aplikace zobrazuje dosazený data.</span><span class="sxs-lookup"><span data-stu-id="c2313-222">The app shows the seeded data.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c2313-223">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="c2313-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c2313-224">Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí).</span><span class="sxs-lookup"><span data-stu-id="c2313-224">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="c2313-225">Zastavením a spuštěním aplikace dosadíte databázi.</span><span class="sxs-lookup"><span data-stu-id="c2313-225">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="c2313-226">Aplikace zobrazuje dosazený data.</span><span class="sxs-lookup"><span data-stu-id="c2313-226">The app shows the seeded data.</span></span>

---

<span data-ttu-id="c2313-227">Aplikace zobrazuje dosazený data:</span><span class="sxs-lookup"><span data-stu-id="c2313-227">The app shows the seeded data:</span></span>

![Aplikace Movie otevřená v Chrome zobrazující data videa](sql/_static/m55.png)

<span data-ttu-id="c2313-229">V dalším kurzu se vyčistí prezentace dat.</span><span class="sxs-lookup"><span data-stu-id="c2313-229">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c2313-230">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c2313-230">Additional resources</span></span>

* [<span data-ttu-id="c2313-231">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="c2313-231">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="c2313-232">[Předchozí: vygenerované Razor Pages](xref:tutorials/razor-pages/page)
> [Další: aktualizace stránek](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="c2313-232">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
