# <a name="work-with-sqlite-in-an-aspnet-core-razor-pages-app"></a><span data-ttu-id="92aa1-101">Práce s SQLite v ASP.NET Core Razor Pages aplikace</span><span class="sxs-lookup"><span data-stu-id="92aa1-101">Work with SQLite in an ASP.NET Core Razor Pages app</span></span>

<span data-ttu-id="92aa1-102">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="92aa1-102">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="92aa1-103">`MovieContext` Objekt zpracovává úlohu s připojením k databázi a mapování `Movie` objekty se záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="92aa1-103">The `MovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="92aa1-104">Kontext databáze je zaregistrován [Dependency Injection (DI)](xref:fundamentals/dependency-injection) kontejneru v `ConfigureServices` metoda ve *Startup.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="92aa1-104">The database context is registered with the [Dependency Injection (DI)](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

[!code-csharp[](code/Startup.cs?name=snippet2&highlight=6-8)]

<span data-ttu-id="92aa1-105">Další informace o používání `DbContext` s DI, přečtěte si téma [pomocí DbContext s DI](/ef/core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="92aa1-105">For more information on using `DbContext` with DI, see [Using DbContext with DI](/ef/core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection).</span></span>

## <a name="sqlite"></a><span data-ttu-id="92aa1-106">SQLite</span><span class="sxs-lookup"><span data-stu-id="92aa1-106">SQLite</span></span>

<span data-ttu-id="92aa1-107">[SQLite](https://www.sqlite.org/) webu stavy:</span><span class="sxs-lookup"><span data-stu-id="92aa1-107">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="92aa1-108">SQLite je samostatná, vysokou spolehlivost, embedded, plně vybavené, veřejné domény, databázový stroj SQL.</span><span class="sxs-lookup"><span data-stu-id="92aa1-108">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="92aa1-109">SQLite je nejpoužívanější databázového stroje na světě.</span><span class="sxs-lookup"><span data-stu-id="92aa1-109">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="92aa1-110">Celá řada nástrojů třetích stran, které si můžete stáhnout, spravovat a zobrazovat databázi SQLite.</span><span class="sxs-lookup"><span data-stu-id="92aa1-110">There are many third party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="92aa1-111">Následující obrázek je z [DB prohlížeč pro SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="92aa1-111">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="92aa1-112">Pokud máte oblíbený nástroj SQLite, na co se vám líbí o něm komentář.</span><span class="sxs-lookup"><span data-stu-id="92aa1-112">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![Prohlížeč DB pro SQLite zobrazující film db](../../tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

## <a name="seed-the-database"></a><span data-ttu-id="92aa1-114">Přidání dat do databáze</span><span class="sxs-lookup"><span data-stu-id="92aa1-114">Seed the database</span></span>

<span data-ttu-id="92aa1-115">Vytvořte novou třídu s názvem `SeedData` v *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="92aa1-115">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="92aa1-116">Generovaného kódu nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="92aa1-116">Replace the generated code with the following:</span></span>

[!code-csharp[](code/Models/SeedData.cs)]

<span data-ttu-id="92aa1-117">Pokud jsou všechny filmy v databázi, vrátí inicializátoru pro dosazení hodnot.</span><span class="sxs-lookup"><span data-stu-id="92aa1-117">If there are any movies in the DB, the seed initializer returns.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="92aa1-118">Přidat inicializační výraz počáteční hodnoty</span><span class="sxs-lookup"><span data-stu-id="92aa1-118">Add the seed initializer</span></span>

<span data-ttu-id="92aa1-119">Přidat inicializační výraz počáteční hodnoty `Main` metodu *Program.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="92aa1-119">Add the seed initializer to the `Main` method in the *Program.cs* file:</span></span>

[!code-csharp[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Program.cs)]

### <a name="test-the-app"></a><span data-ttu-id="92aa1-120">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="92aa1-120">Test the app</span></span>

<span data-ttu-id="92aa1-121">Všechny záznamy z databáze odstraníte, (aby se spustí metodu počáteční hodnota).</span><span class="sxs-lookup"><span data-stu-id="92aa1-121">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="92aa1-122">Zastavení a spuštění aplikace k přidání dat do databáze.</span><span class="sxs-lookup"><span data-stu-id="92aa1-122">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="92aa1-123">Aplikace zobrazí dosazená data.</span><span class="sxs-lookup"><span data-stu-id="92aa1-123">The app shows the seeded data.</span></span>
