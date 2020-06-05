<span data-ttu-id="04e28-101">Spusťte generování uživatelského rozhraní identity:</span><span class="sxs-lookup"><span data-stu-id="04e28-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="04e28-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04e28-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="04e28-103">Z **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt > **Přidat**  >  **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="04e28-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="04e28-104">V levém podokně dialogového okna **Přidat novou vygenerované položky** vyberte **Identita**  >  **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="04e28-104">From the left pane of the **Add New Scaffolded Item** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="04e28-105">V dialogovém okně **Přidat identitu** vyberte požadované možnosti.</span><span class="sxs-lookup"><span data-stu-id="04e28-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="04e28-106">Vyberte existující stránku rozložení, nebo bude soubor rozložení přepsán pomocí nesprávného kódu:</span><span class="sxs-lookup"><span data-stu-id="04e28-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup:</span></span>
    * <span data-ttu-id="04e28-107">`~/Pages/Shared/_Layout.cshtml`pro Razor Pages</span><span class="sxs-lookup"><span data-stu-id="04e28-107">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="04e28-108">`~/Views/Shared/_Layout.cshtml`pro projekty MVC</span><span class="sxs-lookup"><span data-stu-id="04e28-108">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
    * <span data-ttu-id="04e28-109">Aplikace Blazor serveru vytvořené pomocí šablony serveru Blazor ( `blazorserver` ) nejsou ve výchozím nastavení nakonfigurované pro Razor Pages nebo MVC.</span><span class="sxs-lookup"><span data-stu-id="04e28-109">Blazor Server apps created from the Blazor Server template (`blazorserver`) aren't configured for Razor Pages or MVC by default.</span></span> <span data-ttu-id="04e28-110">Ponechte položku stránky rozložení prázdnou.</span><span class="sxs-lookup"><span data-stu-id="04e28-110">Leave the layout page entry blank.</span></span>
  * <span data-ttu-id="04e28-111">Vyberte **+** tlačítko pro vytvoření nové **třídy datového kontextu**.</span><span class="sxs-lookup"><span data-stu-id="04e28-111">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="04e28-112">Přijměte výchozí hodnotu nebo zadejte třídu (například `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="04e28-112">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
* <span data-ttu-id="04e28-113">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="04e28-113">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="04e28-114">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="04e28-114">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="04e28-115">Pokud jste předtím ASP.NET Core lešení nenainstalovali, nainstalujte ho hned takto:</span><span class="sxs-lookup"><span data-stu-id="04e28-115">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="04e28-116">Přidejte požadované odkazy na balíček NuGet do souboru projektu (*. csproj*).</span><span class="sxs-lookup"><span data-stu-id="04e28-116">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="04e28-117">V adresáři projektu spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="04e28-117">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="04e28-118">Spuštěním následujícího příkazu zobrazíte seznam možností generování identit:</span><span class="sxs-lookup"><span data-stu-id="04e28-118">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="04e28-119">Ve složce projektu spusťte generátor identity pomocí možností, které chcete.</span><span class="sxs-lookup"><span data-stu-id="04e28-119">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="04e28-120">Chcete-li například nastavit identitu pomocí výchozího uživatelského rozhraní a minimálního počtu souborů, spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="04e28-120">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
