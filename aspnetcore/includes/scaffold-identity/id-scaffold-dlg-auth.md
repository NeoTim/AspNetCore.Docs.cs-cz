::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c5cce-101">Spusťte generování uživatelského rozhraní identity:</span><span class="sxs-lookup"><span data-stu-id="c5cce-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c5cce-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5cce-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c5cce-103">Z **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt > **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="c5cce-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="c5cce-104">V levém podokně dialogového okna **Přidat generování uživatelského rozhraní** vyberte **Identita** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="c5cce-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="c5cce-105">V dialogovém okně **Přidat identitu** vyberte požadované možnosti.</span><span class="sxs-lookup"><span data-stu-id="c5cce-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="c5cce-106">Vyberte existující stránku rozložení, aby nebyl soubor rozložení přepsán nesprávným označením.</span><span class="sxs-lookup"><span data-stu-id="c5cce-106">Select your existing layout page so your layout file isn't overwritten with incorrect markup.</span></span> <span data-ttu-id="c5cce-107">Když je vybrán existující soubor \* \_ layout. cshtml\* **, není** přepsán.</span><span class="sxs-lookup"><span data-stu-id="c5cce-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="c5cce-108">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c5cce-108">For example:</span></span>
    * <span data-ttu-id="c5cce-109">`~/Pages/Shared/_Layout.cshtml`pro projekty serveru Razor Pages nebo Blazor s existující Razor Pagesou infrastrukturou</span><span class="sxs-lookup"><span data-stu-id="c5cce-109">`~/Pages/Shared/_Layout.cshtml` for Razor Pages or Blazor Server projects with existing Razor Pages infrastructure</span></span>
    * <span data-ttu-id="c5cce-110">`~/Views/Shared/_Layout.cshtml`projekty MVC nebo projekty Blazor serveru se stávající infrastrukturou MVC</span><span class="sxs-lookup"><span data-stu-id="c5cce-110">`~/Views/Shared/_Layout.cshtml` for MVC projects or Blazor Server projects with existing MVC infrastructure</span></span>
* <span data-ttu-id="c5cce-111">Chcete-li použít existující kontext dat, vyberte alespoň jeden soubor, který chcete přepsat.</span><span class="sxs-lookup"><span data-stu-id="c5cce-111">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="c5cce-112">Je nutné vybrat alespoň jeden soubor pro přidání kontextu dat.</span><span class="sxs-lookup"><span data-stu-id="c5cce-112">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="c5cce-113">Vyberte třídu datového kontextu.</span><span class="sxs-lookup"><span data-stu-id="c5cce-113">Select your data context class.</span></span>
  * <span data-ttu-id="c5cce-114">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="c5cce-114">Select **Add**.</span></span>
