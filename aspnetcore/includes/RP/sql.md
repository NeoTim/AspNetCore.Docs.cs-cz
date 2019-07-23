# <a name="work-with-sqlite-in-an-aspnet-core-razor-pages-app"></a>Práce s SQLite v ASP.NET Core Razor Pages aplikace

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

`MovieContext` Objekt zpracovává úlohu s připojením k databázi a mapování `Movie` objekty se záznamy v databázi. Kontext databáze je zaregistrován [Dependency Injection (DI)](xref:fundamentals/dependency-injection) kontejneru v `ConfigureServices` metoda ve *Startup.cs* souboru:

[!code-csharp[](code/Startup.cs?name=snippet2&highlight=6-8)]

Další informace o používání `DbContext` s DI, přečtěte si téma [pomocí DbContext s DI](/ef/core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection).

## <a name="sqlite"></a>SQLite

[SQLite](https://www.sqlite.org/) webu stavy:

> SQLite je samostatná, vysokou spolehlivost, embedded, plně vybavené, veřejné domény, databázový stroj SQL. SQLite je nejpoužívanější databázového stroje na světě.

Celá řada nástrojů třetích stran, které si můžete stáhnout, spravovat a zobrazovat databázi SQLite. Následující obrázek je z [DB prohlížeč pro SQLite](https://sqlitebrowser.org/). Pokud máte oblíbený nástroj SQLite, na co se vám líbí o něm komentář.

![Prohlížeč DB pro SQLite zobrazující film db](../../tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

## <a name="seed-the-database"></a>Přidání dat do databáze

Vytvořte novou třídu s názvem `SeedData` v *modely* složky. Generovaného kódu nahraďte následujícím kódem:

[!code-csharp[](code/Models/SeedData.cs)]

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

[!code-csharp[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Program.cs)]

### <a name="test-the-app"></a>Testování aplikace

Všechny záznamy z databáze odstraníte, (aby se spustí metodu počáteční hodnota). Zastavení a spuštění aplikace k přidání dat do databáze.

Aplikace zobrazí dosazená data.
