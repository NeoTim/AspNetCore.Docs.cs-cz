::: moniker range=">= aspnetcore-3.0"

<a name="dc"></a>

<span data-ttu-id="59bbd-101">Vytvořte složku *dat* .</span><span class="sxs-lookup"><span data-stu-id="59bbd-101">Create a *Data* folder.</span></span>

<span data-ttu-id="59bbd-102">Do složky `MvcMovieContext` *data* přidejte následující třídu:</span><span class="sxs-lookup"><span data-stu-id="59bbd-102">Add the following `MvcMovieContext` class to the *Data* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="59bbd-103">Předchozí kód vytvoří `DbSet` vlastnost pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="59bbd-103">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="59bbd-104">V Entity Framework terminologii sada entit obvykle odpovídá tabulce databáze a entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="59bbd-104">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="59bbd-105">Přidat připojovací řetězec databáze</span><span class="sxs-lookup"><span data-stu-id="59bbd-105">Add a database connection string</span></span>

<span data-ttu-id="59bbd-106">Přidejte do souboru *appSettings. JSON* připojovací řetězec:</span><span class="sxs-lookup"><span data-stu-id="59bbd-106">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="59bbd-107">Přidat balíčky NuGet a nástroje EF</span><span class="sxs-lookup"><span data-stu-id="59bbd-107">Add NuGet packages and EF tools</span></span>

<span data-ttu-id="59bbd-108">Spusťte následující příkazy .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="59bbd-108">Run the following .NET Core CLI commands:</span></span>

```console
dotnet tool install --global dotnet-ef --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SQLite --version 3.0.0-*
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
```

<span data-ttu-id="59bbd-109">Předchozí příkazy přidají do projektu Entity Framework Core nástroje pro rozhraní .NET CLI a několik balíčků.</span><span class="sxs-lookup"><span data-stu-id="59bbd-109">The preceding commands add Entity Framework Core Tools for the .NET CLI and several packages to the project.</span></span> <span data-ttu-id="59bbd-110">`Microsoft.VisualStudio.Web.CodeGeneration.Design` Balíček je vyžadován pro generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="59bbd-110">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="59bbd-111">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="59bbd-111">Register the database context</span></span>

<span data-ttu-id="59bbd-112">Do horní části `using` *Startup.cs*přidejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="59bbd-112">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="59bbd-113">Zaregistrujte kontext databáze pomocí kontejneru pro `Startup.ConfigureServices` [vkládání závislostí](xref:fundamentals/dependency-injection) v.</span><span class="sxs-lookup"><span data-stu-id="59bbd-113">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

<span data-ttu-id="59bbd-114">Sestavte projekt jako kontrolu chyb kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="59bbd-114">Build the project as a check for compiler errors.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="59bbd-115">Přidejte následující `MvcMovieContext` třídu *modely* složky:</span><span class="sxs-lookup"><span data-stu-id="59bbd-115">Add the following `MvcMovieContext` class to the *Models* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

<span data-ttu-id="59bbd-116">Předchozí kód vytvoří `DbSet` vlastnost pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="59bbd-116">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="59bbd-117">V Entity Framework terminologii sada entit obvykle odpovídá tabulce databáze a entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="59bbd-117">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="59bbd-118">Přidat připojovací řetězec databáze</span><span class="sxs-lookup"><span data-stu-id="59bbd-118">Add a database connection string</span></span>

<span data-ttu-id="59bbd-119">Přidejte do souboru *appSettings. JSON* připojovací řetězec:</span><span class="sxs-lookup"><span data-stu-id="59bbd-119">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="59bbd-120">Přidat požadované balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="59bbd-120">Add required NuGet packages</span></span>

<span data-ttu-id="59bbd-121">Spusťte následující .NET Core CLI příkaz pro přidání SQLite a strategii. design do projektu:</span><span class="sxs-lookup"><span data-stu-id="59bbd-121">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```console
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

<span data-ttu-id="59bbd-122">`Microsoft.VisualStudio.Web.CodeGeneration.Design` Balíček je vyžadován pro generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="59bbd-122">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="59bbd-123">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="59bbd-123">Register the database context</span></span>

<span data-ttu-id="59bbd-124">Do horní části `using` *Startup.cs*přidejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="59bbd-124">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="59bbd-125">Zaregistrujte kontext databáze pomocí kontejneru pro `Startup.ConfigureServices` [vkládání závislostí](xref:fundamentals/dependency-injection) v.</span><span class="sxs-lookup"><span data-stu-id="59bbd-125">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="59bbd-126">Sestavte projekt jako kontrolu chyb.</span><span class="sxs-lookup"><span data-stu-id="59bbd-126">Build the project as a check for errors.</span></span>
::: moniker-end