---
ms.openlocfilehash: 655279f0f744e96f1cf590b52e199ed2be3fa106
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58264277"
---
# <a name="work-with-sqlite-in-an-aspnet-core-mvc-app"></a>Práce s SQLite v ASP.NET Core MVC aplikace

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

`MvcMovieContext` Objekt zpracovává úlohu s připojením k databázi a mapování `Movie` objekty se záznamy v databázi. Kontext databáze je zaregistrován [injektáž závislostí](xref:fundamentals/dependency-injection) kontejneru v `ConfigureServices` metoda ve *Startup.cs* souboru:

[!code-csharp[](~/tutorials/first-mvc-app-xplat/start-mvc/sample/MvcMovie/Startup.cs?name=snippet2&highlight=6-8)]

## <a name="sqlite"></a>SQLite

[SQLite](https://www.sqlite.org/) webu stavy:

> SQLite je samostatná, vysokou spolehlivost, embedded, plně vybavené, veřejné domény, databázový stroj SQL. SQLite je nejpoužívanější databázového stroje na světě.

Celá řada nástrojů třetích stran, které si můžete stáhnout, spravovat a zobrazovat databázi SQLite. Následující obrázek je z [DB prohlížeč pro SQLite](http://sqlitebrowser.org/). Pokud máte oblíbený nástroj SQLite, na co se vám líbí o něm komentář.

![Prohlížeč DB pro SQLite zobrazující film db](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

## <a name="seed-the-database"></a>Přidání dat do databáze

Vytvořte novou třídu s názvem `SeedData` v *modely* složky. Generovaného kódu nahraďte následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/SeedData.cs?name=snippet_1)]

Pokud jsou všechny filmy v databázi, vrátí inicializátoru pro dosazení hodnot.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Přidat inicializační výraz počáteční hodnoty

Přidat inicializační výraz počáteční hodnoty `Main` metodu *Program.cs* souboru:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Program.cs)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Program.cs?highlight=6,16-32)]

::: moniker-end

### <a name="test-the-app"></a>Testování aplikace

Všechny záznamy z databáze odstraníte, (aby se spustí metodu počáteční hodnota). Zastavení a spuštění aplikace k přidání dat do databáze.
   
Aplikace zobrazí dosazená data.

![MVC Movie aplikaci otevřít prohlížeč zobrazující data o filmech](~/tutorials/first-mvc-app/working-with-sql/_static/m55.png)
