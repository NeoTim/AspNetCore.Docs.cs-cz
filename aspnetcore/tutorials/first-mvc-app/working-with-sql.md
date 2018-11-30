---
title: Práce s verzí SQL Server LocalDB v ASP.NET Core MVC aplikace
author: rick-anderson
description: Další informace o použití SQL Server LocalDB v jednoduchou aplikaci ASP.NET Core MVC.
ms.author: riande
ms.date: 03/07/2017
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: 49615c25d51cfa671157c2e56b8e0753719c678a
ms.sourcegitcommit: c4572be5ebb301013a5698caf9b5572b76cb2e34
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52710098"
---
# <a name="work-with-sql-server-localdb-in-aspnet-core"></a><span data-ttu-id="41eca-103">Práce s verzí SQL Server LocalDB v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="41eca-103">Work with SQL Server LocalDB in ASP.NET Core</span></span>

<span data-ttu-id="41eca-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="41eca-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="41eca-105">`MvcMovieContext` Objekt zpracovává úlohu s připojením k databázi a mapování `Movie` objekty se záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="41eca-105">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="41eca-106">Kontext databáze je zaregistrován [injektáž závislostí](xref:fundamentals/dependency-injection) kontejneru v `ConfigureServices` metoda ve *Startup.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="41eca-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Startup.cs?name=ConfigureServices&highlight=13-99)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=ConfigureServices&highlight=6-7)]

::: moniker-end

