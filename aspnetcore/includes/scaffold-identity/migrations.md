<span data-ttu-id="45950-101">Generovaný kód databáze identit vyžaduje [Entity Framework Core migrace](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="45950-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="45950-102">Vytvoření migrace a aktualizaci databáze.</span><span class="sxs-lookup"><span data-stu-id="45950-102">Create a migration and update the database.</span></span> <span data-ttu-id="45950-103">Například spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="45950-103">For example, run the following commands:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="45950-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="45950-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="45950-105">V sadě Visual Studio **Konzola správce balíčků**:</span><span class="sxs-lookup"><span data-stu-id="45950-105">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="45950-106">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="45950-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="45950-107">Parametr názvu "CreateIdentitySchema" `Add-Migration` příkazu je libovolný.</span><span class="sxs-lookup"><span data-stu-id="45950-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="45950-108">`"CreateIdentitySchema"`Popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="45950-108">`"CreateIdentitySchema"` describes the migration.</span></span>
