<span data-ttu-id="06ab4-101">Generovaný kód databáze identit vyžaduje [Entity Framework Core migrace](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="06ab4-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="06ab4-102">Vytvoření migrace a aktualizaci databáze.</span><span class="sxs-lookup"><span data-stu-id="06ab4-102">Create a migration and update the database.</span></span> <span data-ttu-id="06ab4-103">Například spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="06ab4-103">For example, run the following commands:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="06ab4-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06ab4-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="06ab4-105">V **konzole správce balíčků**sady Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="06ab4-105">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="06ab4-106">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="06ab4-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="06ab4-107">Parametr názvu "CreateIdentitySchema" příkazu `Add-Migration` je libovolný.</span><span class="sxs-lookup"><span data-stu-id="06ab4-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="06ab4-108">`"CreateIdentitySchema"` popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="06ab4-108">`"CreateIdentitySchema"` describes the migration.</span></span>
