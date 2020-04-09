---
title: Práce s SQL v ASP.NET aplikaci Core MVC
author: rick-anderson
description: Další informace o používání SQL Server LocalDB nebo SQLite v aplikaci ASP.NET Core MVC.
ms.author: riande
ms.date: 8/16/2019
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: d556f07111fb2022a1c2f1a066459566e302835d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665037"
---
# <a name="work-with-sql-in-aspnet-core"></a>Práce s SQL v ASP.NET core

::: moniker range=">= aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Objekt `MvcMovieContext` zpracovává úkol připojení k databázi a `Movie` mapování objektů do databázových záznamů. Kontext databáze je registrován s kontejnerem `ConfigureServices` [vkládání závislostí](xref:fundamentals/dependency-injection) v metodě v *souboru Startup.cs:*

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

Systém ASP.NET [Configuration](xref:fundamentals/configuration/index) core configuration `ConnectionString`přečte soubor . Pro místní vývoj získá připojovací řetězec ze souboru *appsettings.json:*

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

Systém ASP.NET [Configuration](xref:fundamentals/configuration/index) core configuration `ConnectionString`přečte soubor . Pro místní vývoj získá připojovací řetězec ze souboru *appsettings.json:*

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

Když je aplikace nasazena na testovací nebo produkční server, proměnná prostředí slouží k nastavení připojovacího řetězce na produkční SQL Server. Další informace naleznete v [tématu Konfigurace.](xref:fundamentals/configuration/index)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>Místní databáze SQL Server Express

LocalDB je odlehčená verze databázového stroje SQL Server Express, která je určena pro vývoj programů. LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje žádná složitá konfigurace. Ve výchozím nastavení databáze LocalDB vytváří soubory *MDF* v adresáři *C:/Users/{user}.*

* V nabídce **Zobrazení** otevřete **Průzkumník a průzkumník objektů serveru SQL Server** (SSOX).

  ![Nabídka Zobrazit](working-with-sql/_static/ssox.png)

* Klikněte pravým `Movie` tlačítkem myši na tabulku **> Návrhář zobrazení**

  ![Kontextová nabídka otevřená v tabulce Film](working-with-sql/_static/design.png)

  ![Otevřese tabulka filmu v Návrháři](working-with-sql/_static/dv.png)

Poznamenejte si `ID`ikonu klíče vedle aplikace . Ve výchozím nastavení ef vytvoří `ID` vlastnost s názvem primární klíč.