* <span data-ttu-id="c5cce-115">Chcete-li vytvořit nový kontext uživatele a případně vytvořit vlastní třídu uživatelů pro identitu:</span><span class="sxs-lookup"><span data-stu-id="c5cce-115">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="c5cce-116">Vyberte **+** tlačítko pro vytvoření nové **třídy datového kontextu**.</span><span class="sxs-lookup"><span data-stu-id="c5cce-116">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="c5cce-117">Přijměte výchozí hodnotu nebo zadejte třídu (například `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="c5cce-117">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="c5cce-118">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="c5cce-118">Select **Add**.</span></span>

<span data-ttu-id="c5cce-119">Poznámka: Pokud vytváříte nový uživatelský kontext, nemusíte vybírat soubor, který se má přepsat.</span><span class="sxs-lookup"><span data-stu-id="c5cce-119">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c5cce-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c5cce-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c5cce-121">Pokud jste předtím ASP.NET Core lešení nenainstalovali, nainstalujte ho hned takto:</span><span class="sxs-lookup"><span data-stu-id="c5cce-121">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="c5cce-122">Přidejte požadované odkazy na balíček NuGet do souboru projektu (*. csproj*).</span><span class="sxs-lookup"><span data-stu-id="c5cce-122">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="c5cce-123">V adresáři projektu spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="c5cce-123">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="c5cce-124">Spuštěním následujícího příkazu zobrazíte seznam možností generování identit:</span><span class="sxs-lookup"><span data-stu-id="c5cce-124">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="c5cce-125">Ve složce projektu spusťte generátor identity pomocí možností, které chcete.</span><span class="sxs-lookup"><span data-stu-id="c5cce-125">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="c5cce-126">Chcete-li například nastavit identitu pomocí výchozího uživatelského rozhraní a minimálního počtu souborů, spusťte následující příkaz.</span><span class="sxs-lookup"><span data-stu-id="c5cce-126">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="c5cce-127">Použijte pro svůj kontext databáze správný plně kvalifikovaný název:</span><span class="sxs-lookup"><span data-stu-id="c5cce-127">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="c5cce-128">PowerShell používá jako oddělovač příkazů středník.</span><span class="sxs-lookup"><span data-stu-id="c5cce-128">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="c5cce-129">Při používání PowerShellu v seznamu souborů oddělte středníkem a seznam souborů umístěte do dvojitých uvozovek.</span><span class="sxs-lookup"><span data-stu-id="c5cce-129">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="c5cce-130">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c5cce-130">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="c5cce-131">Pokud spustíte generování identit identity bez zadání `--files` příznaku nebo `--useDefaultUI` příznaku, v projektu se vytvoří všechny dostupné stránky uživatelského rozhraní identity.</span><span class="sxs-lookup"><span data-stu-id="c5cce-131">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c5cce-132">Spusťte generování uživatelského rozhraní identity:</span><span class="sxs-lookup"><span data-stu-id="c5cce-132">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c5cce-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5cce-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c5cce-134">Z **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt > **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="c5cce-134">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="c5cce-135">V levém podokně dialogového okna **Přidat generování uživatelského rozhraní** vyberte **Identita** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="c5cce-135">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="c5cce-136">V dialogovém okně **Přidat identitu** vyberte požadované možnosti.</span><span class="sxs-lookup"><span data-stu-id="c5cce-136">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="c5cce-137">Vyberte existující stránku rozložení, nebo bude soubor rozložení přepsán nesprávným označením.</span><span class="sxs-lookup"><span data-stu-id="c5cce-137">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="c5cce-138">Když je vybrán existující soubor \* \_ layout. cshtml\* **, není** přepsán.</span><span class="sxs-lookup"><span data-stu-id="c5cce-138">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="c5cce-139">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c5cce-139">For example:</span></span>
    * <span data-ttu-id="c5cce-140">`~/Pages/Shared/_Layout.cshtml`pro Razor Pages</span><span class="sxs-lookup"><span data-stu-id="c5cce-140">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="c5cce-141">`~/Views/Shared/_Layout.cshtml`pro projekty MVC</span><span class="sxs-lookup"><span data-stu-id="c5cce-141">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="c5cce-142">Chcete-li použít existující kontext dat, vyberte alespoň jeden soubor, který chcete přepsat.</span><span class="sxs-lookup"><span data-stu-id="c5cce-142">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="c5cce-143">Je nutné vybrat alespoň jeden soubor pro přidání kontextu dat.</span><span class="sxs-lookup"><span data-stu-id="c5cce-143">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="c5cce-144">Vyberte třídu datového kontextu.</span><span class="sxs-lookup"><span data-stu-id="c5cce-144">Select your data context class.</span></span>
  * <span data-ttu-id="c5cce-145">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="c5cce-145">Select **Add**.</span></span>
* <span data-ttu-id="c5cce-146">Chcete-li vytvořit nový kontext uživatele a případně vytvořit vlastní třídu uživatelů pro identitu:</span><span class="sxs-lookup"><span data-stu-id="c5cce-146">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="c5cce-147">Vyberte **+** tlačítko pro vytvoření nové **třídy datového kontextu**.</span><span class="sxs-lookup"><span data-stu-id="c5cce-147">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="c5cce-148">Přijměte výchozí hodnotu nebo zadejte třídu (například `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="c5cce-148">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="c5cce-149">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="c5cce-149">Select **Add**.</span></span>

<span data-ttu-id="c5cce-150">Poznámka: Pokud vytváříte nový uživatelský kontext, nemusíte vybírat soubor, který se má přepsat.</span><span class="sxs-lookup"><span data-stu-id="c5cce-150">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c5cce-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c5cce-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c5cce-152">Pokud jste předtím ASP.NET Core lešení nenainstalovali, nainstalujte ho hned takto:</span><span class="sxs-lookup"><span data-stu-id="c5cce-152">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="c5cce-153">Přidejte odkaz na balíček do [Microsoft. VisualStudio. Web. strategii. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) do souboru projektu (*. csproj*).</span><span class="sxs-lookup"><span data-stu-id="c5cce-153">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project file (*.csproj*).</span></span> <span data-ttu-id="c5cce-154">V adresáři projektu spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="c5cce-154">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="c5cce-155">Spuštěním následujícího příkazu zobrazíte seznam možností generování identit:</span><span class="sxs-lookup"><span data-stu-id="c5cce-155">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="c5cce-156">Ve složce projektu spusťte generátor identity pomocí možností, které chcete.</span><span class="sxs-lookup"><span data-stu-id="c5cce-156">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="c5cce-157">Chcete-li například nastavit identitu pomocí výchozího uživatelského rozhraní a minimálního počtu souborů, spusťte následující příkaz.</span><span class="sxs-lookup"><span data-stu-id="c5cce-157">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="c5cce-158">Použijte pro svůj kontext databáze správný plně kvalifikovaný název:</span><span class="sxs-lookup"><span data-stu-id="c5cce-158">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="c5cce-159">PowerShell používá jako oddělovač příkazů středník.</span><span class="sxs-lookup"><span data-stu-id="c5cce-159">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="c5cce-160">Při používání PowerShellu v seznamu souborů oddělte středníkem a seznam souborů umístěte do dvojitých uvozovek.</span><span class="sxs-lookup"><span data-stu-id="c5cce-160">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="c5cce-161">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c5cce-161">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="c5cce-162">Pokud spustíte generování identit identity bez zadání `--files` příznaku nebo `--useDefaultUI` příznaku, v projektu se vytvoří všechny dostupné stránky uživatelského rozhraní identity.</span><span class="sxs-lookup"><span data-stu-id="c5cce-162">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end
