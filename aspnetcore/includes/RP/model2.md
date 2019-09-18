<a name="dc"></a>

### <a name="add-a-database-context-class"></a><span data-ttu-id="9d55e-101">Přidat třídu kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="9d55e-101">Add a database context class</span></span>

<span data-ttu-id="9d55e-102">V projektu RazorPagesMovie vytvořte novou složku s názvem *data*.</span><span class="sxs-lookup"><span data-stu-id="9d55e-102">In the RazorPagesMovie project, create a new folder called *Data*.</span></span> <span data-ttu-id="9d55e-103">Do složky `RazorPagesMovieContext` *data* přidejte následující třídu:</span><span class="sxs-lookup"><span data-stu-id="9d55e-103">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="9d55e-104">Předchozí kód vytvoří `DbSet` vlastnost pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="9d55e-104">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="9d55e-105">V Entity Framework terminologii sada entit obvykle odpovídá tabulce databáze a entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="9d55e-105">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="9d55e-106">Přidat připojovací řetězec databáze</span><span class="sxs-lookup"><span data-stu-id="9d55e-106">Add a database connection string</span></span>

<span data-ttu-id="9d55e-107">Do souboru *appSettings. JSON* přidejte připojovací řetězec, jak ukazuje následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="9d55e-107">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="9d55e-108">Přidat balíčky NuGet a nástroje EF</span><span class="sxs-lookup"><span data-stu-id="9d55e-108">Add NuGet packages and EF tools</span></span>

<span data-ttu-id="9d55e-109">Otevřete terminálu pro projekt RazorPagesMovie.</span><span class="sxs-lookup"><span data-stu-id="9d55e-109">Open a terminal for the RazorPagesMovie project.</span></span>  <span data-ttu-id="9d55e-110">Klikněte pravým tlačítkem myši na název projektu na panelu návrh nebo rozložení a přejděte na **nástroje > otevřít** v terminálu.</span><span class="sxs-lookup"><span data-stu-id="9d55e-110">Right click the project name in the design/layout bar and go to **Tools > Open** in Terminal.</span></span> <span data-ttu-id="9d55e-111">Spusťte následující příkazy .NET Core CLI v termínu:</span><span class="sxs-lookup"><span data-stu-id="9d55e-111">Run the following .NET Core CLI commands in the Termial:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SQLite --version 3.0.0-*
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
```

<span data-ttu-id="9d55e-112">Předchozí příkazy přidají do projektu Entity Framework Core nástroje pro rozhraní .NET CLI a několik balíčků.</span><span class="sxs-lookup"><span data-stu-id="9d55e-112">The preceding commands add Entity Framework Core Tools for the .NET CLI and several packages to the project.</span></span> <span data-ttu-id="9d55e-113">`Microsoft.VisualStudio.Web.CodeGeneration.Design` Balíček je vyžadován pro generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="9d55e-113">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="9d55e-114">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="9d55e-114">Register the database context</span></span>

<span data-ttu-id="9d55e-115">Do horní části `using` *Startup.cs*přidejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="9d55e-115">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="9d55e-116">Zaregistrujte kontext databáze pomocí kontejneru pro `Startup.ConfigureServices` [vkládání závislostí](xref:fundamentals/dependency-injection) v.</span><span class="sxs-lookup"><span data-stu-id="9d55e-116">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="9d55e-117">Přidat požadované balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="9d55e-117">Add required NuGet packages</span></span>

<span data-ttu-id="9d55e-118">Spusťte následující .NET Core CLI příkaz pro přidání SQLite a strategii. design do projektu:</span><span class="sxs-lookup"><span data-stu-id="9d55e-118">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
```

<span data-ttu-id="9d55e-119">`Microsoft.VisualStudio.Web.CodeGeneration.Design` Balíček je vyžadován pro generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="9d55e-119">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="9d55e-120">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="9d55e-120">Register the database context</span></span>

<span data-ttu-id="9d55e-121">Do horní části `using` *Startup.cs*přidejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="9d55e-121">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="9d55e-122">Zaregistrujte kontext databáze pomocí kontejneru pro `Startup.ConfigureServices` [vkládání závislostí](xref:fundamentals/dependency-injection) v.</span><span class="sxs-lookup"><span data-stu-id="9d55e-122">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="9d55e-123">Sestavte projekt jako kontrolu chyb.</span><span class="sxs-lookup"><span data-stu-id="9d55e-123">Build the project as a check for errors.</span></span>
::: moniker-end
