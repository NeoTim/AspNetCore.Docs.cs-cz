::: moniker range=">= aspnetcore-3.0"

<a name="dc"></a>

Vytvořte složku *dat* .

Do složky *data* přidejte následující třídu `MvcMovieContext`:  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

Předchozí kód vytvoří vlastnost `DbSet` pro sadu entit. V Entity Framework terminologii sada entit obvykle odpovídá tabulce databáze a entita odpovídá řádku v tabulce.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Přidat připojovací řetězec databáze

Přidejte do souboru *appSettings. JSON* připojovací řetězec:

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a>Přidat balíčky NuGet a nástroje EF

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Zaregistrujte kontext databáze

Do horní části *Startup.cs*přidejte následující příkazy `using`:

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Zaregistrujte kontext databáze pomocí kontejneru pro [vkládání závislostí](xref:fundamentals/dependency-injection) v `Startup.ConfigureServices`.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

Sestavte projekt jako kontrolu chyb kompilátoru.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Do složky *modely* přidejte následující třídu `MvcMovieContext`:  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

Předchozí kód vytvoří vlastnost `DbSet` pro sadu entit. V Entity Framework terminologii sada entit obvykle odpovídá tabulce databáze a entita odpovídá řádku v tabulce.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Přidat připojovací řetězec databáze

Přidejte do souboru *appSettings. JSON* připojovací řetězec:

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a>Přidat požadované balíčky NuGet

Spusťte následující .NET Core CLI příkaz pro přidání SQLite a strategii. design do projektu:

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

Pro generování uživatelského rozhraní se vyžaduje balíček `Microsoft.VisualStudio.Web.CodeGeneration.Design`.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Zaregistrujte kontext databáze

Do horní části *Startup.cs*přidejte následující příkazy `using`:

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

Zaregistrujte kontext databáze pomocí kontejneru pro [vkládání závislostí](xref:fundamentals/dependency-injection) v `Startup.ConfigureServices`.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Sestavte projekt jako kontrolu chyb.
::: moniker-end