::: moniker range=">= aspnetcore-3.0"

Spusťte generování uživatelského rozhraní identity:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Z **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt > **Přidat** > **novou vygenerované položky**.
* V levém podokně dialogového okna **Přidat generování uživatelského rozhraní** vyberte **Identita** > **Přidat**.
* V dialogovém okně **Přidat identitu** vyberte požadované možnosti.
  * Vyberte existující stránku rozložení, aby nebyl soubor rozložení přepsán nesprávným označením. Když je vybrán existující soubor * \_ layout. cshtml* **, není** přepsán. Příklad:
    * `~/Pages/Shared/_Layout.cshtml`pro projekty serveru Razor Pages nebo Blazor s existující Razor Pagesou infrastrukturou
    * `~/Views/Shared/_Layout.cshtml`projekty MVC nebo projekty Blazor serveru se stávající infrastrukturou MVC
* Chcete-li použít existující kontext dat, vyberte alespoň jeden soubor, který chcete přepsat. Je nutné vybrat alespoň jeden soubor pro přidání kontextu dat.
  * Vyberte třídu datového kontextu.
  * Vyberte **Přidat**.
* Chcete-li vytvořit nový kontext uživatele a případně vytvořit vlastní třídu uživatelů pro identitu:
  * Vyberte **+** tlačítko pro vytvoření nové **třídy datového kontextu**. Přijměte výchozí hodnotu nebo zadejte třídu (například `MyApplication.Data.ApplicationDbContext` ).
  * Vyberte **Přidat**.

Poznámka: Pokud vytváříte nový uživatelský kontext, nemusíte vybírat soubor, který se má přepsat.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Pokud jste předtím ASP.NET Core lešení nenainstalovali, nainstalujte ho hned takto:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Přidejte požadované odkazy na balíček NuGet do souboru projektu (*. csproj*). V adresáři projektu spusťte následující příkazy:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

Spuštěním následujícího příkazu zobrazíte seznam možností generování identit:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

Ve složce projektu spusťte generátor identity pomocí možností, které chcete. Chcete-li například nastavit identitu pomocí výchozího uživatelského rozhraní a minimálního počtu souborů, spusťte následující příkaz. Použijte pro svůj kontext databáze správný plně kvalifikovaný název:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

PowerShell používá jako oddělovač příkazů středník. Při používání PowerShellu v seznamu souborů oddělte středníkem a seznam souborů umístěte do dvojitých uvozovek. Příklad:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Pokud spustíte generování identit identity bez zadání `--files` příznaku nebo `--useDefaultUI` příznaku, v projektu se vytvoří všechny dostupné stránky uživatelského rozhraní identity.

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Spusťte generování uživatelského rozhraní identity:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Z **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt > **Přidat** > **novou vygenerované položky**.
* V levém podokně dialogového okna **Přidat generování uživatelského rozhraní** vyberte **Identita** > **Přidat**.
* V dialogovém okně **Přidat identitu** vyberte požadované možnosti.
  * Vyberte existující stránku rozložení, nebo bude soubor rozložení přepsán nesprávným označením. Když je vybrán existující soubor * \_ layout. cshtml* **, není** přepsán. Příklad:
    * `~/Pages/Shared/_Layout.cshtml`pro Razor Pages
    * `~/Views/Shared/_Layout.cshtml`pro projekty MVC
* Chcete-li použít existující kontext dat, vyberte alespoň jeden soubor, který chcete přepsat. Je nutné vybrat alespoň jeden soubor pro přidání kontextu dat.
  * Vyberte třídu datového kontextu.
  * Vyberte **Přidat**.
* Chcete-li vytvořit nový kontext uživatele a případně vytvořit vlastní třídu uživatelů pro identitu:
  * Vyberte **+** tlačítko pro vytvoření nové **třídy datového kontextu**. Přijměte výchozí hodnotu nebo zadejte třídu (například `MyApplication.Data.ApplicationDbContext` ).
  * Vyberte **Přidat**.

Poznámka: Pokud vytváříte nový uživatelský kontext, nemusíte vybírat soubor, který se má přepsat.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Pokud jste předtím ASP.NET Core lešení nenainstalovali, nainstalujte ho hned takto:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Přidejte odkaz na balíček do [Microsoft. VisualStudio. Web. strategii. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) do souboru projektu (*. csproj*). V adresáři projektu spusťte následující příkazy:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Spuštěním následujícího příkazu zobrazíte seznam možností generování identit:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

Ve složce projektu spusťte generátor identity pomocí možností, které chcete. Chcete-li například nastavit identitu pomocí výchozího uživatelského rozhraní a minimálního počtu souborů, spusťte následující příkaz. Použijte pro svůj kontext databáze správný plně kvalifikovaný název:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

PowerShell používá jako oddělovač příkazů středník. Při používání PowerShellu v seznamu souborů oddělte středníkem a seznam souborů umístěte do dvojitých uvozovek. Příklad:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Pokud spustíte generování identit identity bez zadání `--files` příznaku nebo `--useDefaultUI` příznaku, v projektu se vytvoří všechny dostupné stránky uživatelského rozhraní identity.

---

::: moniker-end