* Klikněte pravým `Movie` tlačítkem myši na tabulku **> Zobrazit data**

  ![Kontextová nabídka otevřená v tabulce Film](working-with-sql/_static/ssox2.png)

  ![Otevřená tabulka filmu zobrazující data tabulky](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a>Osivo databáze

Vytvořte novou `SeedData` třídu pojmenovanou ve složce *Modely.* Nahraďte generovaný kód následujícím:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

Pokud jsou nějaké filmy v DB, inicializátor osiva vrátí a žádné filmy jsou přidány.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Přidejte inicializátor osiva

Nahraďte obsah *Program.cs* následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

Otestování aplikace

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Odstraňte všechny záznamy v databázi. Můžete to udělat s odstranit odkazy v prohlížeči nebo od SSOX.
* Vynutit aplikaci inicializovat (volání metod ve `Startup` třídě), takže metoda osiva spustí. Chcete-li vynutit inicializaci, musí být iis Express zastaven a restartován. Můžete to provést s některou z následujících přístupů:

  * Klikněte pravým tlačítkem myši na ikonu systémové liště IIS Express v oznamovací oblasti a klepněte na **Ukončit** nebo **zastavit web.**

    ![Ikona systémové lišty IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](working-with-sql/_static/stopIIS.png)

    * Pokud jste spouštěli VS v režimu bez ladění, spusťte stisknutím klávesy F5 v režimu ladění.
    * Pokud jste v režimu ladění používali VS, zastavte ladicí program a stiskněte klávesu F5.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

Odstranit všechny záznamy v DB (Tak bude spuštěna metoda osiva). Zastavit a spustit aplikaci pro osílku databáze.

---

Aplikace zobrazuje osývaná data.

![Aplikace MVC Movie otevřená v microsoft edge zobrazující filmová data](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> [Předchozí](adding-model.md)
> [další](controller-methods-views.md)
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Objekt `MvcMovieContext` zpracovává úkol připojení k databázi a `Movie` mapování objektů do databázových záznamů. Kontext databáze je registrován s kontejnerem `ConfigureServices` [vkládání závislostí](xref:fundamentals/dependency-injection) v metodě v *souboru Startup.cs:*

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

Systém ASP.NET [Configuration](xref:fundamentals/configuration/index) core configuration `ConnectionString`přečte soubor . Pro místní vývoj získá připojovací řetězec ze souboru *appsettings.json:*

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Systém ASP.NET [Configuration](xref:fundamentals/configuration/index) core configuration `ConnectionString`přečte soubor . Pro místní vývoj získá připojovací řetězec ze souboru *appsettings.json:*

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

Při nasazení aplikace na testovací nebo produkční server můžete použít proměnnou prostředí nebo jiný přístup k nastavení připojovacího řetězce na skutečný SQL Server. Další informace naleznete v [tématu Konfigurace.](xref:fundamentals/configuration/index)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>Místní databáze SQL Server Express

LocalDB je odlehčená verze databázového stroje SQL Server Express, která je určena pro vývoj programů. LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje žádná složitá konfigurace. Ve výchozím nastavení databáze LocalDB vytváří soubory *MDF* v adresáři *C:/Users/{user}.*

* V nabídce **Zobrazení** otevřete **Průzkumník a průzkumník objektů serveru SQL Server** (SSOX).

  ![Nabídka Zobrazit](working-with-sql/_static/ssox.png)

* Klikněte pravým `Movie` tlačítkem myši na tabulku **> Návrhář zobrazení**

  ![Kontextová nabídka otevřená v tabulce Film](working-with-sql/_static/design.png)

  ![Otevřese tabulka filmu v Návrháři](working-with-sql/_static/dv.png)

Poznamenejte si `ID`ikonu klíče vedle aplikace . Ve výchozím nastavení ef vytvoří `ID` vlastnost s názvem primární klíč.

* Klikněte pravým `Movie` tlačítkem myši na tabulku **> Zobrazit data**

  ![Kontextová nabídka otevřená v tabulce Film](working-with-sql/_static/ssox2.png)

  ![Otevřená tabulka filmu zobrazující data tabulky](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a>Osivo databáze

Vytvořte novou `SeedData` třídu pojmenovanou ve složce *Modely.* Nahraďte generovaný kód následujícím:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

Pokud jsou nějaké filmy v DB, inicializátor osiva vrátí a žádné filmy jsou přidány.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Přidejte inicializátor osiva

Nahraďte obsah *Program.cs* následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

Otestování aplikace

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Odstraňte všechny záznamy v databázi. Můžete to udělat s odstranit odkazy v prohlížeči nebo od SSOX.
* Vynutit aplikaci inicializovat (volání metod ve `Startup` třídě), takže metoda osiva spustí. Chcete-li vynutit inicializaci, musí být iis Express zastaven a restartován. Můžete to provést s některou z následujících přístupů:

  * Klikněte pravým tlačítkem myši na ikonu systémové liště IIS Express v oznamovací oblasti a klepněte na **Ukončit** nebo **zastavit web.**

    ![Ikona systémové lišty IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](working-with-sql/_static/stopIIS.png)

    * Pokud jste spouštěli VS v režimu bez ladění, spusťte stisknutím klávesy F5 v režimu ladění.
    * Pokud jste v režimu ladění používali VS, zastavte ladicí program a stiskněte klávesu F5.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

Odstranit všechny záznamy v DB (Tak bude spuštěna metoda osiva). Zastavit a spustit aplikaci pro osílku databáze.

---

Aplikace zobrazuje osývaná data.

![Aplikace MVC Movie otevřená v microsoft edge zobrazující filmová data](working-with-sql/_static/m55_mac.png)

> [!div class="step-by-step"]
> [Předchozí](adding-model.md)
> [další](controller-methods-views.md)

::: moniker-end
