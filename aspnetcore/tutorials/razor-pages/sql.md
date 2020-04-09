---
title: Práce s databází a ASP.NET jádra
author: rick-anderson
description: Vysvětluje práci s databází a ASP.NET Core.
ms.author: riande
ms.date: 7/22/2019
uid: tutorials/razor-pages/sql
ms.openlocfilehash: b5acb573f8fa39e5300ecdb359113d8697d78934
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664337"
---
# <a name="work-with-a-database-and-aspnet-core"></a>Práce s databází a ASP.NET jádra

[Podle Rick Anderson](https://twitter.com/RickAndMSFT) a Joe [Audette](https://twitter.com/joeaudette)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Objekt `RazorPagesMovieContext` zpracovává úkol připojení k databázi a `Movie` mapování objektů do databázových záznamů. Kontext databáze je registrován s kontejnerem `ConfigureServices` vkládání [závislostí](xref:fundamentals/dependency-injection) v metodě v *Startup.cs*:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

Systém ASP.NET [Configuration](xref:fundamentals/configuration/index) core configuration `ConnectionString`přečte soubor . Pro místní vývoj získá připojovací řetězec ze souboru *appsettings.json.*

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Hodnota názvu databáze (`Database={Database name}`) se bude lišit pro generovaný kód. Hodnota názvu je libovolná.

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Když je aplikace nasazena na testovací nebo produkční server, proměnnou prostředí lze použít k nastavení připojovacího řetězce na reálný databázový server. Další informace naleznete v [tématu Konfigurace.](xref:fundamentals/configuration/index)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>Místní databáze SQL Server Express

LocalDB je odlehčená verze databázového stroje SQL Server Express, která je určena pro vývoj programů. LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje žádná složitá konfigurace. Ve výchozím nastavení databáze `*.mdf` LocalDB `C:\Users\<user>\` vytváří soubory v adresáři.

<a name="ssox"></a>
* V nabídce **Zobrazení** otevřete **Průzkumník a průzkumník objektů serveru SQL Server** (SSOX).

  ![Nabídka Zobrazit](sql/_static/ssox.png)

* Klikněte pravým `Movie` tlačítkem myši na tabulku a vyberte **Zobrazit Návrhář**:

  ![Kontextové nabídky otevřené v tabulce Film](sql/_static/design.png)

  ![Filmové tabulky otevřené v Návrháři](sql/_static/dv.png)

Poznamenejte si `ID`ikonu klíče vedle aplikace . Ve výchozím nastavení EF `ID` vytvoří vlastnost pojmenovanou pro primární klíč.

* Klikněte pravým `Movie` tlačítkem myši na tabulku a vyberte **zobrazit data**:

  ![Otevřená tabulka filmu zobrazující data tabulky](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Osivo databáze

Vytvořte novou `SeedData` třídu pojmenovanou ve složce *Modely* s následujícím kódem:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

Pokud jsou nějaké filmy v DB, inicializátor osiva vrátí a žádné filmy jsou přidány.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Přidejte inicializátor osiva

V *Program.cs*upravte metodu `Main` takto:

* Získejte kontextové instance DB z kontejneru vkládání závislostí.
* Volání metody osiva, předávání k ní kontextu.
* Po dokončení metody osiva zlikvidujte kontext.

Následující kód zobrazuje aktualizovaný *soubor Program.cs.*

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

Následující výjimka nastane, když `Update-Database` nebylspuštěn:

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>Otestování aplikace

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Odstraňte všechny záznamy v databázi. Můžete to udělat s odstranit odkazy v prohlížeči nebo od [SSOX](xref:tutorials/razor-pages/new-field#ssox)
* Vynutit aplikaci inicializovat (volání metod ve `Startup` třídě), takže metoda osiva spustí. Chcete-li vynutit inicializaci, musí být iis Express zastaven a restartován. Můžete to provést s některou z následujících přístupů:

  * Klikněte pravým tlačítkem myši na ikonu na hlavním panelu systému IIS Express v oznamovací oblasti a klepněte na **Ukončit** nebo **zastavit web**:

    ![Ikona systémové lišty IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](sql/_static/stopIIS.png)

    * Pokud jste používali VS v režimu bez ladění, stiskněte klávesu F5 a spusťte v režimu ladění.
    * Pokud jste v režimu ladění používali VS, zastavte ladicí program a stiskněte klávesu F5.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

Odstranit všechny záznamy v DB (Tak bude spuštěna metoda osiva). Zastavit a spustit aplikaci pro osílku databáze.

Aplikace zobrazuje osývaná data.

---

Další kurz zlepší prezentaci dat.

## <a name="additional-resources"></a>Další zdroje

> [!div class="step-by-step"]
> [Předchozí: Scaffolded Razor Stránky](xref:tutorials/razor-pages/page)
> [Další: Aktualizace stránek](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Objekt `RazorPagesMovieContext` zpracovává úkol připojení k databázi a `Movie` mapování objektů do databázových záznamů. Kontext databáze je registrován s kontejnerem `ConfigureServices` vkládání [závislostí](xref:fundamentals/dependency-injection) v metodě v *Startup.cs*:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

Další informace o metodách `ConfigureServices`používaných v písmenech a), r.

* [Obecné nařízení EU o ochraně osobních údajů (GDPR) v ASP.NET jádru](xref:security/gdpr) pro `CookiePolicyOptions`.
* [SetCompatibilityVersion](xref:mvc/compatibility-version)

Systém ASP.NET [Configuration](xref:fundamentals/configuration/index) core configuration `ConnectionString`přečte soubor . Pro místní vývoj získá připojovací řetězec ze souboru *appsettings.json.*

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Hodnota názvu databáze (`Database={Database name}`) se bude lišit pro generovaný kód. Hodnota názvu je libovolná.

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Když je aplikace nasazena na testovací nebo produkční server, proměnnou prostředí lze použít k nastavení připojovacího řetězce na reálný databázový server. Další informace naleznete v [tématu Konfigurace.](xref:fundamentals/configuration/index)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>Místní databáze SQL Server Express

LocalDB je odlehčená verze databázového stroje SQL Server Express, která je určena pro vývoj programů. LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje žádná složitá konfigurace. Ve výchozím nastavení databáze `*.mdf` LocalDB `C:/Users/<user/>` vytváří soubory v adresáři.

<a name="ssox"></a>
* V nabídce **Zobrazení** otevřete **Průzkumník a průzkumník objektů serveru SQL Server** (SSOX).

  ![Nabídka Zobrazit](sql/_static/ssox.png)

* Klikněte pravým `Movie` tlačítkem myši na tabulku a vyberte **Zobrazit Návrhář**:

  ![Kontextová nabídka otevřená v tabulce Film](sql/_static/design.png)

  ![Otevřese tabulka filmu v Návrháři](sql/_static/dv.png)

Poznamenejte si `ID`ikonu klíče vedle aplikace . Ve výchozím nastavení EF `ID` vytvoří vlastnost pojmenovanou pro primární klíč.

* Klikněte pravým `Movie` tlačítkem myši na tabulku a vyberte **zobrazit data**:

  ![Otevřená tabulka filmu zobrazující data tabulky](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Osivo databáze

Vytvořte novou `SeedData` třídu pojmenovanou ve složce *Modely* s následujícím kódem:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

Pokud jsou nějaké filmy v DB, inicializátor osiva vrátí a žádné filmy jsou přidány.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Přidejte inicializátor osiva

V *Program.cs*upravte metodu `Main` takto:

* Získejte kontextové instance DB z kontejneru vkládání závislostí.
* Volání metody osiva, předávání k ní kontextu.
* Po dokončení metody osiva zlikvidujte kontext.

Následující kód zobrazuje aktualizovaný *soubor Program.cs.*

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

Produkční aplikace nebude `Database.Migrate`volat . Je přidán do předchozího kódu, aby se `Update-Database` zabránilo následující výjimku, když nebyla spuštěna:

SqlException: Nelze otevřít databázi "RazorPagesMovieContext-21" požadované přihlášení. Přihlášení se nezdařilo.
Přihlášení se nezdařilo pro uživatelské jméno uživatele.

### <a name="test-the-app"></a>Otestování aplikace

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Odstraňte všechny záznamy v databázi. Můžete to udělat s odstranit odkazy v prohlížeči nebo od [SSOX](xref:tutorials/razor-pages/new-field#ssox)
* Vynutit aplikaci inicializovat (volání metod ve `Startup` třídě), takže metoda osiva spustí. Chcete-li vynutit inicializaci, musí být iis Express zastaven a restartován. Můžete to provést s některou z následujících přístupů:

  * Klepněte pravým tlačítkem myši na ikonu systémové lišty IIS Express v oznamovací oblasti a klepněte na **Ukončit** nebo **zastavit web**:

    ![Ikona systémové lišty IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextová nabídka](sql/_static/stopIIS.png)

    * Pokud jste používali VS v režimu bez ladění, stiskněte klávesu F5 a spusťte v režimu ladění.
    * Pokud jste v režimu ladění používali VS, zastavte ladicí program a stiskněte klávesu F5.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Odstranit všechny záznamy v DB (Tak bude spuštěna metoda osiva). Zastavit a spustit aplikaci pro osílku databáze.

Aplikace zobrazuje osývaná data.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Odstranit všechny záznamy v DB (Tak bude spuštěna metoda osiva). Zastavit a spustit aplikaci pro osílku databáze.

Aplikace zobrazuje osývaná data.

---

Aplikace zobrazuje osývaná data:

![Filmová aplikace otevřená v Chromu zobrazující data filmu](sql/_static/m55.png)

Další kurz vyčistí prezentaci dat.

## <a name="additional-resources"></a>Další zdroje

* [Verze tohoto kurzu pro YouTube](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> [Předchozí: Scaffolded Razor Stránky](xref:tutorials/razor-pages/page)
> [Další: Aktualizace stránek](xref:tutorials/razor-pages/da1)

::: moniker-end
