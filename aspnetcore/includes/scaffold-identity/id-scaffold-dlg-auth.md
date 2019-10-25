Spusťte generování uživatelského rozhraní identity:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

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

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Pokud jste předtím ASP.NET Core lešení nenainstalovali, nainstalujte ho hned takto:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Přidejte odkaz na balíček do [Microsoft. VisualStudio. Web. strategii. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) do souboru projektu (\*. csproj). V adresáři projektu spusťte následující příkaz:

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
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

PowerShell používá jako oddělovač příkazů středník. Při používání PowerShellu v seznamu souborů oddělte středníkem a seznam souborů umístěte do dvojitých uvozovek. Příklad:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Pokud spustíte generování identit identity bez zadání příznaku `--files` nebo příznaku `--useDefaultUI`, v projektu se vytvoří všechny dostupné stránky uživatelského rozhraní identity.

---
