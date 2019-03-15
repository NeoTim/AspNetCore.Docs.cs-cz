---
title: Práce s databází a ASP.NET Core
author: rick-anderson
description: Vysvětluje, práci s databází a ASP.NET Core.
ms.author: riande
ms.date: 12/07/2017
uid: tutorials/razor-pages/sql
ms.openlocfilehash: e2e9be0aa25166e216d34419859cd907d0423f70
ms.sourcegitcommit: 34bf9fc6ea814c039401fca174642f0acb14be3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2019
ms.locfileid: "57841563"
---
# <a name="work-with-a-database-and-aspnet-core"></a>Práce s databází a ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Joe Audette](https://twitter.com/joeaudette)

[!INCLUDE[](~/includes/rp/download.md)]

`RazorPagesMovieContext` Objekt zpracovává úlohu s připojením k databázi a mapování `Movie` objekty se záznamy v databázi. Kontext databáze je zaregistrován [injektáž závislostí](xref:fundamentals/dependency-injection) kontejneru v `ConfigureServices` metoda ve *Startup.cs*:

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---  
<!-- End of VS tabs -->

Další informace o metodách `ConfigureServices`, naleznete v tématu:

* [Podpora EU obecného Regulation (GDPR) v ASP.NET Core](xref:security/gdpr) pro `CookiePolicyOptions`.
* [SetCompatibilityVersion](xref:mvc/compatibility-version)

ASP.NET Core [konfigurace](xref:fundamentals/configuration/index) systému čtení `ConnectionString`. Pro místní vývoj, získá připojovací řetězec z *appsettings.json* souboru.

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Hodnota názvu databáze (`Database={Database name}`) se bude lišit pro vygenerovaný kód. Hodnota názvu je volitelný.

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---  
<!-- End of VS tabs -->

Po nasazení aplikace na testovacím nebo produkčním serveru, proměnné prostředí je možné nastavit připojovací řetězec na skutečné databázový server. Zobrazit [konfigurace](xref:fundamentals/configuration/index) Další informace.

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB je Odlehčená verze SQL Server Express. databázový stroj, která je určená pro vývoj v programu. LocalDB spustí na vyžádání a běží v uživatelském režimu, takže není bez složité konfigurace. Ve výchozím nastavení, vytvoří databázi LocalDB `*.mdf` soubory `C:/Users/<user/>` adresáře.

<a name="ssox"></a>
* Z **zobrazení** nabídce otevřete **Průzkumník objektů systému SQL Server** (SSOX).

  ![Nabídka Zobrazit](sql/_static/ssox.png)

* Klikněte pravým tlačítkem na `Movie` tabulce a vybrat **Návrhář zobrazení**:

  ![Kontextová nabídka otevřít na tabulky Movie](sql/_static/design.png)

  ![Otevřít v Návrháři tabulky Movie](sql/_static/dv.png)

Poznámka: na ikonu klíče vedle `ID`. Ve výchozím nastavení, EF vytvoří vlastnost s názvem `ID` pro primární klíč.

* Klikněte pravým tlačítkem na `Movie` tabulce a vybrat **Data zobrazení**:

  ![Otevřít zobrazení tabulky dat tabulky Movie](sql/_static/vd22.png)
<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---  
<!-- End of VS tabs -->

## <a name="seed-the-database"></a>Přidání dat do databáze

Vytvořte novou třídu s názvem `SeedData` v *modely* složka s následujícím kódem:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

Pokud jsou všechny filmy v databázi, vrátí inicializátoru pro dosazení hodnot a jsou přidány žádné video.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```
<a name="si"></a>
### <a name="add-the-seed-initializer"></a>Přidat inicializační výraz počáteční hodnoty

V *Program.cs*, změnit `Main` metoda můžete provádět následující:

* Instance kontextu databáze získáte z kontejneru pro vkládání závislostí.
* Volejte metodu počáteční hodnoty předání kontextu.
* Kontext Dispose po dokončení počáteční hodnoty metody.

Následující kód ukazuje aktualizovaný *Program.cs* souboru.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

Produkční aplikace by volat `Database.Migrate`. Přidá se do předchozí kód, aby se zabránilo následující výjimku při `Update-Database` nebyl spuštěn:

SqlException: Databázi "RazorPagesMovieContext 21" požadovaný v přihlášení nelze otevřít. Přihlášení se nezdařilo.
Přihlašovací jméno uživatele 'jméno uživatele' se nezdařilo.

### <a name="test-the-app"></a>Testování aplikace

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Odstraníte všechny záznamy z databáze. To lze provést pomocí odstranit odkazy v prohlížeči nebo z [SSOX](xref:tutorials/razor-pages/new-field#ssox)
* Se aplikace inicializuje (volat metody ve `Startup` třídy), spustí seed – metoda. Pokud chcete vynutit inicializace, služba IIS Express musí zastavit, restartovat. Provést s některou z následujících postupů:

  * Klikněte pravým tlačítkem na službu IIS Express systému na hlavním panelu ikonu v oznamovací oblasti a klepněte na **ukončovací** nebo **zastavení webu**:

    ![Služba IIS Express ikonu na hlavním panelu](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextové nabídky](sql/_static/stopIIS.png)

    * Pokud VS byly spuštěny v režimu bez ladění, stiskněte klávesu F5 ke spuštění v režimu ladění.
    * Pokud jste používali zastavení ladicího programu VS v režimu ladění a stisknutím klávesy F5.

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Všechny záznamy z databáze odstraníte, (aby se spustí metodu počáteční hodnota). Zastavení a spuštění aplikace k přidání dat do databáze.

Aplikace zobrazí dosazená data.

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Všechny záznamy z databáze odstraníte, (aby se spustí metodu počáteční hodnota). Zastavení a spuštění aplikace k přidání dat do databáze.

Aplikace zobrazí dosazená data.

---  
<!-- End of VS tabs -->


   
Aplikace bude zobrazovat dosazená data:

![Otevřít v prohlížeči Chrome ukazující data o filmech aplikace Movie](sql/_static/m55.png)

V dalším kurzu se vyčistit prezentace data.

## <a name="additional-resources"></a>Další zdroje

* [Verzi tohoto kurzu na webu YouTube](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> [Předchozí: Generované uživatelské rozhraní pro stránky Razor](xref:tutorials/razor-pages/page)
> [Další: Aktualizace stránek](xref:tutorials/razor-pages/da1)
