Spusťte následující příkazy příkazového příkazu .NET Core CLI:

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

Předchozí příkazy přidat:

* Nástroj [lešení generátoru aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).
* Core nástroje entity framework u rozhraní SE klis.Net Core CLI.
* Ef Core SQLite zprostředkovatele, který nainstaluje balíček EF Core jako závislost.
* Balíky potřebné pro lešení: `Microsoft.VisualStudio.Web.CodeGeneration.Design` a `Microsoft.EntityFrameworkCore.SqlServer`.

Pokyny pro konfiguraci více prostředí, která umožňuje aplikaci konfigurovat <xref:fundamentals/environments#environment-based-startup-class-and-methods>kontexty databáze podle prostředí, naleznete v tématu .

[!INCLUDE[](~/includes/scaffoldTFM.md)]