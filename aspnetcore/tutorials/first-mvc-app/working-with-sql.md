---
title: Práce s SQL v aplikaci ASP.NET Core MVC
author: rick-anderson
description: Přečtěte si o použití SQL Server LocalDB nebo SQLite v aplikaci ASP.NET Core MVC.
ms.author: riande
ms.date: 8/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: 54b10f10fb048819fced223f77f06a32102512d0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776204"
---
# <a name="work-with-sql-in-aspnet-core"></a><span data-ttu-id="bb384-103">Práce s SQL v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bb384-103">Work with SQL in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bb384-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bb384-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bb384-105">`MvcMovieContext` Objekt zpracovává úlohu připojení k databázi a mapování `Movie` objektů na záznamy databáze.</span><span class="sxs-lookup"><span data-stu-id="bb384-105">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="bb384-106">Kontext databáze je zaregistrován s kontejnerem [Injektáže závislosti](xref:fundamentals/dependency-injection) v `ConfigureServices` metodě v souboru *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="bb384-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bb384-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bb384-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="bb384-108">[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="bb384-108">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="bb384-109">Pro místní vývoj načte připojovací řetězec ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="bb384-109">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="bb384-110">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="bb384-110">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

<span data-ttu-id="bb384-111">[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="bb384-111">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="bb384-112">Pro místní vývoj načte připojovací řetězec ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="bb384-112">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="bb384-113">Při nasazení aplikace do testovacího nebo provozního serveru lze použít proměnnou prostředí k nastavení připojovacího řetězce na produkční SQL Server.</span><span class="sxs-lookup"><span data-stu-id="bb384-113">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a production SQL Server.</span></span> <span data-ttu-id="bb384-114">Další informace najdete v části [Konfigurace](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="bb384-114">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bb384-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bb384-115">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="bb384-116">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="bb384-116">SQL Server Express LocalDB</span></span>

<span data-ttu-id="bb384-117">LocalDB je zjednodušená verze databázového stroje SQL Server Express, který je zaměřený na vývoj programu.</span><span class="sxs-lookup"><span data-stu-id="bb384-117">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="bb384-118">LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje složitá konfigurace.</span><span class="sxs-lookup"><span data-stu-id="bb384-118">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="bb384-119">Ve výchozím nastavení vytvoří databáze LocalDB soubory *. mdf* v adresáři *C:/Users/{User}* .</span><span class="sxs-lookup"><span data-stu-id="bb384-119">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="bb384-120">V nabídce **Zobrazit** otevřete **Průzkumník objektů systému SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="bb384-120">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Nabídka Zobrazit](working-with-sql/_static/ssox.png)

* <span data-ttu-id="bb384-122">Klikněte pravým tlačítkem `Movie` na tabulku **> návrháře zobrazení.**</span><span class="sxs-lookup"><span data-stu-id="bb384-122">Right click on the `Movie` table **> View Designer**</span></span>

  ![Kontextová nabídka otevřená v tabulce videí](working-with-sql/_static/design.png)

  ![Tabulka videí otevřená v Návrháři](working-with-sql/_static/dv.png)

<span data-ttu-id="bb384-125">Poznamenejte si ikonu klíče vedle `ID`.</span><span class="sxs-lookup"><span data-stu-id="bb384-125">Note the key icon next to `ID`.</span></span> <span data-ttu-id="bb384-126">Ve výchozím nastavení objekt EF vytvoří vlastnost s názvem `ID` primární klíč.</span><span class="sxs-lookup"><span data-stu-id="bb384-126">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="bb384-127">Klikněte pravým tlačítkem `Movie` na tabulku **> zobrazit data.**</span><span class="sxs-lookup"><span data-stu-id="bb384-127">Right click on the `Movie` table **> View Data**</span></span>

  ![Kontextová nabídka otevřená v tabulce videí](working-with-sql/_static/ssox2.png)

  ![Tabulka videa otevřená zobrazující data tabulky](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="bb384-130">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="bb384-130">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="bb384-131">Dosazení databáze</span><span class="sxs-lookup"><span data-stu-id="bb384-131">Seed the database</span></span>

<span data-ttu-id="bb384-132">Vytvořte novou třídu s názvem `SeedData` ve složce *modely* .</span><span class="sxs-lookup"><span data-stu-id="bb384-132">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="bb384-133">Vygenerovaný kód nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="bb384-133">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="bb384-134">Pokud databáze obsahuje nějaké filmy, inicializátor počáteční hodnoty se vrátí a nepřidá se žádné filmy.</span><span class="sxs-lookup"><span data-stu-id="bb384-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="bb384-135">Přidat inicializátor počáteční hodnoty</span><span class="sxs-lookup"><span data-stu-id="bb384-135">Add the seed initializer</span></span>

<span data-ttu-id="bb384-136">Obsah *program.cs* nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="bb384-136">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

<span data-ttu-id="bb384-137">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="bb384-137">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bb384-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bb384-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bb384-139">Odstraňte všechny záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="bb384-139">Delete all the records in the DB.</span></span> <span data-ttu-id="bb384-140">Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z SSOX.</span><span class="sxs-lookup"><span data-stu-id="bb384-140">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="bb384-141">Vynuťte inicializaci aplikace (volání metod ve `Startup` třídě), takže se metoda počáteční hodnoty spustí.</span><span class="sxs-lookup"><span data-stu-id="bb384-141">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="bb384-142">Chcete-li vynutit inicializaci, IIS Express musí být zastavena a restartována.</span><span class="sxs-lookup"><span data-stu-id="bb384-142">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="bb384-143">Můžete to provést s některým z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="bb384-143">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="bb384-144">Klikněte pravým tlačítkem myši na ikonu IIS Express systému v oznamovací oblasti a klepněte na **konec** nebo **zastavení webu** .</span><span class="sxs-lookup"><span data-stu-id="bb384-144">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![IIS Express ikona na hlavním panelu systému](working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="bb384-147">Pokud jste spustili VS v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="bb384-147">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="bb384-148">Pokud jste spustili VS v režimu ladění, ukončete ladicí program a stiskněte klávesu F5.</span><span class="sxs-lookup"><span data-stu-id="bb384-148">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="bb384-149">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="bb384-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="bb384-150">Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí).</span><span class="sxs-lookup"><span data-stu-id="bb384-150">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="bb384-151">Zastavením a spuštěním aplikace dosadíte databázi.</span><span class="sxs-lookup"><span data-stu-id="bb384-151">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="bb384-152">Aplikace zobrazuje dosazený data.</span><span class="sxs-lookup"><span data-stu-id="bb384-152">The app shows the seeded data.</span></span>

![Filmová aplikace MVC otevřená v Microsoft Edge ukazující data videa](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="bb384-154">[Předchozí](adding-model.md)
> [Další](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="bb384-154">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bb384-155">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bb384-155">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bb384-156">`MvcMovieContext` Objekt zpracovává úlohu připojení k databázi a mapování `Movie` objektů na záznamy databáze.</span><span class="sxs-lookup"><span data-stu-id="bb384-156">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="bb384-157">Kontext databáze je zaregistrován s kontejnerem [Injektáže závislosti](xref:fundamentals/dependency-injection) v `ConfigureServices` metodě v souboru *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="bb384-157">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bb384-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bb384-158">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="bb384-159">[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="bb384-159">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="bb384-160">Pro místní vývoj načte připojovací řetězec ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="bb384-160">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="bb384-161">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="bb384-161">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="bb384-162">[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="bb384-162">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="bb384-163">Pro místní vývoj načte připojovací řetězec ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="bb384-163">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="bb384-164">Když nasadíte aplikaci na testovací nebo provozní server, můžete k nastavení připojovacího řetězce na reálné SQL Server použít proměnnou prostředí nebo jiný přístup.</span><span class="sxs-lookup"><span data-stu-id="bb384-164">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="bb384-165">Další informace najdete v části [Konfigurace](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="bb384-165">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bb384-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bb384-166">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="bb384-167">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="bb384-167">SQL Server Express LocalDB</span></span>

<span data-ttu-id="bb384-168">LocalDB je zjednodušená verze databázového stroje SQL Server Express, který je zaměřený na vývoj programu.</span><span class="sxs-lookup"><span data-stu-id="bb384-168">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="bb384-169">LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje složitá konfigurace.</span><span class="sxs-lookup"><span data-stu-id="bb384-169">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="bb384-170">Ve výchozím nastavení vytvoří databáze LocalDB soubory *. mdf* v adresáři *C:/Users/{User}* .</span><span class="sxs-lookup"><span data-stu-id="bb384-170">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="bb384-171">V nabídce **Zobrazit** otevřete **Průzkumník objektů systému SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="bb384-171">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Nabídka Zobrazit](working-with-sql/_static/ssox.png)

* <span data-ttu-id="bb384-173">Klikněte pravým tlačítkem `Movie` na tabulku **> návrháře zobrazení.**</span><span class="sxs-lookup"><span data-stu-id="bb384-173">Right click on the `Movie` table **> View Designer**</span></span>

  ![Kontextová nabídka otevřená v tabulce videí](working-with-sql/_static/design.png)

  ![Tabulka videí otevřená v Návrháři](working-with-sql/_static/dv.png)

<span data-ttu-id="bb384-176">Poznamenejte si ikonu klíče vedle `ID`.</span><span class="sxs-lookup"><span data-stu-id="bb384-176">Note the key icon next to `ID`.</span></span> <span data-ttu-id="bb384-177">Ve výchozím nastavení objekt EF vytvoří vlastnost s názvem `ID` primární klíč.</span><span class="sxs-lookup"><span data-stu-id="bb384-177">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="bb384-178">Klikněte pravým tlačítkem `Movie` na tabulku **> zobrazit data.**</span><span class="sxs-lookup"><span data-stu-id="bb384-178">Right click on the `Movie` table **> View Data**</span></span>

  ![Kontextová nabídka otevřená v tabulce videí](working-with-sql/_static/ssox2.png)

  ![Tabulka videa otevřená zobrazující data tabulky](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="bb384-181">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="bb384-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="bb384-182">Dosazení databáze</span><span class="sxs-lookup"><span data-stu-id="bb384-182">Seed the database</span></span>

<span data-ttu-id="bb384-183">Vytvořte novou třídu s názvem `SeedData` ve složce *modely* .</span><span class="sxs-lookup"><span data-stu-id="bb384-183">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="bb384-184">Vygenerovaný kód nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="bb384-184">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="bb384-185">Pokud databáze obsahuje nějaké filmy, inicializátor počáteční hodnoty se vrátí a nepřidá se žádné filmy.</span><span class="sxs-lookup"><span data-stu-id="bb384-185">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="bb384-186">Přidat inicializátor počáteční hodnoty</span><span class="sxs-lookup"><span data-stu-id="bb384-186">Add the seed initializer</span></span>

<span data-ttu-id="bb384-187">Obsah *program.cs* nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="bb384-187">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

<span data-ttu-id="bb384-188">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="bb384-188">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bb384-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bb384-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bb384-190">Odstraňte všechny záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="bb384-190">Delete all the records in the DB.</span></span> <span data-ttu-id="bb384-191">Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z SSOX.</span><span class="sxs-lookup"><span data-stu-id="bb384-191">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="bb384-192">Vynuťte inicializaci aplikace (volání metod ve `Startup` třídě), takže se metoda počáteční hodnoty spustí.</span><span class="sxs-lookup"><span data-stu-id="bb384-192">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="bb384-193">Chcete-li vynutit inicializaci, IIS Express musí být zastavena a restartována.</span><span class="sxs-lookup"><span data-stu-id="bb384-193">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="bb384-194">Můžete to provést s některým z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="bb384-194">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="bb384-195">Klikněte pravým tlačítkem myši na ikonu IIS Express systému v oznamovací oblasti a klepněte na **konec** nebo **zastavení webu** .</span><span class="sxs-lookup"><span data-stu-id="bb384-195">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![IIS Express ikona na hlavním panelu systému](working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="bb384-198">Pokud jste spustili VS v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="bb384-198">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="bb384-199">Pokud jste spustili VS v režimu ladění, ukončete ladicí program a stiskněte klávesu F5.</span><span class="sxs-lookup"><span data-stu-id="bb384-199">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="bb384-200">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="bb384-200">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="bb384-201">Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí).</span><span class="sxs-lookup"><span data-stu-id="bb384-201">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="bb384-202">Zastavením a spuštěním aplikace dosadíte databázi.</span><span class="sxs-lookup"><span data-stu-id="bb384-202">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="bb384-203">Aplikace zobrazuje dosazený data.</span><span class="sxs-lookup"><span data-stu-id="bb384-203">The app shows the seeded data.</span></span>

![Filmová aplikace MVC otevřená v Microsoft Edge ukazující data videa](working-with-sql/_static/m55_mac.png)

> [!div class="step-by-step"]
> <span data-ttu-id="bb384-205">[Předchozí](adding-model.md)
> [Další](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="bb384-205">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>

::: moniker-end
