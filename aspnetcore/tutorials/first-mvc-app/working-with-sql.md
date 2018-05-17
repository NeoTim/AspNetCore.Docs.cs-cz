---
title: Práce s LocalDB serveru SQL v ASP.NET Core
author: rick-anderson
description: Další informace o použití SQL serveru LocalDB v jednoduchou aplikaci ASP.NET MVC jádra.
manager: wpickett
ms.author: riande
ms.date: 03/07/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: 6190c6dd958c62299aea89db398e691c1650af02
ms.sourcegitcommit: 9bc34b8269d2a150b844c3b8646dcb30278a95ea
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
---
# <a name="work-with-sql-server-localdb-in-aspnet-core"></a><span data-ttu-id="9767a-103">Práce s LocalDB serveru SQL v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9767a-103">Work with SQL Server LocalDB in ASP.NET Core</span></span>

<span data-ttu-id="9767a-104">podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9767a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9767a-105">`MvcMovieContext` Objekt zpracovává úlohu s připojením k databázi a mapování `Movie` objekty záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="9767a-105">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="9767a-106">Kontext databáze není zaregistrována [vkládání závislostí](xref:fundamentals/dependency-injection) kontejneru v `ConfigureServices` metoda v *Startup.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="9767a-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

[!code-csharp[](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=ConfigureServices&highlight=6-7)]

