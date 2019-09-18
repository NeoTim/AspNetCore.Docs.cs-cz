Generovaný kód databáze identit vyžaduje [Entity Framework Core migrace](/ef/core/managing-schemas/migrations/). Vytvoření migrace a aktualizaci databáze. Například spusťte následující příkazy:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

V sadě Visual Studio **Konzola správce balíčků**:

```powershell
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

Parametr názvu "CreateIdentitySchema" `Add-Migration` příkazu je libovolný. `"CreateIdentitySchema"`Popisuje migraci.
