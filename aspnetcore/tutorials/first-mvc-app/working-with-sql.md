---
title: Práce s SQL v aplikaci MVC ASP.NET Core
author: rick-anderson
description: Další informace o použití SQL Server LocalDB nebo SQLite v aplikaci ASP.NET Core MVC.
ms.author: riande
ms.date: 03/07/2017
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: a6b2026a43bda94a84a05749e9ea395308dec231
ms.sourcegitcommit: 34bf9fc6ea814c039401fca174642f0acb14be3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2019
ms.locfileid: "57841433"
---
# <a name="work-with-sql-in-aspnet-core"></a><span data-ttu-id="9407b-103">Práce s SQL v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9407b-103">Work with SQL in ASP.NET Core</span></span>

<span data-ttu-id="9407b-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9407b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9407b-105">`MvcMovieContext` Objekt zpracovává úlohu s připojením k databázi a mapování `Movie` objekty se záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="9407b-105">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="9407b-106">Kontext databáze je zaregistrován [injektáž závislostí](xref:fundamentals/dependency-injection) kontejneru v `ConfigureServices` metoda ve *Startup.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="9407b-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9407b-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9407b-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="9407b-108">ASP.NET Core [konfigurace](xref:fundamentals/configuration/index) systému čtení `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="9407b-108">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="9407b-109">Pro místní vývoj, získá připojovací řetězec z *appsettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="9407b-109">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

<!-- Code -------------------------->
# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="9407b-110">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9407b-110">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="9407b-111">ASP.NET Core [konfigurace](xref:fundamentals/configuration/index) systému čtení `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="9407b-111">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="9407b-112">Pro místní vývoj, získá připojovací řetězec z *appsettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="9407b-112">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---  
<!-- End of VS tabs -->

<span data-ttu-id="9407b-113">Když nasadíte aplikaci na testovacím nebo produkčním serveru, můžete použít proměnné prostředí nebo jiné přístup se nastavit připojovací řetězec na skutečný SQL Server.</span><span class="sxs-lookup"><span data-stu-id="9407b-113">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="9407b-114">Zobrazit [konfigurace](xref:fundamentals/configuration/index) Další informace.</span><span class="sxs-lookup"><span data-stu-id="9407b-114">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9407b-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9407b-115">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="9407b-116">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="9407b-116">SQL Server Express LocalDB</span></span>

<span data-ttu-id="9407b-117">LocalDB je Odlehčená verze SQL serveru Express databázového stroje, která je určená pro vývoj v programu.</span><span class="sxs-lookup"><span data-stu-id="9407b-117">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="9407b-118">LocalDB spustí na vyžádání a běží v uživatelském režimu, takže není bez složité konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9407b-118">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="9407b-119">Ve výchozím nastavení, vytvoří databázi LocalDB *.mdf* soubory *C:/uživatele / {user}* adresáře.</span><span class="sxs-lookup"><span data-stu-id="9407b-119">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="9407b-120">Z **zobrazení** nabídce otevřete **Průzkumník objektů systému SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="9407b-120">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Nabídka Zobrazit](working-with-sql/_static/ssox.png)

* <span data-ttu-id="9407b-122">Klikněte pravým tlačítkem na `Movie` tabulky **> Návrhář zobrazení**</span><span class="sxs-lookup"><span data-stu-id="9407b-122">Right click on the `Movie` table **> View Designer**</span></span>

  ![Kontextová nabídka otevřít na tabulky Movie](working-with-sql/_static/design.png)

  ![Otevřít v Návrháři tabulky Movie](working-with-sql/_static/dv.png)

