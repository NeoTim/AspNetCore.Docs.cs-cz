::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="051ac-101">Spusťte generování uživatelského rozhraní identity:</span><span class="sxs-lookup"><span data-stu-id="051ac-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="051ac-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="051ac-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="051ac-103">Z **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt > **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="051ac-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="051ac-104">V levém podokně dialogového okna **Přidat generování uživatelského rozhraní** vyberte **Identita** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="051ac-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="051ac-105">V dialogovém okně **Přidat identitu** vyberte požadované možnosti.</span><span class="sxs-lookup"><span data-stu-id="051ac-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="051ac-106">Vyberte existující stránku rozložení, nebo bude soubor rozložení přepsán nesprávným označením.</span><span class="sxs-lookup"><span data-stu-id="051ac-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="051ac-107">Když je vybrán existující soubor *\_layout. cshtml* **, není** přepsán.</span><span class="sxs-lookup"><span data-stu-id="051ac-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span>

 <span data-ttu-id="051ac-108">Například: `~/Pages/Shared/_Layout.cshtml` pro Razor Pages `~/Views/Shared/_Layout.cshtml` pro projekty MVC</span><span class="sxs-lookup"><span data-stu-id="051ac-108">For example: `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="051ac-109">Chcete-li použít existující kontext dat, vyberte alespoň jeden soubor, který chcete přepsat.</span><span class="sxs-lookup"><span data-stu-id="051ac-109">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="051ac-110">Je nutné vybrat alespoň jeden soubor pro přidání kontextu dat.</span><span class="sxs-lookup"><span data-stu-id="051ac-110">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="051ac-111">Vyberte třídu datového kontextu.</span><span class="sxs-lookup"><span data-stu-id="051ac-111">Select your data context class.</span></span>
  * <span data-ttu-id="051ac-112">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="051ac-112">Select **Add**.</span></span>
* <span data-ttu-id="051ac-113">Chcete-li vytvořit nový kontext uživatele a případně vytvořit vlastní třídu uživatelů pro identitu:</span><span class="sxs-lookup"><span data-stu-id="051ac-113">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="051ac-114">Výběrem tlačítka **+** vytvořte novou **třídu datového kontextu**.</span><span class="sxs-lookup"><span data-stu-id="051ac-114">Select the **+** button to create a new **Data context class**.</span></span>
  * <span data-ttu-id="051ac-115">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="051ac-115">Select **Add**.</span></span>

<span data-ttu-id="051ac-116">Poznámka: Pokud vytváříte nový uživatelský kontext, nemusíte vybírat soubor, který se má přepsat.</span><span class="sxs-lookup"><span data-stu-id="051ac-116">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="051ac-117">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="051ac-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="051ac-118">Pokud jste nenainstalovali dříve generátor ASP.NET Core, nainstalujte ho:</span><span class="sxs-lookup"><span data-stu-id="051ac-118">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="051ac-119">Přidejte do souboru projektu (\*. csproj) požadované odkazy na balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="051ac-119">Add required NuGet package references to the project (\*.csproj) file.</span></span> <span data-ttu-id="051ac-120">Spusťte následující příkaz v adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="051ac-120">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="051ac-121">Spusťte následující příkaz k výpisu možností generátor Identity:</span><span class="sxs-lookup"><span data-stu-id="051ac-121">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="051ac-122">Ve složce projektu spusťte generátor identity pomocí možností, které chcete.</span><span class="sxs-lookup"><span data-stu-id="051ac-122">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="051ac-123">Chcete-li například nastavit identitu pomocí výchozího uživatelského rozhraní a minimálního počtu souborů, spusťte následující příkaz.</span><span class="sxs-lookup"><span data-stu-id="051ac-123">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="051ac-124">Použijte pro svůj kontext databáze správný plně kvalifikovaný název:</span><span class="sxs-lookup"><span data-stu-id="051ac-124">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="051ac-125">PowerShell používá jako oddělovač příkazů středník.</span><span class="sxs-lookup"><span data-stu-id="051ac-125">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="051ac-126">Při používání PowerShellu v seznamu souborů oddělte středníkem a seznam souborů umístěte do dvojitých uvozovek.</span><span class="sxs-lookup"><span data-stu-id="051ac-126">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="051ac-127">Příklad:</span><span class="sxs-lookup"><span data-stu-id="051ac-127">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="051ac-128">Pokud spustíte generování identit identity bez zadání příznaku `--files` nebo příznaku `--useDefaultUI`, v projektu se vytvoří všechny dostupné stránky uživatelského rozhraní identity.</span><span class="sxs-lookup"><span data-stu-id="051ac-128">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="051ac-129">Spusťte generování uživatelského rozhraní identity:</span><span class="sxs-lookup"><span data-stu-id="051ac-129">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="051ac-130">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="051ac-130">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="051ac-131">Z **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt > **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="051ac-131">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="051ac-132">V levém podokně dialogového okna **Přidat generování uživatelského rozhraní** vyberte **Identita** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="051ac-132">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="051ac-133">V dialogovém okně **Přidat identitu** vyberte požadované možnosti.</span><span class="sxs-lookup"><span data-stu-id="051ac-133">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="051ac-134">Vyberte existující stránku rozložení, nebo bude soubor rozložení přepsán nesprávným označením.</span><span class="sxs-lookup"><span data-stu-id="051ac-134">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="051ac-135">Když je vybrán existující soubor *\_layout. cshtml* **, není** přepsán.</span><span class="sxs-lookup"><span data-stu-id="051ac-135">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span>

 <span data-ttu-id="051ac-136">Například: `~/Pages/Shared/_Layout.cshtml` pro Razor Pages `~/Views/Shared/_Layout.cshtml` pro projekty MVC</span><span class="sxs-lookup"><span data-stu-id="051ac-136">For example: `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="051ac-137">Chcete-li použít existující kontext dat, vyberte alespoň jeden soubor, který chcete přepsat.</span><span class="sxs-lookup"><span data-stu-id="051ac-137">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="051ac-138">Je nutné vybrat alespoň jeden soubor pro přidání kontextu dat.</span><span class="sxs-lookup"><span data-stu-id="051ac-138">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="051ac-139">Vyberte třídu datového kontextu.</span><span class="sxs-lookup"><span data-stu-id="051ac-139">Select your data context class.</span></span>
  * <span data-ttu-id="051ac-140">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="051ac-140">Select **Add**.</span></span>
