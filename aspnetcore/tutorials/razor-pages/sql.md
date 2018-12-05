---
title: Práce s databází a ASP.NET Core
author: rick-anderson
description: Vysvětluje, práci s databází a ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.date: 12/07/2017
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 817102a7b89ef4f078d7d0a0bf03ba7cb2745a5d
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52861274"
---
# <a name="work-with-a-database-and-aspnet-core"></a><span data-ttu-id="7a99f-103">Práce s databází a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7a99f-103">Work with a database and ASP.NET Core</span></span>

<span data-ttu-id="7a99f-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="7a99f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="7a99f-105">`RazorPagesMovieContext` Objekt zpracovává úlohu s připojením k databázi a mapování `Movie` objekty se záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="7a99f-105">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="7a99f-106">Kontext databáze je zaregistrován [injektáž závislostí](xref:fundamentals/dependency-injection) kontejneru v `ConfigureServices` metoda ve *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="7a99f-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7a99f-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7a99f-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7a99f-108">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7a99f-108">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7a99f-109">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7a99f-109">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---  
<!-- End of VS tabs -->

<span data-ttu-id="7a99f-110">Další informace o metodách `ConfigureServices`, naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="7a99f-110">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="7a99f-111">[Podpora EU obecného Regulation (GDPR) v ASP.NET Core](xref:security/gdpr) pro `CookiePolicyOptions`.</span><span class="sxs-lookup"><span data-stu-id="7a99f-111">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="7a99f-112">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="7a99f-112">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="7a99f-113">ASP.NET Core [konfigurace](xref:fundamentals/configuration/index) systému čtení `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="7a99f-113">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="7a99f-114">Pro místní vývoj, získá připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="7a99f-114">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7a99f-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7a99f-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7a99f-116">Hodnota názvu databáze (`Database={Database name}`) se bude lišit pro vygenerovaný kód.</span><span class="sxs-lookup"><span data-stu-id="7a99f-116">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="7a99f-117">Hodnota názvu je volitelný.</span><span class="sxs-lookup"><span data-stu-id="7a99f-117">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7a99f-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7a99f-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7a99f-119">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7a99f-119">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---  
<!-- End of VS tabs -->

<span data-ttu-id="7a99f-120">Po nasazení aplikace na testovacím nebo produkčním serveru, proměnné prostředí je možné nastavit připojovací řetězec na skutečné databázový server.</span><span class="sxs-lookup"><span data-stu-id="7a99f-120">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="7a99f-121">Zobrazit [konfigurace](xref:fundamentals/configuration/index) Další informace.</span><span class="sxs-lookup"><span data-stu-id="7a99f-121">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7a99f-122">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7a99f-122">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="7a99f-123">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="7a99f-123">SQL Server Express LocalDB</span></span>

<span data-ttu-id="7a99f-124">LocalDB je Odlehčená verze SQL Server Express. databázový stroj, která je určená pro vývoj v programu.</span><span class="sxs-lookup"><span data-stu-id="7a99f-124">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="7a99f-125">LocalDB spustí na vyžádání a běží v uživatelském režimu, takže není bez složité konfigurace.</span><span class="sxs-lookup"><span data-stu-id="7a99f-125">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="7a99f-126">Ve výchozím nastavení, vytvoří databázi LocalDB `*.mdf` soubory `C:/Users/<user/>` adresáře.</span><span class="sxs-lookup"><span data-stu-id="7a99f-126">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="7a99f-127">Z **zobrazení** nabídce otevřete **Průzkumník objektů systému SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="7a99f-127">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Nabídka Zobrazit](sql/_static/ssox.png)

* <span data-ttu-id="7a99f-129">Klikněte pravým tlačítkem na `Movie` tabulce a vybrat **Návrhář zobrazení**:</span><span class="sxs-lookup"><span data-stu-id="7a99f-129">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Kontextová nabídka otevřít na tabulky Movie](sql/_static/design.png)

  ![Otevřít v Návrháři tabulky Movie](sql/_static/dv.png)

<span data-ttu-id="7a99f-132">Poznámka: na ikonu klíče vedle `ID`.</span><span class="sxs-lookup"><span data-stu-id="7a99f-132">Note the key icon next to `ID`.</span></span> <span data-ttu-id="7a99f-133">Ve výchozím nastavení, EF vytvoří vlastnost s názvem `ID` pro primární klíč.</span><span class="sxs-lookup"><span data-stu-id="7a99f-133">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="7a99f-134">Klikněte pravým tlačítkem na `Movie` tabulce a vybrat **Data zobrazení**:</span><span class="sxs-lookup"><span data-stu-id="7a99f-134">Right click on the `Movie` table and select **View Data**:</span></span>

  <span data-ttu-id="7a99f-135">![Otevřít zobrazení tabulky dat tabulky Movie](sql/_static/vd22.png)
<!-- Code --------------------------></span><span class="sxs-lookup"><span data-stu-id="7a99f-135">![Movie table open showing table data](sql/_static/vd22.png)
<!-- Code --------------------------></span></span>
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7a99f-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7a99f-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7a99f-137">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7a99f-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]

---  
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="7a99f-138">Přidání dat do databáze</span><span class="sxs-lookup"><span data-stu-id="7a99f-138">Seed the database</span></span>

<span data-ttu-id="7a99f-139">Vytvořte novou třídu s názvem `SeedData` v *modely* složka s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="7a99f-139">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="7a99f-140">Pokud jsou všechny filmy v databázi, vrátí inicializátoru pro dosazení hodnot a jsou přidány žádné video.</span><span class="sxs-lookup"><span data-stu-id="7a99f-140">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```
<a name="si"></a>
### <a name="add-the-seed-initializer"></a><span data-ttu-id="7a99f-141">Přidat inicializační výraz počáteční hodnoty</span><span class="sxs-lookup"><span data-stu-id="7a99f-141">Add the seed initializer</span></span>