<span data-ttu-id="41eca-107">ASP.NET Core [konfigurace](xref:fundamentals/configuration/index) systému čtení `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="41eca-107">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="41eca-108">Pro místní vývoj, získá připojovací řetězec z *appsettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="41eca-108">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

<span data-ttu-id="41eca-109">Když nasadíte aplikaci na testovacím nebo produkčním serveru, můžete použít proměnné prostředí nebo jiné přístup se nastavit připojovací řetězec na skutečný SQL Server.</span><span class="sxs-lookup"><span data-stu-id="41eca-109">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="41eca-110">Zobrazit [konfigurace](xref:fundamentals/configuration/index) Další informace.</span><span class="sxs-lookup"><span data-stu-id="41eca-110">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

## <a name="sql-server-express-localdb"></a><span data-ttu-id="41eca-111">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="41eca-111">SQL Server Express LocalDB</span></span>

<span data-ttu-id="41eca-112">LocalDB je Odlehčená verze SQL serveru Express databázového stroje, která je určená pro vývoj v programu.</span><span class="sxs-lookup"><span data-stu-id="41eca-112">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="41eca-113">LocalDB spustí na vyžádání a běží v uživatelském režimu, takže není bez složité konfigurace.</span><span class="sxs-lookup"><span data-stu-id="41eca-113">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="41eca-114">Ve výchozím nastavení, vytvoří databázi LocalDB "\*.mdf" soubory *C:/uživatele/\<uživatele\>*  adresáře.</span><span class="sxs-lookup"><span data-stu-id="41eca-114">By default, LocalDB database creates "\*.mdf" files in the *C:/Users/\<user\>* directory.</span></span>

* <span data-ttu-id="41eca-115">Z **zobrazení** nabídce otevřete **Průzkumník objektů systému SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="41eca-115">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Nabídka Zobrazit](working-with-sql/_static/ssox.png)

* <span data-ttu-id="41eca-117">Klikněte pravým tlačítkem na `Movie` tabulky **> Návrhář zobrazení**</span><span class="sxs-lookup"><span data-stu-id="41eca-117">Right click on the `Movie` table **> View Designer**</span></span>

  ![Kontextová nabídka otevřít na tabulky Movie](working-with-sql/_static/design.png)

  ![Otevřít v Návrháři tabulky Movie](working-with-sql/_static/dv.png)

<span data-ttu-id="41eca-120">Poznámka: na ikonu klíče vedle `ID`.</span><span class="sxs-lookup"><span data-stu-id="41eca-120">Note the key icon next to `ID`.</span></span> <span data-ttu-id="41eca-121">Ve výchozím nastavení, budou EF vlastnost s názvem `ID` primární klíč.</span><span class="sxs-lookup"><span data-stu-id="41eca-121">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="41eca-122">Klikněte pravým tlačítkem na `Movie` tabulky **> Data zobrazení**</span><span class="sxs-lookup"><span data-stu-id="41eca-122">Right click on the `Movie` table **> View Data**</span></span>

  ![Kontextová nabídka otevřít na tabulky Movie](working-with-sql/_static/ssox2.png)

  ![Otevřít zobrazení tabulky dat tabulky Movie](working-with-sql/_static/vd22.png)

## <a name="seed-the-database"></a><span data-ttu-id="41eca-125">Přidání dat do databáze</span><span class="sxs-lookup"><span data-stu-id="41eca-125">Seed the database</span></span>

<span data-ttu-id="41eca-126">Vytvořte novou třídu s názvem `SeedData` v *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="41eca-126">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="41eca-127">Generovaného kódu nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="41eca-127">Replace the generated code with the following:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="41eca-128">Pokud jsou všechny filmy v databázi, vrátí inicializátoru pro dosazení hodnot a jsou přidány žádné video.</span><span class="sxs-lookup"><span data-stu-id="41eca-128">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>
### <a name="add-the-seed-initializer"></a><span data-ttu-id="41eca-129">Přidat inicializační výraz počáteční hodnoty</span><span class="sxs-lookup"><span data-stu-id="41eca-129">Add the seed initializer</span></span>

<span data-ttu-id="41eca-130">Nahraďte obsah *Program.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="41eca-130">Replace the contents of *Program.cs* with the following code:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Program.cs)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="41eca-131">Přidat inicializační výraz počáteční hodnoty `Main` metodu *Program.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="41eca-131">Add the seed initializer to the `Main` method in the *Program.cs* file:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Program.cs?highlight=6,14-32)]

::: moniker-end

<span data-ttu-id="41eca-132">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="41eca-132">Test the app</span></span>

* <span data-ttu-id="41eca-133">Odstraníte všechny záznamy z databáze.</span><span class="sxs-lookup"><span data-stu-id="41eca-133">Delete all the records in the DB.</span></span> <span data-ttu-id="41eca-134">Můžete to provést s odstranit odkazy v prohlížeči nebo z SSOX.</span><span class="sxs-lookup"><span data-stu-id="41eca-134">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="41eca-135">Se aplikace inicializuje (volat metody ve `Startup` třídy), spustí seed – metoda.</span><span class="sxs-lookup"><span data-stu-id="41eca-135">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="41eca-136">Pokud chcete vynutit inicializace, služba IIS Express musí zastavit, restartovat.</span><span class="sxs-lookup"><span data-stu-id="41eca-136">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="41eca-137">Provést s některou z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="41eca-137">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="41eca-138">Klikněte pravým tlačítkem na službu IIS Express systému na hlavním panelu ikonu v oznamovací oblasti a klepněte na **ukončovací** nebo **zastavení webu**</span><span class="sxs-lookup"><span data-stu-id="41eca-138">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![Služba IIS Express ikonu na hlavním panelu](working-with-sql/_static/iisExIcon.png)

    ![Kontextové nabídky](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="41eca-141">Pokud VS byly spuštěny v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění</span><span class="sxs-lookup"><span data-stu-id="41eca-141">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="41eca-142">Pokud jste používali VS v režimu ladění zastavení ladicího programu a stisknutím klávesy F5</span><span class="sxs-lookup"><span data-stu-id="41eca-142">If you were running VS in debug mode, stop the debugger and press F5</span></span>

<span data-ttu-id="41eca-143">Aplikace zobrazí dosazená data.</span><span class="sxs-lookup"><span data-stu-id="41eca-143">The app shows the seeded data.</span></span>

![Aplikace MVC Movie otevřete v Microsoft Edge zobrazující data o filmech](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="41eca-145">[Předchozí](adding-model.md)
> [další](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="41eca-145">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>  
