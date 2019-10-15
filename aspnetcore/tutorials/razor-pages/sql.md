---
title: Práce s databází a ASP.NET Core
author: rick-anderson
description: Vysvětluje práci s databází a ASP.NET Core.
ms.author: riande
ms.date: 7/22/2019
uid: tutorials/razor-pages/sql
ms.openlocfilehash: b5acb573f8fa39e5300ecdb359113d8697d78934
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72334233"
---
# <a name="work-with-a-database-and-aspnet-core"></a>Práce s databází a ASP.NET Core

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Jan Audette](https://twitter.com/joeaudette)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Objekt `RazorPagesMovieContext` zpracovává úlohu připojení k databázi a mapování objektů `Movie` na záznamy databáze. Kontext databáze je zaregistrován s kontejnerem [vkládání závislostí](xref:fundamentals/dependency-injection) v metodě `ConfigureServices` v *Startup.cs*:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString`. Pro místní vývoj získá připojovací řetězec ze souboru *appSettings. JSON* .

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Hodnota názvu pro databázi (`Database={Database name}`) bude pro vygenerovaný kód odlišná. Hodnota name je libovolná.

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Při nasazení aplikace do testovacího nebo provozního serveru lze použít proměnnou prostředí k nastavení připojovacího řetězce na skutečný databázový server. Další informace najdete v části [Konfigurace](xref:fundamentals/configuration/index) .

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB je zjednodušená verze databázového stroje SQL Server Express, který je zaměřený na vývoj programu. LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje složitá konfigurace. Ve výchozím nastavení vytvoří databáze LocalDB soubory s hodnotou `*.mdf` v adresáři `C:\Users\<user>\`.

<a name="ssox"></a>
* V nabídce **Zobrazit** otevřete **Průzkumník objektů systému SQL Server** (SSOX).

  ![Nabídka Zobrazit](sql/_static/ssox.png)

* Klikněte pravým tlačítkem na tabulku `Movie` a vyberte **Zobrazit návrháře**:

  ![Kontextové nabídky otevřené v tabulce videí](sql/_static/design.png)

  ![Tabulky filmů otevřené v Návrháři](sql/_static/dv.png)

Poznamenejte si ikonu klíče vedle `ID`. Ve výchozím nastavení EF pro primární klíč vytvoří vlastnost s názvem `ID`.

* Klikněte pravým tlačítkem na tabulku `Movie` a vyberte **Zobrazit data**:

  ![Tabulka videa otevřená zobrazující data tabulky](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Dosazení databáze

Vytvořte novou třídu s názvem `SeedData` ve složce *modely* pomocí následujícího kódu:

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

V *program.cs*upravte metodu `Main` a proveďte následující:

* Získá instanci kontextu databáze z kontejneru vkládání závislostí.
* Zavolejte metodu počáteční hodnoty a předejte jí kontext.
* Vyřazení kontextu, když se dokončí metoda počáteční hodnoty.

Následující kód ukazuje aktualizovaný soubor *program.cs* .

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

Pokud se nespustí `Update-Database`, dojde k následující výjimce:

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>Testování aplikace

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Odstraňte všechny záznamy v databázi. Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z [SSOX](xref:tutorials/razor-pages/new-field#ssox) .
* Vynuťte inicializaci aplikace (voláním metod ve třídě `Startup`), takže se metoda počáteční hodnoty spustí. Chcete-li vynutit inicializaci, IIS Express musí být zastavena a restartována. Můžete to provést s některým z následujících přístupů:

  * Klikněte pravým tlačítkem myši na ikonu IIS Express systému v oznamovací oblasti a klepněte na **konec** nebo **zastavení webu**:

    ![IIS Express ikona na hlavním panelu systému](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](sql/_static/stopIIS.png)

    * Pokud jste spustili VS v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění.
    * Pokud jste spustili VS v režimu ladění, ukončete ladicí program a stiskněte klávesu F5.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí). Zastavením a spuštěním aplikace dosadíte databázi.

Aplikace zobrazuje dosazený data.

---

V dalším kurzu dojde k vylepšení prezentace dat.

## <a name="additional-resources"></a>Další zdroje

> [!div class="step-by-step"]
> [Předchozí: vygenerované Razor Pages](xref:tutorials/razor-pages/page)
> [Next: aktualizace stránek](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Objekt `RazorPagesMovieContext` zpracovává úlohu připojení k databázi a mapování objektů `Movie` na záznamy databáze. Kontext databáze je zaregistrován s kontejnerem [vkládání závislostí](xref:fundamentals/dependency-injection) v metodě `ConfigureServices` v *Startup.cs*:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

Další informace o metodách používaných v `ConfigureServices` naleznete v tématu:

* [Podpora obecné nařízení o ochraně osobních údajů v rámci EU (GDPR) v ASP.NET Core](xref:security/gdpr) pro `CookiePolicyOptions`.
* [SetCompatibilityVersion](xref:mvc/compatibility-version)

[Konfigurační](xref:fundamentals/configuration/index) systém ASP.NET Core přečte `ConnectionString`. Pro místní vývoj získá připojovací řetězec ze souboru *appSettings. JSON* .

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Hodnota názvu pro databázi (`Database={Database name}`) bude pro vygenerovaný kód odlišná. Hodnota name je libovolná.

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Při nasazení aplikace do testovacího nebo provozního serveru lze použít proměnnou prostředí k nastavení připojovacího řetězce na skutečný databázový server. Další informace najdete v části [Konfigurace](xref:fundamentals/configuration/index) .

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB je zjednodušená verze databázového stroje SQL Server Express, který je zaměřený na vývoj programu. LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje složitá konfigurace. Ve výchozím nastavení vytvoří databáze LocalDB soubory s hodnotou `*.mdf` v adresáři `C:/Users/<user/>`.

<a name="ssox"></a>
* V nabídce **Zobrazit** otevřete **Průzkumník objektů systému SQL Server** (SSOX).

  ![Nabídka Zobrazit](sql/_static/ssox.png)

* Klikněte pravým tlačítkem na tabulku `Movie` a vyberte **Zobrazit návrháře**:

  ![Kontextová nabídka otevřená v tabulce videí](sql/_static/design.png)

  ![Tabulka videí otevřená v Návrháři](sql/_static/dv.png)

Poznamenejte si ikonu klíče vedle `ID`. Ve výchozím nastavení EF pro primární klíč vytvoří vlastnost s názvem `ID`.

* Klikněte pravým tlačítkem na tabulku `Movie` a vyberte **Zobrazit data**:

  ![Tabulka videa otevřená zobrazující data tabulky](sql/_static/vd22.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Dosazení databáze

Vytvořte novou třídu s názvem `SeedData` ve složce *modely* pomocí následujícího kódu:

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

V *program.cs*upravte metodu `Main` a proveďte následující:

* Získá instanci kontextu databáze z kontejneru vkládání závislostí.
* Zavolejte metodu počáteční hodnoty a předejte jí kontext.
* Vyřazení kontextu, když se dokončí metoda počáteční hodnoty.

Následující kód ukazuje aktualizovaný soubor *program.cs* .

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

Produkční aplikace by nevolala `Database.Migrate`. Přidá se k předchozímu kódu, aby se zabránilo následující výjimce při `Update-Database` nebyl spuštěn:

SqlException: Nelze otevřít databázi "RazorPagesMovieContext-21" požadovanou pro přihlášení. Přihlášení se nezdařilo.
Přihlášení uživatele "uživatelské jméno" se nezdařilo.

### <a name="test-the-app"></a>Testování aplikace

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Odstraňte všechny záznamy v databázi. Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z [SSOX](xref:tutorials/razor-pages/new-field#ssox) .
* Vynuťte inicializaci aplikace (voláním metod ve třídě `Startup`), takže se metoda počáteční hodnoty spustí. Chcete-li vynutit inicializaci, IIS Express musí být zastavena a restartována. Můžete to provést s některým z následujících přístupů:

  * V oznamovací oblasti klikněte pravým tlačítkem na ikonu na hlavním panelu IIS Express systému a klepněte na **konec** nebo **zastavení webu**:

    ![IIS Express ikona na hlavním panelu systému](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](sql/_static/stopIIS.png)

    * Pokud jste spustili VS v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění.
    * Pokud jste spustili VS v režimu ladění, ukončete ladicí program a stiskněte klávesu F5.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí). Zastavením a spuštěním aplikace dosadíte databázi.

Aplikace zobrazuje dosazený data.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Odstraní všechny záznamy v databázi (takže se metoda počáteční hodnoty spustí). Zastavením a spuštěním aplikace dosadíte databázi.

Aplikace zobrazuje dosazený data.

---

Aplikace zobrazuje dosazený data:

![Aplikace Movie otevřená v Chrome zobrazující data videa](sql/_static/m55.png)

V dalším kurzu se vyčistí prezentace dat.

## <a name="additional-resources"></a>Další zdroje

* [Verze YouTube tohoto kurzu](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> [Předchozí: vygenerované Razor Pages](xref:tutorials/razor-pages/page)
> [Next: aktualizace stránek](xref:tutorials/razor-pages/da1)

::: moniker-end
