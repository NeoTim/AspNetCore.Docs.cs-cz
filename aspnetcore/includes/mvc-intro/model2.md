::: moniker range=">= aspnetcore-3.0"

<a name="dc"></a>

<span data-ttu-id="884a5-101">Vytvořte složku *dat* .</span><span class="sxs-lookup"><span data-stu-id="884a5-101">Create a *Data* folder.</span></span>

<span data-ttu-id="884a5-102">Do složky *data* přidejte následující třídu `MvcMovieContext`:</span><span class="sxs-lookup"><span data-stu-id="884a5-102">Add the following `MvcMovieContext` class to the *Data* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="884a5-103">Předchozí kód vytvoří vlastnost `DbSet` pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="884a5-103">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="884a5-104">V Entity Framework terminologii sada entit obvykle odpovídá tabulce databáze a entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="884a5-104">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="884a5-105">Přidat připojovací řetězec databáze</span><span class="sxs-lookup"><span data-stu-id="884a5-105">Add a database connection string</span></span>

<span data-ttu-id="884a5-106">Přidejte do souboru *appSettings. JSON* připojovací řetězec:</span><span class="sxs-lookup"><span data-stu-id="884a5-106">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="884a5-107">Přidat balíčky NuGet a nástroje EF</span><span class="sxs-lookup"><span data-stu-id="884a5-107">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="884a5-108">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="884a5-108">Register the database context</span></span>

<span data-ttu-id="884a5-109">Do horní části *Startup.cs*přidejte následující příkazy `using`:</span><span class="sxs-lookup"><span data-stu-id="884a5-109">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="884a5-110">Zaregistrujte kontext databáze pomocí kontejneru pro [vkládání závislostí](xref:fundamentals/dependency-injection) v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="884a5-110">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

<span data-ttu-id="884a5-111">Sestavte projekt jako kontrolu chyb kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="884a5-111">Build the project as a check for compiler errors.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="884a5-112">Do složky *modely* přidejte následující třídu `MvcMovieContext`:</span><span class="sxs-lookup"><span data-stu-id="884a5-112">Add the following `MvcMovieContext` class to the *Models* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

<span data-ttu-id="884a5-113">Předchozí kód vytvoří vlastnost `DbSet` pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="884a5-113">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="884a5-114">V Entity Framework terminologii sada entit obvykle odpovídá tabulce databáze a entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="884a5-114">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="884a5-115">Přidat připojovací řetězec databáze</span><span class="sxs-lookup"><span data-stu-id="884a5-115">Add a database connection string</span></span>

<span data-ttu-id="884a5-116">Přidejte do souboru *appSettings. JSON* připojovací řetězec:</span><span class="sxs-lookup"><span data-stu-id="884a5-116">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="884a5-117">Přidat požadované balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="884a5-117">Add required NuGet packages</span></span>

<span data-ttu-id="884a5-118">Spusťte následující .NET Core CLI příkaz pro přidání SQLite a strategii. design do projektu:</span><span class="sxs-lookup"><span data-stu-id="884a5-118">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

<span data-ttu-id="884a5-119">Pro generování uživatelského rozhraní se vyžaduje balíček `Microsoft.VisualStudio.Web.CodeGeneration.Design`.</span><span class="sxs-lookup"><span data-stu-id="884a5-119">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="884a5-120">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="884a5-120">Register the database context</span></span>

<span data-ttu-id="884a5-121">Do horní části *Startup.cs*přidejte následující příkazy `using`:</span><span class="sxs-lookup"><span data-stu-id="884a5-121">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="884a5-122">Zaregistrujte kontext databáze pomocí kontejneru pro [vkládání závislostí](xref:fundamentals/dependency-injection) v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="884a5-122">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="884a5-123">Sestavte projekt jako kontrolu chyb.</span><span class="sxs-lookup"><span data-stu-id="884a5-123">Build the project as a check for errors.</span></span>
::: moniker-end