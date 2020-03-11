::: moniker range=">= aspnetcore-3.0"

Spusťte generování uživatelského rozhraní identity:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Z **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt > **Přidat** > **novou vygenerované položky**.
* V levém podokně dialogového okna **Přidat generování uživatelského rozhraní** vyberte **Identita** > **Přidat**.
* V dialogovém okně **Přidat identitu** vyberte požadované možnosti.
  * Vyberte existující stránku rozložení, nebo bude soubor rozložení přepsán nesprávným označením. Když je vybrán existující soubor *\_layout. cshtml* **, není** přepsán.

 Například: `~/Pages/Shared/_Layout.cshtml` pro Razor Pages `~/Views/Shared/_Layout.cshtml` pro projekty MVC
* Chcete-li použít existující kontext dat, vyberte alespoň jeden soubor, který chcete přepsat. Je nutné vybrat alespoň jeden soubor pro přidání kontextu dat.
  * Vyberte třídu datového kontextu.
  * Vyberte **Přidat**.
* Chcete-li vytvořit nový kontext uživatele a případně vytvořit vlastní třídu uživatelů pro identitu:
  * Výběrem tlačítka **+** vytvořte novou **třídu datového kontextu**.
  * Vyberte **Přidat**.

Poznámka: Pokud vytváříte nový uživatelský kontext, nemusíte vybírat soubor, který se má přepsat.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Pokud jste nenainstalovali dříve generátor ASP.NET Core, nainstalujte ho:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Přidejte do souboru projektu (\*. csproj) požadované odkazy na balíček NuGet. Spusťte následující příkaz v adresáři projektu:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

Spusťte následující příkaz k výpisu možností generátor Identity:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

Ve složce projektu spusťte generátor identity pomocí možností, které chcete. Chcete-li například nastavit identitu pomocí výchozího uživatelského rozhraní a minimálního počtu souborů, spusťte následující příkaz. Použijte pro svůj kontext databáze správný plně kvalifikovaný název:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

PowerShell používá jako oddělovač příkazů středník. Při používání PowerShellu v seznamu souborů oddělte středníkem a seznam souborů umístěte do dvojitých uvozovek. Příklad:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Pokud spustíte generování identit identity bez zadání příznaku `--files` nebo příznaku `--useDefaultUI`, v projektu se vytvoří všechny dostupné stránky uživatelského rozhraní identity.

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Spusťte generování uživatelského rozhraní identity:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Z **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt > **Přidat** > **novou vygenerované položky**.
* V levém podokně dialogového okna **Přidat generování uživatelského rozhraní** vyberte **Identita** > **Přidat**.
* V dialogovém okně **Přidat identitu** vyberte požadované možnosti.
  * Vyberte existující stránku rozložení, nebo bude soubor rozložení přepsán nesprávným označením. Když je vybrán existující soubor *\_layout. cshtml* **, není** přepsán.

 Například: `~/Pages/Shared/_Layout.cshtml` pro Razor Pages `~/Views/Shared/_Layout.cshtml` pro projekty MVC
* Chcete-li použít existující kontext dat, vyberte alespoň jeden soubor, který chcete přepsat. Je nutné vybrat alespoň jeden soubor pro přidání kontextu dat.
  * Vyberte třídu datového kontextu.
  * Vyberte **Přidat**.
* Chcete-li vytvořit nový kontext uživatele a případně vytvořit vlastní třídu uživatelů pro identitu:
  * Výběrem tlačítka **+** vytvořte novou **třídu datového kontextu**.
  * Vyberte **Přidat**.

Poznámka: Pokud vytváříte nový uživatelský kontext, nemusíte vybírat soubor, který se má přepsat.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Pokud jste nenainstalovali dříve generátor ASP.NET Core, nainstalujte ho:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Přidejte odkaz na balíček do [Microsoft. VisualStudio. Web. strategii. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) do souboru projektu (\*. csproj). Spusťte následující příkaz v adresáři projektu:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Spusťte následující příkaz k výpisu možností generátor Identity:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

Ve složce projektu spusťte generátor identity pomocí možností, které chcete. Chcete-li například nastavit identitu pomocí výchozího uživatelského rozhraní a minimálního počtu souborů, spusťte následující příkaz. Použijte pro svůj kontext databáze správný plně kvalifikovaný název:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

PowerShell používá jako oddělovač příkazů středník. Při používání PowerShellu v seznamu souborů oddělte středníkem a seznam souborů umístěte do dvojitých uvozovek. Příklad:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Pokud spustíte generování identit identity bez zadání příznaku `--files` nebo příznaku `--useDefaultUI`, v projektu se vytvoří všechny dostupné stránky uživatelského rozhraní identity.

---

::: moniker-end