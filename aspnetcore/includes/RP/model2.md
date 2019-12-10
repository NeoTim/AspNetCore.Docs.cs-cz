<a name="dc"></a>

### <a name="add-a-database-context-class"></a><span data-ttu-id="6038f-101">Přidat třídu kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="6038f-101">Add a database context class</span></span>

<span data-ttu-id="6038f-102">V projektu RazorPagesMovie vytvořte novou složku s názvem *data*.</span><span class="sxs-lookup"><span data-stu-id="6038f-102">In the RazorPagesMovie project, create a new folder called *Data*.</span></span> <span data-ttu-id="6038f-103">Do složky *data* přidejte následující třídu `RazorPagesMovieContext`:</span><span class="sxs-lookup"><span data-stu-id="6038f-103">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="6038f-104">Předchozí kód vytvoří vlastnost `DbSet` pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="6038f-104">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="6038f-105">V Entity Framework terminologii sada entit obvykle odpovídá tabulce databáze a entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="6038f-105">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="6038f-106">Přidat připojovací řetězec databáze</span><span class="sxs-lookup"><span data-stu-id="6038f-106">Add a database connection string</span></span>

<span data-ttu-id="6038f-107">Do souboru *appSettings. JSON* přidejte připojovací řetězec, jak ukazuje následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="6038f-107">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="6038f-108">Přidat balíčky NuGet a nástroje EF</span><span class="sxs-lookup"><span data-stu-id="6038f-108">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="6038f-109">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="6038f-109">Register the database context</span></span>

<span data-ttu-id="6038f-110">Do horní části *Startup.cs*přidejte následující příkazy `using`:</span><span class="sxs-lookup"><span data-stu-id="6038f-110">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="6038f-111">Zaregistrujte kontext databáze pomocí kontejneru pro [vkládání závislostí](xref:fundamentals/dependency-injection) v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6038f-111">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="6038f-112">Přidat požadované balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="6038f-112">Add required NuGet packages</span></span>

<span data-ttu-id="6038f-113">Spusťte následující .NET Core CLI příkaz pro přidání SQLite a strategii. design do projektu:</span><span class="sxs-lookup"><span data-stu-id="6038f-113">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
```

<span data-ttu-id="6038f-114">Pro generování uživatelského rozhraní se vyžaduje balíček `Microsoft.VisualStudio.Web.CodeGeneration.Design`.</span><span class="sxs-lookup"><span data-stu-id="6038f-114">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="6038f-115">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="6038f-115">Register the database context</span></span>

<span data-ttu-id="6038f-116">Do horní části *Startup.cs*přidejte následující příkazy `using`:</span><span class="sxs-lookup"><span data-stu-id="6038f-116">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="6038f-117">Zaregistrujte kontext databáze pomocí kontejneru pro [vkládání závislostí](xref:fundamentals/dependency-injection) v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6038f-117">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="6038f-118">Sestavte projekt jako kontrolu chyb.</span><span class="sxs-lookup"><span data-stu-id="6038f-118">Build the project as a check for errors.</span></span>

::: moniker-end
