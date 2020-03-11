Spusťte generování uživatelského rozhraní identity:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Z **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt > **Přidat** > **novou vygenerované položky**.
* V levém podokně dialogového okna **Přidat generování uživatelského rozhraní** vyberte **Identita** > **Přidat**.
* V dialogovém okně **Přidat identitu** vyberte požadované možnosti.
  * Vyberte existující stránku rozložení, nebo bude soubor rozložení přepsán nesprávným označením. Například `~/Pages/Shared/_Layout.cshtml` pro Razor Pages `~/Views/Shared/_Layout.cshtml` pro projekty MVC
  * Výběrem tlačítka **+** vytvořte novou **třídu datového kontextu**.
* Vyberte **Přidat**.

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

Ve složce projektu spusťte generátor identity pomocí možností, které chcete. Chcete-li například nastavit identitu pomocí výchozího uživatelského rozhraní a minimálního počtu souborů, spusťte následující příkaz:

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
