::: moniker range=">= aspnetcore-3.0"

<a name="dc"></a>

Vytvořte složku *Data.*

Přidejte `MvcMovieContext` do složky *Data* následující třídu:  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

Předchozí kód vytvoří `DbSet` vlastnost pro sadu entit. V terminologii entity Framework sada entit obvykle odpovídá databázové tabulce a entita odpovídá řádku v tabulce.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Přidání připojovacího řetězce databáze

Přidejte připojovací řetězec do souboru *appsettings.json:*

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a>Přidání balíčků NuGet a nástrojů EF

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Registrace kontextu databáze

V horní `using` části *Startup.cs*přidejte následující příkazy :

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Zaregistrujte kontext databáze s kontejnerem `Startup.ConfigureServices`vkládání závislostí v [aplikaci](xref:fundamentals/dependency-injection) .

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

Sestavení projektu jako kontrola chyb kompilátoru.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Přidejte `MvcMovieContext` do složky *Modely* následující třídu:  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

Předchozí kód vytvoří `DbSet` vlastnost pro sadu entit. V terminologii entity Framework sada entit obvykle odpovídá databázové tabulce a entita odpovídá řádku v tabulce.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Přidání připojovacího řetězce databáze

Přidejte připojovací řetězec do souboru *appsettings.json:*

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a>Přidání požadovaných balíčků NuGet

Spusťte následující příkaz .NET Core CLI a přidejte do projektu příkazy SQLite a CodeGeneration.Design:

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

Balíček `Microsoft.VisualStudio.Web.CodeGeneration.Design` je nutný pro lešení.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Registrace kontextu databáze

V horní `using` části *Startup.cs*přidejte následující příkazy :

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

Zaregistrujte kontext databáze s kontejnerem `Startup.ConfigureServices`vkládání závislostí v [aplikaci](xref:fundamentals/dependency-injection) .

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Sestavení projektu jako kontrola chyb.
::: moniker-end