<span data-ttu-id="9767a-107">ASP.NET Core [konfigurace](xref:fundamentals/configuration/index) systému čtení `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="9767a-107">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="9767a-108">Pro místní vývoj, získá připojovací řetězec z *appSettings.JSON určený* souboru:</span><span class="sxs-lookup"><span data-stu-id="9767a-108">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

<span data-ttu-id="9767a-109">Když nasadíte aplikaci k testu nebo produkčním serveru, můžete použít proměnné prostředí nebo jiný přístup k nastavení připojovacího řetězce k skutečné systému SQL Server.</span><span class="sxs-lookup"><span data-stu-id="9767a-109">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="9767a-110">V tématu [konfigurace](xref:fundamentals/configuration/index) Další informace.</span><span class="sxs-lookup"><span data-stu-id="9767a-110">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

## <a name="sql-server-express-localdb"></a><span data-ttu-id="9767a-111">Databáze SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="9767a-111">SQL Server Express LocalDB</span></span>

<span data-ttu-id="9767a-112">LocalDB je Odlehčená verze SQL serveru Express databázového stroje je cílová pro vývoj programu.</span><span class="sxs-lookup"><span data-stu-id="9767a-112">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="9767a-113">LocalDB spustí na vyžádání a běží v uživatelském režimu, takže není žádná komplexní konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9767a-113">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="9767a-114">Ve výchozím nastavení, vytvoří databáze LocalDB "\*.mdf" soubory *C: či uživatelů nebo\<uživatele\>*  adresáře.</span><span class="sxs-lookup"><span data-stu-id="9767a-114">By default, LocalDB database creates "\*.mdf" files in the *C:/Users/\<user\>* directory.</span></span>

* <span data-ttu-id="9767a-115">Z **zobrazení** nabídce otevřete **Průzkumník objektů systému SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="9767a-115">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Nabídka Zobrazit](working-with-sql/_static/ssox.png)

* <span data-ttu-id="9767a-117">Klikněte pravým tlačítkem na `Movie` tabulky **> Návrhář zobrazení**</span><span class="sxs-lookup"><span data-stu-id="9767a-117">Right click on the `Movie` table **> View Designer**</span></span>

  ![Otevřete v tabulce film kontextové nabídky](working-with-sql/_static/design.png)

  ![Tabulka film otevřít v Návrháři](working-with-sql/_static/dv.png)

<span data-ttu-id="9767a-120">Poznámka: na ikonu klíče do `ID`.</span><span class="sxs-lookup"><span data-stu-id="9767a-120">Note the key icon next to `ID`.</span></span> <span data-ttu-id="9767a-121">Ve výchozím nastavení, bude EF vlastnost s názvem `ID` primární klíč.</span><span class="sxs-lookup"><span data-stu-id="9767a-121">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="9767a-122">Klikněte pravým tlačítkem na `Movie` tabulky **> zobrazení dat**</span><span class="sxs-lookup"><span data-stu-id="9767a-122">Right click on the `Movie` table **> View Data**</span></span>

  ![Otevřete v tabulce film kontextové nabídky](working-with-sql/_static/ssox2.png)

  ![Tabulka film otevřete zobrazení dat v tabulce](working-with-sql/_static/vd22.png)

## <a name="seed-the-database"></a><span data-ttu-id="9767a-125">Počáteční hodnoty databáze</span><span class="sxs-lookup"><span data-stu-id="9767a-125">Seed the database</span></span>

<span data-ttu-id="9767a-126">Vytvořte novou třídu s názvem `SeedData` v *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="9767a-126">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="9767a-127">Generovaného kódu nahraďte následujícím textem:</span><span class="sxs-lookup"><span data-stu-id="9767a-127">Replace the generated code with the following:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="9767a-128">Pokud jsou všechny filmy v databázi, vrátí inicializátoru počáteční hodnoty a jsou přidány žádné filmy.</span><span class="sxs-lookup"><span data-stu-id="9767a-128">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>
### <a name="add-the-seed-initializer"></a><span data-ttu-id="9767a-129">Přidat inicializátoru počáteční hodnoty</span><span class="sxs-lookup"><span data-stu-id="9767a-129">Add the seed initializer</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="9767a-130">ASP.NET základní 2.x</span><span class="sxs-lookup"><span data-stu-id="9767a-130">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x/)

<span data-ttu-id="9767a-131">Přidat inicializátoru počáteční hodnoty do `Main` metoda v *Program.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="9767a-131">Add the seed initializer to the `Main` method in the *Program.cs* file:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Program.cs?highlight=6,14-32)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="9767a-132">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="9767a-132">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x/)

<span data-ttu-id="9767a-133">Přidejte na konec inicializátoru počáteční hodnoty `Configure` metoda v *Startup.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="9767a-133">Add the seed initializer to the end of the `Configure` method in the *Startup.cs* file.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Startup.cs?highlight=9&name=snippet_seed)]

---

<span data-ttu-id="9767a-134">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="9767a-134">Test the app</span></span>

* <span data-ttu-id="9767a-135">Odstraňte všechny záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="9767a-135">Delete all the records in the DB.</span></span> <span data-ttu-id="9767a-136">Můžete provést s odstranit odkazy v prohlížeči nebo z SSOX.</span><span class="sxs-lookup"><span data-stu-id="9767a-136">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="9767a-137">Vynutit aplikace k chybě při inicializaci (volání metody v `Startup` třída), spustí metodu počáteční hodnoty.</span><span class="sxs-lookup"><span data-stu-id="9767a-137">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="9767a-138">Pokud chcete vynutit inicializace, IIS Express musí být zastavena a restartována.</span><span class="sxs-lookup"><span data-stu-id="9767a-138">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="9767a-139">Můžete to provést pomocí některé z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="9767a-139">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="9767a-140">Klikněte pravým tlačítkem na hlavním panelu ikonu IIS Express systému v oznamovací oblasti a klepněte na **ukončení** nebo **zastavit lokality**</span><span class="sxs-lookup"><span data-stu-id="9767a-140">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![Služba IIS Express ikonu na hlavním panelu](working-with-sql/_static/iisExIcon.png)

    ![Kontextové nabídky](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="9767a-143">Pokud VS byly spuštěny v režimu bez ladění, stisknutím klávesy F5 spusťte v režimu ladění</span><span class="sxs-lookup"><span data-stu-id="9767a-143">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="9767a-144">Pokud VS byly spuštěny v režimu ladění, zastavení ladicího programu a stisknutím klávesy F5</span><span class="sxs-lookup"><span data-stu-id="9767a-144">If you were running VS in debug mode, stop the debugger and press F5</span></span>

<span data-ttu-id="9767a-145">Aplikace zobrazuje dosazená data.</span><span class="sxs-lookup"><span data-stu-id="9767a-145">The app shows the seeded data.</span></span>

![Aplikace MVC film otevřete v Microsoft Edge znázorňující film data](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="9767a-147">[Předchozí](adding-model.md)
> [další](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="9767a-147">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>  
