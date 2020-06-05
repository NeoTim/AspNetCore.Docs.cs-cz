Spusťte generování uživatelského rozhraní identity:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Z **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt > **Přidat**  >  **novou vygenerované položky**.
* V levém podokně dialogového okna **Přidat novou vygenerované položky** vyberte **Identita**  >  **Přidat**.
* V dialogovém okně **Přidat identitu** vyberte požadované možnosti.
  * Vyberte existující stránku rozložení, nebo bude soubor rozložení přepsán pomocí nesprávného kódu:
    * `~/Pages/Shared/_Layout.cshtml`pro Razor Pages
    * `~/Views/Shared/_Layout.cshtml`pro projekty MVC
    * Aplikace Blazor serveru vytvořené pomocí šablony serveru Blazor ( `blazorserver` ) nejsou ve výchozím nastavení nakonfigurované pro Razor Pages nebo MVC. Ponechte položku stránky rozložení prázdnou.
  * Vyberte **+** tlačítko pro vytvoření nové **třídy datového kontextu**. Přijměte výchozí hodnotu nebo zadejte třídu (například `MyApplication.Data.ApplicationDbContext` ).
* Vyberte **Přidat**.

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

Ve složce projektu spusťte generátor identity pomocí možností, které chcete. Chcete-li například nastavit identitu pomocí výchozího uživatelského rozhraní a minimálního počtu souborů, spusťte následující příkaz:

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
