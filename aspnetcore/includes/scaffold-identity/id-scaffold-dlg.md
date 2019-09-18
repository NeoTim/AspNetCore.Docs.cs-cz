Spusťte generování uživatelského rozhraní identity:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Z **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt > **přidat** > **novou vygenerovanou položku**.
* V levém podokně **přidat vygenerované uživatelské rozhraní** dialogového okna, vyberte **Identity** > **přidat**.
* V dialogovém okně **Přidat identitu** vyberte požadované možnosti.
  * Vyberte existující stránku rozložení, nebo bude soubor rozložení přepsán nesprávným označením. Například `~/Pages/Shared/_Layout.cshtml` pro Razor Pages `~/Views/Shared/_Layout.cshtml` pro projekty MVC
  * Vyberte **+** tlačítko pro vytvoření nového **třída kontextu dat**.
* Vyberte **přidat**.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

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

Ve složce projektu spusťte generátor identity pomocí možností, které chcete. Chcete-li například nastavit identitu pomocí výchozího uživatelského rozhraní a minimálního počtu souborů, spusťte následující příkaz:

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
