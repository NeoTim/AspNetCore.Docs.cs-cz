Spusťte následující příkazy .NET Core CLI:

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

Do předchozích příkazů přidejte:

* [Nástroj pro generování uživatelského rozhraní ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).
* Entity Framework Core nástroje pro .NET Core CLI.
* Zprostředkovatel SQLite EF Core, který nainstaluje EF Core balíček jako závislost.
* Balíčky nutné pro generování uživatelského rozhraní `Microsoft.VisualStudio.Web.CodeGeneration.Design` : `Microsoft.EntityFrameworkCore.SqlServer`a.
