<a name="dc"></a>

### <a name="add-a-database-context-class"></a><span data-ttu-id="aae1f-101">Přidání třídy kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="aae1f-101">Add a database context class</span></span>

<span data-ttu-id="aae1f-102">V projektu RazorPagesMovie vytvořte novou složku s názvem *Data*.</span><span class="sxs-lookup"><span data-stu-id="aae1f-102">In the RazorPagesMovie project, create a new folder called *Data*.</span></span> <span data-ttu-id="aae1f-103">Přidejte `RazorPagesMovieContext` do složky *Data* následující třídu:</span><span class="sxs-lookup"><span data-stu-id="aae1f-103">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="aae1f-104">Předchozí kód vytvoří `DbSet` vlastnost pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="aae1f-104">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="aae1f-105">V terminologii entity Framework sada entit obvykle odpovídá databázové tabulce a entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="aae1f-105">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="aae1f-106">Kód se nezkompiluje, dokud nebudou přidány závislosti v pozdějším kroku.</span><span class="sxs-lookup"><span data-stu-id="aae1f-106">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="aae1f-107">Přidání připojovacího řetězce databáze</span><span class="sxs-lookup"><span data-stu-id="aae1f-107">Add a database connection string</span></span>

<span data-ttu-id="aae1f-108">Přidejte připojovací řetězec do souboru *appsettings.json,* jak je znázorněno v následujícím zvýrazněném kódu:</span><span class="sxs-lookup"><span data-stu-id="aae1f-108">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="aae1f-109">Přidání balíčků NuGet a nástrojů EF</span><span class="sxs-lookup"><span data-stu-id="aae1f-109">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="aae1f-110">Registrace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="aae1f-110">Register the database context</span></span>

<span data-ttu-id="aae1f-111">V horní `using` části *Startup.cs*přidejte následující příkazy :</span><span class="sxs-lookup"><span data-stu-id="aae1f-111">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="aae1f-112">Zaregistrujte kontext databáze s kontejnerem `Startup.ConfigureServices`vkládání závislostí v [aplikaci](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="aae1f-112">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="aae1f-113">Přidání požadovaných balíčků NuGet</span><span class="sxs-lookup"><span data-stu-id="aae1f-113">Add required NuGet packages</span></span>

<span data-ttu-id="aae1f-114">Spusťte následující příkaz .NET Core CLI a přidejte do projektu příkazy SQLite a CodeGeneration.Design:</span><span class="sxs-lookup"><span data-stu-id="aae1f-114">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
```

<span data-ttu-id="aae1f-115">Balíček `Microsoft.VisualStudio.Web.CodeGeneration.Design` je nutný pro lešení.</span><span class="sxs-lookup"><span data-stu-id="aae1f-115">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="aae1f-116">Registrace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="aae1f-116">Register the database context</span></span>

<span data-ttu-id="aae1f-117">V horní `using` části *Startup.cs*přidejte následující příkazy :</span><span class="sxs-lookup"><span data-stu-id="aae1f-117">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="aae1f-118">Zaregistrujte kontext databáze s kontejnerem `Startup.ConfigureServices`vkládání závislostí v [aplikaci](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="aae1f-118">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="aae1f-119">Sestavení projektu jako kontrola chyb.</span><span class="sxs-lookup"><span data-stu-id="aae1f-119">Build the project as a check for errors.</span></span>

::: moniker-end