* <span data-ttu-id="051ac-141">Chcete-li vytvořit nový kontext uživatele a případně vytvořit vlastní třídu uživatelů pro identitu:</span><span class="sxs-lookup"><span data-stu-id="051ac-141">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="051ac-142">Výběrem tlačítka **+** vytvořte novou **třídu datového kontextu**.</span><span class="sxs-lookup"><span data-stu-id="051ac-142">Select the **+** button to create a new **Data context class**.</span></span>
  * <span data-ttu-id="051ac-143">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="051ac-143">Select **Add**.</span></span>

<span data-ttu-id="051ac-144">Poznámka: Pokud vytváříte nový uživatelský kontext, nemusíte vybírat soubor, který se má přepsat.</span><span class="sxs-lookup"><span data-stu-id="051ac-144">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="051ac-145">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="051ac-145">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="051ac-146">Pokud jste nenainstalovali dříve generátor ASP.NET Core, nainstalujte ho:</span><span class="sxs-lookup"><span data-stu-id="051ac-146">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="051ac-147">Přidejte odkaz na balíček do [Microsoft. VisualStudio. Web. strategii. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) do souboru projektu (\*. csproj).</span><span class="sxs-lookup"><span data-stu-id="051ac-147">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (\*.csproj) file.</span></span> <span data-ttu-id="051ac-148">Spusťte následující příkaz v adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="051ac-148">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="051ac-149">Spusťte následující příkaz k výpisu možností generátor Identity:</span><span class="sxs-lookup"><span data-stu-id="051ac-149">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="051ac-150">Ve složce projektu spusťte generátor identity pomocí možností, které chcete.</span><span class="sxs-lookup"><span data-stu-id="051ac-150">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="051ac-151">Chcete-li například nastavit identitu pomocí výchozího uživatelského rozhraní a minimálního počtu souborů, spusťte následující příkaz.</span><span class="sxs-lookup"><span data-stu-id="051ac-151">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="051ac-152">Použijte pro svůj kontext databáze správný plně kvalifikovaný název:</span><span class="sxs-lookup"><span data-stu-id="051ac-152">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="051ac-153">PowerShell používá jako oddělovač příkazů středník.</span><span class="sxs-lookup"><span data-stu-id="051ac-153">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="051ac-154">Při používání PowerShellu v seznamu souborů oddělte středníkem a seznam souborů umístěte do dvojitých uvozovek.</span><span class="sxs-lookup"><span data-stu-id="051ac-154">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="051ac-155">Příklad:</span><span class="sxs-lookup"><span data-stu-id="051ac-155">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="051ac-156">Pokud spustíte generování identit identity bez zadání příznaku `--files` nebo příznaku `--useDefaultUI`, v projektu se vytvoří všechny dostupné stránky uživatelského rozhraní identity.</span><span class="sxs-lookup"><span data-stu-id="051ac-156">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end