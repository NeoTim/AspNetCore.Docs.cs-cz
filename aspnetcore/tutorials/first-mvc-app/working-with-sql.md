---
title: Práce s SQL v aplikaci MVC ASP.NET Core
author: rick-anderson
description: Další informace o použití SQL Server LocalDB nebo SQLite v aplikaci ASP.NET Core MVC.
ms.author: riande
ms.date: 03/07/2017
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: 983742276f3519b540cd62e4ada6eb5189650aa8
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265218"
---
# <a name="work-with-sql-in-aspnet-core"></a>Práce s SQL v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

`MvcMovieContext` Objekt zpracovává úlohu s připojením k databázi a mapování `Movie` objekty se záznamy v databázi. Kontext databáze je zaregistrován [injektáž závislostí](xref:fundamentals/dependency-injection) kontejneru v `ConfigureServices` metoda ve *Startup.cs* souboru:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

ASP.NET Core [konfigurace](xref:fundamentals/configuration/index) systému čtení `ConnectionString`. Pro místní vývoj, získá připojovací řetězec z *appsettings.json* souboru:

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

ASP.NET Core [konfigurace](xref:fundamentals/configuration/index) systému čtení `ConnectionString`. Pro místní vývoj, získá připojovací řetězec z *appsettings.json* souboru:

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

Když nasadíte aplikaci na testovacím nebo produkčním serveru, můžete použít proměnné prostředí nebo jiné přístup se nastavit připojovací řetězec na skutečný SQL Server. Zobrazit [konfigurace](xref:fundamentals/configuration/index) Další informace.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB je Odlehčená verze SQL serveru Express databázového stroje, která je určená pro vývoj v programu. LocalDB spustí na vyžádání a běží v uživatelském režimu, takže není bez složité konfigurace. Ve výchozím nastavení, vytvoří databázi LocalDB *.mdf* soubory *C:/uživatele / {user}* adresáře.

* Z **zobrazení** nabídce otevřete **Průzkumník objektů systému SQL Server** (SSOX).

  ![Nabídka Zobrazit](working-with-sql/_static/ssox.png)

* Klikněte pravým tlačítkem na `Movie` tabulky **> Návrhář zobrazení**

  ![Kontextová nabídka otevřít na tabulky Movie](working-with-sql/_static/design.png)

  ![Otevřít v Návrháři tabulky Movie](working-with-sql/_static/dv.png)

Poznámka: na ikonu klíče vedle `ID`. Ve výchozím nastavení, budou EF vlastnost s názvem `ID` primární klíč.

* Klikněte pravým tlačítkem na `Movie` tabulky **> Data zobrazení**

  ![Kontextová nabídka otevřít na tabulky Movie](working-with-sql/_static/ssox2.png)

  ![Otevřít zobrazení tabulky dat tabulky Movie](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a>Přidání dat do databáze

Vytvořte novou třídu s názvem `SeedData` v *modely* složky. Generovaného kódu nahraďte následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

Pokud jsou všechny filmy v databázi, vrátí inicializátoru pro dosazení hodnot a jsou přidány žádné video.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Přidat inicializační výraz počáteční hodnoty

Nahraďte obsah *Program.cs* následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

Testování aplikace

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Odstraníte všechny záznamy z databáze. Můžete to provést s odstranit odkazy v prohlížeči nebo z SSOX.
* Se aplikace inicializuje (volat metody ve `Startup` třídy), spustí seed – metoda. Pokud chcete vynutit inicializace, služba IIS Express musí zastavit, restartovat. Provést s některou z následujících postupů:

  * Klikněte pravým tlačítkem na službu IIS Express systému na hlavním panelu ikonu v oznamovací oblasti a klepněte na **ukončovací** nebo **zastavení webu**

    ![Služba IIS Express ikonu na hlavním panelu](working-with-sql/_static/iisExIcon.png)

    ![Kontextové nabídky](working-with-sql/_static/stopIIS.png)

    * Pokud VS byly spuštěny v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění
    * Pokud jste používali VS v režimu ladění zastavení ladicího programu a stisknutím klávesy F5

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

Všechny záznamy z databáze odstraníte, (aby se spustí metodu počáteční hodnota). Zastavení a spuštění aplikace k přidání dat do databáze.

---

Aplikace zobrazí dosazená data.

![Aplikace MVC Movie otevřete v Microsoft Edge zobrazující data o filmech](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> [Předchozí](adding-model.md)
> [další](controller-methods-views.md)
