---
title: Práce s SQL v aplikaci ASP.NET Core MVC
author: rick-anderson
description: Přečtěte si o použití SQL Server LocalDB nebo SQLite v aplikaci ASP.NET Core MVC.
ms.author: riande
ms.date: 8/16/2019
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: d556f07111fb2022a1c2f1a066459566e302835d
ms.sourcegitcommit: da2fb2d78ce70accdba903ccbfdcfffdd0112123
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75722765"
---
# <a name="work-with-sql-in-aspnet-core"></a><span data-ttu-id="525e1-103">Práce s SQL v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="525e1-103">Work with SQL in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="525e1-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="525e1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="525e1-105">Objekt `MvcMovieContext` zpracovává úlohu připojení k databázi a mapování objektů `Movie` na záznamy databáze.</span><span class="sxs-lookup"><span data-stu-id="525e1-105">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="525e1-106">Kontext databáze je zaregistrován s kontejnerem [vkládání závislostí](xref:fundamentals/dependency-injection) v metodě `ConfigureServices` v souboru *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="525e1-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="525e1-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="525e1-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="525e1-108">[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="525e1-108">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="525e1-109">Pro místní vývoj načte připojovací řetězec ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="525e1-109">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="525e1-110">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="525e1-110">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

<span data-ttu-id="525e1-111">[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="525e1-111">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="525e1-112">Pro místní vývoj načte připojovací řetězec ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="525e1-112">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="525e1-113">Při nasazení aplikace do testovacího nebo provozního serveru lze použít proměnnou prostředí k nastavení připojovacího řetězce na produkční SQL Server.</span><span class="sxs-lookup"><span data-stu-id="525e1-113">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a production SQL Server.</span></span> <span data-ttu-id="525e1-114">Další informace najdete v části [Konfigurace](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="525e1-114">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="525e1-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="525e1-115">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="525e1-116">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="525e1-116">SQL Server Express LocalDB</span></span>

<span data-ttu-id="525e1-117">LocalDB je zjednodušená verze databázového stroje SQL Server Express, který je zaměřený na vývoj programu.</span><span class="sxs-lookup"><span data-stu-id="525e1-117">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="525e1-118">LocalDB spustí na vyžádání a běží v uživatelském režimu, takže není bez složité konfigurace.</span><span class="sxs-lookup"><span data-stu-id="525e1-118">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="525e1-119">Ve výchozím nastavení vytvoří databáze LocalDB soubory *. mdf* v adresáři *C:/Users/{User}* .</span><span class="sxs-lookup"><span data-stu-id="525e1-119">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="525e1-120">V nabídce **Zobrazit** otevřete **Průzkumník objektů systému SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="525e1-120">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Nabídka Zobrazit](working-with-sql/_static/ssox.png)

* <span data-ttu-id="525e1-122">Klikněte pravým tlačítkem na `Movie` tabulce **> návrháře zobrazení**</span><span class="sxs-lookup"><span data-stu-id="525e1-122">Right click on the `Movie` table **> View Designer**</span></span>

  ![Kontextová nabídka otevřená v tabulce videí](working-with-sql/_static/design.png)

  ![Tabulka videí otevřená v Návrháři](working-with-sql/_static/dv.png)

<span data-ttu-id="525e1-125">Poznamenejte si ikonu klíče vedle `ID`.</span><span class="sxs-lookup"><span data-stu-id="525e1-125">Note the key icon next to `ID`.</span></span> <span data-ttu-id="525e1-126">Ve výchozím nastavení objekt EF vytvoří vlastnost s názvem `ID` primární klíč.</span><span class="sxs-lookup"><span data-stu-id="525e1-126">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="525e1-127">Klikněte pravým tlačítkem na `Movie` tabulce **> zobrazit data.**</span><span class="sxs-lookup"><span data-stu-id="525e1-127">Right click on the `Movie` table **> View Data**</span></span>

  ![Kontextová nabídka otevřená v tabulce videí](working-with-sql/_static/ssox2.png)

  ![Tabulka videa otevřená zobrazující data tabulky](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="525e1-130">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="525e1-130">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="525e1-131">Přidání dat do databáze</span><span class="sxs-lookup"><span data-stu-id="525e1-131">Seed the database</span></span>

<span data-ttu-id="525e1-132">Vytvořte novou třídu s názvem `SeedData` ve složce *modely* .</span><span class="sxs-lookup"><span data-stu-id="525e1-132">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="525e1-133">Vygenerovaný kód nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="525e1-133">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="525e1-134">Pokud databáze obsahuje nějaké filmy, inicializátor počáteční hodnoty se vrátí a nepřidá se žádné filmy.</span><span class="sxs-lookup"><span data-stu-id="525e1-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="525e1-135">Přidat inicializátor počáteční hodnoty</span><span class="sxs-lookup"><span data-stu-id="525e1-135">Add the seed initializer</span></span>

<span data-ttu-id="525e1-136">Obsah *program.cs* nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="525e1-136">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

<span data-ttu-id="525e1-137">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="525e1-137">Test the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="525e1-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="525e1-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="525e1-139">Odstraňte všechny záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="525e1-139">Delete all the records in the DB.</span></span> <span data-ttu-id="525e1-140">Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z SSOX.</span><span class="sxs-lookup"><span data-stu-id="525e1-140">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="525e1-141">Vynuťte inicializaci aplikace (voláním metod ve třídě `Startup`), takže se metoda počáteční hodnoty spustí.</span><span class="sxs-lookup"><span data-stu-id="525e1-141">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="525e1-142">Chcete-li vynutit inicializaci, IIS Express musí být zastavena a restartována.</span><span class="sxs-lookup"><span data-stu-id="525e1-142">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="525e1-143">Můžete to provést s některým z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="525e1-143">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="525e1-144">Klikněte pravým tlačítkem myši na ikonu IIS Express systému v oznamovací oblasti a klepněte na **konec** nebo **zastavení webu** .</span><span class="sxs-lookup"><span data-stu-id="525e1-144">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![IIS Express ikona na hlavním panelu systému](working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="525e1-147">Pokud jste spustili VS v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="525e1-147">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="525e1-148">Pokud jste spustili VS v režimu ladění, ukončete ladicí program a stiskněte klávesu F5.</span><span class="sxs-lookup"><span data-stu-id="525e1-148">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="525e1-149">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="525e1-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="525e1-150">Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí).</span><span class="sxs-lookup"><span data-stu-id="525e1-150">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="525e1-151">Zastavením a spuštěním aplikace dosadíte databázi.</span><span class="sxs-lookup"><span data-stu-id="525e1-151">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="525e1-152">Aplikace zobrazuje dosazený data.</span><span class="sxs-lookup"><span data-stu-id="525e1-152">The app shows the seeded data.</span></span>

![Filmová aplikace MVC otevřená v Microsoft Edge ukazující data videa](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="525e1-154">[Předchozí](adding-model.md)
> [Další](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="525e1-154">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="525e1-155">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="525e1-155">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="525e1-156">Objekt `MvcMovieContext` zpracovává úlohu připojení k databázi a mapování objektů `Movie` na záznamy databáze.</span><span class="sxs-lookup"><span data-stu-id="525e1-156">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="525e1-157">Kontext databáze je zaregistrován s kontejnerem [vkládání závislostí](xref:fundamentals/dependency-injection) v metodě `ConfigureServices` v souboru *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="525e1-157">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="525e1-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="525e1-158">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="525e1-159">[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="525e1-159">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="525e1-160">Pro místní vývoj načte připojovací řetězec ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="525e1-160">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="525e1-161">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="525e1-161">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="525e1-162">[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="525e1-162">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="525e1-163">Pro místní vývoj načte připojovací řetězec ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="525e1-163">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="525e1-164">Když nasadíte aplikaci na testovací nebo provozní server, můžete k nastavení připojovacího řetězce na reálné SQL Server použít proměnnou prostředí nebo jiný přístup.</span><span class="sxs-lookup"><span data-stu-id="525e1-164">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="525e1-165">Další informace najdete v části [Konfigurace](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="525e1-165">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="525e1-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="525e1-166">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="525e1-167">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="525e1-167">SQL Server Express LocalDB</span></span>

<span data-ttu-id="525e1-168">LocalDB je zjednodušená verze databázového stroje SQL Server Express, který je zaměřený na vývoj programu.</span><span class="sxs-lookup"><span data-stu-id="525e1-168">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="525e1-169">LocalDB spustí na vyžádání a běží v uživatelském režimu, takže není bez složité konfigurace.</span><span class="sxs-lookup"><span data-stu-id="525e1-169">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="525e1-170">Ve výchozím nastavení vytvoří databáze LocalDB soubory *. mdf* v adresáři *C:/Users/{User}* .</span><span class="sxs-lookup"><span data-stu-id="525e1-170">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="525e1-171">V nabídce **Zobrazit** otevřete **Průzkumník objektů systému SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="525e1-171">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Nabídka Zobrazit](working-with-sql/_static/ssox.png)

* <span data-ttu-id="525e1-173">Klikněte pravým tlačítkem na `Movie` tabulce **> návrháře zobrazení**</span><span class="sxs-lookup"><span data-stu-id="525e1-173">Right click on the `Movie` table **> View Designer**</span></span>

  ![Kontextová nabídka otevřená v tabulce videí](working-with-sql/_static/design.png)

  ![Tabulka videí otevřená v Návrháři](working-with-sql/_static/dv.png)

<span data-ttu-id="525e1-176">Poznamenejte si ikonu klíče vedle `ID`.</span><span class="sxs-lookup"><span data-stu-id="525e1-176">Note the key icon next to `ID`.</span></span> <span data-ttu-id="525e1-177">Ve výchozím nastavení objekt EF vytvoří vlastnost s názvem `ID` primární klíč.</span><span class="sxs-lookup"><span data-stu-id="525e1-177">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="525e1-178">Klikněte pravým tlačítkem na `Movie` tabulce **> zobrazit data.**</span><span class="sxs-lookup"><span data-stu-id="525e1-178">Right click on the `Movie` table **> View Data**</span></span>

  ![Kontextová nabídka otevřená v tabulce videí](working-with-sql/_static/ssox2.png)

  ![Tabulka videa otevřená zobrazující data tabulky](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="525e1-181">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="525e1-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="525e1-182">Přidání dat do databáze</span><span class="sxs-lookup"><span data-stu-id="525e1-182">Seed the database</span></span>

<span data-ttu-id="525e1-183">Vytvořte novou třídu s názvem `SeedData` ve složce *modely* .</span><span class="sxs-lookup"><span data-stu-id="525e1-183">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="525e1-184">Vygenerovaný kód nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="525e1-184">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="525e1-185">Pokud databáze obsahuje nějaké filmy, inicializátor počáteční hodnoty se vrátí a nepřidá se žádné filmy.</span><span class="sxs-lookup"><span data-stu-id="525e1-185">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="525e1-186">Přidat inicializátor počáteční hodnoty</span><span class="sxs-lookup"><span data-stu-id="525e1-186">Add the seed initializer</span></span>

<span data-ttu-id="525e1-187">Obsah *program.cs* nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="525e1-187">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

<span data-ttu-id="525e1-188">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="525e1-188">Test the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="525e1-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="525e1-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="525e1-190">Odstraňte všechny záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="525e1-190">Delete all the records in the DB.</span></span> <span data-ttu-id="525e1-191">Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z SSOX.</span><span class="sxs-lookup"><span data-stu-id="525e1-191">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="525e1-192">Vynuťte inicializaci aplikace (voláním metod ve třídě `Startup`), takže se metoda počáteční hodnoty spustí.</span><span class="sxs-lookup"><span data-stu-id="525e1-192">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="525e1-193">Chcete-li vynutit inicializaci, IIS Express musí být zastavena a restartována.</span><span class="sxs-lookup"><span data-stu-id="525e1-193">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="525e1-194">Můžete to provést s některým z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="525e1-194">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="525e1-195">Klikněte pravým tlačítkem myši na ikonu IIS Express systému v oznamovací oblasti a klepněte na **konec** nebo **zastavení webu** .</span><span class="sxs-lookup"><span data-stu-id="525e1-195">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![IIS Express ikona na hlavním panelu systému](working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="525e1-198">Pokud jste spustili VS v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="525e1-198">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="525e1-199">Pokud jste spustili VS v režimu ladění, ukončete ladicí program a stiskněte klávesu F5.</span><span class="sxs-lookup"><span data-stu-id="525e1-199">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="525e1-200">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="525e1-200">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="525e1-201">Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí).</span><span class="sxs-lookup"><span data-stu-id="525e1-201">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="525e1-202">Zastavením a spuštěním aplikace dosadíte databázi.</span><span class="sxs-lookup"><span data-stu-id="525e1-202">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="525e1-203">Aplikace zobrazuje dosazený data.</span><span class="sxs-lookup"><span data-stu-id="525e1-203">The app shows the seeded data.</span></span>

![Filmová aplikace MVC otevřená v Microsoft Edge ukazující data videa](working-with-sql/_static/m55_mac.png)

> [!div class="step-by-step"]
> <span data-ttu-id="525e1-205">[Předchozí](adding-model.md)
> [Další](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="525e1-205">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>

::: moniker-end
