# <a name="work-with-sqlite-in-an-aspnet-core-mvc-app"></a><span data-ttu-id="4649a-101">Práce s SQLite v ASP.NET Core MVC aplikace</span><span class="sxs-lookup"><span data-stu-id="4649a-101">Work with SQLite in an ASP.NET Core MVC app</span></span>

<span data-ttu-id="4649a-102">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4649a-102">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4649a-103">`MvcMovieContext` Objekt zpracovává úlohu s připojením k databázi a mapování `Movie` objekty se záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="4649a-103">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="4649a-104">Kontext databáze je zaregistrován [injektáž závislostí](xref:fundamentals/dependency-injection) kontejneru v `ConfigureServices` metoda ve *Startup.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="4649a-104">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app-xplat/start-mvc/sample/MvcMovie/Startup.cs?name=snippet2&highlight=6-8)]

## <a name="sqlite"></a><span data-ttu-id="4649a-105">SQLite</span><span class="sxs-lookup"><span data-stu-id="4649a-105">SQLite</span></span>

<span data-ttu-id="4649a-106">[SQLite](https://www.sqlite.org/) webu stavy:</span><span class="sxs-lookup"><span data-stu-id="4649a-106">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="4649a-107">SQLite je samostatná, vysokou spolehlivost, embedded, plně vybavené, veřejné domény, databázový stroj SQL.</span><span class="sxs-lookup"><span data-stu-id="4649a-107">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="4649a-108">SQLite je nejpoužívanější databázového stroje na světě.</span><span class="sxs-lookup"><span data-stu-id="4649a-108">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="4649a-109">Celá řada nástrojů třetích stran, které si můžete stáhnout, spravovat a zobrazovat databázi SQLite.</span><span class="sxs-lookup"><span data-stu-id="4649a-109">There are many third party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="4649a-110">Následující obrázek je z [DB prohlížeč pro SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="4649a-110">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="4649a-111">Pokud máte oblíbený nástroj SQLite, na co se vám líbí o něm komentář.</span><span class="sxs-lookup"><span data-stu-id="4649a-111">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![Prohlížeč DB pro SQLite zobrazující film db](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

## <a name="seed-the-database"></a><span data-ttu-id="4649a-113">Přidání dat do databáze</span><span class="sxs-lookup"><span data-stu-id="4649a-113">Seed the database</span></span>

<span data-ttu-id="4649a-114">Vytvořte novou třídu s názvem `SeedData` v *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="4649a-114">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="4649a-115">Generovaného kódu nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4649a-115">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="4649a-116">Pokud jsou všechny filmy v databázi, vrátí inicializátoru pro dosazení hodnot.</span><span class="sxs-lookup"><span data-stu-id="4649a-116">If there are any movies in the DB, the seed initializer returns.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="4649a-117">Přidat inicializační výraz počáteční hodnoty</span><span class="sxs-lookup"><span data-stu-id="4649a-117">Add the seed initializer</span></span>

<span data-ttu-id="4649a-118">Přidat inicializační výraz počáteční hodnoty `Main` metodu *Program.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="4649a-118">Add the seed initializer to the `Main` method in the *Program.cs* file:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Program.cs)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Program.cs?highlight=6,16-32)]

::: moniker-end

### <a name="test-the-app"></a><span data-ttu-id="4649a-119">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="4649a-119">Test the app</span></span>

<span data-ttu-id="4649a-120">Všechny záznamy z databáze odstraníte, (aby se spustí metodu počáteční hodnota).</span><span class="sxs-lookup"><span data-stu-id="4649a-120">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="4649a-121">Zastavení a spuštění aplikace k přidání dat do databáze.</span><span class="sxs-lookup"><span data-stu-id="4649a-121">Stop and start the app to seed the database.</span></span>
   
<span data-ttu-id="4649a-122">Aplikace zobrazí dosazená data.</span><span class="sxs-lookup"><span data-stu-id="4649a-122">The app shows the seeded data.</span></span>

![MVC Movie aplikaci otevřít prohlížeč zobrazující data o filmech](~/tutorials/first-mvc-app/working-with-sql/_static/m55.png)
