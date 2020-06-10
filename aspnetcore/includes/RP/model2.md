<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>Přidat balíčky NuGet a nástroje EF

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a>Přidat třídu kontextu databáze

V projektu RazorPagesMovie vytvořte novou složku s názvem *data*. `RazorPagesMovieContext`Do složky *data* přidejte následující třídu:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

Předchozí kód vytvoří `DbSet` vlastnost pro sadu entit. V Entity Framework terminologii sada entit obvykle odpovídá tabulce databáze a entita odpovídá řádku v tabulce. Kód se nebude kompilovat, dokud nebudou přidány závislosti v pozdějším kroku.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Přidat připojovací řetězec databáze

Do souboru *appSettings. JSON* přidejte připojovací řetězec, jak ukazuje následující zvýrazněný kód:

::: moniker range=">= aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Registrace kontextu databáze

Do horní části `using` *Startup.cs*přidejte následující příkazy:

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Zaregistrujte kontext databáze pomocí kontejneru pro [vkládání závislostí](xref:fundamentals/dependency-injection) v `Startup.ConfigureServices` .

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a>Přidat požadované balíčky NuGet

Spusťte následující .NET Core CLI příkaz pro přidání SQLite a strategii. design do projektu:

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
```

`Microsoft.VisualStudio.Web.CodeGeneration.Design`Balíček je vyžadován pro generování uživatelského rozhraní.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Registrace kontextu databáze

Do horní části `using` *Startup.cs*přidejte následující příkazy:

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

Zaregistrujte kontext databáze pomocí kontejneru pro [vkládání závislostí](xref:fundamentals/dependency-injection) v `Startup.ConfigureServices` .

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Sestavte projekt jako kontrolu chyb.

::: moniker-end
