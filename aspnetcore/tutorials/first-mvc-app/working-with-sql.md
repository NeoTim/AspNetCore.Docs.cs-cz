---
title: 5. část práce s databází v aplikaci ASP.NET Core MVC
author: rick-anderson
description: Část 5 – přidání modelu do aplikace ASP.NET Core MVC
ms.author: riande
ms.date: 8/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: 7fdd147f4de00a593a77977d2b53e5369667fe78
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652519"
---
# <a name="part-5-work-with-a-database-in-an-aspnet-core-mvc-app"></a><span data-ttu-id="70ff4-103">5. část práce s databází v aplikaci ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="70ff4-103">Part 5, work with a database in an ASP.NET Core MVC app</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="70ff4-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="70ff4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="70ff4-105">`MvcMovieContext`Objekt zpracovává úlohu připojení k databázi a mapování `Movie` objektů na záznamy databáze.</span><span class="sxs-lookup"><span data-stu-id="70ff4-105">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="70ff4-106">Kontext databáze je zaregistrován s kontejnerem [Injektáže závislosti](xref:fundamentals/dependency-injection) v `ConfigureServices` metodě v souboru *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="70ff4-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="70ff4-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="70ff4-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="70ff4-108">[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="70ff4-108">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="70ff4-109">Pro místní vývoj načte připojovací řetězec ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="70ff4-109">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="70ff4-110">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="70ff4-110">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

