<span data-ttu-id="8b356-101">Spusťte následující příkazy .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="8b356-101">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="8b356-102">Do předchozích příkazů přidejte:</span><span class="sxs-lookup"><span data-stu-id="8b356-102">The preceding commands add:</span></span>

* <span data-ttu-id="8b356-103">[Nástroj pro generování uživatelského rozhraní ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="8b356-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="8b356-104">Entity Framework Core nástroje pro .NET Core CLI.</span><span class="sxs-lookup"><span data-stu-id="8b356-104">The Entity Framework Core Tools for the .NET Core CLI.</span></span>
* <span data-ttu-id="8b356-105">Zprostředkovatel SQLite EF Core, který nainstaluje EF Core balíček jako závislost.</span><span class="sxs-lookup"><span data-stu-id="8b356-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="8b356-106">Balíčky nutné pro generování uživatelského rozhraní `Microsoft.VisualStudio.Web.CodeGeneration.Design` : `Microsoft.EntityFrameworkCore.SqlServer`a.</span><span class="sxs-lookup"><span data-stu-id="8b356-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>
