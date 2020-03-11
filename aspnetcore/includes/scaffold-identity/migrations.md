Generovaný kód databáze identit vyžaduje [Entity Framework Core migrace](/ef/core/managing-schemas/migrations/). Vytvoření migrace a aktualizaci databáze. Například spusťte následující příkazy:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

V **konzole správce balíčků**sady Visual Studio:

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

Parametr názvu "CreateIdentitySchema" příkazu `Add-Migration` je libovolný. `"CreateIdentitySchema"` popisuje migraci.
