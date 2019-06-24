---
title: Visual Studio publikačních profilů pro nasazení aplikace ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vytvořit profily publikování v sadě Visual Studio a jejich použití pro správu nasazení aplikací ASP.NET Core do různých cílů.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/21/2019
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: 50be5a20f6d927270ef2d9dbc6c1cbf24196978f
ms.sourcegitcommit: 28646e8ca62fb094db1557b5c0c02d5b45531824
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2019
ms.locfileid: "67333419"
---
# <a name="visual-studio-publish-profiles-for-aspnet-core-app-deployment"></a><span data-ttu-id="2fd4d-103">Visual Studio publikačních profilů pro nasazení aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2fd4d-103">Visual Studio publish profiles for ASP.NET Core app deployment</span></span>

<span data-ttu-id="2fd4d-104">Podle [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2fd4d-104">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2fd4d-105">Tento dokument se zaměřuje na pomocí Visual Studio 2019 nebo později k vytvoření a použití publikačních profilů.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-105">This document focuses on using Visual Studio 2019 or later to create and use publish profiles.</span></span> <span data-ttu-id="2fd4d-106">Profily publikování vytvořené pomocí sady Visual Studio je možné pomocí nástroje MSBuild a sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-106">The publish profiles created with Visual Studio can be used with MSBuild and Visual Studio.</span></span> <span data-ttu-id="2fd4d-107">Pokyny k publikování do Azure najdete v tématu <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-107">For instructions on publishing to Azure, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

<span data-ttu-id="2fd4d-108">`dotnet new mvc` Příkaz vytvoří soubor projektu obsahující následující úrovni kořenového adresáře [ \<Projekt > element](/visualstudio/msbuild/project-element-msbuild):</span><span class="sxs-lookup"><span data-stu-id="2fd4d-108">The `dotnet new mvc` command produces a project file containing the following root-level [\<Project> element](/visualstudio/msbuild/project-element-msbuild):</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

<span data-ttu-id="2fd4d-109">Předchozí `<Project>` elementu `Sdk` atribut importuje MSBuild [vlastnosti](/visualstudio/msbuild/msbuild-properties) a [cíle](/visualstudio/msbuild/msbuild-targets) z *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\ SDK.props* a *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-109">The preceding `<Project>` element's `Sdk` attribute imports the MSBuild [properties](/visualstudio/msbuild/msbuild-properties) and [targets](/visualstudio/msbuild/msbuild-targets) from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* and *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectively.</span></span> <span data-ttu-id="2fd4d-110">Výchozím umístěním pro `$(MSBuildSDKsPath)` (s Visual Studio Enterprise. 2019) je *% programfiles (x86) %\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* složky.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-110">The default location for `$(MSBuildSDKsPath)` (with Visual Studio 2019 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="2fd4d-111">`Microsoft.NET.Sdk.Web` (Sada web SDK) závisí na dalších sad SDK, včetně `Microsoft.NET.Sdk` (.NET Core SDK) a `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span><span class="sxs-lookup"><span data-stu-id="2fd4d-111">`Microsoft.NET.Sdk.Web` (Web SDK) depends on other SDKs, including `Microsoft.NET.Sdk` (.NET Core SDK) and `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span></span> <span data-ttu-id="2fd4d-112">Vlastnosti nástroje MSBuild a cíle, které jsou spojené s každou závislé sady SDK jsou importovány.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-112">The MSBuild properties and targets associated with each dependent SDK are imported.</span></span> <span data-ttu-id="2fd4d-113">Publikujte cíle importu příslušné sady cíle založené na metodě publikování použít.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-113">Publish targets import the appropriate set of targets based on the publish method used.</span></span>

<span data-ttu-id="2fd4d-114">Při načtení projektu nástroje MSBuild nebo Visual Studio provedou tyto akce vysoké úrovně:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-114">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="2fd4d-115">Sestavit projekt</span><span class="sxs-lookup"><span data-stu-id="2fd4d-115">Build project</span></span>
* <span data-ttu-id="2fd4d-116">COMPUTE souborů k publikování</span><span class="sxs-lookup"><span data-stu-id="2fd4d-116">Compute files to publish</span></span>
* <span data-ttu-id="2fd4d-117">Publikování souborů do cíle</span><span class="sxs-lookup"><span data-stu-id="2fd4d-117">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="2fd4d-118">COMPUTE položky projektu</span><span class="sxs-lookup"><span data-stu-id="2fd4d-118">Compute project items</span></span>

<span data-ttu-id="2fd4d-119">Při načtení projektu [položky projektu nástroje MSBuild](/visualstudio/msbuild/common-msbuild-project-items) jsou vypočítány (soubory).</span><span class="sxs-lookup"><span data-stu-id="2fd4d-119">When the project is loaded, the [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) (files) are computed.</span></span> <span data-ttu-id="2fd4d-120">Typ položky určuje způsob zpracování souboru.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-120">The item type determines how the file is processed.</span></span> <span data-ttu-id="2fd4d-121">Ve výchozím nastavení *.cs* soubory jsou zahrnuty v `Compile` seznam položek.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-121">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="2fd4d-122">Soubory `Compile` jsou zkompilovány seznam položek.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-122">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="2fd4d-123">`Content` Seznamu položek obsahuje soubory, které jsou publikovány kromě výstupy sestavení.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-123">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="2fd4d-124">Ve výchozím nastavení, soubory odpovídající vzorům `wwwroot\**`, `**\*.config`, a `**\*.json` jsou součástí `Content` seznam položek.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-124">By default, files matching the patterns `wwwroot\**`, `**\*.config`, and `**\*.json` are included in the `Content` item list.</span></span> <span data-ttu-id="2fd4d-125">Například `wwwroot\**` [model podpory zástupných znaků](https://gruntjs.com/configuring-tasks#globbing-patterns) vyhledá všechny soubory v *wwwroot* složce a jejích podsložkách.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-125">For example, the `wwwroot\**` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder and its subfolders.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2fd4d-126">Importuje Web SDK [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="2fd4d-126">The Web SDK imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="2fd4d-127">V důsledku toho soubory odpovídající vzorům `**\*.cshtml` a `**\*.razor` jsou taky součástí `Content` seznam položek.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-127">As a result, files matching the patterns `**\*.cshtml` and `**\*.razor` are also included in the `Content` item list.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="2fd4d-128">Importuje Web SDK [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="2fd4d-128">The Web SDK imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="2fd4d-129">V důsledku toho soubory odpovídající `**\*.cshtml` vzoru jsou taky součástí `Content` seznam položek.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-129">As a result, files matching the `**\*.cshtml` pattern are also included in the `Content` item list.</span></span>

::: moniker-end

<span data-ttu-id="2fd4d-130">Pokud chcete explicitně přidat soubor do seznamu publikovat, přidejte přímo v souboru *.csproj* sdílené, jak je znázorněno [vložených souborů](#include-files) části.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-130">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in the [Include Files](#include-files) section.</span></span>

<span data-ttu-id="2fd4d-131">Při výběru **publikovat** tlačítko v sadě Visual Studio nebo při publikování z příkazového řádku:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-131">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="2fd4d-132">Jsou vypočítané vlastnosti/položky (soubory, které jsou potřebné k sestavení).</span><span class="sxs-lookup"><span data-stu-id="2fd4d-132">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="2fd4d-133">**Visual Studio pouze**: Obnovení balíčků NuGet.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-133">**Visual Studio only**: NuGet packages are restored.</span></span> <span data-ttu-id="2fd4d-134">(Obnovit musí být explicitní uživatelem na rozhraní příkazového řádku).</span><span class="sxs-lookup"><span data-stu-id="2fd4d-134">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="2fd4d-135">Sestavení projektu.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-135">The project builds.</span></span>
* <span data-ttu-id="2fd4d-136">Publikovat položky se zpracovávají (soubory, které jsou potřebné k publikování).</span><span class="sxs-lookup"><span data-stu-id="2fd4d-136">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="2fd4d-137">Publikování projektu (vypočítané soubory se zkopírují do cílového umístění publikování).</span><span class="sxs-lookup"><span data-stu-id="2fd4d-137">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="2fd4d-138">Když se odkazuje projekt ASP.NET Core `Microsoft.NET.Sdk.Web` v souboru projektu *app_offline.htm* soubor umístěn v kořenovém adresáři webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-138">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="2fd4d-139">Pokud soubor existuje, modul ASP.NET Core řádně ukončí aplikaci, slouží *app_offline.htm* souboru během nasazení.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-139">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="2fd4d-140">Další informace najdete v tématu [odkaz Konfigurace modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="2fd4d-140">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="2fd4d-141">Publikování základních příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="2fd4d-141">Basic command-line publishing</span></span>

<span data-ttu-id="2fd4d-142">Příkazový řádek publikování funguje na všech platformách .NET Core podporovány a nevyžaduje, aby Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-142">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="2fd4d-143">V následujících příkladech, rozhraní příkazového řádku .NET Core pro [dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkaz spustí z adresáře projektu (který obsahuje *.csproj* souboru).</span><span class="sxs-lookup"><span data-stu-id="2fd4d-143">In the following examples, the .NET Core CLI's [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="2fd4d-144">Pokud složka projektu není aktuální pracovní adresář, explicitně předejte v cestě k souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-144">If the project folder isn't the current working directory, explicitly pass in the project file path.</span></span> <span data-ttu-id="2fd4d-145">Příklad:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-145">For example:</span></span>

```console
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="2fd4d-146">Spusťte následující příkazy k vytvoření a publikování webové aplikace:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-146">Run the following commands to create and publish a web app:</span></span>

```console
dotnet new mvc
dotnet publish
```

<span data-ttu-id="2fd4d-147">`dotnet publish` Příkaz vytvoří varianta následující výstup:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-147">The `dotnet publish` command produces a variation of the following output:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

<span data-ttu-id="2fd4d-148">Složky pro publikování výchozí formát je *bin\Debug\\{MONIKER CÍLOVÉHO rozhraní} \publish\\* .</span><span class="sxs-lookup"><span data-stu-id="2fd4d-148">The default publish folder format is *bin\Debug\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="2fd4d-149">Například *bin\Debug\netcoreapp2.2\publish\\* .</span><span class="sxs-lookup"><span data-stu-id="2fd4d-149">For example, *bin\Debug\netcoreapp2.2\publish\\*.</span></span>

<span data-ttu-id="2fd4d-150">Následující příkaz určuje `Release` sestavení a publikování adresáře:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-150">The following command specifies a `Release` build and the publishing directory:</span></span>

```console
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="2fd4d-151">`dotnet publish` Volání MSBuild, který vyvolá příkaz `Publish` cíl.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-151">The `dotnet publish` command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="2fd4d-152">Žádné parametry předány `dotnet publish` jsou předávaným do MSBuild.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-152">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="2fd4d-153">`-c` a `-o` parametry mapování v nástroji MSBuild `Configuration` a `OutputPath` vlastnosti, v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-153">The `-c` and `-o` parameters map to MSBuild's `Configuration` and `OutputPath` properties, respectively.</span></span>

<span data-ttu-id="2fd4d-154">Vlastnosti nástroje MSBuild mohou být předány některou z následujících formátů:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-154">MSBuild properties can be passed using either of the following formats:</span></span>

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="2fd4d-155">Například následující příkaz publikuje `Release` sestavení do sdílené síťové složky.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-155">For example, the following command publishes a `Release` build to a network share.</span></span> <span data-ttu-id="2fd4d-156">Sdílené síťové složky se zadaným lomítka ( *//r8/* ) a funguje na všech platformách .NET Core podporovány.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-156">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

`dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb`

<span data-ttu-id="2fd4d-157">Potvrďte, že publikované aplikace pro nasazení neběží.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-157">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="2fd4d-158">Soubory *publikovat* složky jsou zamčené, když je aplikace spuštěna.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-158">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="2fd4d-159">Nasazení nebyla vytvořena, protože uzamčena, nelze zkopírovat soubory.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-159">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="2fd4d-160">Profily publikování</span><span class="sxs-lookup"><span data-stu-id="2fd4d-160">Publish profiles</span></span>

<span data-ttu-id="2fd4d-161">Tato část používá Visual Studio 2019 nebo novější k vytvoření profilu publikování.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-161">This section uses Visual Studio 2019 or later to create a publishing profile.</span></span> <span data-ttu-id="2fd4d-162">Po vytvoření profilu publikování ze sady Visual Studio nebo příkazového řádku je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-162">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span> <span data-ttu-id="2fd4d-163">Publikování profilů může zjednodušit proces publikování, a může existovat libovolný počet profilů.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-163">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span>

<span data-ttu-id="2fd4d-164">Vytvořte profil publikování v sadě Visual Studio výběrem jedné z následujících cest:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-164">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="2fd4d-165">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-165">Right-click the project in **Solution Explorer** and select **Publish**.</span></span>
* <span data-ttu-id="2fd4d-166">Vyberte **publikovat {název projektu}** z **sestavení** nabídky.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-166">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="2fd4d-167">**Publikovat** kartu Možnosti stránky aplikace se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-167">The **Publish** tab of the app capabilities page is displayed.</span></span> <span data-ttu-id="2fd4d-168">Pokud projekt nemá profil publikování, **vyberte cíl publikování** zobrazí se stránka.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-168">If the project lacks a publish profile, the **Pick a publish target** page is displayed.</span></span> <span data-ttu-id="2fd4d-169">Zobrazí se dotaz, vyberte jednu z těchto cílů publikovat:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-169">You're asked to select one of the following publish targets:</span></span>

* <span data-ttu-id="2fd4d-170">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="2fd4d-170">Azure App Service</span></span>
* <span data-ttu-id="2fd4d-171">Azure App Service v Linuxu</span><span class="sxs-lookup"><span data-stu-id="2fd4d-171">Azure App Service on Linux</span></span>
* <span data-ttu-id="2fd4d-172">Azure Virtual Machines</span><span class="sxs-lookup"><span data-stu-id="2fd4d-172">Azure Virtual Machines</span></span>
* <span data-ttu-id="2fd4d-173">Folder</span><span class="sxs-lookup"><span data-stu-id="2fd4d-173">Folder</span></span>
* <span data-ttu-id="2fd4d-174">Služba IIS, FTP, nasazení webu (pro všechny webové servery)</span><span class="sxs-lookup"><span data-stu-id="2fd4d-174">IIS, FTP, Web Deploy (for any web server)</span></span>
* <span data-ttu-id="2fd4d-175">Importovat profil</span><span class="sxs-lookup"><span data-stu-id="2fd4d-175">Import Profile</span></span>

<span data-ttu-id="2fd4d-176">Pokud chcete určit nejvhodnější publikovat cíl, naleznete v tématu [jaké možnosti publikování jsou pro mě nejlepší](/visualstudio/ide/not-in-toc/web-publish-options).</span><span class="sxs-lookup"><span data-stu-id="2fd4d-176">To determine the most appropriate publish target, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="2fd4d-177">Když **složky** publikovat cíl se vybírá, zadejte cestu ke složce pro ukládání publikovaných prostředků.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-177">When the **Folder** publish target is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="2fd4d-178">Výchozí cesta je *bin\\{konfigurace projektu}\\{MONIKER CÍLOVÉHO rozhraní} \publish\\* .</span><span class="sxs-lookup"><span data-stu-id="2fd4d-178">The default folder path is *bin\\{PROJECT CONFIGURATION}\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="2fd4d-179">Například *bin\Release\netcoreapp2.2\publish\\* .</span><span class="sxs-lookup"><span data-stu-id="2fd4d-179">For example, *bin\Release\netcoreapp2.2\publish\\*.</span></span> <span data-ttu-id="2fd4d-180">Vyberte **vytvořit profil** tlačítko Dokončit.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-180">Select the **Create Profile** button to finish.</span></span>

<span data-ttu-id="2fd4d-181">Po vytvoření profilu publikování **publikovat** změny obsahu karty.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-181">Once a publish profile is created, the **Publish** tab's content changes.</span></span> <span data-ttu-id="2fd4d-182">V rozevíracím seznamu se zobrazí nově vytvořený profil.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-182">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="2fd4d-183">V rozevíracím seznamu níže vyberte **vytvořit nový profil** vytvořte další nový profil.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-183">Below the drop-down list, select **Create new profile** to create another new profile.</span></span>

<span data-ttu-id="2fd4d-184">Nástroj Publikovat sady Visual Studio vytvoří *vlastnosti/PublishProfiles / {název profilu} .pubxml* MSBuild soubor s popisem profilu publikování.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-184">Visual Studio's publish tool produces a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file describing the publish profile.</span></span> <span data-ttu-id="2fd4d-185">*.Pubxml* souboru:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-185">The *.pubxml* file:</span></span>

* <span data-ttu-id="2fd4d-186">Obsahuje konfigurační nastavení publikování a je využívána proces publikování.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-186">Contains publish configuration settings and is consumed by the publishing process.</span></span>
* <span data-ttu-id="2fd4d-187">Můžete upravit tak, aby vlastní nastavení sestavení a publikování procesu.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-187">Can be modified to customize the build and publish process.</span></span>

<span data-ttu-id="2fd4d-188">Při publikování do Azure cíl, *.pubxml* soubor obsahuje identifikátor vašeho předplatného Azure.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-188">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="2fd4d-189">S cílovým typem přidává se tento soubor do správy zdrojového kódu se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-189">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="2fd4d-190">Při publikování na cíl mimo Azure, je k vrácení se změnami *.pubxml* souboru.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-190">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="2fd4d-191">Se šifrují citlivé údaje (třeba publikovat heslo) na úrovni uživatele nebo počítače.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-191">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="2fd4d-192">Je uložený v *vlastnosti/PublishProfiles / {název profilu}.pubxml.user* souboru.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-192">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="2fd4d-193">Protože tento soubor můžete uložit citlivé informace, by neměla být zaškrtnuta do správy zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-193">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="2fd4d-194">Přehled o tom, jak publikovat webovou aplikaci ASP.NET Core, najdete v části <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-194">For an overview of how to publish an ASP.NET Core web app, see <xref:host-and-deploy/index>.</span></span> <span data-ttu-id="2fd4d-195">Úlohy MSBuild a cíle plánování pro publikování webové aplikace ASP.NET Core je open source na [aspnet/websdk úložiště](https://github.com/aspnet/websdk).</span><span class="sxs-lookup"><span data-stu-id="2fd4d-195">The MSBuild tasks and targets necessary to publish an ASP.NET Core web app are open-source at the [aspnet/websdk repository](https://github.com/aspnet/websdk).</span></span>

<span data-ttu-id="2fd4d-196">`dotnet publish` Složku, MSDeploy, můžete použít příkaz a [Kudu](https://github.com/projectkudu/kudu/wiki) publikační profily.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-196">The `dotnet publish` command can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles.</span></span> <span data-ttu-id="2fd4d-197">Protože MSDeploy nemá podporu pro různé platformy, následující možnosti MSDeploy jsou podporovány pouze na Windows.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-197">Because MSDeploy lacks cross-platform support, the following MSDeploy options are supported only on Windows.</span></span>

<span data-ttu-id="2fd4d-198">**Složka (funguje napříč platformami):**</span><span class="sxs-lookup"><span data-stu-id="2fd4d-198">**Folder (works cross-platform):**</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="2fd4d-199">**MSDeploy:**</span><span class="sxs-lookup"><span data-stu-id="2fd4d-199">**MSDeploy:**</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="2fd4d-200">**MSDeploy balíčku:**</span><span class="sxs-lookup"><span data-stu-id="2fd4d-200">**MSDeploy package:**</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="2fd4d-201">V předchozích ukázkách, nepředávejte `deployonbuild` k `dotnet publish`.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-201">In the preceding examples, don't pass `deployonbuild` to `dotnet publish`.</span></span>

<span data-ttu-id="2fd4d-202">Další informace najdete v tématu [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span><span class="sxs-lookup"><span data-stu-id="2fd4d-202">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="2fd4d-203">`dotnet publish` podporuje rozhraní API Kudu k publikování do Azure z jakékoliv platformy.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-203">`dotnet publish` supports Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="2fd4d-204">Visual Studio publikování podporuje Kudu rozhraní API, ale je podporovaný modulem WebSDK pro různé platformy publikovat do Azure.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-204">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>

<span data-ttu-id="2fd4d-205">Přidat profil publikování projektu *vlastnosti/PublishProfiles* složka s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-205">Add a publish profile to the project's *Properties/PublishProfiles* folder with the following content:</span></span>

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

<span data-ttu-id="2fd4d-206">Spusťte následující příkaz, který zip obsah publikovat a publikujete ji do Azure pomocí rozhraní API Kudu:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-206">Run the following command to zip up the publish contents and publish it to Azure using the Kudu APIs:</span></span>

```console
dotnet publish /p:PublishProfile=Azure /p:Configuration=Release
```

<span data-ttu-id="2fd4d-207">Při použití profil publikování, nastavte následující vlastnosti MSBuild:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-207">Set the following MSBuild properties when using a publish profile:</span></span>

* `DeployOnBuild=true`
* `PublishProfile={PUBLISH PROFILE}`

<span data-ttu-id="2fd4d-208">Při publikování tak, že profil s názvem *FolderProfile*, lze provést jednu z následujících příkazů:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-208">When publishing with a profile named *FolderProfile*, either of the commands below can be executed:</span></span>

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild      /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="2fd4d-209">.NET Core CLI [dotnet sestavení](/dotnet/core/tools/dotnet-build) příkaz volání `msbuild` ke spuštění sestavení a publikování procesu.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-209">The .NET Core CLI's [dotnet build](/dotnet/core/tools/dotnet-build) command calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="2fd4d-210">`dotnet build` a `msbuild` příkazy jsou ekvivalentní při předávání ve složce profilu.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-210">The `dotnet build` and `msbuild` commands are equivalent when passing in a folder profile.</span></span> <span data-ttu-id="2fd4d-211">Při volání metody `msbuild` přímo na Windows, se používá rozhraní .NET Framework verze nástroje MSBuild.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-211">When calling `msbuild` directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="2fd4d-212">Volání `dotnet build` na mimo složku profilu:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-212">Calling `dotnet build` on a non-folder profile:</span></span>

* <span data-ttu-id="2fd4d-213">Vyvolá `msbuild`, který používá MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-213">Invokes `msbuild`, which uses MSDeploy.</span></span>
* <span data-ttu-id="2fd4d-214">Důsledkem chyby (i když a systémem Windows).</span><span class="sxs-lookup"><span data-stu-id="2fd4d-214">Results in a failure (even when running on Windows).</span></span> <span data-ttu-id="2fd4d-215">Chcete-li publikovat pomocí profilu bez složky, zavolejte `msbuild` přímo.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-215">To publish with a non-folder profile, call `msbuild` directly.</span></span>

<span data-ttu-id="2fd4d-216">Profil publikování následující složka byla vytvořena pomocí sady Visual Studio a publikuje do sdílené síťové složky:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-216">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

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

<span data-ttu-id="2fd4d-217">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-217">In the preceding example:</span></span>

* <span data-ttu-id="2fd4d-218">`<ExcludeApp_Data>` Vlastnost je k dispozici pouze pro uspokojení požadavku na XML schématu.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-218">The `<ExcludeApp_Data>` property is present merely to satisfy an XML schema requirement.</span></span> <span data-ttu-id="2fd4d-219">`<ExcludeApp_Data>` Vlastnost nemá žádný vliv na proces publikování, i v případě *App_Data* složky v kořenové složce projektu.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-219">The `<ExcludeApp_Data>` property has no effect on the publish process, even if there's an *App_Data* folder in the project root.</span></span> <span data-ttu-id="2fd4d-220">*App_Data* složky neobdrží zvláštní zacházení, stejně jako v projektech ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-220">The *App_Data* folder doesn't receive special treatment as it does in ASP.NET 4.x projects.</span></span>

* <span data-ttu-id="2fd4d-221">`<LastUsedBuildConfiguration>` Je nastavena na `Release`.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-221">The `<LastUsedBuildConfiguration>` property is set to `Release`.</span></span> <span data-ttu-id="2fd4d-222">Při publikování ze sady Visual Studio, hodnota `<LastUsedBuildConfiguration>` nastavená pomocí hodnoty, když se spustí proces publikování.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-222">When publishing from Visual Studio, the value of `<LastUsedBuildConfiguration>` is set using the value when the publish process is started.</span></span> <span data-ttu-id="2fd4d-223">`<LastUsedBuildConfiguration>` je speciální a nesmí být přepsána nastaveními v importovaný soubor MSBuild.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-223">`<LastUsedBuildConfiguration>` is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="2fd4d-224">Tato vlastnost může, ale být přepsáno z příkazového řádku pomocí jedné z následujících přístupů.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-224">This property can, however, be overridden from the command line using one of the following approaches.</span></span>
  * <span data-ttu-id="2fd4d-225">Použití .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-225">Using the .NET Core CLI:</span></span>

    ```console
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * <span data-ttu-id="2fd4d-226">Použití nástroje MSBuild:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-226">Using MSBuild:</span></span>

    ```console
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  <span data-ttu-id="2fd4d-227">Další informace najdete v tématu [MSBuild: jak nastavit vlastnost konfigurace](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span><span class="sxs-lookup"><span data-stu-id="2fd4d-227">For more information, see [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span></span>

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="2fd4d-228">Publikování do koncového bodu MSDeploy z příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="2fd4d-228">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="2fd4d-229">Následující příklad používá webovou aplikaci ASP.NET Core, vytvořené pomocí sady Visual Studio s názvem *AzureWebApp*.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-229">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp*.</span></span> <span data-ttu-id="2fd4d-230">Profil publikování aplikací Azure se přidá s Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-230">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="2fd4d-231">Další informace o tom, jak vytvořit profil, najdete v článku [publikační profily](#publish-profiles) oddílu.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-231">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="2fd4d-232">Chcete-li nasadit aplikaci pomocí profilu publikování, spusťte `msbuild` příkaz ze sady Visual Studio **Developer Command Prompt**.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-232">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt**.</span></span> <span data-ttu-id="2fd4d-233">Je k dispozici v příkazovém řádku *sady Visual Studio* složky **Start** nabídky na hlavním panelu Windows.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-233">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="2fd4d-234">Pro snadnější přístup, můžete přidat do příkazového řádku **nástroje** nabídky v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-234">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="2fd4d-235">Další informace najdete v tématu [Developer Command Prompt pro sadu Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="2fd4d-235">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="2fd4d-236">Nástroj MSBuild používá následující syntaxe příkazu:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-236">MSBuild uses the following command syntax:</span></span>

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="2fd4d-237">POLOŽKY {PATH} &ndash; Cestu k souboru projektu vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-237">{PATH} &ndash; Path to the app's project file.</span></span>
* <span data-ttu-id="2fd4d-238">{PROFIL} &ndash; Název profilu publikování.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-238">{PROFILE} &ndash; Name of the publish profile.</span></span>
* <span data-ttu-id="2fd4d-239">{USERNAME} &ndash; MSDeploy uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-239">{USERNAME} &ndash; MSDeploy username.</span></span> <span data-ttu-id="2fd4d-240">{USERNAME} najdete v profilu publikování.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-240">The {USERNAME} can be found in the publish profile.</span></span>
* <span data-ttu-id="2fd4d-241">{HESLO} &ndash; MSDeploy heslo.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-241">{PASSWORD} &ndash; MSDeploy password.</span></span> <span data-ttu-id="2fd4d-242">Získat {heslo} z *{profil}. PublishSettings* souboru.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-242">Obtain the {PASSWORD} from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="2fd4d-243">Stáhněte si *. PublishSettings* soubor z aplikace:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-243">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="2fd4d-244">**Průzkumník řešení**: Vyberte **zobrazení** > **Cloud Explorer**.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-244">**Solution Explorer**: Select **View** > **Cloud Explorer**.</span></span> <span data-ttu-id="2fd4d-245">Připojení k vašemu předplatnému Azure.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-245">Connect with your Azure subscription.</span></span> <span data-ttu-id="2fd4d-246">Otevřít **App Services**.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-246">Open **App Services**.</span></span> <span data-ttu-id="2fd4d-247">Klikněte pravým tlačítkem na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-247">Right-click the app.</span></span> <span data-ttu-id="2fd4d-248">Vyberte **stáhnout profil publikování**.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-248">Select **Download Publish Profile**.</span></span>
  * <span data-ttu-id="2fd4d-249">Azure portal: Vyberte **získat profil publikování** ve webové aplikaci **přehled** panelu.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-249">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="2fd4d-250">Následující příklad používá profil publikování s názvem *AzureWebApp – nasazení webu*:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-250">The following example uses a publish profile named *AzureWebApp - Web Deploy*:</span></span>

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="2fd4d-251">Profil publikování můžete také použít s .NET Core CLI [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) z příkazové okno Windows:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-251">A publish profile can also be used with the .NET Core CLI's [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command shell:</span></span>

```console
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> <span data-ttu-id="2fd4d-252">`dotnet msbuild` Příkaz příkaz pro různé platformy a můžete zkompilovat aplikace ASP.NET Core v macOS a Linuxu.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-252">The `dotnet msbuild` command is a cross-platform command and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="2fd4d-253">Však není schopné nasadit aplikaci do Azure nebo jiné koncové body MSDeploy MSBuild v systému macOS a Linux.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-253">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoints.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="2fd4d-254">Nastavení prostředí</span><span class="sxs-lookup"><span data-stu-id="2fd4d-254">Set the environment</span></span>

<span data-ttu-id="2fd4d-255">Zahrnout `<EnvironmentName>` vlastnost v profilu publikování ( *.pubxml*) nebo soubor projektu a nastavení aplikace [prostředí](xref:fundamentals/environments):</span><span class="sxs-lookup"><span data-stu-id="2fd4d-255">Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="2fd4d-256">Pokud budete potřebovat *web.config* transformace (například nastavení proměnné prostředí na základě konfigurace, profil nebo prostředí), najdete v článku <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-256">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="2fd4d-257">Vyloučení souborů</span><span class="sxs-lookup"><span data-stu-id="2fd4d-257">Exclude files</span></span>

<span data-ttu-id="2fd4d-258">Při publikování webové aplikace ASP.NET Core, jsou zahrnuty následující prostředky:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-258">When publishing ASP.NET Core web apps, the following assets are included:</span></span>

* <span data-ttu-id="2fd4d-259">Artefakty sestavení</span><span class="sxs-lookup"><span data-stu-id="2fd4d-259">Build artifacts</span></span>
* <span data-ttu-id="2fd4d-260">Složky a soubory odpovídající následujících vzorů podpory zástupných znaků:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-260">Folders and files matching the following globbing patterns:</span></span>
  * <span data-ttu-id="2fd4d-261">`**\*.config` (například *web.config*)</span><span class="sxs-lookup"><span data-stu-id="2fd4d-261">`**\*.config` (for example, *web.config*)</span></span>
  * <span data-ttu-id="2fd4d-262">`**\*.json` (například *appsettings.json*)</span><span class="sxs-lookup"><span data-stu-id="2fd4d-262">`**\*.json` (for example, *appsettings.json*)</span></span>
  * `wwwroot\**`

<span data-ttu-id="2fd4d-263">Podporuje MSBuild [vzorů podpory zástupných znaků](https://gruntjs.com/configuring-tasks#globbing-patterns).</span><span class="sxs-lookup"><span data-stu-id="2fd4d-263">MSBuild supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="2fd4d-264">Například následující `<Content>` element potlačí kopírování textu ( *.txt*) soubory *wwwroot\content* složce a jejích podsložkách:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-264">For example, the following `<Content>` element suppresses the copying of text (*.txt*) files in the *wwwroot\content* folder and its subfolders:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="2fd4d-265">Předchozí kód lze přidat do profilu publikování nebo *.csproj* souboru.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-265">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="2fd4d-266">Když se přidá *.csproj* , pravidlo se přidá soubor všechny profily publikování v projektu.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-266">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="2fd4d-267">Následující `<MsDeploySkipRules>` element vyloučí všechny soubory *wwwroot\content* složky:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-267">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot\content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="2fd4d-268">`<MsDeploySkipRules>` nedojde k odstranění *přeskočit* cílů nasazení webu.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-268">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="2fd4d-269">`<Content>` cílové soubory a složky, jsou odstraněny z nasazení webu.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-269">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="2fd4d-270">Předpokládejme například, že nasazené webové aplikace má následující soubory:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-270">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="2fd4d-271">*Views/Home/About1.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2fd4d-271">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="2fd4d-272">*Views/Home/About2.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2fd4d-272">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="2fd4d-273">*Views/Home/About3.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2fd4d-273">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="2fd4d-274">Pokud následující `<MsDeploySkipRules>` prvky jsou přidány, by dojít k odstranění těchto souborů na web pro nasazení.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-274">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

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

<span data-ttu-id="2fd4d-275">Předchozí `<MsDeploySkipRules>` zabránit prvky *přeskočeno* soubory z nasazení.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-275">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="2fd4d-276">Tyto soubory neodstraní po nasazení.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-276">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="2fd4d-277">Následující `<Content>` element odstraní cílové soubory na web pro nasazení:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-277">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="2fd4d-278">Pomocí příkazového řádku nasazení s předchozím `<Content>` element vrací varianta následující výstup:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-278">Using command-line deployment with the preceding `<Content>` element yields a variation of the following output:</span></span>

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

## <a name="include-files"></a><span data-ttu-id="2fd4d-279">Soubory k zahrnutí</span><span class="sxs-lookup"><span data-stu-id="2fd4d-279">Include files</span></span>

<span data-ttu-id="2fd4d-280">Následující části osnovy různé přístupy k zahrnutí souboru na čas publikování.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-280">The following sections outline different approaches for file inclusion at publish time.</span></span> <span data-ttu-id="2fd4d-281">[Zahrnutí souboru Obecné](#general-file-inclusion) části používá `DotNetPublishFiles` položky, které zajišťuje publikování souboru cílů v sadě SDK webové.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-281">The [General file inclusion](#general-file-inclusion) section uses the `DotNetPublishFiles` item, which is provided by a publish targets file in the Web SDK.</span></span> <span data-ttu-id="2fd4d-282">[Selektivní soubor zahrnutí](#selective-file-inclusion) části používá `ResolvedFileToPublish` položky, které zajišťuje publikování souboru cílů v .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-282">The [Selective file inclusion](#selective-file-inclusion) section uses the `ResolvedFileToPublish` item, which is provided by a publish targets file in the .NET Core SDK.</span></span> <span data-ttu-id="2fd4d-283">Vzhledem k tomu, že Web SDK závisí na .NET Core SDK, buď položka se dá použít v projektu aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-283">Because the Web SDK depends on the .NET Core SDK, either item can be used in an ASP.NET Core project.</span></span>

### <a name="general-file-inclusion"></a><span data-ttu-id="2fd4d-284">Obecné sdílené zahrnutí</span><span class="sxs-lookup"><span data-stu-id="2fd4d-284">General file inclusion</span></span>

<span data-ttu-id="2fd4d-285">Následující příklad `<ItemGroup>` element ukazuje kopírování složky nachází mimo adresář projektu do složky publikovaného webu.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-285">The following example's `<ItemGroup>` element demonstrates copying a folder located outside of the project directory to a folder of the published site.</span></span> <span data-ttu-id="2fd4d-286">Všechny soubory přidané do následující značky `<ItemGroup>` jsou zahrnuté ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-286">Any files added to the following markup's `<ItemGroup>` are included by default.</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="2fd4d-287">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-287">The preceding markup:</span></span>

* <span data-ttu-id="2fd4d-288">Lze přidat *.csproj* souboru nebo profil publikování.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-288">Can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="2fd4d-289">Pokud je přidána do *.csproj* souboru, je zahrnutý v každé profilu publikování v projektu.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-289">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>
* <span data-ttu-id="2fd4d-290">Deklaruje `_CustomFiles` položku, kterou chcete ukládat soubory odpovídající `Include` model podpory zástupných znaků atributu.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-290">Declares a `_CustomFiles` item to store files matching the `Include` attribute's globbing pattern.</span></span> <span data-ttu-id="2fd4d-291">*Imagí* složky odkazuje ve vzoru se nachází mimo adresář projektu.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-291">The *images* folder referenced in the pattern is located outside of the project directory.</span></span> <span data-ttu-id="2fd4d-292">A [rezervované vlastnosti](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties)s názvem `$(MSBuildProjectDirectory)`, se přeloží na absolutní cestu k souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-292">A [reserved property](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), named `$(MSBuildProjectDirectory)`, resolves to the project file's absolute path.</span></span>
* <span data-ttu-id="2fd4d-293">Obsahuje seznam souborů, které mají `DotNetPublishFiles` položky.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-293">Provides a list of files to the `DotNetPublishFiles` item.</span></span> <span data-ttu-id="2fd4d-294">Ve výchozím nastavení, položka společnosti `<DestinationRelativePath>` element je prázdný.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-294">By default, the item's `<DestinationRelativePath>` element is empty.</span></span> <span data-ttu-id="2fd4d-295">Výchozí hodnota je přepsána v kódu a používá [známá metadata položky](/visualstudio/msbuild/msbuild-well-known-item-metadata) například `%(RecursiveDir)`.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-295">The default value is overridden in the markup and uses [well-known item metadata](/visualstudio/msbuild/msbuild-well-known-item-metadata) such as `%(RecursiveDir)`.</span></span> <span data-ttu-id="2fd4d-296">Představuje vnitřní text *wwwroot/imagí* složky publikovaného webu.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-296">The inner text represents the *wwwroot/images* folder of the published site.</span></span>

### <a name="selective-file-inclusion"></a><span data-ttu-id="2fd4d-297">Zahrnutí selektivní souboru</span><span class="sxs-lookup"><span data-stu-id="2fd4d-297">Selective file inclusion</span></span>

<span data-ttu-id="2fd4d-298">Ukazuje zvýrazněný kód v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-298">The highlighted markup in the following example demonstrates:</span></span>

* <span data-ttu-id="2fd4d-299">Kopírování souboru do publikovaného webu se sídlem mimo projekt *wwwroot* složky.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-299">Copying a file located outside of the project into the published site's *wwwroot* folder.</span></span> <span data-ttu-id="2fd4d-300">Název souboru *ReadMe2.md* zachovaný.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-300">The file name of *ReadMe2.md* is maintained.</span></span>
* <span data-ttu-id="2fd4d-301">S výjimkou *wwwroot\Content* složky.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-301">Excluding the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="2fd4d-302">S výjimkou *Views\Home\About2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-302">Excluding *Views\Home\About2.cshtml*.</span></span>

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

<span data-ttu-id="2fd4d-303">V předchozím příkladu `ResolvedFileToPublish` položku, jejíž výchozí chování je vždy kopírovat soubory součástí `Include` atribut publikovaného webu.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-303">The preceding example uses the `ResolvedFileToPublish` item, whose default behavior is to always copy the files provided in the `Include` attribute to the published site.</span></span> <span data-ttu-id="2fd4d-304">Výchozí chování potlačíte včetně `<CopyToPublishDirectory>` podřízený element s vnitřní text buď `Never` nebo `PreserveNewest`.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-304">Override the default behavior by including a `<CopyToPublishDirectory>` child element with inner text of either `Never` or `PreserveNewest`.</span></span> <span data-ttu-id="2fd4d-305">Příklad:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-305">For example:</span></span>

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

<span data-ttu-id="2fd4d-306">Další ukázky nasazení, najdete v článku [Web SDK úložiště Readme](https://github.com/aspnet/websdk).</span><span class="sxs-lookup"><span data-stu-id="2fd4d-306">For more deployment samples, see the [Web SDK repository Readme](https://github.com/aspnet/websdk).</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="2fd4d-307">Spustit cíl před nebo po publikování</span><span class="sxs-lookup"><span data-stu-id="2fd4d-307">Run a target before or after publishing</span></span>

<span data-ttu-id="2fd4d-308">Předdefinované `BeforePublish` a `AfterPublish` cíle spustit cíl před nebo za cíl publikování.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-308">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="2fd4d-309">Přidáte do profilu publikování k protokolování zpráv konzoly před i po publikování následující prvky:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-309">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="2fd4d-310">Publikovat na server pomocí nedůvěryhodného certifikátu</span><span class="sxs-lookup"><span data-stu-id="2fd4d-310">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="2fd4d-311">Přidat `<AllowUntrustedCertificate>` vlastnost s hodnotou `True` do profilu publikování:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-311">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="2fd4d-312">Kudu service</span><span class="sxs-lookup"><span data-stu-id="2fd4d-312">The Kudu service</span></span>

<span data-ttu-id="2fd4d-313">Chcete-li zobrazit soubory v nasazení služby Azure App Service webovou aplikaci, použijte [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span><span class="sxs-lookup"><span data-stu-id="2fd4d-313">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="2fd4d-314">Připojit `scm` tokenu název webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-314">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="2fd4d-315">Příklad:</span><span class="sxs-lookup"><span data-stu-id="2fd4d-315">For example:</span></span>

| <span data-ttu-id="2fd4d-316">Adresa URL</span><span class="sxs-lookup"><span data-stu-id="2fd4d-316">URL</span></span>                                    | <span data-ttu-id="2fd4d-317">Výsledek</span><span class="sxs-lookup"><span data-stu-id="2fd4d-317">Result</span></span>       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | <span data-ttu-id="2fd4d-318">Webové aplikace</span><span class="sxs-lookup"><span data-stu-id="2fd4d-318">Web App</span></span>      |
| `http://mysite.scm.azurewebsites.net/` | <span data-ttu-id="2fd4d-319">Kudu service</span><span class="sxs-lookup"><span data-stu-id="2fd4d-319">Kudu service</span></span> |

<span data-ttu-id="2fd4d-320">Vyberte [ladění konzoly](https://github.com/projectkudu/kudu/wiki/Kudu-console) položka nabídky zobrazit, upravit, odstranit nebo přidat soubory.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-320">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2fd4d-321">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2fd4d-321">Additional resources</span></span>

* <span data-ttu-id="2fd4d-322">[Webu nasadit](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) zjednodušuje nasazení webové aplikace a weby pro servery služby IIS.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-322">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="2fd4d-323">[Úložiště GitHub sady SDK webové](https://github.com/aspnet/websdk/issues): Soubor problémů a požádat o funkce pro nasazení.</span><span class="sxs-lookup"><span data-stu-id="2fd4d-323">[Web SDK GitHub repository](https://github.com/aspnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="2fd4d-324">Publikování webové aplikace v ASP.NET do virtuálního počítače Azure ze sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fd4d-324">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
