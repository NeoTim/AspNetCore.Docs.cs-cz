---
title: 4. část s databází a ASP.NET Core
author: rick-anderson
description: Část 4 série kurzů na Razor stránkách.
ms.author: riande
ms.date: 7/22/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 21ae2ed4e91a0b3e52b1cdad1f4f4686c50614ba
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652984"
---
# <a name="part-4-with-a-database-and-aspnet-core"></a>4. část s databází a ASP.NET Core

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Jan Audette](https://twitter.com/joeaudette)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

`RazorPagesMovieContext`Objekt zpracovává úlohu připojení k databázi a mapování `Movie` objektů na záznamy databáze. Kontext databáze je zaregistrován s kontejnerem [Injektáže závislosti](xref:fundamentals/dependency-injection) v `ConfigureServices` metodě v *Startup.cs*:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString` . Pro místní vývoj získá připojovací řetězec ze souboru *appSettings. JSON* .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Hodnota názvu pro databázi ( `Database={Database name}` ) bude pro vygenerovaný kód odlišná. Hodnota name je libovolná.

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Při nasazení aplikace do testovacího nebo provozního serveru lze použít proměnnou prostředí k nastavení připojovacího řetězce na skutečný databázový server. Další informace najdete v části [Konfigurace](xref:fundamentals/configuration/index) .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB je zjednodušená verze databázového stroje SQL Server Express, který je zaměřený na vývoj programu. LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje složitá konfigurace. Ve výchozím nastavení vytvoří databáze LocalDB `*.mdf` soubory v `C:\Users\<user>\` adresáři.

<a name="ssox"></a>
* V nabídce **Zobrazit** otevřete **Průzkumník objektů systému SQL Server** (SSOX).

  ![Nabídka Zobrazit](sql/_static/ssox.png)

* Klikněte pravým tlačítkem na `Movie` tabulku a vyberte **Zobrazit návrháře**:

  ![Kontextové nabídky otevřené v tabulce videí](sql/_static/design.png)

  ![Tabulky filmů otevřené v Návrháři](sql/_static/dv.png)

Poznamenejte si ikonu klíče vedle `ID` . Ve výchozím nastavení EF vytvoří vlastnost s názvem `ID` pro primární klíč.

* Klikněte pravým tlačítkem na `Movie` tabulku a vyberte **Zobrazit data**:

  ![Tabulka videa otevřená zobrazující data tabulky](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Dosazení databáze

Vytvořte novou třídu s názvem `SeedData` ve složce *modely* s následujícím kódem:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

Pokud databáze obsahuje nějaké filmy, inicializátor počáteční hodnoty se vrátí a nepřidá se žádné filmy.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Přidat inicializátor počáteční hodnoty

V *program.cs*upravte `Main` metodu a proveďte následující:

* Získá instanci kontextu databáze z kontejneru vkládání závislostí.
* Zavolejte metodu počáteční hodnoty a předejte jí kontext.
* Vyřazení kontextu, když se dokončí metoda počáteční hodnoty.

Následující kód ukazuje aktualizovaný soubor *program.cs* .

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

K následující výjimce dojde v případě, že nebyla `Update-Database` spuštěna:

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>Otestování aplikace

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Odstraňte všechny záznamy v databázi. Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z [SSOX](xref:tutorials/razor-pages/new-field#ssox) .
* Vynuťte inicializaci aplikace (volání metod ve `Startup` třídě), takže se metoda počáteční hodnoty spustí. Chcete-li vynutit inicializaci, IIS Express musí být zastavena a restartována. Můžete to provést s některým z následujících přístupů:

  * Klikněte pravým tlačítkem myši na ikonu IIS Express systému v oznamovací oblasti a klepněte na **konec** nebo **zastavení webu**:

    ![IIS Express ikona na hlavním panelu systému](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](sql/_static/stopIIS.png)

    * Pokud jste spustili VS v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění.
    * Pokud jste spustili VS v režimu ladění, ukončete ladicí program a stiskněte klávesu F5.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí). Zastavením a spuštěním aplikace dosadíte databázi.

Aplikace zobrazuje dosazený data.

---

V dalším kurzu dojde k vylepšení prezentace dat.

## <a name="additional-resources"></a>Další zdroje

> [!div class="step-by-step"]
> [Předchozí: vygenerované uživatelské rozhraní Razor Stránky](xref:tutorials/razor-pages/page) 
>  [Další: aktualizace stránek](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

`RazorPagesMovieContext`Objekt zpracovává úlohu připojení k databázi a mapování `Movie` objektů na záznamy databáze. Kontext databáze je zaregistrován s kontejnerem [Injektáže závislosti](xref:fundamentals/dependency-injection) v `ConfigureServices` metodě v *Startup.cs*:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

Další informace o metodách používaných v nástroji `ConfigureServices` naleznete v tématu:

* [Podpora GDPR (EU obecné nařízení o ochraně osobních údajů) v ASP.NET Core](xref:security/gdpr) pro `CookiePolicyOptions` .
* [SetCompatibilityVersion](xref:mvc/compatibility-version)

[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString` . Pro místní vývoj získá připojovací řetězec ze souboru *appSettings. JSON* .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Hodnota názvu pro databázi ( `Database={Database name}` ) bude pro vygenerovaný kód odlišná. Hodnota name je libovolná.

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Při nasazení aplikace do testovacího nebo provozního serveru lze použít proměnnou prostředí k nastavení připojovacího řetězce na skutečný databázový server. Další informace najdete v části [Konfigurace](xref:fundamentals/configuration/index) .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB je zjednodušená verze databázového stroje SQL Server Express, který je zaměřený na vývoj programu. LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje složitá konfigurace. Ve výchozím nastavení vytvoří databáze LocalDB `*.mdf` soubory v `C:/Users/<user/>` adresáři.

<a name="ssox"></a>
* V nabídce **Zobrazit** otevřete **Průzkumník objektů systému SQL Server** (SSOX).

  ![Nabídka Zobrazit](sql/_static/ssox.png)

* Klikněte pravým tlačítkem na `Movie` tabulku a vyberte **Zobrazit návrháře**:

  ![Kontextová nabídka otevřená v tabulce videí](sql/_static/design.png)

  ![Tabulka videí otevřená v Návrháři](sql/_static/dv.png)

Poznamenejte si ikonu klíče vedle `ID` . Ve výchozím nastavení EF vytvoří vlastnost s názvem `ID` pro primární klíč.

* Klikněte pravým tlačítkem na `Movie` tabulku a vyberte **Zobrazit data**:

  ![Tabulka videa otevřená zobrazující data tabulky](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Dosazení databáze

Vytvořte novou třídu s názvem `SeedData` ve složce *modely* s následujícím kódem:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

Pokud databáze obsahuje nějaké filmy, inicializátor počáteční hodnoty se vrátí a nepřidá se žádné filmy.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Přidat inicializátor počáteční hodnoty

V *program.cs*upravte `Main` metodu a proveďte následující:

* Získá instanci kontextu databáze z kontejneru vkládání závislostí.
* Zavolejte metodu počáteční hodnoty a předejte jí kontext.
* Vyřazení kontextu, když se dokončí metoda počáteční hodnoty.

Následující kód ukazuje aktualizovaný soubor *program.cs* .

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

Produkční aplikace by nevolala `Database.Migrate` . Přidá se k předchozímu kódu, aby se zabránilo následující výjimce, pokud nebyla `Update-Database` spuštěna:

SqlException: Nelze otevřít databázi "RazorPagesMovieContext-21" požadovanou pro přihlášení. Přihlášení se nezdařilo.
Přihlášení uživatele "uživatelské jméno" se nezdařilo.

### <a name="test-the-app"></a>Otestování aplikace

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Odstraňte všechny záznamy v databázi. Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z [SSOX](xref:tutorials/razor-pages/new-field#ssox) .
* Vynuťte inicializaci aplikace (volání metod ve `Startup` třídě), takže se metoda počáteční hodnoty spustí. Chcete-li vynutit inicializaci, IIS Express musí být zastavena a restartována. Můžete to provést s některým z následujících přístupů:

  * V oznamovací oblasti klikněte pravým tlačítkem na ikonu na hlavním panelu IIS Express systému a klepněte na **konec** nebo **zastavení webu**:

    ![IIS Express ikona na hlavním panelu systému](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](sql/_static/stopIIS.png)

    * Pokud jste spustili VS v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění.
    * Pokud jste spustili VS v režimu ladění, ukončete ladicí program a stiskněte klávesu F5.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí). Zastavením a spuštěním aplikace dosadíte databázi.

Aplikace zobrazuje dosazený data.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí). Zastavením a spuštěním aplikace dosadíte databázi.

Aplikace zobrazuje dosazený data.

---

Aplikace zobrazuje dosazený data:

![Aplikace Movie otevřená v Chrome zobrazující data videa](sql/_static/m55.png)

V dalším kurzu se vyčistí prezentace dat.

## <a name="additional-resources"></a>Další zdroje

* [Verze YouTube tohoto kurzu](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> [Předchozí: vygenerované uživatelské rozhraní Razor Stránky](xref:tutorials/razor-pages/page) 
>  [Další: aktualizace stránek](xref:tutorials/razor-pages/da1)

::: moniker-end