<span data-ttu-id="70ff4-111">[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="70ff4-111">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="70ff4-112">Pro místní vývoj načte připojovací řetězec ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="70ff4-112">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="70ff4-113">Při nasazení aplikace do testovacího nebo provozního serveru lze použít proměnnou prostředí k nastavení připojovacího řetězce na produkční SQL Server.</span><span class="sxs-lookup"><span data-stu-id="70ff4-113">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a production SQL Server.</span></span> <span data-ttu-id="70ff4-114">Další informace najdete v části [Konfigurace](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="70ff4-114">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="70ff4-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="70ff4-115">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="70ff4-116">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="70ff4-116">SQL Server Express LocalDB</span></span>

<span data-ttu-id="70ff4-117">LocalDB je zjednodušená verze databázového stroje SQL Server Express, který je zaměřený na vývoj programu.</span><span class="sxs-lookup"><span data-stu-id="70ff4-117">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="70ff4-118">LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje složitá konfigurace.</span><span class="sxs-lookup"><span data-stu-id="70ff4-118">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="70ff4-119">Ve výchozím nastavení vytvoří databáze LocalDB soubory *. mdf* v adresáři *C:/Users/{User}* .</span><span class="sxs-lookup"><span data-stu-id="70ff4-119">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="70ff4-120">V nabídce **Zobrazit** otevřete **Průzkumník objektů systému SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="70ff4-120">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Nabídka Zobrazit](working-with-sql/_static/ssox.png)

* <span data-ttu-id="70ff4-122">Klikněte pravým tlačítkem na `Movie` tabulku **> návrháře zobrazení.**</span><span class="sxs-lookup"><span data-stu-id="70ff4-122">Right click on the `Movie` table **> View Designer**</span></span>

  ![Kontextová nabídka otevřená v tabulce videí](working-with-sql/_static/design.png)

  ![Tabulka videí otevřená v Návrháři](working-with-sql/_static/dv.png)

<span data-ttu-id="70ff4-125">Poznamenejte si ikonu klíče vedle `ID` .</span><span class="sxs-lookup"><span data-stu-id="70ff4-125">Note the key icon next to `ID`.</span></span> <span data-ttu-id="70ff4-126">Ve výchozím nastavení objekt EF vytvoří vlastnost s názvem `ID` primární klíč.</span><span class="sxs-lookup"><span data-stu-id="70ff4-126">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="70ff4-127">Klikněte pravým tlačítkem na `Movie` tabulku **> zobrazit data.**</span><span class="sxs-lookup"><span data-stu-id="70ff4-127">Right click on the `Movie` table **> View Data**</span></span>

  ![Kontextová nabídka otevřená v tabulce videí](working-with-sql/_static/ssox2.png)

  ![Tabulka videa otevřená zobrazující data tabulky](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="70ff4-130">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="70ff4-130">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="70ff4-131">Dosazení databáze</span><span class="sxs-lookup"><span data-stu-id="70ff4-131">Seed the database</span></span>

<span data-ttu-id="70ff4-132">Vytvořte novou třídu s názvem `SeedData` ve složce *modely* .</span><span class="sxs-lookup"><span data-stu-id="70ff4-132">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="70ff4-133">Vygenerovaný kód nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="70ff4-133">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="70ff4-134">Pokud databáze obsahuje nějaké filmy, inicializátor počáteční hodnoty se vrátí a nepřidá se žádné filmy.</span><span class="sxs-lookup"><span data-stu-id="70ff4-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="70ff4-135">Přidat inicializátor počáteční hodnoty</span><span class="sxs-lookup"><span data-stu-id="70ff4-135">Add the seed initializer</span></span>

<span data-ttu-id="70ff4-136">Obsah *program.cs* nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="70ff4-136">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

<span data-ttu-id="70ff4-137">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="70ff4-137">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="70ff4-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="70ff4-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="70ff4-139">Odstraňte všechny záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="70ff4-139">Delete all the records in the DB.</span></span> <span data-ttu-id="70ff4-140">Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z SSOX.</span><span class="sxs-lookup"><span data-stu-id="70ff4-140">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="70ff4-141">Vynuťte inicializaci aplikace (volání metod ve `Startup` třídě), takže se metoda počáteční hodnoty spustí.</span><span class="sxs-lookup"><span data-stu-id="70ff4-141">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="70ff4-142">Chcete-li vynutit inicializaci, IIS Express musí být zastavena a restartována.</span><span class="sxs-lookup"><span data-stu-id="70ff4-142">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="70ff4-143">Můžete to provést s některým z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="70ff4-143">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="70ff4-144">Klikněte pravým tlačítkem myši na ikonu IIS Express systému v oznamovací oblasti a klepněte na **konec** nebo **zastavení webu** .</span><span class="sxs-lookup"><span data-stu-id="70ff4-144">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![IIS Express ikona na hlavním panelu systému](working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="70ff4-147">Pokud jste spustili VS v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="70ff4-147">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="70ff4-148">Pokud jste spustili VS v režimu ladění, ukončete ladicí program a stiskněte klávesu F5.</span><span class="sxs-lookup"><span data-stu-id="70ff4-148">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="70ff4-149">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="70ff4-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="70ff4-150">Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí).</span><span class="sxs-lookup"><span data-stu-id="70ff4-150">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="70ff4-151">Zastavením a spuštěním aplikace dosadíte databázi.</span><span class="sxs-lookup"><span data-stu-id="70ff4-151">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="70ff4-152">Aplikace zobrazuje dosazený data.</span><span class="sxs-lookup"><span data-stu-id="70ff4-152">The app shows the seeded data.</span></span>

![Filmová aplikace MVC otevřená v Microsoft Edge ukazující data videa](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="70ff4-154">[Předchozí](adding-model.md) 
>  [Další](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="70ff4-154">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="70ff4-155">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="70ff4-155">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="70ff4-156">`MvcMovieContext`Objekt zpracovává úlohu připojení k databázi a mapování `Movie` objektů na záznamy databáze.</span><span class="sxs-lookup"><span data-stu-id="70ff4-156">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="70ff4-157">Kontext databáze je zaregistrován s kontejnerem [Injektáže závislosti](xref:fundamentals/dependency-injection) v `ConfigureServices` metodě v souboru *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="70ff4-157">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="70ff4-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="70ff4-158">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="70ff4-159">[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="70ff4-159">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="70ff4-160">Pro místní vývoj načte připojovací řetězec ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="70ff4-160">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="70ff4-161">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="70ff4-161">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="70ff4-162">[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="70ff4-162">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="70ff4-163">Pro místní vývoj načte připojovací řetězec ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="70ff4-163">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="70ff4-164">Když nasadíte aplikaci na testovací nebo provozní server, můžete k nastavení připojovacího řetězce na reálné SQL Server použít proměnnou prostředí nebo jiný přístup.</span><span class="sxs-lookup"><span data-stu-id="70ff4-164">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="70ff4-165">Další informace najdete v části [Konfigurace](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="70ff4-165">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="70ff4-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="70ff4-166">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="70ff4-167">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="70ff4-167">SQL Server Express LocalDB</span></span>

<span data-ttu-id="70ff4-168">LocalDB je zjednodušená verze databázového stroje SQL Server Express, který je zaměřený na vývoj programu.</span><span class="sxs-lookup"><span data-stu-id="70ff4-168">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="70ff4-169">LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje složitá konfigurace.</span><span class="sxs-lookup"><span data-stu-id="70ff4-169">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="70ff4-170">Ve výchozím nastavení vytvoří databáze LocalDB soubory *. mdf* v adresáři *C:/Users/{User}* .</span><span class="sxs-lookup"><span data-stu-id="70ff4-170">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="70ff4-171">V nabídce **Zobrazit** otevřete **Průzkumník objektů systému SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="70ff4-171">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Nabídka Zobrazit](working-with-sql/_static/ssox.png)

* <span data-ttu-id="70ff4-173">Klikněte pravým tlačítkem na `Movie` tabulku **> návrháře zobrazení.**</span><span class="sxs-lookup"><span data-stu-id="70ff4-173">Right click on the `Movie` table **> View Designer**</span></span>

  ![Kontextová nabídka otevřená v tabulce videí](working-with-sql/_static/design.png)

  ![Tabulka videí otevřená v Návrháři](working-with-sql/_static/dv.png)

<span data-ttu-id="70ff4-176">Poznamenejte si ikonu klíče vedle `ID` .</span><span class="sxs-lookup"><span data-stu-id="70ff4-176">Note the key icon next to `ID`.</span></span> <span data-ttu-id="70ff4-177">Ve výchozím nastavení objekt EF vytvoří vlastnost s názvem `ID` primární klíč.</span><span class="sxs-lookup"><span data-stu-id="70ff4-177">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="70ff4-178">Klikněte pravým tlačítkem na `Movie` tabulku **> zobrazit data.**</span><span class="sxs-lookup"><span data-stu-id="70ff4-178">Right click on the `Movie` table **> View Data**</span></span>

  ![Kontextová nabídka otevřená v tabulce videí](working-with-sql/_static/ssox2.png)

  ![Tabulka videa otevřená zobrazující data tabulky](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="70ff4-181">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="70ff4-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="70ff4-182">Dosazení databáze</span><span class="sxs-lookup"><span data-stu-id="70ff4-182">Seed the database</span></span>

<span data-ttu-id="70ff4-183">Vytvořte novou třídu s názvem `SeedData` ve složce *modely* .</span><span class="sxs-lookup"><span data-stu-id="70ff4-183">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="70ff4-184">Vygenerovaný kód nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="70ff4-184">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="70ff4-185">Pokud databáze obsahuje nějaké filmy, inicializátor počáteční hodnoty se vrátí a nepřidá se žádné filmy.</span><span class="sxs-lookup"><span data-stu-id="70ff4-185">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="70ff4-186">Přidat inicializátor počáteční hodnoty</span><span class="sxs-lookup"><span data-stu-id="70ff4-186">Add the seed initializer</span></span>

<span data-ttu-id="70ff4-187">Obsah *program.cs* nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="70ff4-187">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

<span data-ttu-id="70ff4-188">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="70ff4-188">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="70ff4-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="70ff4-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="70ff4-190">Odstraňte všechny záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="70ff4-190">Delete all the records in the DB.</span></span> <span data-ttu-id="70ff4-191">Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z SSOX.</span><span class="sxs-lookup"><span data-stu-id="70ff4-191">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="70ff4-192">Vynuťte inicializaci aplikace (volání metod ve `Startup` třídě), takže se metoda počáteční hodnoty spustí.</span><span class="sxs-lookup"><span data-stu-id="70ff4-192">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="70ff4-193">Chcete-li vynutit inicializaci, IIS Express musí být zastavena a restartována.</span><span class="sxs-lookup"><span data-stu-id="70ff4-193">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="70ff4-194">Můžete to provést s některým z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="70ff4-194">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="70ff4-195">Klikněte pravým tlačítkem myši na ikonu IIS Express systému v oznamovací oblasti a klepněte na **konec** nebo **zastavení webu** .</span><span class="sxs-lookup"><span data-stu-id="70ff4-195">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![IIS Express ikona na hlavním panelu systému](working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="70ff4-198">Pokud jste spustili VS v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="70ff4-198">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="70ff4-199">Pokud jste spustili VS v režimu ladění, ukončete ladicí program a stiskněte klávesu F5.</span><span class="sxs-lookup"><span data-stu-id="70ff4-199">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="70ff4-200">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="70ff4-200">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="70ff4-201">Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí).</span><span class="sxs-lookup"><span data-stu-id="70ff4-201">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="70ff4-202">Zastavením a spuštěním aplikace dosadíte databázi.</span><span class="sxs-lookup"><span data-stu-id="70ff4-202">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="70ff4-203">Aplikace zobrazuje dosazený data.</span><span class="sxs-lookup"><span data-stu-id="70ff4-203">The app shows the seeded data.</span></span>

![Filmová aplikace MVC otevřená v Microsoft Edge ukazující data videa](working-with-sql/_static/m55_mac.png)

> [!div class="step-by-step"]
> <span data-ttu-id="70ff4-205">[Předchozí](adding-model.md) 
>  [Další](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="70ff4-205">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>

::: moniker-end
