<a name="dc"></a>

### <a name="add-a-database-context-class"></a>Přidání třídy kontextu databáze

V projektu RazorPagesMovie vytvořte novou složku s názvem *Data*. Přidejte `RazorPagesMovieContext` do složky *Data* následující třídu:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

Předchozí kód vytvoří `DbSet` vlastnost pro sadu entit. V terminologii entity Framework sada entit obvykle odpovídá databázové tabulce a entita odpovídá řádku v tabulce. Kód se nezkompiluje, dokud nebudou přidány závislosti v pozdějším kroku.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Přidání připojovacího řetězce databáze

Přidejte připojovací řetězec do souboru *appsettings.json,* jak je znázorněno v následujícím zvýrazněném kódu:

::: moniker range=">= aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a>Přidání balíčků NuGet a nástrojů EF

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Registrace kontextu databáze

V horní `using` části *Startup.cs*přidejte následující příkazy :

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Zaregistrujte kontext databáze s kontejnerem `Startup.ConfigureServices`vkládání závislostí v [aplikaci](xref:fundamentals/dependency-injection) .

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a>Přidání požadovaných balíčků NuGet

Spusťte následující příkaz .NET Core CLI a přidejte do projektu příkazy SQLite a CodeGeneration.Design:

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
```

Balíček `Microsoft.VisualStudio.Web.CodeGeneration.Design` je nutný pro lešení.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Registrace kontextu databáze

V horní `using` části *Startup.cs*přidejte následující příkazy :

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

Zaregistrujte kontext databáze s kontejnerem `Startup.ConfigureServices`vkládání závislostí v [aplikaci](xref:fundamentals/dependency-injection) .

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Sestavení projektu jako kontrola chyb.

::: moniker-end
