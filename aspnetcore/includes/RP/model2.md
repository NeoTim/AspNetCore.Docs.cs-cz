<a name="dc"></a>

### <a name="add-a-database-context-class"></a><span data-ttu-id="fa107-101">Přidat třídu kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="fa107-101">Add a database context class</span></span>

<span data-ttu-id="fa107-102">Do složky `RazorPagesMovieContext` *data* přidejte následující třídu:</span><span class="sxs-lookup"><span data-stu-id="fa107-102">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="fa107-103">Předchozí kód vytvoří `DbSet` vlastnost pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="fa107-103">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="fa107-104">V Entity Framework terminologii sada entit obvykle odpovídá tabulce databáze a entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="fa107-104">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="fa107-105">Přidat připojovací řetězec databáze</span><span class="sxs-lookup"><span data-stu-id="fa107-105">Add a database connection string</span></span>

<span data-ttu-id="fa107-106">Do souboru *appSettings. JSON* přidejte připojovací řetězec, jak ukazuje následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="fa107-106">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="fa107-107">Přidat požadované balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="fa107-107">Add required NuGet packages</span></span>

<span data-ttu-id="fa107-108">Spuštěním následujících příkazů .NET Core CLI přidejte SQLite, Entity Framework Core a strategii. design do projektu:</span><span class="sxs-lookup"><span data-stu-id="fa107-108">Run the following .NET Core CLI commands to add SQLite, Entity Framework Core, and  CodeGeneration.Design to the project:</span></span>

```console
dotnet add package Microsoft.EntityFrameworkCore.SQLite --version 3.0.0-*
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
```

<span data-ttu-id="fa107-109">`Microsoft.VisualStudio.Web.CodeGeneration.Design` Balíček je vyžadován pro generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="fa107-109">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="fa107-110">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="fa107-110">Register the database context</span></span>

<span data-ttu-id="fa107-111">Do horní části `using` *Startup.cs*přidejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="fa107-111">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="fa107-112">Zaregistrujte kontext databáze pomocí kontejneru pro `Startup.ConfigureServices` [vkládání závislostí](xref:fundamentals/dependency-injection) v.</span><span class="sxs-lookup"><span data-stu-id="fa107-112">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="fa107-113">Přidat požadované balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="fa107-113">Add required NuGet packages</span></span>

<span data-ttu-id="fa107-114">Spusťte následující .NET Core CLI příkaz pro přidání SQLite a strategii. design do projektu:</span><span class="sxs-lookup"><span data-stu-id="fa107-114">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```console
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design

```

<span data-ttu-id="fa107-115">`Microsoft.VisualStudio.Web.CodeGeneration.Design` Balíček je vyžadován pro generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="fa107-115">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="fa107-116">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="fa107-116">Register the database context</span></span>

<span data-ttu-id="fa107-117">Do horní části `using` *Startup.cs*přidejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="fa107-117">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="fa107-118">Zaregistrujte kontext databáze pomocí kontejneru pro `Startup.ConfigureServices` [vkládání závislostí](xref:fundamentals/dependency-injection) v.</span><span class="sxs-lookup"><span data-stu-id="fa107-118">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="fa107-119">Sestavte projekt jako kontrolu chyb.</span><span class="sxs-lookup"><span data-stu-id="fa107-119">Build the project as a check for errors.</span></span>
::: moniker-end