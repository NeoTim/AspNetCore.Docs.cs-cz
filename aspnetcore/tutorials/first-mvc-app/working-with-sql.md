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
# <a name="work-with-sql-in-aspnet-core"></a>Práce s SQL v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

Objekt `MvcMovieContext` zpracovává úlohu připojení k databázi a mapování objektů `Movie` na záznamy databáze. Kontext databáze je zaregistrován s kontejnerem [vkládání závislostí](xref:fundamentals/dependency-injection) v metodě `ConfigureServices` v souboru *Startup.cs* :

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString`. Pro místní vývoj načte připojovací řetězec ze souboru *appSettings. JSON* :

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString`. Pro místní vývoj načte připojovací řetězec ze souboru *appSettings. JSON* :

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

Při nasazení aplikace do testovacího nebo provozního serveru lze použít proměnnou prostředí k nastavení připojovacího řetězce na produkční SQL Server. Další informace najdete v části [Konfigurace](xref:fundamentals/configuration/index) .

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB je zjednodušená verze databázového stroje SQL Server Express, který je zaměřený na vývoj programu. LocalDB spustí na vyžádání a běží v uživatelském režimu, takže není bez složité konfigurace. Ve výchozím nastavení vytvoří databáze LocalDB soubory *. mdf* v adresáři *C:/Users/{User}* .

* V nabídce **Zobrazit** otevřete **Průzkumník objektů systému SQL Server** (SSOX).

  ![Nabídka Zobrazit](working-with-sql/_static/ssox.png)

* Klikněte pravým tlačítkem na `Movie` tabulce **> návrháře zobrazení**

  ![Kontextová nabídka otevřená v tabulce videí](working-with-sql/_static/design.png)

  ![Tabulka videí otevřená v Návrháři](working-with-sql/_static/dv.png)

Poznamenejte si ikonu klíče vedle `ID`. Ve výchozím nastavení objekt EF vytvoří vlastnost s názvem `ID` primární klíč.

* Klikněte pravým tlačítkem na `Movie` tabulce **> zobrazit data.**

  ![Kontextová nabídka otevřená v tabulce videí](working-with-sql/_static/ssox2.png)

  ![Tabulka videa otevřená zobrazující data tabulky](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a>Přidání dat do databáze

Vytvořte novou třídu s názvem `SeedData` ve složce *modely* . Vygenerovaný kód nahraďte následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

Pokud databáze obsahuje nějaké filmy, inicializátor počáteční hodnoty se vrátí a nepřidá se žádné filmy.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Přidat inicializátor počáteční hodnoty

Obsah *program.cs* nahraďte následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

Testování aplikace

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Odstraňte všechny záznamy v databázi. Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z SSOX.
* Vynuťte inicializaci aplikace (voláním metod ve třídě `Startup`), takže se metoda počáteční hodnoty spustí. Chcete-li vynutit inicializaci, IIS Express musí být zastavena a restartována. Můžete to provést s některým z následujících přístupů:

  * Klikněte pravým tlačítkem myši na ikonu IIS Express systému v oznamovací oblasti a klepněte na **konec** nebo **zastavení webu** .

    ![IIS Express ikona na hlavním panelu systému](working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](working-with-sql/_static/stopIIS.png)

    * Pokud jste spustili VS v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění.
    * Pokud jste spustili VS v režimu ladění, ukončete ladicí program a stiskněte klávesu F5.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí). Zastavením a spuštěním aplikace dosadíte databázi.

---

Aplikace zobrazuje dosazený data.

![Filmová aplikace MVC otevřená v Microsoft Edge ukazující data videa](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> [Předchozí](adding-model.md)
> [Další](controller-methods-views.md)
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

Objekt `MvcMovieContext` zpracovává úlohu připojení k databázi a mapování objektů `Movie` na záznamy databáze. Kontext databáze je zaregistrován s kontejnerem [vkládání závislostí](xref:fundamentals/dependency-injection) v metodě `ConfigureServices` v souboru *Startup.cs* :

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString`. Pro místní vývoj načte připojovací řetězec ze souboru *appSettings. JSON* :

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString`. Pro místní vývoj načte připojovací řetězec ze souboru *appSettings. JSON* :

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

Když nasadíte aplikaci na testovací nebo provozní server, můžete k nastavení připojovacího řetězce na reálné SQL Server použít proměnnou prostředí nebo jiný přístup. Další informace najdete v části [Konfigurace](xref:fundamentals/configuration/index) .

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB je zjednodušená verze databázového stroje SQL Server Express, který je zaměřený na vývoj programu. LocalDB spustí na vyžádání a běží v uživatelském režimu, takže není bez složité konfigurace. Ve výchozím nastavení vytvoří databáze LocalDB soubory *. mdf* v adresáři *C:/Users/{User}* .

* V nabídce **Zobrazit** otevřete **Průzkumník objektů systému SQL Server** (SSOX).

  ![Nabídka Zobrazit](working-with-sql/_static/ssox.png)

* Klikněte pravým tlačítkem na `Movie` tabulce **> návrháře zobrazení**

  ![Kontextová nabídka otevřená v tabulce videí](working-with-sql/_static/design.png)

  ![Tabulka videí otevřená v Návrháři](working-with-sql/_static/dv.png)

Poznamenejte si ikonu klíče vedle `ID`. Ve výchozím nastavení objekt EF vytvoří vlastnost s názvem `ID` primární klíč.

* Klikněte pravým tlačítkem na `Movie` tabulce **> zobrazit data.**

  ![Kontextová nabídka otevřená v tabulce videí](working-with-sql/_static/ssox2.png)

  ![Tabulka videa otevřená zobrazující data tabulky](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a>Přidání dat do databáze

Vytvořte novou třídu s názvem `SeedData` ve složce *modely* . Vygenerovaný kód nahraďte následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

Pokud databáze obsahuje nějaké filmy, inicializátor počáteční hodnoty se vrátí a nepřidá se žádné filmy.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Přidat inicializátor počáteční hodnoty

Obsah *program.cs* nahraďte následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

Testování aplikace

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Odstraňte všechny záznamy v databázi. Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z SSOX.
* Vynuťte inicializaci aplikace (voláním metod ve třídě `Startup`), takže se metoda počáteční hodnoty spustí. Chcete-li vynutit inicializaci, IIS Express musí být zastavena a restartována. Můžete to provést s některým z následujících přístupů:

  * Klikněte pravým tlačítkem myši na ikonu IIS Express systému v oznamovací oblasti a klepněte na **konec** nebo **zastavení webu** .

    ![IIS Express ikona na hlavním panelu systému](working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](working-with-sql/_static/stopIIS.png)

    * Pokud jste spustili VS v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění.
    * Pokud jste spustili VS v režimu ladění, ukončete ladicí program a stiskněte klávesu F5.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí). Zastavením a spuštěním aplikace dosadíte databázi.

---

Aplikace zobrazuje dosazený data.

![Filmová aplikace MVC otevřená v Microsoft Edge ukazující data videa](working-with-sql/_static/m55_mac.png)

> [!div class="step-by-step"]
> [Předchozí](adding-model.md)
> [Další](controller-methods-views.md)

::: moniker-end
