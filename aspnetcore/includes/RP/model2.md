<a name="dc"></a>

### <a name="add-a-database-context-class"></a>Přidat třídu kontextu databáze

V projektu RazorPagesMovie vytvořte novou složku s názvem *data*. Do složky `RazorPagesMovieContext` *data* přidejte následující třídu:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Předchozí kód vytvoří `DbSet` vlastnost pro sadu entit. V Entity Framework terminologii sada entit obvykle odpovídá tabulce databáze a entita odpovídá řádku v tabulce.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Přidat připojovací řetězec databáze

Do souboru *appSettings. JSON* přidejte připojovací řetězec, jak ukazuje následující zvýrazněný kód:

::: moniker range=">= aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-required-nuget-packages"></a>Přidat požadované balíčky NuGet

Otevřete terminálu pro projekt RazorPagesMovie.  Klikněte pravým tlačítkem myši na název projektu na panelu návrh nebo rozložení a přejděte na **nástroje > otevřít** v terminálu. V Termech spusťte následující příkazy .NET Core CLI. Příkazy přidat SQLite, Entity Framework Core a strategii. design do projektu:

```console
dotnet add package Microsoft.EntityFrameworkCore.SQLite --version 3.0.0-*
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
```

`Microsoft.VisualStudio.Web.CodeGeneration.Design` Balíček je vyžadován pro generování uživatelského rozhraní.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Zaregistrujte kontext databáze

Do horní části `using` *Startup.cs*přidejte následující příkazy:

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

Zaregistrujte kontext databáze pomocí kontejneru pro `Startup.ConfigureServices` [vkládání závislostí](xref:fundamentals/dependency-injection) v.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a>Přidat požadované balíčky NuGet

Spusťte následující .NET Core CLI příkaz pro přidání SQLite a strategii. design do projektu:

```console
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design

```

`Microsoft.VisualStudio.Web.CodeGeneration.Design` Balíček je vyžadován pro generování uživatelského rozhraní.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Zaregistrujte kontext databáze

Do horní části `using` *Startup.cs*přidejte následující příkazy:

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

Zaregistrujte kontext databáze pomocí kontejneru pro `Startup.ConfigureServices` [vkládání závislostí](xref:fundamentals/dependency-injection) v.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Sestavte projekt jako kontrolu chyb.
::: moniker-end