<span data-ttu-id="9407b-125">Poznámka: na ikonu klíče vedle `ID`.</span><span class="sxs-lookup"><span data-stu-id="9407b-125">Note the key icon next to `ID`.</span></span> <span data-ttu-id="9407b-126">Ve výchozím nastavení, budou EF vlastnost s názvem `ID` primární klíč.</span><span class="sxs-lookup"><span data-stu-id="9407b-126">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="9407b-127">Klikněte pravým tlačítkem na `Movie` tabulky **> Data zobrazení**</span><span class="sxs-lookup"><span data-stu-id="9407b-127">Right click on the `Movie` table **> View Data**</span></span>

  ![Kontextová nabídka otevřít na tabulky Movie](working-with-sql/_static/ssox2.png)

  ![Otevřít zobrazení tabulky dat tabulky Movie](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="9407b-130">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9407b-130">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---  
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="9407b-131">Přidání dat do databáze</span><span class="sxs-lookup"><span data-stu-id="9407b-131">Seed the database</span></span>

<span data-ttu-id="9407b-132">Vytvořte novou třídu s názvem `SeedData` v *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="9407b-132">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="9407b-133">Generovaného kódu nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="9407b-133">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="9407b-134">Pokud jsou všechny filmy v databázi, vrátí inicializátoru pro dosazení hodnot a jsou přidány žádné video.</span><span class="sxs-lookup"><span data-stu-id="9407b-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>
### <a name="add-the-seed-initializer"></a><span data-ttu-id="9407b-135">Přidat inicializační výraz počáteční hodnoty</span><span class="sxs-lookup"><span data-stu-id="9407b-135">Add the seed initializer</span></span>

<span data-ttu-id="9407b-136">Nahraďte obsah *Program.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="9407b-136">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

<span data-ttu-id="9407b-137">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="9407b-137">Test the app</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9407b-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9407b-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9407b-139">Odstraníte všechny záznamy z databáze.</span><span class="sxs-lookup"><span data-stu-id="9407b-139">Delete all the records in the DB.</span></span> <span data-ttu-id="9407b-140">Můžete to provést s odstranit odkazy v prohlížeči nebo z SSOX.</span><span class="sxs-lookup"><span data-stu-id="9407b-140">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="9407b-141">Se aplikace inicializuje (volat metody ve `Startup` třídy), spustí seed – metoda.</span><span class="sxs-lookup"><span data-stu-id="9407b-141">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="9407b-142">Pokud chcete vynutit inicializace, služba IIS Express musí zastavit, restartovat.</span><span class="sxs-lookup"><span data-stu-id="9407b-142">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="9407b-143">Provést s některou z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="9407b-143">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="9407b-144">Klikněte pravým tlačítkem na službu IIS Express systému na hlavním panelu ikonu v oznamovací oblasti a klepněte na **ukončovací** nebo **zastavení webu**</span><span class="sxs-lookup"><span data-stu-id="9407b-144">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![Služba IIS Express ikonu na hlavním panelu](working-with-sql/_static/iisExIcon.png)

    ![Kontextové nabídky](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="9407b-147">Pokud VS byly spuštěny v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění</span><span class="sxs-lookup"><span data-stu-id="9407b-147">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="9407b-148">Pokud jste používali VS v režimu ladění zastavení ladicího programu a stisknutím klávesy F5</span><span class="sxs-lookup"><span data-stu-id="9407b-148">If you were running VS in debug mode, stop the debugger and press F5</span></span>

<!-- Code -------------------------->
# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="9407b-149">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9407b-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9407b-150">Všechny záznamy z databáze odstraníte, (aby se spustí metodu počáteční hodnota).</span><span class="sxs-lookup"><span data-stu-id="9407b-150">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="9407b-151">Zastavení a spuštění aplikace k přidání dat do databáze.</span><span class="sxs-lookup"><span data-stu-id="9407b-151">Stop and start the app to seed the database.</span></span>

---  
<!-- End of VS tabs -->

<span data-ttu-id="9407b-152">Aplikace zobrazí dosazená data.</span><span class="sxs-lookup"><span data-stu-id="9407b-152">The app shows the seeded data.</span></span>

![Aplikace MVC Movie otevřete v Microsoft Edge zobrazující data o filmech](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="9407b-154">[Předchozí](adding-model.md)
> [další](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="9407b-154">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>  
