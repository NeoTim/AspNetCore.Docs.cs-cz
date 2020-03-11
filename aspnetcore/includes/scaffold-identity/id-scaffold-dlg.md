<span data-ttu-id="d2447-101">Spusťte generování uživatelského rozhraní identity:</span><span class="sxs-lookup"><span data-stu-id="d2447-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d2447-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2447-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d2447-103">Z **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt > **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="d2447-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="d2447-104">V levém podokně dialogového okna **Přidat generování uživatelského rozhraní** vyberte **Identita** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="d2447-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **ADD**.</span></span>
* <span data-ttu-id="d2447-105">V dialogovém okně **Přidat identitu** vyberte požadované možnosti.</span><span class="sxs-lookup"><span data-stu-id="d2447-105">In the **ADD Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="d2447-106">Vyberte existující stránku rozložení, nebo bude soubor rozložení přepsán nesprávným označením.</span><span class="sxs-lookup"><span data-stu-id="d2447-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="d2447-107">Například `~/Pages/Shared/_Layout.cshtml` pro Razor Pages `~/Views/Shared/_Layout.cshtml` pro projekty MVC</span><span class="sxs-lookup"><span data-stu-id="d2447-107">For example `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
  * <span data-ttu-id="d2447-108">Výběrem tlačítka **+** vytvořte novou **třídu datového kontextu**.</span><span class="sxs-lookup"><span data-stu-id="d2447-108">Select the **+** button to create a new **Data context class**.</span></span>
* <span data-ttu-id="d2447-109">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="d2447-109">Select **ADD**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d2447-110">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="d2447-110">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d2447-111">Pokud jste nenainstalovali dříve generátor ASP.NET Core, nainstalujte ho:</span><span class="sxs-lookup"><span data-stu-id="d2447-111">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="d2447-112">Přidejte do souboru projektu (\*. csproj) požadované odkazy na balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="d2447-112">Add required NuGet package references to the project (\*.csproj) file.</span></span> <span data-ttu-id="d2447-113">Spusťte následující příkaz v adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="d2447-113">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="d2447-114">Spusťte následující příkaz k výpisu možností generátor Identity:</span><span class="sxs-lookup"><span data-stu-id="d2447-114">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="d2447-115">Ve složce projektu spusťte generátor identity pomocí možností, které chcete.</span><span class="sxs-lookup"><span data-stu-id="d2447-115">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="d2447-116">Chcete-li například nastavit identitu pomocí výchozího uživatelského rozhraní a minimálního počtu souborů, spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d2447-116">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
