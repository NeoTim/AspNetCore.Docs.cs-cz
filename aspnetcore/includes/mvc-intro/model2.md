::: moniker range=">= aspnetcore-3.0"

<a name="dc"></a>

<span data-ttu-id="cecb7-101">Vytvořte složku *dat* .</span><span class="sxs-lookup"><span data-stu-id="cecb7-101">Create a *Data* folder.</span></span>

<span data-ttu-id="cecb7-102">Do složky `MvcMovieContext` *data* přidejte následující třídu:</span><span class="sxs-lookup"><span data-stu-id="cecb7-102">Add the following `MvcMovieContext` class to the *Data* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="cecb7-103">Předchozí kód vytvoří `DbSet` vlastnost pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="cecb7-103">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="cecb7-104">V Entity Framework terminologii sada entit obvykle odpovídá tabulce databáze a entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="cecb7-104">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="cecb7-105">Přidat připojovací řetězec databáze</span><span class="sxs-lookup"><span data-stu-id="cecb7-105">Add a database connection string</span></span>

<span data-ttu-id="cecb7-106">Přidejte do souboru *appSettings. JSON* připojovací řetězec:</span><span class="sxs-lookup"><span data-stu-id="cecb7-106">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="cecb7-107">Přidat balíčky NuGet a nástroje EF</span><span class="sxs-lookup"><span data-stu-id="cecb7-107">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="cecb7-108">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="cecb7-108">Register the database context</span></span>

<span data-ttu-id="cecb7-109">Do horní části `using` *Startup.cs*přidejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="cecb7-109">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="cecb7-110">Zaregistrujte kontext databáze pomocí kontejneru pro `Startup.ConfigureServices` [vkládání závislostí](xref:fundamentals/dependency-injection) v.</span><span class="sxs-lookup"><span data-stu-id="cecb7-110">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

<span data-ttu-id="cecb7-111">Sestavte projekt jako kontrolu chyb kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="cecb7-111">Build the project as a check for compiler errors.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cecb7-112">Přidejte následující `MvcMovieContext` třídu *modely* složky:</span><span class="sxs-lookup"><span data-stu-id="cecb7-112">Add the following `MvcMovieContext` class to the *Models* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

<span data-ttu-id="cecb7-113">Předchozí kód vytvoří `DbSet` vlastnost pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="cecb7-113">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="cecb7-114">V Entity Framework terminologii sada entit obvykle odpovídá tabulce databáze a entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="cecb7-114">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="cecb7-115">Přidat připojovací řetězec databáze</span><span class="sxs-lookup"><span data-stu-id="cecb7-115">Add a database connection string</span></span>

<span data-ttu-id="cecb7-116">Přidejte do souboru *appSettings. JSON* připojovací řetězec:</span><span class="sxs-lookup"><span data-stu-id="cecb7-116">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="cecb7-117">Přidat požadované balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="cecb7-117">Add required NuGet packages</span></span>

<span data-ttu-id="cecb7-118">Spusťte následující .NET Core CLI příkaz pro přidání SQLite a strategii. design do projektu:</span><span class="sxs-lookup"><span data-stu-id="cecb7-118">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

<span data-ttu-id="cecb7-119">`Microsoft.VisualStudio.Web.CodeGeneration.Design` Balíček je vyžadován pro generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="cecb7-119">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="cecb7-120">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="cecb7-120">Register the database context</span></span>

<span data-ttu-id="cecb7-121">Do horní části `using` *Startup.cs*přidejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="cecb7-121">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="cecb7-122">Zaregistrujte kontext databáze pomocí kontejneru pro `Startup.ConfigureServices` [vkládání závislostí](xref:fundamentals/dependency-injection) v.</span><span class="sxs-lookup"><span data-stu-id="cecb7-122">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="cecb7-123">Sestavte projekt jako kontrolu chyb.</span><span class="sxs-lookup"><span data-stu-id="cecb7-123">Build the project as a check for errors.</span></span>
::: moniker-end