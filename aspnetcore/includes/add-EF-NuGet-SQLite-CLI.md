<span data-ttu-id="e49a7-101">Spusťte následující příkazy příkazového příkazu .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="e49a7-101">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="e49a7-102">Předchozí příkazy přidat:</span><span class="sxs-lookup"><span data-stu-id="e49a7-102">The preceding commands add:</span></span>

* <span data-ttu-id="e49a7-103">Nástroj [lešení generátoru aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="e49a7-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="e49a7-104">Core nástroje entity framework u rozhraní SE klis.Net Core CLI.</span><span class="sxs-lookup"><span data-stu-id="e49a7-104">The Entity Framework Core Tools for the .NET Core CLI.</span></span>
* <span data-ttu-id="e49a7-105">Ef Core SQLite zprostředkovatele, který nainstaluje balíček EF Core jako závislost.</span><span class="sxs-lookup"><span data-stu-id="e49a7-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="e49a7-106">Balíky potřebné pro lešení: `Microsoft.VisualStudio.Web.CodeGeneration.Design` a `Microsoft.EntityFrameworkCore.SqlServer`.</span><span class="sxs-lookup"><span data-stu-id="e49a7-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="e49a7-107">Pokyny pro konfiguraci více prostředí, která umožňuje aplikaci konfigurovat <xref:fundamentals/environments#environment-based-startup-class-and-methods>kontexty databáze podle prostředí, naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="e49a7-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM.md)]