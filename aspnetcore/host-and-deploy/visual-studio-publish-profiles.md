---
<span data-ttu-id="aef0b-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="aef0b-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aef0b-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-102">'Blazor'</span></span>
- <span data-ttu-id="aef0b-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aef0b-103">'Identity'</span></span>
- <span data-ttu-id="aef0b-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aef0b-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="aef0b-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-105">'Razor'</span></span>
- <span data-ttu-id="aef0b-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="aef0b-106">'SignalR' uid:</span></span> 

---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a><span data-ttu-id="aef0b-107">Publikační profily sady Visual Studio (. pubxml) pro nasazení aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aef0b-107">Visual Studio publish profiles (.pubxml) for ASP.NET Core app deployment</span></span>

<span data-ttu-id="aef0b-108">Od [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="aef0b-108">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="aef0b-109">Tento dokument se zaměřuje na použití sady Visual Studio 2019 nebo novější k vytváření a používání profilů publikování.</span><span class="sxs-lookup"><span data-stu-id="aef0b-109">This document focuses on using Visual Studio 2019 or later to create and use publish profiles.</span></span> <span data-ttu-id="aef0b-110">Profily publikování vytvořené pomocí sady Visual Studio lze použít s nástroji MSBuild a sadou Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="aef0b-110">The publish profiles created with Visual Studio can be used with MSBuild and Visual Studio.</span></span> <span data-ttu-id="aef0b-111">Pokyny k publikování do Azure najdete v tématu <xref:tutorials/publish-to-azure-webapp-using-vs> .</span><span class="sxs-lookup"><span data-stu-id="aef0b-111">For instructions on publishing to Azure, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

<span data-ttu-id="aef0b-112">`dotnet new mvc`Příkaz vytvoří soubor projektu obsahující následující [ \<Project> element](/visualstudio/msbuild/project-element-msbuild)na kořenové úrovni:</span><span class="sxs-lookup"><span data-stu-id="aef0b-112">The `dotnet new mvc` command produces a project file containing the following root-level [\<Project> element](/visualstudio/msbuild/project-element-msbuild):</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

<span data-ttu-id="aef0b-113">Předchozí `<Project>` `Sdk` atribut elementu importuje [vlastnosti](/visualstudio/msbuild/msbuild-properties) a [cíle](/visualstudio/msbuild/msbuild-targets) nástroje MSBuild z *$ (MSBuildSDKsPath) \Microsoft.NET.SDK.Web\Sdk\Sdk.props* a *$ (MSBuildSDKsPath) \Microsoft.NET.SDK.Web\Sdk\Sdk.targets*, v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="aef0b-113">The preceding `<Project>` element's `Sdk` attribute imports the MSBuild [properties](/visualstudio/msbuild/msbuild-properties) and [targets](/visualstudio/msbuild/msbuild-targets) from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* and *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectively.</span></span> <span data-ttu-id="aef0b-114">Výchozím umístěním pro `$(MSBuildSDKsPath)` (se sadou Visual Studio 2019 Enterprise) je složka *% ProgramFiles (x86)% \ Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* .</span><span class="sxs-lookup"><span data-stu-id="aef0b-114">The default location for `$(MSBuildSDKsPath)` (with Visual Studio 2019 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="aef0b-115">`Microsoft.NET.Sdk.Web`([Web SDK](xref:razor-pages/web-sdk)) závisí na jiných sadách SDK, včetně `Microsoft.NET.Sdk` ([.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) a `Microsoft.NET.Sdk.Razor` ([ Razor SDK](xref:razor-pages/sdk)).</span><span class="sxs-lookup"><span data-stu-id="aef0b-115">`Microsoft.NET.Sdk.Web` ([Web SDK](xref:razor-pages/web-sdk)) depends on other SDKs, including `Microsoft.NET.Sdk` ([.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) and `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span></span> <span data-ttu-id="aef0b-116">Naimportují se vlastnosti a cíle nástroje MSBuild přidružené ke každé závislé sadě SDK.</span><span class="sxs-lookup"><span data-stu-id="aef0b-116">The MSBuild properties and targets associated with each dependent SDK are imported.</span></span> <span data-ttu-id="aef0b-117">Cíle publikování importují odpovídající sadu cílů na základě používané metody publikování.</span><span class="sxs-lookup"><span data-stu-id="aef0b-117">Publish targets import the appropriate set of targets based on the publish method used.</span></span>

<span data-ttu-id="aef0b-118">Když nástroj MSBuild nebo Visual Studio načte projekt, dojde k následujícím akcím na nejvyšší úrovni:</span><span class="sxs-lookup"><span data-stu-id="aef0b-118">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="aef0b-119">Sestavit projekt</span><span class="sxs-lookup"><span data-stu-id="aef0b-119">Build project</span></span>
* <span data-ttu-id="aef0b-120">Soubory COMPUTE k publikování</span><span class="sxs-lookup"><span data-stu-id="aef0b-120">Compute files to publish</span></span>
* <span data-ttu-id="aef0b-121">Publikovat soubory do cílového umístění</span><span class="sxs-lookup"><span data-stu-id="aef0b-121">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="aef0b-122">Položky výpočetního projektu</span><span class="sxs-lookup"><span data-stu-id="aef0b-122">Compute project items</span></span>

<span data-ttu-id="aef0b-123">Po načtení projektu jsou vypočítány [položky projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) (soubory).</span><span class="sxs-lookup"><span data-stu-id="aef0b-123">When the project is loaded, the [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) (files) are computed.</span></span> <span data-ttu-id="aef0b-124">Typ položky určuje, jak je soubor zpracován.</span><span class="sxs-lookup"><span data-stu-id="aef0b-124">The item type determines how the file is processed.</span></span> <span data-ttu-id="aef0b-125">Ve výchozím nastavení jsou soubory *. cs* zahrnuty v `Compile` seznamu položek.</span><span class="sxs-lookup"><span data-stu-id="aef0b-125">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="aef0b-126">Soubory v `Compile` seznamu položek jsou kompilovány.</span><span class="sxs-lookup"><span data-stu-id="aef0b-126">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="aef0b-127">`Content`Seznam položek obsahuje soubory, které jsou publikovány kromě výstupů sestavení.</span><span class="sxs-lookup"><span data-stu-id="aef0b-127">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="aef0b-128">Ve výchozím nastavení jsou soubory, které odpovídají vzorům `wwwroot\**` , `**\*.config` a `**\*.json` zahrnuty v `Content` seznamu položek.</span><span class="sxs-lookup"><span data-stu-id="aef0b-128">By default, files matching the patterns `wwwroot\**`, `**\*.config`, and `**\*.json` are included in the `Content` item list.</span></span> <span data-ttu-id="aef0b-129">Například `wwwroot\**` [vzor expanze názvů](https://gruntjs.com/configuring-tasks#globbing-patterns) odpovídá všem souborům ve složce *wwwroot* a jejích podsložkách.</span><span class="sxs-lookup"><span data-stu-id="aef0b-129">For example, the `wwwroot\**` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder and its subfolders.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="aef0b-130">Sada [Web SDK](xref:razor-pages/web-sdk) importuje [ Razor sadu SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="aef0b-130">The [Web SDK](xref:razor-pages/web-sdk) imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="aef0b-131">V důsledku toho jsou soubory, které odpovídají vzorům, `**\*.cshtml` `**\*.razor` zahrnuty také v `Content` seznamu položek.</span><span class="sxs-lookup"><span data-stu-id="aef0b-131">As a result, files matching the patterns `**\*.cshtml` and `**\*.razor` are also included in the `Content` item list.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="aef0b-132">Sada [Web SDK](xref:razor-pages/web-sdk) importuje [ Razor sadu SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="aef0b-132">The [Web SDK](xref:razor-pages/web-sdk) imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="aef0b-133">V důsledku toho jsou soubory, které odpovídají `**\*.cshtml` vzoru, zahrnuty také v `Content` seznamu položek.</span><span class="sxs-lookup"><span data-stu-id="aef0b-133">As a result, files matching the `**\*.cshtml` pattern are also included in the `Content` item list.</span></span>

::: moniker-end

<span data-ttu-id="aef0b-134">Chcete-li explicitně přidat soubor do seznamu publikování, přidejte ho přímo do souboru *. csproj* , jak je znázorněno v části [zahrnuté soubory](#include-files) .</span><span class="sxs-lookup"><span data-stu-id="aef0b-134">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in the [Include Files](#include-files) section.</span></span>

<span data-ttu-id="aef0b-135">Při výběru tlačítka **publikovat** v aplikaci Visual Studio nebo při publikování z příkazového řádku:</span><span class="sxs-lookup"><span data-stu-id="aef0b-135">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="aef0b-136">Jsou vypočítány vlastnosti nebo položky (soubory, které jsou nutné k sestavení).</span><span class="sxs-lookup"><span data-stu-id="aef0b-136">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="aef0b-137">**Pouze Visual Studio**: obnoveny balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="aef0b-137">**Visual Studio only**: NuGet packages are restored.</span></span> <span data-ttu-id="aef0b-138">(Obnovení musí být uživatelem v rozhraní příkazového řádku explicitní.)</span><span class="sxs-lookup"><span data-stu-id="aef0b-138">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="aef0b-139">Sestavení projektu.</span><span class="sxs-lookup"><span data-stu-id="aef0b-139">The project builds.</span></span>
* <span data-ttu-id="aef0b-140">Položky publikování jsou vypočítány (soubory, které jsou nutné k publikování).</span><span class="sxs-lookup"><span data-stu-id="aef0b-140">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="aef0b-141">Projekt je publikován (vypočítané soubory jsou zkopírovány do umístění pro publikování).</span><span class="sxs-lookup"><span data-stu-id="aef0b-141">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="aef0b-142">Pokud projekt ASP.NET Core odkazuje `Microsoft.NET.Sdk.Web` v souboru projektu, soubor *App_offline. htm* se umístí do kořenového adresáře webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="aef0b-142">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="aef0b-143">Když je soubor přítomen, modul ASP.NET Core aplikaci korektně ukončí a během nasazování zachová soubor *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="aef0b-143">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="aef0b-144">Další informace najdete v referenčních informacích k [konfiguraci modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="aef0b-144">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="aef0b-145">Základní publikování z příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="aef0b-145">Basic command-line publishing</span></span>

<span data-ttu-id="aef0b-146">Publikování z příkazového řádku funguje na všech platformách podporovaných .NET Core a nevyžaduje sadu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="aef0b-146">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="aef0b-147">V následujících příkladech se příkaz [dotnet publish](/dotnet/core/tools/dotnet-publish) .NET Core CLI spouští z adresáře projektu (který obsahuje soubor *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="aef0b-147">In the following examples, the .NET Core CLI's [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="aef0b-148">Pokud složka projektu není aktuálním pracovním adresářem, explicitně předejte cestu k souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="aef0b-148">If the project folder isn't the current working directory, explicitly pass in the project file path.</span></span> <span data-ttu-id="aef0b-149">Příklad:</span><span class="sxs-lookup"><span data-stu-id="aef0b-149">For example:</span></span>

```dotnetcli
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="aef0b-150">Spuštěním následujících příkazů vytvořte a publikujte webovou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="aef0b-150">Run the following commands to create and publish a web app:</span></span>

```dotnetcli
dotnet new mvc
dotnet publish
```

<span data-ttu-id="aef0b-151">`dotnet publish`Příkaz vytvoří variaci následujícího výstupu:</span><span class="sxs-lookup"><span data-stu-id="aef0b-151">The `dotnet publish` command produces a variation of the following output:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

<span data-ttu-id="aef0b-152">Výchozí formát složky pro publikování je \*bin\Debug \\ {target Framework MONIKER} \publish \\ \*.</span><span class="sxs-lookup"><span data-stu-id="aef0b-152">The default publish folder format is *bin\Debug\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="aef0b-153">Například \*bin\Debug\netcoreapp2.2\publish \\ \*.</span><span class="sxs-lookup"><span data-stu-id="aef0b-153">For example, *bin\Debug\netcoreapp2.2\publish\\*.</span></span>

<span data-ttu-id="aef0b-154">Následující příkaz určuje `Release` sestavení a adresář pro publikování:</span><span class="sxs-lookup"><span data-stu-id="aef0b-154">The following command specifies a `Release` build and the publishing directory:</span></span>

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="aef0b-155">`dotnet publish`Příkaz volá MSBuild, který vyvolá `Publish` cíl.</span><span class="sxs-lookup"><span data-stu-id="aef0b-155">The `dotnet publish` command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="aef0b-156">Všechny parametry předané do `dotnet publish` jsou předány do nástroje MSBuild.</span><span class="sxs-lookup"><span data-stu-id="aef0b-156">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="aef0b-157">`-c`Parametry a se `-o` mapují do `Configuration` vlastností a v `OutputPath` uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="aef0b-157">The `-c` and `-o` parameters map to MSBuild's `Configuration` and `OutputPath` properties, respectively.</span></span>

<span data-ttu-id="aef0b-158">Vlastnosti nástroje MSBuild lze předat pomocí některého z následujících formátů:</span><span class="sxs-lookup"><span data-stu-id="aef0b-158">MSBuild properties can be passed using either of the following formats:</span></span>

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="aef0b-159">Například následující příkaz publikuje `Release` sestavení do síťové sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="aef0b-159">For example, the following command publishes a `Release` build to a network share.</span></span> <span data-ttu-id="aef0b-160">Sdílená síťová složka je určena s použitím lomítka (*//R8/*) a funguje na všech podporovaných platformách .NET Core.</span><span class="sxs-lookup"><span data-stu-id="aef0b-160">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

<span data-ttu-id="aef0b-161">Potvrďte, že publikovaná aplikace pro nasazení není spuštěná.</span><span class="sxs-lookup"><span data-stu-id="aef0b-161">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="aef0b-162">Soubory ve složce pro *publikování* jsou při spuštění aplikace zamčené.</span><span class="sxs-lookup"><span data-stu-id="aef0b-162">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="aef0b-163">Nasazení nelze provést, protože uzamčené soubory nelze zkopírovat.</span><span class="sxs-lookup"><span data-stu-id="aef0b-163">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="aef0b-164">Profily publikování</span><span class="sxs-lookup"><span data-stu-id="aef0b-164">Publish profiles</span></span>

<span data-ttu-id="aef0b-165">V této části se používá Visual Studio 2019 nebo novější k vytvoření profilu publikování.</span><span class="sxs-lookup"><span data-stu-id="aef0b-165">This section uses Visual Studio 2019 or later to create a publishing profile.</span></span> <span data-ttu-id="aef0b-166">Po vytvoření profilu, publikování ze sady Visual Studio nebo příkazového řádku je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="aef0b-166">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span> <span data-ttu-id="aef0b-167">Publikování profilů může zjednodušit proces publikování a může existovat libovolný počet profilů.</span><span class="sxs-lookup"><span data-stu-id="aef0b-167">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span>

<span data-ttu-id="aef0b-168">Vytvořte profil publikování v aplikaci Visual Studio tak, že vyberete jednu z následujících cest:</span><span class="sxs-lookup"><span data-stu-id="aef0b-168">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="aef0b-169">V **Průzkumník řešení** klikněte pravým tlačítkem na projekt a vyberte **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="aef0b-169">Right-click the project in **Solution Explorer** and select **Publish**.</span></span>
* <span data-ttu-id="aef0b-170">V nabídce **sestavení** vyberte **publikovat {Project Name}** .</span><span class="sxs-lookup"><span data-stu-id="aef0b-170">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="aef0b-171">Zobrazí se karta **publikovat** stránky možnosti aplikace.</span><span class="sxs-lookup"><span data-stu-id="aef0b-171">The **Publish** tab of the app capabilities page is displayed.</span></span> <span data-ttu-id="aef0b-172">Pokud projekt nemá profil publikování, zobrazí se stránka **vybrat cíl publikování** .</span><span class="sxs-lookup"><span data-stu-id="aef0b-172">If the project lacks a publish profile, the **Pick a publish target** page is displayed.</span></span> <span data-ttu-id="aef0b-173">Budete požádáni o výběr jednoho z následujících cílů publikování:</span><span class="sxs-lookup"><span data-stu-id="aef0b-173">You're asked to select one of the following publish targets:</span></span>

* <span data-ttu-id="aef0b-174">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="aef0b-174">Azure App Service</span></span>
* <span data-ttu-id="aef0b-175">Azure App Service v systému Linux</span><span class="sxs-lookup"><span data-stu-id="aef0b-175">Azure App Service on Linux</span></span>
* <span data-ttu-id="aef0b-176">Azure Virtual Machines</span><span class="sxs-lookup"><span data-stu-id="aef0b-176">Azure Virtual Machines</span></span>
* <span data-ttu-id="aef0b-177">Složka</span><span class="sxs-lookup"><span data-stu-id="aef0b-177">Folder</span></span>
* <span data-ttu-id="aef0b-178">IIS, FTP, Nasazení webu (pro libovolný webový server)</span><span class="sxs-lookup"><span data-stu-id="aef0b-178">IIS, FTP, Web Deploy (for any web server)</span></span>
* <span data-ttu-id="aef0b-179">Importovat profil</span><span class="sxs-lookup"><span data-stu-id="aef0b-179">Import Profile</span></span>

<span data-ttu-id="aef0b-180">K určení nejvhodnějšího cíle publikování si přečtěte téma [co jsou možnosti publikování správné](/visualstudio/ide/not-in-toc/web-publish-options).</span><span class="sxs-lookup"><span data-stu-id="aef0b-180">To determine the most appropriate publish target, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="aef0b-181">Když je vybraná možnost cíl publikování **složky** , zadejte cestu ke složce pro uložení publikovaných prostředků.</span><span class="sxs-lookup"><span data-stu-id="aef0b-181">When the **Folder** publish target is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="aef0b-182">Výchozí cesta ke složce je \*bin \\ {konfigurace projektu} \\ {cílový MONIKER rozhraní} \publish \\ \*.</span><span class="sxs-lookup"><span data-stu-id="aef0b-182">The default folder path is *bin\\{PROJECT CONFIGURATION}\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="aef0b-183">Například \*bin\Release\netcoreapp2.2\publish \\ \*.</span><span class="sxs-lookup"><span data-stu-id="aef0b-183">For example, *bin\Release\netcoreapp2.2\publish\\*.</span></span> <span data-ttu-id="aef0b-184">Kliknutím na tlačítko **vytvořit profil** dokončíte.</span><span class="sxs-lookup"><span data-stu-id="aef0b-184">Select the **Create Profile** button to finish.</span></span>

<span data-ttu-id="aef0b-185">Po vytvoření profilu publikování se změní obsah karty **publikovat** .</span><span class="sxs-lookup"><span data-stu-id="aef0b-185">Once a publish profile is created, the **Publish** tab's content changes.</span></span> <span data-ttu-id="aef0b-186">Nově vytvořený profil se zobrazí v rozevíracím seznamu.</span><span class="sxs-lookup"><span data-stu-id="aef0b-186">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="aef0b-187">V rozevíracím seznamu vyberte **vytvořit nový profil** a vytvořte další nový profil.</span><span class="sxs-lookup"><span data-stu-id="aef0b-187">Below the drop-down list, select **Create new profile** to create another new profile.</span></span>

<span data-ttu-id="aef0b-188">Nástroj pro publikování sady Visual Studio vytvoří soubor. *PublishProfiles/{Profile}. pubxml* MSBuild popisující profil publikování.</span><span class="sxs-lookup"><span data-stu-id="aef0b-188">Visual Studio's publish tool produces a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file describing the publish profile.</span></span> <span data-ttu-id="aef0b-189">Soubor *. pubxml* :</span><span class="sxs-lookup"><span data-stu-id="aef0b-189">The *.pubxml* file:</span></span>

* <span data-ttu-id="aef0b-190">Obsahuje nastavení konfigurace publikování a je využito procesem publikování.</span><span class="sxs-lookup"><span data-stu-id="aef0b-190">Contains publish configuration settings and is consumed by the publishing process.</span></span>
* <span data-ttu-id="aef0b-191">Lze upravit pro přizpůsobení procesu sestavení a publikování.</span><span class="sxs-lookup"><span data-stu-id="aef0b-191">Can be modified to customize the build and publish process.</span></span>

<span data-ttu-id="aef0b-192">Při publikování do cíle Azure obsahuje soubor *. pubxml* váš identifikátor předplatného Azure.</span><span class="sxs-lookup"><span data-stu-id="aef0b-192">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="aef0b-193">U tohoto cílového typu se nedoporučuje přidat tento soubor do správy zdrojových kódů.</span><span class="sxs-lookup"><span data-stu-id="aef0b-193">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="aef0b-194">Při publikování do cíle mimo Azure je bezpečné se vrátit se změnami soubor *. pubxml* .</span><span class="sxs-lookup"><span data-stu-id="aef0b-194">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="aef0b-195">Citlivé informace (například heslo pro publikování) jsou zašifrované na úrovni jednotlivých uživatelů nebo počítačů.</span><span class="sxs-lookup"><span data-stu-id="aef0b-195">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="aef0b-196">Je uložený v souboru *Properties/PublishProfiles/{Profile}. pubxml. User* .</span><span class="sxs-lookup"><span data-stu-id="aef0b-196">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="aef0b-197">Vzhledem k tomu, že tento soubor může ukládat citlivé informace, neměl by být zkontrolován do správy zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="aef0b-197">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="aef0b-198">Přehled publikování ASP.NET Core webové aplikace najdete v tématu <xref:host-and-deploy/index> .</span><span class="sxs-lookup"><span data-stu-id="aef0b-198">For an overview of how to publish an ASP.NET Core web app, see <xref:host-and-deploy/index>.</span></span> <span data-ttu-id="aef0b-199">Úlohy a cíle nástroje MSBuild potřebné k publikování ASP.NET Core webové aplikace jsou open source v [úložišti ASPNET/websdk](https://github.com/aspnet/websdk).</span><span class="sxs-lookup"><span data-stu-id="aef0b-199">The MSBuild tasks and targets necessary to publish an ASP.NET Core web app are open-source in the [aspnet/websdk repository](https://github.com/aspnet/websdk).</span></span>

<span data-ttu-id="aef0b-200">V následujících příkazech můžete použít profily pro publikování složek, MSDeploy a [Kudu](https://github.com/projectkudu/kudu/wiki) .</span><span class="sxs-lookup"><span data-stu-id="aef0b-200">The following commands can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles.</span></span> <span data-ttu-id="aef0b-201">Vzhledem k tomu, že MSDeploy chybí podpora pro různé platformy, jsou následující možnosti MSDeploy podporovány pouze v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="aef0b-201">Because MSDeploy lacks cross-platform support, the following MSDeploy options are supported only on Windows.</span></span>

<span data-ttu-id="aef0b-202">**Složka (funguje pro různé platformy):**</span><span class="sxs-lookup"><span data-stu-id="aef0b-202">**Folder (works cross-platform):**</span></span>

<!--

NOTE: Add back the following 'dotnet publish' folder publish example after https://github.com/aspnet/websdk/issues/888 is resolved.

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

-->

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="aef0b-203">**MSDeploy**</span><span class="sxs-lookup"><span data-stu-id="aef0b-203">**MSDeploy:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="aef0b-204">**Balíček MSDeploy:**</span><span class="sxs-lookup"><span data-stu-id="aef0b-204">**MSDeploy package:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="aef0b-205">V předchozích příkladech:</span><span class="sxs-lookup"><span data-stu-id="aef0b-205">In the preceding examples:</span></span>

* <span data-ttu-id="aef0b-206">`dotnet publish`a `dotnet build` podporují rozhraní API Kudu pro publikování do Azure z jakékoli platformy.</span><span class="sxs-lookup"><span data-stu-id="aef0b-206">`dotnet publish` and `dotnet build` support Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="aef0b-207">Publikování Visual studia podporuje rozhraní API Kudu, ale podporuje je WebSDK pro publikování mezi platformami v Azure.</span><span class="sxs-lookup"><span data-stu-id="aef0b-207">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>
* <span data-ttu-id="aef0b-208">Nepředávejte `DeployOnBuild` do `dotnet publish` příkazu.</span><span class="sxs-lookup"><span data-stu-id="aef0b-208">Don't pass `DeployOnBuild` to the `dotnet publish` command.</span></span>

<span data-ttu-id="aef0b-209">Další informace najdete v tématu [Microsoft. NET. SDK. Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span><span class="sxs-lookup"><span data-stu-id="aef0b-209">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="aef0b-210">Přidejte profil publikování do složky *vlastností projektu/PublishProfiles* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="aef0b-210">Add a publish profile to the project's *Properties/PublishProfiles* folder with the following content:</span></span>

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

## <a name="folder-publish-example"></a><span data-ttu-id="aef0b-211">Příklad publikování složky</span><span class="sxs-lookup"><span data-stu-id="aef0b-211">Folder publish example</span></span>

<span data-ttu-id="aef0b-212">Při publikování s profilem s názvem *FolderProfile*použijte některý z následujících příkazů:</span><span class="sxs-lookup"><span data-stu-id="aef0b-212">When publishing with a profile named *FolderProfile*, use either of the following commands:</span></span>

<!--

NOTE: Temporarily removed until https://github.com/aspnet/websdk/issues/888 is resolved.

* `dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`

-->

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="aef0b-213">Volání příkazu [dotnet sestavení](/dotnet/core/tools/dotnet-build) .NET Core CLI `msbuild` pro spuštění procesu sestavení a publikování.</span><span class="sxs-lookup"><span data-stu-id="aef0b-213">The .NET Core CLI's [dotnet build](/dotnet/core/tools/dotnet-build) command calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="aef0b-214">`dotnet build`Příkazy a `msbuild` jsou ekvivalentní při předávání do profilu složky.</span><span class="sxs-lookup"><span data-stu-id="aef0b-214">The `dotnet build` and `msbuild` commands are equivalent when passing in a folder profile.</span></span> <span data-ttu-id="aef0b-215">Při volání `msbuild` přímo ve Windows se používá .NET Framework verze nástroje MSBuild.</span><span class="sxs-lookup"><span data-stu-id="aef0b-215">When calling `msbuild` directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="aef0b-216">Volání `dotnet build` na profil mimo složku:</span><span class="sxs-lookup"><span data-stu-id="aef0b-216">Calling `dotnet build` on a non-folder profile:</span></span>

* <span data-ttu-id="aef0b-217">Vyvolá `msbuild` , který používá MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="aef0b-217">Invokes `msbuild`, which uses MSDeploy.</span></span>
* <span data-ttu-id="aef0b-218">Výsledkem je selhání (i při spuštění ve Windows).</span><span class="sxs-lookup"><span data-stu-id="aef0b-218">Results in a failure (even when running on Windows).</span></span> <span data-ttu-id="aef0b-219">Chcete-li publikovat s profilem mimo složku, zavolejte `msbuild` přímo.</span><span class="sxs-lookup"><span data-stu-id="aef0b-219">To publish with a non-folder profile, call `msbuild` directly.</span></span>

<span data-ttu-id="aef0b-220">V aplikaci Visual Studio se vytvořila následující složka publikovat profil a publikuje se do sdílené síťové složky:</span><span class="sxs-lookup"><span data-stu-id="aef0b-220">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

<span data-ttu-id="aef0b-221">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="aef0b-221">In the preceding example:</span></span>

* <span data-ttu-id="aef0b-222">`<ExcludeApp_Data>`Vlastnost je přítomna pouze pro splnění požadavku schématu XML.</span><span class="sxs-lookup"><span data-stu-id="aef0b-222">The `<ExcludeApp_Data>` property is present merely to satisfy an XML schema requirement.</span></span> <span data-ttu-id="aef0b-223">`<ExcludeApp_Data>`Vlastnost nemá žádný vliv na proces publikování, a to ani v případě, že je v kořenovém adresáři projektu *App_Data* složka.</span><span class="sxs-lookup"><span data-stu-id="aef0b-223">The `<ExcludeApp_Data>` property has no effect on the publish process, even if there's an *App_Data* folder in the project root.</span></span> <span data-ttu-id="aef0b-224">Složka *App_Data* neobdrží zvláštní zacházení, protože to funguje v projektech ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="aef0b-224">The *App_Data* folder doesn't receive special treatment as it does in ASP.NET 4.x projects.</span></span>

<!--

NOTE: Temporarily removed from 'Using the .NET Core CLI' below until https://github.com/aspnet/websdk/issues/888 is resolved.

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

-->

* <span data-ttu-id="aef0b-225">`<LastUsedBuildConfiguration>`Vlastnost je nastavena na hodnotu `Release` .</span><span class="sxs-lookup"><span data-stu-id="aef0b-225">The `<LastUsedBuildConfiguration>` property is set to `Release`.</span></span> <span data-ttu-id="aef0b-226">Při publikování ze sady Visual Studio je hodnota `<LastUsedBuildConfiguration>` nastavena pomocí hodnoty při spuštění procesu publikování.</span><span class="sxs-lookup"><span data-stu-id="aef0b-226">When publishing from Visual Studio, the value of `<LastUsedBuildConfiguration>` is set using the value when the publish process is started.</span></span> <span data-ttu-id="aef0b-227">`<LastUsedBuildConfiguration>`má hodnotu Special a v importovaném souboru MSBuild by se neměl přepsat.</span><span class="sxs-lookup"><span data-stu-id="aef0b-227">`<LastUsedBuildConfiguration>` is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="aef0b-228">Tato vlastnost se ale dá přepsat z příkazového řádku pomocí jednoho z následujících přístupů.</span><span class="sxs-lookup"><span data-stu-id="aef0b-228">This property can, however, be overridden from the command line using one of the following approaches.</span></span>
  * <span data-ttu-id="aef0b-229">Použití .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="aef0b-229">Using the .NET Core CLI:</span></span>

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * <span data-ttu-id="aef0b-230">Pomocí nástroje MSBuild:</span><span class="sxs-lookup"><span data-stu-id="aef0b-230">Using MSBuild:</span></span>

    ```console
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  <span data-ttu-id="aef0b-231">Další informace naleznete v tématu [MSBuild: jak nastavit vlastnost konfigurace](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span><span class="sxs-lookup"><span data-stu-id="aef0b-231">For more information, see [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span></span>

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="aef0b-232">Publikování na koncový bod MSDeploy z příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="aef0b-232">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="aef0b-233">V následujícím příkladu se používá webová aplikace ASP.NET Core vytvořená v aplikaci Visual Studio s názvem *AzureWebApp*.</span><span class="sxs-lookup"><span data-stu-id="aef0b-233">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp*.</span></span> <span data-ttu-id="aef0b-234">Do sady Visual Studio se přidá profil publikování aplikací Azure.</span><span class="sxs-lookup"><span data-stu-id="aef0b-234">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="aef0b-235">Další informace o tom, jak vytvořit profil, najdete v části [publikování profilů](#publish-profiles) .</span><span class="sxs-lookup"><span data-stu-id="aef0b-235">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="aef0b-236">K nasazení aplikace pomocí profilu publikování spusťte `msbuild` příkaz ze sady Visual Studio **Developer Command Prompt**.</span><span class="sxs-lookup"><span data-stu-id="aef0b-236">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt**.</span></span> <span data-ttu-id="aef0b-237">Příkazový řádek je k dispozici ve složce sady *Visual Studio* v nabídce **Start** na hlavním panelu systému Windows.</span><span class="sxs-lookup"><span data-stu-id="aef0b-237">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="aef0b-238">Pro snazší přístup můžete přidat příkazový řádek do nabídky **nástroje** v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="aef0b-238">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="aef0b-239">Další informace najdete v tématu [Developer Command Prompt pro Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="aef0b-239">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="aef0b-240">Nástroj MSBuild používá následující syntaxi příkazu:</span><span class="sxs-lookup"><span data-stu-id="aef0b-240">MSBuild uses the following command syntax:</span></span>

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="aef0b-241">{PATH}: cesta k souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="aef0b-241">{PATH}: Path to the app's project file.</span></span>
* <span data-ttu-id="aef0b-242">{PROFILe}: název publikačního profilu.</span><span class="sxs-lookup"><span data-stu-id="aef0b-242">{PROFILE}: Name of the publish profile.</span></span>
* <span data-ttu-id="aef0b-243">{USERNAME}: uživatelské jméno MSDeploy</span><span class="sxs-lookup"><span data-stu-id="aef0b-243">{USERNAME}: MSDeploy username.</span></span> <span data-ttu-id="aef0b-244">UŽIVATEL {USERNAME} se dá najít v profilu publikování.</span><span class="sxs-lookup"><span data-stu-id="aef0b-244">The {USERNAME} can be found in the publish profile.</span></span>
* <span data-ttu-id="aef0b-245">{PASSWORD}: heslo MSDeploy</span><span class="sxs-lookup"><span data-stu-id="aef0b-245">{PASSWORD}: MSDeploy password.</span></span> <span data-ttu-id="aef0b-246">Získá {PASSWORD} z *{Profile}. Soubor PublishSettings*</span><span class="sxs-lookup"><span data-stu-id="aef0b-246">Obtain the {PASSWORD} from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="aef0b-247">Stáhněte si *. Soubor PublishSettings* z některého z těchto:</span><span class="sxs-lookup"><span data-stu-id="aef0b-247">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="aef0b-248">**Průzkumník řešení**: vyberte **Zobrazit**  >  **Průzkumníka cloudu**.</span><span class="sxs-lookup"><span data-stu-id="aef0b-248">**Solution Explorer**: Select **View** > **Cloud Explorer**.</span></span> <span data-ttu-id="aef0b-249">Spojte se s vaším předplatným Azure.</span><span class="sxs-lookup"><span data-stu-id="aef0b-249">Connect with your Azure subscription.</span></span> <span data-ttu-id="aef0b-250">Otevřete **App Services**.</span><span class="sxs-lookup"><span data-stu-id="aef0b-250">Open **App Services**.</span></span> <span data-ttu-id="aef0b-251">Klikněte pravým tlačítkem na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="aef0b-251">Right-click the app.</span></span> <span data-ttu-id="aef0b-252">Vyberte **Stáhnout profil publikování**.</span><span class="sxs-lookup"><span data-stu-id="aef0b-252">Select **Download Publish Profile**.</span></span>
  * <span data-ttu-id="aef0b-253">Azure Portal: na panelu **přehledu** webové aplikace vyberte **získat profil publikování** .</span><span class="sxs-lookup"><span data-stu-id="aef0b-253">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="aef0b-254">Následující příklad používá profil publikování s názvem *AzureWebApp-nasazení webu*:</span><span class="sxs-lookup"><span data-stu-id="aef0b-254">The following example uses a publish profile named *AzureWebApp - Web Deploy*:</span></span>

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="aef0b-255">Profil publikování lze také použít s příkazem .NET Core CLI [dotnet MSBuild](/dotnet/core/tools/dotnet-msbuild) z příkazového prostředí systému Windows:</span><span class="sxs-lookup"><span data-stu-id="aef0b-255">A publish profile can also be used with the .NET Core CLI's [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command shell:</span></span>

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> <span data-ttu-id="aef0b-256">`dotnet msbuild`Příkaz je příkaz pro různé platformy a může kompilovat ASP.NET Core aplikace v systému MacOS a Linux.</span><span class="sxs-lookup"><span data-stu-id="aef0b-256">The `dotnet msbuild` command is a cross-platform command and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="aef0b-257">MSBuild v macOS a Linux ale neumožňuje nasazení aplikace do Azure nebo jiných koncových bodů MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="aef0b-257">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoints.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="aef0b-258">Nastavení prostředí</span><span class="sxs-lookup"><span data-stu-id="aef0b-258">Set the environment</span></span>

<span data-ttu-id="aef0b-259">Zahrňte `<EnvironmentName>` vlastnost do profilu publikování (*. pubxml*) nebo soubor projektu pro nastavení [prostředí](xref:fundamentals/environments)aplikace:</span><span class="sxs-lookup"><span data-stu-id="aef0b-259">Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="aef0b-260">Pokud požadujete transformace *Web. config* (například nastavení proměnných prostředí na základě konfigurace, profilu nebo prostředí), přečtěte si téma <xref:host-and-deploy/iis/transform-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="aef0b-260">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="aef0b-261">Vyloučit soubory</span><span class="sxs-lookup"><span data-stu-id="aef0b-261">Exclude files</span></span>

<span data-ttu-id="aef0b-262">Při publikování ASP.NET Core Web Apps jsou k dispozici následující prostředky:</span><span class="sxs-lookup"><span data-stu-id="aef0b-262">When publishing ASP.NET Core web apps, the following assets are included:</span></span>

* <span data-ttu-id="aef0b-263">Sestavit artefakty</span><span class="sxs-lookup"><span data-stu-id="aef0b-263">Build artifacts</span></span>
* <span data-ttu-id="aef0b-264">Složky a soubory, které odpovídají následujícím vzorům pro expanzi názvů:</span><span class="sxs-lookup"><span data-stu-id="aef0b-264">Folders and files matching the following globbing patterns:</span></span>
  * <span data-ttu-id="aef0b-265">`**\*.config`(například *Web. config*)</span><span class="sxs-lookup"><span data-stu-id="aef0b-265">`**\*.config` (for example, *web.config*)</span></span>
  * <span data-ttu-id="aef0b-266">`**\*.json`(například *appSettings. JSON*)</span><span class="sxs-lookup"><span data-stu-id="aef0b-266">`**\*.json` (for example, *appsettings.json*)</span></span>
  * `wwwroot\**`

<span data-ttu-id="aef0b-267">Nástroj MSBuild podporuje [vzory expanze](https://gruntjs.com/configuring-tasks#globbing-patterns).</span><span class="sxs-lookup"><span data-stu-id="aef0b-267">MSBuild supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="aef0b-268">Například následující `<Content>` prvek potlačí kopírování souborů textu (*. txt*) ve složce *wwwroot\content* a jejích podsložkách:</span><span class="sxs-lookup"><span data-stu-id="aef0b-268">For example, the following `<Content>` element suppresses the copying of text (*.txt*) files in the *wwwroot\content* folder and its subfolders:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="aef0b-269">Předchozí kód lze přidat do publikačního profilu nebo souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="aef0b-269">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="aef0b-270">Po přidání do souboru *. csproj* je pravidlo přidáno do všech profilů publikování v projektu.</span><span class="sxs-lookup"><span data-stu-id="aef0b-270">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="aef0b-271">Následující `<MsDeploySkipRules>` prvek vyloučí všechny soubory ze složky *wwwroot\content* :</span><span class="sxs-lookup"><span data-stu-id="aef0b-271">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot\content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="aef0b-272">`<MsDeploySkipRules>`neodstraní cíle *přeskočení* z webu nasazení.</span><span class="sxs-lookup"><span data-stu-id="aef0b-272">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="aef0b-273">`<Content>`cílové soubory a složky se odstraní z lokality nasazení.</span><span class="sxs-lookup"><span data-stu-id="aef0b-273">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="aef0b-274">Předpokládejme například, že nasazená webová aplikace obsahovala následující soubory:</span><span class="sxs-lookup"><span data-stu-id="aef0b-274">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="aef0b-275">*Views/Home/About1. cshtml*</span><span class="sxs-lookup"><span data-stu-id="aef0b-275">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="aef0b-276">*Views/Home/About2. cshtml*</span><span class="sxs-lookup"><span data-stu-id="aef0b-276">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="aef0b-277">*Views/Home/About3. cshtml*</span><span class="sxs-lookup"><span data-stu-id="aef0b-277">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="aef0b-278">Pokud `<MsDeploySkipRules>` jsou přidány následující prvky, tyto soubory nebyly odstraněny na webu nasazení.</span><span class="sxs-lookup"><span data-stu-id="aef0b-278">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="aef0b-279">Předchozí `<MsDeploySkipRules>` prvky zabraňují nasazení *vynechaných* souborů.</span><span class="sxs-lookup"><span data-stu-id="aef0b-279">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="aef0b-280">Po nasazení tyto soubory neodstraní.</span><span class="sxs-lookup"><span data-stu-id="aef0b-280">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="aef0b-281">Následující `<Content>` element odstraní cílové soubory v lokalitě nasazení:</span><span class="sxs-lookup"><span data-stu-id="aef0b-281">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="aef0b-282">Použití nasazení příkazového řádku s předchozím `<Content>` elementem vrací variaci následujícího výstupu:</span><span class="sxs-lookup"><span data-stu-id="aef0b-282">Using command-line deployment with the preceding `<Content>` element yields a variation of the following output:</span></span>

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\{TARGET FRAMEWORK MONIKER}\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a><span data-ttu-id="aef0b-283">Soubory k zahrnutí</span><span class="sxs-lookup"><span data-stu-id="aef0b-283">Include files</span></span>

<span data-ttu-id="aef0b-284">Následující oddíly popisují různé přístupy k zařazení souborů v době publikování.</span><span class="sxs-lookup"><span data-stu-id="aef0b-284">The following sections outline different approaches for file inclusion at publish time.</span></span> <span data-ttu-id="aef0b-285">Oddíl [Obecné zahrnutí souborů](#general-file-inclusion) používá `DotNetPublishFiles` položku, která je poskytována souborem cílů publikování v sadě [Web SDK](xref:razor-pages/web-sdk).</span><span class="sxs-lookup"><span data-stu-id="aef0b-285">The [General file inclusion](#general-file-inclusion) section uses the `DotNetPublishFiles` item, which is provided by a publish targets file in the [Web SDK](xref:razor-pages/web-sdk).</span></span> <span data-ttu-id="aef0b-286">Oddíl [zahrnutí selektivních souborů](#selective-file-inclusion) používá `ResolvedFileToPublish` položku, která je k dispozici v souboru cílů publikování v [.NET Core SDK](/dotnet/core/project-sdk/msbuild-props).</span><span class="sxs-lookup"><span data-stu-id="aef0b-286">The [Selective file inclusion](#selective-file-inclusion) section uses the `ResolvedFileToPublish` item, which is provided by a publish targets file in the [.NET Core SDK](/dotnet/core/project-sdk/msbuild-props).</span></span> <span data-ttu-id="aef0b-287">Vzhledem k tomu, že webová sada SDK závisí na .NET Core SDK, může být každá položka použita v projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="aef0b-287">Because the Web SDK depends on the .NET Core SDK, either item can be used in an ASP.NET Core project.</span></span>

### <a name="general-file-inclusion"></a><span data-ttu-id="aef0b-288">Obecné zahrnutí souborů</span><span class="sxs-lookup"><span data-stu-id="aef0b-288">General file inclusion</span></span>

<span data-ttu-id="aef0b-289">Následující příklad `<ItemGroup>` elementu ukazuje zkopírování složky nacházející se mimo adresář projektu do složky publikovaného webu.</span><span class="sxs-lookup"><span data-stu-id="aef0b-289">The following example's `<ItemGroup>` element demonstrates copying a folder located outside of the project directory to a folder of the published site.</span></span> <span data-ttu-id="aef0b-290">Ve výchozím nastavení jsou zahrnuty všechny soubory přidané do následujících značek `<ItemGroup>` .</span><span class="sxs-lookup"><span data-stu-id="aef0b-290">Any files added to the following markup's `<ItemGroup>` are included by default.</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="aef0b-291">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="aef0b-291">The preceding markup:</span></span>

* <span data-ttu-id="aef0b-292">Lze přidat do souboru *. csproj* nebo do profilu publikování.</span><span class="sxs-lookup"><span data-stu-id="aef0b-292">Can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="aef0b-293">Pokud je přidán do souboru *. csproj* , je zahrnut do každého profilu publikování v projektu.</span><span class="sxs-lookup"><span data-stu-id="aef0b-293">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>
* <span data-ttu-id="aef0b-294">Deklaruje `_CustomFiles` položku pro ukládání souborů, které odpovídají `Include` vzoru rozexpanzení atributu.</span><span class="sxs-lookup"><span data-stu-id="aef0b-294">Declares a `_CustomFiles` item to store files matching the `Include` attribute's globbing pattern.</span></span> <span data-ttu-id="aef0b-295">Složka *imagí* , na kterou se odkazuje ve vzoru, je umístěna mimo adresář projektu.</span><span class="sxs-lookup"><span data-stu-id="aef0b-295">The *images* folder referenced in the pattern is located outside of the project directory.</span></span> <span data-ttu-id="aef0b-296">[Vyhrazená vlastnost](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties)s názvem `$(MSBuildProjectDirectory)` se překládá na absolutní cestu souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="aef0b-296">A [reserved property](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), named `$(MSBuildProjectDirectory)`, resolves to the project file's absolute path.</span></span>
* <span data-ttu-id="aef0b-297">Poskytuje seznam souborů k `DotNetPublishFiles` položce.</span><span class="sxs-lookup"><span data-stu-id="aef0b-297">Provides a list of files to the `DotNetPublishFiles` item.</span></span> <span data-ttu-id="aef0b-298">Ve výchozím nastavení `<DestinationRelativePath>` je element položky prázdný.</span><span class="sxs-lookup"><span data-stu-id="aef0b-298">By default, the item's `<DestinationRelativePath>` element is empty.</span></span> <span data-ttu-id="aef0b-299">Výchozí hodnota je přepsána ve značce a používá [dobře známá metadata položky](/visualstudio/msbuild/msbuild-well-known-item-metadata) , jako je například `%(RecursiveDir)` .</span><span class="sxs-lookup"><span data-stu-id="aef0b-299">The default value is overridden in the markup and uses [well-known item metadata](/visualstudio/msbuild/msbuild-well-known-item-metadata) such as `%(RecursiveDir)`.</span></span> <span data-ttu-id="aef0b-300">Vnitřní text představuje složku *wwwroot/image* publikovaného webu.</span><span class="sxs-lookup"><span data-stu-id="aef0b-300">The inner text represents the *wwwroot/images* folder of the published site.</span></span>

### <a name="selective-file-inclusion"></a><span data-ttu-id="aef0b-301">Zahrnutí selektivních souborů</span><span class="sxs-lookup"><span data-stu-id="aef0b-301">Selective file inclusion</span></span>

<span data-ttu-id="aef0b-302">Zvýrazněný kód ukazuje následující příklad:</span><span class="sxs-lookup"><span data-stu-id="aef0b-302">The highlighted markup in the following example demonstrates:</span></span>

* <span data-ttu-id="aef0b-303">Kopírování souboru, který se nachází mimo projekt, do složky *wwwroot* publikované lokality.</span><span class="sxs-lookup"><span data-stu-id="aef0b-303">Copying a file located outside of the project into the published site's *wwwroot* folder.</span></span> <span data-ttu-id="aef0b-304">Název souboru *ReadMe2.MD* se zachová.</span><span class="sxs-lookup"><span data-stu-id="aef0b-304">The file name of *ReadMe2.md* is maintained.</span></span>
* <span data-ttu-id="aef0b-305">Vyloučení složky *wwwroot\Content*</span><span class="sxs-lookup"><span data-stu-id="aef0b-305">Excluding the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="aef0b-306">Kromě *Views\Home\About2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="aef0b-306">Excluding *Views\Home\About2.cshtml*.</span></span>

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

<span data-ttu-id="aef0b-307">Předchozí příklad používá `ResolvedFileToPublish` položku, jejíž výchozí chování je vždy kopírovat soubory, které jsou zadány v `Include` atributu do publikované lokality.</span><span class="sxs-lookup"><span data-stu-id="aef0b-307">The preceding example uses the `ResolvedFileToPublish` item, whose default behavior is to always copy the files provided in the `Include` attribute to the published site.</span></span> <span data-ttu-id="aef0b-308">Přepište výchozí chování zahrnutím `<CopyToPublishDirectory>` podřízeného prvku s vnitřním textem buď `Never` nebo `PreserveNewest` .</span><span class="sxs-lookup"><span data-stu-id="aef0b-308">Override the default behavior by including a `<CopyToPublishDirectory>` child element with inner text of either `Never` or `PreserveNewest`.</span></span> <span data-ttu-id="aef0b-309">Příklad:</span><span class="sxs-lookup"><span data-stu-id="aef0b-309">For example:</span></span>

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

<span data-ttu-id="aef0b-310">Další ukázky nasazení najdete v [souboru Readme pro úložiště web SDK](https://github.com/aspnet/websdk).</span><span class="sxs-lookup"><span data-stu-id="aef0b-310">For more deployment samples, see the [Web SDK repository Readme](https://github.com/aspnet/websdk).</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="aef0b-311">Spustit cíl před nebo po publikování</span><span class="sxs-lookup"><span data-stu-id="aef0b-311">Run a target before or after publishing</span></span>

<span data-ttu-id="aef0b-312">Předdefinované `BeforePublish` a `AfterPublish` cíle provádějí cíl před nebo po cíli publikování.</span><span class="sxs-lookup"><span data-stu-id="aef0b-312">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="aef0b-313">Přidejte následující prvky do profilu publikování pro protokolování zpráv konzoly před i po publikování:</span><span class="sxs-lookup"><span data-stu-id="aef0b-313">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="aef0b-314">Publikování na serveru pomocí nedůvěryhodného certifikátu</span><span class="sxs-lookup"><span data-stu-id="aef0b-314">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="aef0b-315">Přidejte `<AllowUntrustedCertificate>` vlastnost s hodnotou `True` do profilu publikování:</span><span class="sxs-lookup"><span data-stu-id="aef0b-315">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="aef0b-316">Služba Kudu</span><span class="sxs-lookup"><span data-stu-id="aef0b-316">The Kudu service</span></span>

<span data-ttu-id="aef0b-317">Pokud chcete zobrazit soubory ve Azure App Service nasazení webové aplikace, použijte [službu Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span><span class="sxs-lookup"><span data-stu-id="aef0b-317">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="aef0b-318">Přidejte `scm` token do názvu webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="aef0b-318">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="aef0b-319">Příklad:</span><span class="sxs-lookup"><span data-stu-id="aef0b-319">For example:</span></span>

| <span data-ttu-id="aef0b-320">URL</span><span class="sxs-lookup"><span data-stu-id="aef0b-320">URL</span></span>                                    | <span data-ttu-id="aef0b-321">Výsledek</span><span class="sxs-lookup"><span data-stu-id="aef0b-321">Result</span></span>       |
| ---
<span data-ttu-id="aef0b-322">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="aef0b-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aef0b-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-323">'Blazor'</span></span>
- <span data-ttu-id="aef0b-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aef0b-324">'Identity'</span></span>
- <span data-ttu-id="aef0b-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aef0b-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="aef0b-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-326">'Razor'</span></span>
- <span data-ttu-id="aef0b-327">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="aef0b-327">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aef0b-328">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="aef0b-328">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aef0b-329">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-329">'Blazor'</span></span>
- <span data-ttu-id="aef0b-330">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aef0b-330">'Identity'</span></span>
- <span data-ttu-id="aef0b-331">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aef0b-331">'Let's Encrypt'</span></span>
- <span data-ttu-id="aef0b-332">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-332">'Razor'</span></span>
- <span data-ttu-id="aef0b-333">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="aef0b-333">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aef0b-334">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="aef0b-334">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aef0b-335">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-335">'Blazor'</span></span>
- <span data-ttu-id="aef0b-336">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aef0b-336">'Identity'</span></span>
- <span data-ttu-id="aef0b-337">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aef0b-337">'Let's Encrypt'</span></span>
- <span data-ttu-id="aef0b-338">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-338">'Razor'</span></span>
- <span data-ttu-id="aef0b-339">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="aef0b-339">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aef0b-340">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="aef0b-340">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aef0b-341">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-341">'Blazor'</span></span>
- <span data-ttu-id="aef0b-342">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aef0b-342">'Identity'</span></span>
- <span data-ttu-id="aef0b-343">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aef0b-343">'Let's Encrypt'</span></span>
- <span data-ttu-id="aef0b-344">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-344">'Razor'</span></span>
- <span data-ttu-id="aef0b-345">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="aef0b-345">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aef0b-346">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="aef0b-346">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aef0b-347">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-347">'Blazor'</span></span>
- <span data-ttu-id="aef0b-348">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aef0b-348">'Identity'</span></span>
- <span data-ttu-id="aef0b-349">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aef0b-349">'Let's Encrypt'</span></span>
- <span data-ttu-id="aef0b-350">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-350">'Razor'</span></span>
- <span data-ttu-id="aef0b-351">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="aef0b-351">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aef0b-352">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="aef0b-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aef0b-353">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-353">'Blazor'</span></span>
- <span data-ttu-id="aef0b-354">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aef0b-354">'Identity'</span></span>
- <span data-ttu-id="aef0b-355">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aef0b-355">'Let's Encrypt'</span></span>
- <span data-ttu-id="aef0b-356">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-356">'Razor'</span></span>
- <span data-ttu-id="aef0b-357">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="aef0b-357">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aef0b-358">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="aef0b-358">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aef0b-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-359">'Blazor'</span></span>
- <span data-ttu-id="aef0b-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aef0b-360">'Identity'</span></span>
- <span data-ttu-id="aef0b-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aef0b-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="aef0b-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-362">'Razor'</span></span>
- <span data-ttu-id="aef0b-363">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="aef0b-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aef0b-364">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="aef0b-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aef0b-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-365">'Blazor'</span></span>
- <span data-ttu-id="aef0b-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aef0b-366">'Identity'</span></span>
- <span data-ttu-id="aef0b-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aef0b-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="aef0b-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-368">'Razor'</span></span>
- <span data-ttu-id="aef0b-369">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="aef0b-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aef0b-370">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="aef0b-370">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aef0b-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-371">'Blazor'</span></span>
- <span data-ttu-id="aef0b-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aef0b-372">'Identity'</span></span>
- <span data-ttu-id="aef0b-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aef0b-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="aef0b-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-374">'Razor'</span></span>
- <span data-ttu-id="aef0b-375">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="aef0b-375">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aef0b-376">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="aef0b-376">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aef0b-377">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-377">'Blazor'</span></span>
- <span data-ttu-id="aef0b-378">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aef0b-378">'Identity'</span></span>
- <span data-ttu-id="aef0b-379">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aef0b-379">'Let's Encrypt'</span></span>
- <span data-ttu-id="aef0b-380">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-380">'Razor'</span></span>
- <span data-ttu-id="aef0b-381">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="aef0b-381">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aef0b-382">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="aef0b-382">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aef0b-383">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-383">'Blazor'</span></span>
- <span data-ttu-id="aef0b-384">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aef0b-384">'Identity'</span></span>
- <span data-ttu-id="aef0b-385">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aef0b-385">'Let's Encrypt'</span></span>
- <span data-ttu-id="aef0b-386">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-386">'Razor'</span></span>
- <span data-ttu-id="aef0b-387">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="aef0b-387">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aef0b-388">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="aef0b-388">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aef0b-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-389">'Blazor'</span></span>
- <span data-ttu-id="aef0b-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aef0b-390">'Identity'</span></span>
- <span data-ttu-id="aef0b-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aef0b-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="aef0b-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-392">'Razor'</span></span>
- <span data-ttu-id="aef0b-393">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="aef0b-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aef0b-394">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="aef0b-394">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aef0b-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-395">'Blazor'</span></span>
- <span data-ttu-id="aef0b-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aef0b-396">'Identity'</span></span>
- <span data-ttu-id="aef0b-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aef0b-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="aef0b-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-398">'Razor'</span></span>
- <span data-ttu-id="aef0b-399">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="aef0b-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aef0b-400">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="aef0b-400">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aef0b-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-401">'Blazor'</span></span>
- <span data-ttu-id="aef0b-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aef0b-402">'Identity'</span></span>
- <span data-ttu-id="aef0b-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aef0b-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="aef0b-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-404">'Razor'</span></span>
- <span data-ttu-id="aef0b-405">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="aef0b-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aef0b-406">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="aef0b-406">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aef0b-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-407">'Blazor'</span></span>
- <span data-ttu-id="aef0b-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aef0b-408">'Identity'</span></span>
- <span data-ttu-id="aef0b-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aef0b-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="aef0b-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-410">'Razor'</span></span>
- <span data-ttu-id="aef0b-411">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="aef0b-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aef0b-412">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="aef0b-412">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aef0b-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-413">'Blazor'</span></span>
- <span data-ttu-id="aef0b-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aef0b-414">'Identity'</span></span>
- <span data-ttu-id="aef0b-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aef0b-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="aef0b-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-416">'Razor'</span></span>
- <span data-ttu-id="aef0b-417">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="aef0b-417">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aef0b-418">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="aef0b-418">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aef0b-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-419">'Blazor'</span></span>
- <span data-ttu-id="aef0b-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aef0b-420">'Identity'</span></span>
- <span data-ttu-id="aef0b-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aef0b-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="aef0b-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-422">'Razor'</span></span>
- <span data-ttu-id="aef0b-423">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="aef0b-423">'SignalR' uid:</span></span> 

<span data-ttu-id="aef0b-424">------------------- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="aef0b-424">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aef0b-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-425">'Blazor'</span></span>
- <span data-ttu-id="aef0b-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aef0b-426">'Identity'</span></span>
- <span data-ttu-id="aef0b-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aef0b-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="aef0b-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-428">'Razor'</span></span>
- <span data-ttu-id="aef0b-429">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="aef0b-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aef0b-430">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="aef0b-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aef0b-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-431">'Blazor'</span></span>
- <span data-ttu-id="aef0b-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aef0b-432">'Identity'</span></span>
- <span data-ttu-id="aef0b-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aef0b-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="aef0b-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-434">'Razor'</span></span>
- <span data-ttu-id="aef0b-435">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="aef0b-435">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aef0b-436">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="aef0b-436">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aef0b-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-437">'Blazor'</span></span>
- <span data-ttu-id="aef0b-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aef0b-438">'Identity'</span></span>
- <span data-ttu-id="aef0b-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aef0b-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="aef0b-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-440">'Razor'</span></span>
- <span data-ttu-id="aef0b-441">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="aef0b-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aef0b-442">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="aef0b-442">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aef0b-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-443">'Blazor'</span></span>
- <span data-ttu-id="aef0b-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aef0b-444">'Identity'</span></span>
- <span data-ttu-id="aef0b-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aef0b-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="aef0b-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aef0b-446">'Razor'</span></span>
- <span data-ttu-id="aef0b-447">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="aef0b-447">'SignalR' uid:</span></span> 

<span data-ttu-id="aef0b-448">------ | | `http://mysite.azurewebsites.net/`     | Webová aplikace | | `http://mysite.scm.azurewebsites.net/` | Služba Kudu |</span><span class="sxs-lookup"><span data-stu-id="aef0b-448">------ | | `http://mysite.azurewebsites.net/`     | Web App      | | `http://mysite.scm.azurewebsites.net/` | Kudu service |</span></span>

<span data-ttu-id="aef0b-449">Vyberte položku nabídky [konzoly ladění](https://github.com/projectkudu/kudu/wiki/Kudu-console) pro zobrazení, úpravy, odstranění nebo přidání souborů.</span><span class="sxs-lookup"><span data-stu-id="aef0b-449">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aef0b-450">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="aef0b-450">Additional resources</span></span>

* <span data-ttu-id="aef0b-451">[Nasazení webu](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) zjednodušuje nasazení webových aplikací a webů na servery služby IIS.</span><span class="sxs-lookup"><span data-stu-id="aef0b-451">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="aef0b-452">[Úložiště GitHub web SDK](https://github.com/aspnet/websdk/issues): problémy se soubory a žádosti pro nasazení.</span><span class="sxs-lookup"><span data-stu-id="aef0b-452">[Web SDK GitHub repository](https://github.com/aspnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="aef0b-453">Publikování webové aplikace v ASP.NET ve virtuálním počítači Azure ze sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aef0b-453">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
