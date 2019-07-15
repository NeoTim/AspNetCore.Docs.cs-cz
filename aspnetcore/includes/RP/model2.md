<a name="dc"></a>

### <a name="add-a-database-context-class"></a><span data-ttu-id="af09b-101">Přidání třídy kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="af09b-101">Add a database context class</span></span>

<span data-ttu-id="af09b-102">Přidejte následující `RazorPagesMovieContext` třídu *Data* složky:</span><span class="sxs-lookup"><span data-stu-id="af09b-102">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="af09b-103">Předchozí kód vytvoří `DbSet` vlastnost sady entit.</span><span class="sxs-lookup"><span data-stu-id="af09b-103">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="af09b-104">V terminologii Entity Framework obvykle sadu entit odpovídá databázové tabulky a entity odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="af09b-104">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="af09b-105">Přidat připojovací řetězec databáze</span><span class="sxs-lookup"><span data-stu-id="af09b-105">Add a database connection string</span></span>

<span data-ttu-id="af09b-106">Přidat připojovací řetězec pro *appsettings.json* sdílené, jak je znázorněno v následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="af09b-106">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="af09b-107">Přidat požadované balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="af09b-107">Add required NuGet packages</span></span>

<span data-ttu-id="af09b-108">Spusťte následující příkaz rozhraní příkazového řádku .NET Core pro přidání SQLite a CodeGeneration.Design do projektu:</span><span class="sxs-lookup"><span data-stu-id="af09b-108">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```console
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design

```

<span data-ttu-id="af09b-109">`Microsoft.VisualStudio.Web.CodeGeneration.Design` Balíčky jsou požadovány pro generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="af09b-109">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="af09b-110">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="af09b-110">Register the database context</span></span>

<span data-ttu-id="af09b-111">Přidejte následující `using` příkazů v horní části *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="af09b-111">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="af09b-112">Zaregistrujte kontext databáze s [injektáž závislostí](xref:fundamentals/dependency-injection) kontejneru v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="af09b-112">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="af09b-113">Sestavte projekt jako kontrolu chyb.</span><span class="sxs-lookup"><span data-stu-id="af09b-113">Build the project as a check for errors.</span></span>
