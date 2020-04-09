::: moniker range=">= aspnetcore-3.0"

<a name="dc"></a>

<span data-ttu-id="14f66-101">Vytvořte složku *Data.*</span><span class="sxs-lookup"><span data-stu-id="14f66-101">Create a *Data* folder.</span></span>

<span data-ttu-id="14f66-102">Přidejte `MvcMovieContext` do složky *Data* následující třídu:</span><span class="sxs-lookup"><span data-stu-id="14f66-102">Add the following `MvcMovieContext` class to the *Data* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="14f66-103">Předchozí kód vytvoří `DbSet` vlastnost pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="14f66-103">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="14f66-104">V terminologii entity Framework sada entit obvykle odpovídá databázové tabulce a entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="14f66-104">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="14f66-105">Přidání připojovacího řetězce databáze</span><span class="sxs-lookup"><span data-stu-id="14f66-105">Add a database connection string</span></span>

<span data-ttu-id="14f66-106">Přidejte připojovací řetězec do souboru *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="14f66-106">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="14f66-107">Přidání balíčků NuGet a nástrojů EF</span><span class="sxs-lookup"><span data-stu-id="14f66-107">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="14f66-108">Registrace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="14f66-108">Register the database context</span></span>

<span data-ttu-id="14f66-109">V horní `using` části *Startup.cs*přidejte následující příkazy :</span><span class="sxs-lookup"><span data-stu-id="14f66-109">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="14f66-110">Zaregistrujte kontext databáze s kontejnerem `Startup.ConfigureServices`vkládání závislostí v [aplikaci](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="14f66-110">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

<span data-ttu-id="14f66-111">Sestavení projektu jako kontrola chyb kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="14f66-111">Build the project as a check for compiler errors.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="14f66-112">Přidejte `MvcMovieContext` do složky *Modely* následující třídu:</span><span class="sxs-lookup"><span data-stu-id="14f66-112">Add the following `MvcMovieContext` class to the *Models* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

<span data-ttu-id="14f66-113">Předchozí kód vytvoří `DbSet` vlastnost pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="14f66-113">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="14f66-114">V terminologii entity Framework sada entit obvykle odpovídá databázové tabulce a entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="14f66-114">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="14f66-115">Přidání připojovacího řetězce databáze</span><span class="sxs-lookup"><span data-stu-id="14f66-115">Add a database connection string</span></span>

<span data-ttu-id="14f66-116">Přidejte připojovací řetězec do souboru *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="14f66-116">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="14f66-117">Přidání požadovaných balíčků NuGet</span><span class="sxs-lookup"><span data-stu-id="14f66-117">Add required NuGet packages</span></span>

<span data-ttu-id="14f66-118">Spusťte následující příkaz .NET Core CLI a přidejte do projektu příkazy SQLite a CodeGeneration.Design:</span><span class="sxs-lookup"><span data-stu-id="14f66-118">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

<span data-ttu-id="14f66-119">Balíček `Microsoft.VisualStudio.Web.CodeGeneration.Design` je nutný pro lešení.</span><span class="sxs-lookup"><span data-stu-id="14f66-119">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="14f66-120">Registrace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="14f66-120">Register the database context</span></span>

<span data-ttu-id="14f66-121">V horní `using` části *Startup.cs*přidejte následující příkazy :</span><span class="sxs-lookup"><span data-stu-id="14f66-121">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="14f66-122">Zaregistrujte kontext databáze s kontejnerem `Startup.ConfigureServices`vkládání závislostí v [aplikaci](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="14f66-122">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="14f66-123">Sestavení projektu jako kontrola chyb.</span><span class="sxs-lookup"><span data-stu-id="14f66-123">Build the project as a check for errors.</span></span>
::: moniker-end