<span data-ttu-id="7a99f-142">V *Program.cs*, změnit `Main` metoda můžete provádět následující:</span><span class="sxs-lookup"><span data-stu-id="7a99f-142">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="7a99f-143">Instance kontextu databáze získáte z kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="7a99f-143">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="7a99f-144">Volejte metodu počáteční hodnoty předání kontextu.</span><span class="sxs-lookup"><span data-stu-id="7a99f-144">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="7a99f-145">Kontext Dispose po dokončení počáteční hodnoty metody.</span><span class="sxs-lookup"><span data-stu-id="7a99f-145">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="7a99f-146">Následující kód ukazuje aktualizovaný *Program.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="7a99f-146">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="7a99f-147">Produkční aplikace by volat `Database.Migrate`.</span><span class="sxs-lookup"><span data-stu-id="7a99f-147">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="7a99f-148">Přidá se do předchozí kód, aby se zabránilo následující výjimku při `Update-Database` nebyl spuštěn:</span><span class="sxs-lookup"><span data-stu-id="7a99f-148">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="7a99f-149">SqlException: Databázi "RazorPagesMovieContext 21" požadovaný v přihlášení nelze otevřít.</span><span class="sxs-lookup"><span data-stu-id="7a99f-149">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="7a99f-150">Přihlášení se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="7a99f-150">The login failed.</span></span>
<span data-ttu-id="7a99f-151">Přihlašovací jméno uživatele 'jméno uživatele' se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="7a99f-151">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="7a99f-152">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="7a99f-152">Test the app</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7a99f-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7a99f-153">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7a99f-154">Odstraníte všechny záznamy z databáze.</span><span class="sxs-lookup"><span data-stu-id="7a99f-154">Delete all the records in the DB.</span></span> <span data-ttu-id="7a99f-155">To lze provést pomocí odstranit odkazy v prohlížeči nebo z [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="7a99f-155">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="7a99f-156">Se aplikace inicializuje (volat metody ve `Startup` třídy), spustí seed – metoda.</span><span class="sxs-lookup"><span data-stu-id="7a99f-156">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="7a99f-157">Pokud chcete vynutit inicializace, služba IIS Express musí zastavit, restartovat.</span><span class="sxs-lookup"><span data-stu-id="7a99f-157">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="7a99f-158">Provést s některou z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="7a99f-158">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="7a99f-159">Klikněte pravým tlačítkem na službu IIS Express systému na hlavním panelu ikonu v oznamovací oblasti a klepněte na **ukončovací** nebo **zastavení webu**:</span><span class="sxs-lookup"><span data-stu-id="7a99f-159">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Služba IIS Express ikonu na hlavním panelu](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextové nabídky](sql/_static/stopIIS.png)

    * <span data-ttu-id="7a99f-162">Pokud VS byly spuštěny v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="7a99f-162">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="7a99f-163">Pokud jste používali zastavení ladicího programu VS v režimu ladění a stisknutím klávesy F5.</span><span class="sxs-lookup"><span data-stu-id="7a99f-163">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7a99f-164">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7a99f-164">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7a99f-165">Všechny záznamy z databáze odstraníte, (aby se spustí metodu počáteční hodnota).</span><span class="sxs-lookup"><span data-stu-id="7a99f-165">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="7a99f-166">Zastavení a spuštění aplikace k přidání dat do databáze.</span><span class="sxs-lookup"><span data-stu-id="7a99f-166">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="7a99f-167">Aplikace zobrazí dosazená data.</span><span class="sxs-lookup"><span data-stu-id="7a99f-167">The app shows the seeded data.</span></span>

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7a99f-168">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7a99f-168">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7a99f-169">Všechny záznamy z databáze odstraníte, (aby se spustí metodu počáteční hodnota).</span><span class="sxs-lookup"><span data-stu-id="7a99f-169">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="7a99f-170">Zastavení a spuštění aplikace k přidání dat do databáze.</span><span class="sxs-lookup"><span data-stu-id="7a99f-170">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="7a99f-171">Aplikace zobrazí dosazená data.</span><span class="sxs-lookup"><span data-stu-id="7a99f-171">The app shows the seeded data.</span></span>

---  
<!-- End of VS tabs -->


   
<span data-ttu-id="7a99f-172">Aplikace bude zobrazovat dosazená data:</span><span class="sxs-lookup"><span data-stu-id="7a99f-172">The app shows the seeded data:</span></span>

![Otevřít v prohlížeči Chrome ukazující data o filmech aplikace Movie](sql/_static/m55.png)

<span data-ttu-id="7a99f-174">V dalším kurzu se vyčistit prezentace data.</span><span class="sxs-lookup"><span data-stu-id="7a99f-174">The next tutorial will clean up the presentation of the data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="7a99f-175">[Předchozí: Generované uživatelské rozhraní pro stránky Razor](xref:tutorials/razor-pages/page)
> [Další: aktualizace stránek](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="7a99f-175">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>
