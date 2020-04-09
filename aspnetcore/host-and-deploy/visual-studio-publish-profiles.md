---
title: Visual Studio publikuje profily (.pubxml) pro nasazení aplikace ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vytvořit profily publikování ve Visual Studiu a použít je pro správu nasazení aplikací ASP.NET Core na různé cíle.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: 274dd2cd528d3766aa07f69aac3470a131c79ffe
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659374"
---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a><span data-ttu-id="dba1d-103">Visual Studio publikuje profily (.pubxml) pro nasazení aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dba1d-103">Visual Studio publish profiles (.pubxml) for ASP.NET Core app deployment</span></span>

<span data-ttu-id="dba1d-104">Podle [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="dba1d-104">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="dba1d-105">Tento dokument se zaměřuje na použití Visual Studio 2019 nebo novější k vytvoření a použití profilů publikování.</span><span class="sxs-lookup"><span data-stu-id="dba1d-105">This document focuses on using Visual Studio 2019 or later to create and use publish profiles.</span></span> <span data-ttu-id="dba1d-106">Profily publikování vytvořené pomocí sady Visual Studio lze použít s MSBuild a Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="dba1d-106">The publish profiles created with Visual Studio can be used with MSBuild and Visual Studio.</span></span> <span data-ttu-id="dba1d-107">Pokyny k publikování v <xref:tutorials/publish-to-azure-webapp-using-vs>Azure najdete v tématu .</span><span class="sxs-lookup"><span data-stu-id="dba1d-107">For instructions on publishing to Azure, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

<span data-ttu-id="dba1d-108">Příkaz `dotnet new mvc` vytvoří soubor projektu obsahující následující prvek [ \<projektu>](/visualstudio/msbuild/project-element-msbuild)kořenové úrovně :</span><span class="sxs-lookup"><span data-stu-id="dba1d-108">The `dotnet new mvc` command produces a project file containing the following root-level [\<Project> element](/visualstudio/msbuild/project-element-msbuild):</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

<span data-ttu-id="dba1d-109">Atribut předchozího `<Project>` `Sdk` prvku importuje [vlastnosti](/visualstudio/msbuild/msbuild-properties) a [cíle](/visualstudio/msbuild/msbuild-targets) MSBuild z *aplikace $(MSBuildSDKSPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* a *$(MSBuildSDKSPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*.</span><span class="sxs-lookup"><span data-stu-id="dba1d-109">The preceding `<Project>` element's `Sdk` attribute imports the MSBuild [properties](/visualstudio/msbuild/msbuild-properties) and [targets](/visualstudio/msbuild/msbuild-targets) from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* and *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectively.</span></span> <span data-ttu-id="dba1d-110">Výchozí umístění `$(MSBuildSDKsPath)` pro (s aplikací Visual Studio 2019 Enterprise) je složka *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks.*</span><span class="sxs-lookup"><span data-stu-id="dba1d-110">The default location for `$(MSBuildSDKsPath)` (with Visual Studio 2019 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="dba1d-111">`Microsoft.NET.Sdk.Web`(Web SDK) závisí na jiných sadách SDK, včetně `Microsoft.NET.Sdk` `Microsoft.NET.Sdk.Razor` (.NET Core SDK) a[(Razor SDK).](xref:razor-pages/sdk)</span><span class="sxs-lookup"><span data-stu-id="dba1d-111">`Microsoft.NET.Sdk.Web` (Web SDK) depends on other SDKs, including `Microsoft.NET.Sdk` (.NET Core SDK) and `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span></span> <span data-ttu-id="dba1d-112">Vlastnosti msbuild a cíle přidružené ke každé závislé sady SDK jsou importovány.</span><span class="sxs-lookup"><span data-stu-id="dba1d-112">The MSBuild properties and targets associated with each dependent SDK are imported.</span></span> <span data-ttu-id="dba1d-113">Cíl publikování importuje příslušnou sadu cílů na základě použité metody publikování.</span><span class="sxs-lookup"><span data-stu-id="dba1d-113">Publish targets import the appropriate set of targets based on the publish method used.</span></span>

<span data-ttu-id="dba1d-114">Když MSBuild nebo Visual Studio načte projekt, dojde k následujícím akcím vysoké úrovně:</span><span class="sxs-lookup"><span data-stu-id="dba1d-114">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="dba1d-115">Sestavení projektu</span><span class="sxs-lookup"><span data-stu-id="dba1d-115">Build project</span></span>
* <span data-ttu-id="dba1d-116">Výpočetní soubory k publikování</span><span class="sxs-lookup"><span data-stu-id="dba1d-116">Compute files to publish</span></span>
* <span data-ttu-id="dba1d-117">Publikování souborů do cíle</span><span class="sxs-lookup"><span data-stu-id="dba1d-117">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="dba1d-118">Vypočítat položky projektu</span><span class="sxs-lookup"><span data-stu-id="dba1d-118">Compute project items</span></span>

<span data-ttu-id="dba1d-119">Při načtení projektu jsou vypočítány [položky projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) (soubory).</span><span class="sxs-lookup"><span data-stu-id="dba1d-119">When the project is loaded, the [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) (files) are computed.</span></span> <span data-ttu-id="dba1d-120">Typ položky určuje způsob zpracování souboru.</span><span class="sxs-lookup"><span data-stu-id="dba1d-120">The item type determines how the file is processed.</span></span> <span data-ttu-id="dba1d-121">Ve výchozím nastavení jsou soubory `Compile` *.cs* zahrnuty v seznamu položek.</span><span class="sxs-lookup"><span data-stu-id="dba1d-121">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="dba1d-122">Soubory v `Compile` seznamu položek jsou kompilovány.</span><span class="sxs-lookup"><span data-stu-id="dba1d-122">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="dba1d-123">Seznam `Content` položek obsahuje soubory, které jsou publikovány kromě výstupů sestavení.</span><span class="sxs-lookup"><span data-stu-id="dba1d-123">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="dba1d-124">Ve výchozím nastavení jsou `wwwroot\**`soubory `**\*.config`odpovídající `**\*.json` vzorům `Content` a jsou zahrnuty do seznamu položek.</span><span class="sxs-lookup"><span data-stu-id="dba1d-124">By default, files matching the patterns `wwwroot\**`, `**\*.config`, and `**\*.json` are included in the `Content` item list.</span></span> <span data-ttu-id="dba1d-125">Například `wwwroot\**` [globbing vzor](https://gruntjs.com/configuring-tasks#globbing-patterns) odpovídá všechny soubory ve složce *wwwroot* a jeho podsložky.</span><span class="sxs-lookup"><span data-stu-id="dba1d-125">For example, the `wwwroot\**` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder and its subfolders.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dba1d-126">Sada Web SDK importuje [sadu Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="dba1d-126">The Web SDK imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="dba1d-127">Výsledkem je, že soubory `**\*.cshtml` `**\*.razor` odpovídající vzorky `Content` a jsou také zahrnuty v seznamu položek.</span><span class="sxs-lookup"><span data-stu-id="dba1d-127">As a result, files matching the patterns `**\*.cshtml` and `**\*.razor` are also included in the `Content` item list.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="dba1d-128">Sada Web SDK importuje [sadu Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="dba1d-128">The Web SDK imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="dba1d-129">V důsledku toho jsou `**\*.cshtml` soubory odpovídající vzoru `Content` také zahrnuty do seznamu položek.</span><span class="sxs-lookup"><span data-stu-id="dba1d-129">As a result, files matching the `**\*.cshtml` pattern are also included in the `Content` item list.</span></span>

::: moniker-end

<span data-ttu-id="dba1d-130">Chcete-li explicitně přidat soubor do seznamu publikování, přidejte jej přímo do souboru *.csproj,* jak je znázorněno v části [Zahrnout soubory.](#include-files)</span><span class="sxs-lookup"><span data-stu-id="dba1d-130">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in the [Include Files](#include-files) section.</span></span>

<span data-ttu-id="dba1d-131">Při výběru tlačítka **Publikovat** v sadě Visual Studio nebo při publikování z příkazového řádku:</span><span class="sxs-lookup"><span data-stu-id="dba1d-131">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="dba1d-132">Vlastnosti nebo položky jsou vypočítány (soubory, které jsou potřebné k sestavení).</span><span class="sxs-lookup"><span data-stu-id="dba1d-132">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="dba1d-133">**Pouze Visual Studio**: Balíčky NuGet jsou obnoveny.</span><span class="sxs-lookup"><span data-stu-id="dba1d-133">**Visual Studio only**: NuGet packages are restored.</span></span> <span data-ttu-id="dba1d-134">(Obnovení musí být explicitní uživatelem na rozhraní se konstruum.)</span><span class="sxs-lookup"><span data-stu-id="dba1d-134">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="dba1d-135">Projekt se staví.</span><span class="sxs-lookup"><span data-stu-id="dba1d-135">The project builds.</span></span>
* <span data-ttu-id="dba1d-136">Položky publikování jsou vypočítány (soubory, které jsou potřebné k publikování).</span><span class="sxs-lookup"><span data-stu-id="dba1d-136">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="dba1d-137">Projekt je publikován (vypočítané soubory jsou zkopírovány do cíle publikování).</span><span class="sxs-lookup"><span data-stu-id="dba1d-137">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="dba1d-138">Když ASP.NET základní projekt `Microsoft.NET.Sdk.Web` odkazuje v souboru projektu, soubor *app_offline.htm* je umístěn v kořenovém adresáři webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="dba1d-138">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="dba1d-139">Když je soubor k dispozici, ASP.NET základní modul řádně vypne aplikaci a slouží *souboru app_offline.htm* během nasazení.</span><span class="sxs-lookup"><span data-stu-id="dba1d-139">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="dba1d-140">Další informace naleznete v [odkazu na konfiguraci ASP.NET core modulem](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="dba1d-140">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="dba1d-141">Základní publikování příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="dba1d-141">Basic command-line publishing</span></span>

<span data-ttu-id="dba1d-142">Publikování příkazového řádku funguje na všech platformách podporovaných jádrem .NET a nevyžaduje visual studio.</span><span class="sxs-lookup"><span data-stu-id="dba1d-142">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="dba1d-143">V následujících příkladech je příkaz publikování [dotnet](/dotnet/core/tools/dotnet-publish) rozhraní .NET Core CLI spuštěn z adresáře projektu (který obsahuje soubor *.csproj).*</span><span class="sxs-lookup"><span data-stu-id="dba1d-143">In the following examples, the .NET Core CLI's [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="dba1d-144">Pokud složka projektu není aktuální pracovní adresář, explicitně předat v cestě k souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="dba1d-144">If the project folder isn't the current working directory, explicitly pass in the project file path.</span></span> <span data-ttu-id="dba1d-145">Příklad:</span><span class="sxs-lookup"><span data-stu-id="dba1d-145">For example:</span></span>

```dotnetcli
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="dba1d-146">Chcete-li vytvořit a publikovat webovou aplikaci, spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="dba1d-146">Run the following commands to create and publish a web app:</span></span>

```dotnetcli
dotnet new mvc
dotnet publish
```

<span data-ttu-id="dba1d-147">Příkaz `dotnet publish` vytvoří variantu následujícího výstupu:</span><span class="sxs-lookup"><span data-stu-id="dba1d-147">The `dotnet publish` command produces a variation of the following output:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

<span data-ttu-id="dba1d-148">Výchozí formát složky publikování je *bin\Debug\\{TARGET FRAMEWORK\\MONIKER}\publish*.</span><span class="sxs-lookup"><span data-stu-id="dba1d-148">The default publish folder format is *bin\Debug\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="dba1d-149">Například *bin\Debug\netcoreapp2.2\publish\\*.</span><span class="sxs-lookup"><span data-stu-id="dba1d-149">For example, *bin\Debug\netcoreapp2.2\publish\\*.</span></span>

<span data-ttu-id="dba1d-150">Následující příkaz určuje `Release` adresář sestavení a publikování:</span><span class="sxs-lookup"><span data-stu-id="dba1d-150">The following command specifies a `Release` build and the publishing directory:</span></span>

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="dba1d-151">Příkaz `dotnet publish` volá MSBuild, který `Publish` vyvolá cíl.</span><span class="sxs-lookup"><span data-stu-id="dba1d-151">The `dotnet publish` command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="dba1d-152">Všechny parametry `dotnet publish` předané jsou předány MSBuild.</span><span class="sxs-lookup"><span data-stu-id="dba1d-152">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="dba1d-153">A `-c` `-o` parametry mapovat na MSBuild `Configuration` a `OutputPath` vlastnosti, v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="dba1d-153">The `-c` and `-o` parameters map to MSBuild's `Configuration` and `OutputPath` properties, respectively.</span></span>

<span data-ttu-id="dba1d-154">Vlastnosti MSBuild lze předat pomocí některého z následujících formátů:</span><span class="sxs-lookup"><span data-stu-id="dba1d-154">MSBuild properties can be passed using either of the following formats:</span></span>

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="dba1d-155">Například následující příkaz publikuje `Release` sestavení do sdílené síťové složky.</span><span class="sxs-lookup"><span data-stu-id="dba1d-155">For example, the following command publishes a `Release` build to a network share.</span></span> <span data-ttu-id="dba1d-156">Sdílená síť je určena lomítkem *(//r8/*) a funguje na všech platformách podporovaných rozhraním .NET Core.</span><span class="sxs-lookup"><span data-stu-id="dba1d-156">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

<span data-ttu-id="dba1d-157">Zkontrolujte, že publikovaná aplikace pro nasazení není spuštěná.</span><span class="sxs-lookup"><span data-stu-id="dba1d-157">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="dba1d-158">Soubory ve složce *publikování* jsou při spuštění aplikace uzamčeny.</span><span class="sxs-lookup"><span data-stu-id="dba1d-158">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="dba1d-159">Nasazení nemůže dojít, protože uzamčené soubory nelze zkopírovat.</span><span class="sxs-lookup"><span data-stu-id="dba1d-159">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="dba1d-160">Profily publikování</span><span class="sxs-lookup"><span data-stu-id="dba1d-160">Publish profiles</span></span>

<span data-ttu-id="dba1d-161">Tato část používá Visual Studio 2019 nebo novější k vytvoření profilu publikování.</span><span class="sxs-lookup"><span data-stu-id="dba1d-161">This section uses Visual Studio 2019 or later to create a publishing profile.</span></span> <span data-ttu-id="dba1d-162">Po vytvoření profilu je k dispozici publikování z sady Visual Studio nebo příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="dba1d-162">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span> <span data-ttu-id="dba1d-163">Profily publikování mohou zjednodušit proces publikování a může existovat libovolný počet profilů.</span><span class="sxs-lookup"><span data-stu-id="dba1d-163">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span>

<span data-ttu-id="dba1d-164">Vytvořte profil publikování v sadě Visual Studio výběrem jedné z následujících cest:</span><span class="sxs-lookup"><span data-stu-id="dba1d-164">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="dba1d-165">Klepněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="dba1d-165">Right-click the project in **Solution Explorer** and select **Publish**.</span></span>
* <span data-ttu-id="dba1d-166">V nabídce Sestavení vyberte **Publikovat {NÁZEV PROJEKTU}.** **Build**</span><span class="sxs-lookup"><span data-stu-id="dba1d-166">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="dba1d-167">Zobrazí se karta **Publikovat** na stránce možností aplikace.</span><span class="sxs-lookup"><span data-stu-id="dba1d-167">The **Publish** tab of the app capabilities page is displayed.</span></span> <span data-ttu-id="dba1d-168">Pokud projekt nemá profil publikování, zobrazí se cílová stránka **Vyskladnění publikování.**</span><span class="sxs-lookup"><span data-stu-id="dba1d-168">If the project lacks a publish profile, the **Pick a publish target** page is displayed.</span></span> <span data-ttu-id="dba1d-169">Budete vyzváni k výběru jednoho z následujících cílů publikování:</span><span class="sxs-lookup"><span data-stu-id="dba1d-169">You're asked to select one of the following publish targets:</span></span>

* <span data-ttu-id="dba1d-170">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="dba1d-170">Azure App Service</span></span>
* <span data-ttu-id="dba1d-171">Služba Azure App Service na Linuxu</span><span class="sxs-lookup"><span data-stu-id="dba1d-171">Azure App Service on Linux</span></span>
* <span data-ttu-id="dba1d-172">Azure Virtual Machines</span><span class="sxs-lookup"><span data-stu-id="dba1d-172">Azure Virtual Machines</span></span>
* <span data-ttu-id="dba1d-173">Složka</span><span class="sxs-lookup"><span data-stu-id="dba1d-173">Folder</span></span>
* <span data-ttu-id="dba1d-174">Nasazení služby IIS, FTP, web (pro libovolný webový server)</span><span class="sxs-lookup"><span data-stu-id="dba1d-174">IIS, FTP, Web Deploy (for any web server)</span></span>
* <span data-ttu-id="dba1d-175">Importovat profil</span><span class="sxs-lookup"><span data-stu-id="dba1d-175">Import Profile</span></span>

<span data-ttu-id="dba1d-176">Chcete-li určit nejvhodnější cíl publikování, přečtěte [si téma Jaké možnosti publikování jsou pro mě vhodné](/visualstudio/ide/not-in-toc/web-publish-options).</span><span class="sxs-lookup"><span data-stu-id="dba1d-176">To determine the most appropriate publish target, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="dba1d-177">Když je vybraný cíl publikování **složky,** zadejte cestu ke složce pro uložení publikovaných datových zdrojů.</span><span class="sxs-lookup"><span data-stu-id="dba1d-177">When the **Folder** publish target is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="dba1d-178">Výchozí cesta ke složce je *\\přihrádka {KONFIGURACE PROJEKTU}\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span><span class="sxs-lookup"><span data-stu-id="dba1d-178">The default folder path is *bin\\{PROJECT CONFIGURATION}\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="dba1d-179">Například *bin\Release\netcoreapp2.2\publish\\*.</span><span class="sxs-lookup"><span data-stu-id="dba1d-179">For example, *bin\Release\netcoreapp2.2\publish\\*.</span></span> <span data-ttu-id="dba1d-180">K dokončení vyberte tlačítko **Vytvořit profil.**</span><span class="sxs-lookup"><span data-stu-id="dba1d-180">Select the **Create Profile** button to finish.</span></span>

<span data-ttu-id="dba1d-181">Po vytvoření profilu publikování se obsah karty **Publikovat** změní.</span><span class="sxs-lookup"><span data-stu-id="dba1d-181">Once a publish profile is created, the **Publish** tab's content changes.</span></span> <span data-ttu-id="dba1d-182">Nově vytvořený profil se zobrazí v rozevíracím seznamu.</span><span class="sxs-lookup"><span data-stu-id="dba1d-182">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="dba1d-183">Pod rozevíracím seznamem vyberte **Vytvořit nový profil** a vytvořte další nový profil.</span><span class="sxs-lookup"><span data-stu-id="dba1d-183">Below the drop-down list, select **Create new profile** to create another new profile.</span></span>

<span data-ttu-id="dba1d-184">Nástroj pro publikování sady Visual Studio vytváří soubor *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild popisující profil publikování.</span><span class="sxs-lookup"><span data-stu-id="dba1d-184">Visual Studio's publish tool produces a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file describing the publish profile.</span></span> <span data-ttu-id="dba1d-185">Soubor *.pubxml:*</span><span class="sxs-lookup"><span data-stu-id="dba1d-185">The *.pubxml* file:</span></span>

* <span data-ttu-id="dba1d-186">Obsahuje nastavení konfigurace publikování a je spotřebována procesem publikování.</span><span class="sxs-lookup"><span data-stu-id="dba1d-186">Contains publish configuration settings and is consumed by the publishing process.</span></span>
* <span data-ttu-id="dba1d-187">Lze upravit přizpůsobit proces sestavení a publikování.</span><span class="sxs-lookup"><span data-stu-id="dba1d-187">Can be modified to customize the build and publish process.</span></span>

<span data-ttu-id="dba1d-188">Při publikování na cíl Azure obsahuje soubor *PubXML* identifikátor předplatného Azure.</span><span class="sxs-lookup"><span data-stu-id="dba1d-188">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="dba1d-189">U tohoto typu cíle se nedoporučuje přidávat tento soubor do správy zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="dba1d-189">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="dba1d-190">Při publikování na cíl, který není Azure, je bezpečné soubor *pubxml.*</span><span class="sxs-lookup"><span data-stu-id="dba1d-190">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="dba1d-191">Citlivé informace (například heslo pro publikování) jsou šifrovány na úrovni uživatele/počítače.</span><span class="sxs-lookup"><span data-stu-id="dba1d-191">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="dba1d-192">Je uložen v souboru *Vlastnosti/PublishProfiles/{PROFILE NAME}.pubxml.user.*</span><span class="sxs-lookup"><span data-stu-id="dba1d-192">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="dba1d-193">Vzhledem k tomu, že tento soubor může ukládat citlivé informace, neměl by být zaškrtávádo správy zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="dba1d-193">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="dba1d-194">Přehled publikování webové aplikace ASP.NET Core najdete <xref:host-and-deploy/index>v tématu .</span><span class="sxs-lookup"><span data-stu-id="dba1d-194">For an overview of how to publish an ASP.NET Core web app, see <xref:host-and-deploy/index>.</span></span> <span data-ttu-id="dba1d-195">Úlohy a cíle MSBuild nezbytné k publikování webové aplikace ASP.NET Core jsou open source v [úložišti aspnet/websdk](https://github.com/aspnet/websdk).</span><span class="sxs-lookup"><span data-stu-id="dba1d-195">The MSBuild tasks and targets necessary to publish an ASP.NET Core web app are open-source in the [aspnet/websdk repository](https://github.com/aspnet/websdk).</span></span>

<span data-ttu-id="dba1d-196">Následující příkazy mohou používat profily publikování složky, MSDeploy a [Kudu.](https://github.com/projectkudu/kudu/wiki)</span><span class="sxs-lookup"><span data-stu-id="dba1d-196">The following commands can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles.</span></span> <span data-ttu-id="dba1d-197">Vzhledem k tomu, že msdeploy postrádá podporu napříč platformami, jsou následující možnosti MSDeploy podporovány pouze v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="dba1d-197">Because MSDeploy lacks cross-platform support, the following MSDeploy options are supported only on Windows.</span></span>

<span data-ttu-id="dba1d-198">**Složka (funguje napříč platformami):**</span><span class="sxs-lookup"><span data-stu-id="dba1d-198">**Folder (works cross-platform):**</span></span>

<!--

NOTE: Add back the following 'dotnet publish' folder publish example after https://github.com/aspnet/websdk/issues/888 is resolved.

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

-->

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="dba1d-199">**MSDeploy:**</span><span class="sxs-lookup"><span data-stu-id="dba1d-199">**MSDeploy:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="dba1d-200">**Balíček MSDeploy:**</span><span class="sxs-lookup"><span data-stu-id="dba1d-200">**MSDeploy package:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="dba1d-201">V předchozích příkladech:</span><span class="sxs-lookup"><span data-stu-id="dba1d-201">In the preceding examples:</span></span>

* <span data-ttu-id="dba1d-202">`dotnet publish`a `dotnet build` podporujte kudu API pro publikování do Azure z libovolné platformy.</span><span class="sxs-lookup"><span data-stu-id="dba1d-202">`dotnet publish` and `dotnet build` support Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="dba1d-203">Visual Studio publikování podporuje Kudu API, ale je podporované WebSDK pro publikování napříč platformami do Azure.</span><span class="sxs-lookup"><span data-stu-id="dba1d-203">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>
* <span data-ttu-id="dba1d-204">`DeployOnBuild` Nepřecházejte `dotnet publish` na příkaz.</span><span class="sxs-lookup"><span data-stu-id="dba1d-204">Don't pass `DeployOnBuild` to the `dotnet publish` command.</span></span>

<span data-ttu-id="dba1d-205">Další informace naleznete v tématu [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span><span class="sxs-lookup"><span data-stu-id="dba1d-205">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="dba1d-206">Přidejte profil publikování do složky *Vlastnosti/PublishProfiles* projektu s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="dba1d-206">Add a publish profile to the project's *Properties/PublishProfiles* folder with the following content:</span></span>

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

## <a name="folder-publish-example"></a><span data-ttu-id="dba1d-207">Příklad publikování složky</span><span class="sxs-lookup"><span data-stu-id="dba1d-207">Folder publish example</span></span>

<span data-ttu-id="dba1d-208">Při publikování pomocí profilu s názvem *FolderProfile*použijte některý z následujících příkazů:</span><span class="sxs-lookup"><span data-stu-id="dba1d-208">When publishing with a profile named *FolderProfile*, use either of the following commands:</span></span>

<!--

NOTE: Temporarily removed until https://github.com/aspnet/websdk/issues/888 is resolved.

* `dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`

-->

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="dba1d-209">Příkaz [dotnet sestavení](/dotnet/core/tools/dotnet-build) rozhraní .NET Core `msbuild` cli volání ke spuštění procesu sestavení a publikování.</span><span class="sxs-lookup"><span data-stu-id="dba1d-209">The .NET Core CLI's [dotnet build](/dotnet/core/tools/dotnet-build) command calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="dba1d-210">`dotnet build` Příkazy `msbuild` a jsou ekvivalentní při předávání v profilu složky.</span><span class="sxs-lookup"><span data-stu-id="dba1d-210">The `dotnet build` and `msbuild` commands are equivalent when passing in a folder profile.</span></span> <span data-ttu-id="dba1d-211">Při `msbuild` volání přímo v systému Windows se používá verze rozhraní .NET Framework msbuild.</span><span class="sxs-lookup"><span data-stu-id="dba1d-211">When calling `msbuild` directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="dba1d-212">Volání `dotnet build` na profil bez složky:</span><span class="sxs-lookup"><span data-stu-id="dba1d-212">Calling `dotnet build` on a non-folder profile:</span></span>

* <span data-ttu-id="dba1d-213">Vyvolá `msbuild`, který používá MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="dba1d-213">Invokes `msbuild`, which uses MSDeploy.</span></span>
* <span data-ttu-id="dba1d-214">Výsledkem je selhání (i při spuštění v systému Windows).</span><span class="sxs-lookup"><span data-stu-id="dba1d-214">Results in a failure (even when running on Windows).</span></span> <span data-ttu-id="dba1d-215">Chcete-li publikovat s profilem `msbuild` bez složky, zavolejte přímo.</span><span class="sxs-lookup"><span data-stu-id="dba1d-215">To publish with a non-folder profile, call `msbuild` directly.</span></span>

<span data-ttu-id="dba1d-216">Následující profil publikování složky byl vytvořen pomocí sady Visual Studio a publikuje se do sdílené síťové složky:</span><span class="sxs-lookup"><span data-stu-id="dba1d-216">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

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

<span data-ttu-id="dba1d-217">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="dba1d-217">In the preceding example:</span></span>

* <span data-ttu-id="dba1d-218">Vlastnost `<ExcludeApp_Data>` je k dispozici pouze pro splnění požadavku na schéma XML.</span><span class="sxs-lookup"><span data-stu-id="dba1d-218">The `<ExcludeApp_Data>` property is present merely to satisfy an XML schema requirement.</span></span> <span data-ttu-id="dba1d-219">Vlastnost `<ExcludeApp_Data>` nemá žádný vliv na proces publikování, i v případě, že je *App_Data* složky v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="dba1d-219">The `<ExcludeApp_Data>` property has no effect on the publish process, even if there's an *App_Data* folder in the project root.</span></span> <span data-ttu-id="dba1d-220">Složka *App_Data* nedostává zvláštní zacházení, jako tomu je v ASP.NET 4.x projekty.</span><span class="sxs-lookup"><span data-stu-id="dba1d-220">The *App_Data* folder doesn't receive special treatment as it does in ASP.NET 4.x projects.</span></span>

<!--

NOTE: Temporarily removed from 'Using the .NET Core CLI' below until https://github.com/aspnet/websdk/issues/888 is resolved.

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

-->

* <span data-ttu-id="dba1d-221">Vlastnost `<LastUsedBuildConfiguration>` je nastavena na `Release`.</span><span class="sxs-lookup"><span data-stu-id="dba1d-221">The `<LastUsedBuildConfiguration>` property is set to `Release`.</span></span> <span data-ttu-id="dba1d-222">Při publikování z visual studia `<LastUsedBuildConfiguration>` je hodnota nastavena pomocí hodnoty při spuštění procesu publikování.</span><span class="sxs-lookup"><span data-stu-id="dba1d-222">When publishing from Visual Studio, the value of `<LastUsedBuildConfiguration>` is set using the value when the publish process is started.</span></span> <span data-ttu-id="dba1d-223">`<LastUsedBuildConfiguration>`je zvláštní a v importovaném souboru MSBuild by neměl být přepsán.</span><span class="sxs-lookup"><span data-stu-id="dba1d-223">`<LastUsedBuildConfiguration>` is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="dba1d-224">Tato vlastnost však může být přepsána z příkazového řádku pomocí jednoho z následujících přístupů.</span><span class="sxs-lookup"><span data-stu-id="dba1d-224">This property can, however, be overridden from the command line using one of the following approaches.</span></span>
  * <span data-ttu-id="dba1d-225">Použití rozhraní CLI jádra .NET:</span><span class="sxs-lookup"><span data-stu-id="dba1d-225">Using the .NET Core CLI:</span></span>

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * <span data-ttu-id="dba1d-226">Pomocí msbuildu:</span><span class="sxs-lookup"><span data-stu-id="dba1d-226">Using MSBuild:</span></span>

    ```console
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  <span data-ttu-id="dba1d-227">Další informace naleznete v tématu [MSBuild: jak nastavit vlastnost konfigurace](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span><span class="sxs-lookup"><span data-stu-id="dba1d-227">For more information, see [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span></span>

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="dba1d-228">Publikování do koncového bodu MSDeploy z příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="dba1d-228">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="dba1d-229">Následující příklad používá webovou aplikaci ASP.NET Core vytvořenou visual studio s názvem *AzureWebApp*.</span><span class="sxs-lookup"><span data-stu-id="dba1d-229">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp*.</span></span> <span data-ttu-id="dba1d-230">Profil publikování Aplikací Azure se přidá ve Visual Studiu.</span><span class="sxs-lookup"><span data-stu-id="dba1d-230">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="dba1d-231">Další informace o vytvoření profilu naleznete v části [Publikovat profily.](#publish-profiles)</span><span class="sxs-lookup"><span data-stu-id="dba1d-231">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="dba1d-232">Chcete-li aplikaci nasadit pomocí `msbuild` profilu publikování, spusťte příkaz z **příkazového řádku pro vývojáře**sady Visual Studio .</span><span class="sxs-lookup"><span data-stu-id="dba1d-232">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt**.</span></span> <span data-ttu-id="dba1d-233">Příkazový řádek je k dispozici ve složce *Visual Studio* v nabídce **Start** na hlavním panelu systému Windows.</span><span class="sxs-lookup"><span data-stu-id="dba1d-233">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="dba1d-234">Pro snadnější přístup můžete přidat příkazový řádek do nabídky **Nástroje** v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="dba1d-234">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="dba1d-235">Další informace naleznete v [tématu Developer Command Prompt Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="dba1d-235">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="dba1d-236">MSBuild používá následující syntaxi příkazů:</span><span class="sxs-lookup"><span data-stu-id="dba1d-236">MSBuild uses the following command syntax:</span></span>

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="dba1d-237">{CESTA} &ndash; Cesta k souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="dba1d-237">{PATH} &ndash; Path to the app's project file.</span></span>
* <span data-ttu-id="dba1d-238">{PROFIL} &ndash; Název profilu publikování.</span><span class="sxs-lookup"><span data-stu-id="dba1d-238">{PROFILE} &ndash; Name of the publish profile.</span></span>
* <span data-ttu-id="dba1d-239">{UŽIVATELSKÉ JMÉNO} &ndash; Uživatelské jméno MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="dba1d-239">{USERNAME} &ndash; MSDeploy username.</span></span> <span data-ttu-id="dba1d-240">{USERNAME} lze nalézt v profilu publikování.</span><span class="sxs-lookup"><span data-stu-id="dba1d-240">The {USERNAME} can be found in the publish profile.</span></span>
* <span data-ttu-id="dba1d-241">{HESLO} &ndash; MSDeploy heslo.</span><span class="sxs-lookup"><span data-stu-id="dba1d-241">{PASSWORD} &ndash; MSDeploy password.</span></span> <span data-ttu-id="dba1d-242">Získejte {PASSWORD} z *{PROFILE}. Soubor PublishSettings.*</span><span class="sxs-lookup"><span data-stu-id="dba1d-242">Obtain the {PASSWORD} from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="dba1d-243">Stáhněte si *soubor . Soubor PublishSettings* z obou těchto:</span><span class="sxs-lookup"><span data-stu-id="dba1d-243">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="dba1d-244">**Průzkumník řešení**: Vyberte **zobrazit** > **Průzkumníka cloudu**.</span><span class="sxs-lookup"><span data-stu-id="dba1d-244">**Solution Explorer**: Select **View** > **Cloud Explorer**.</span></span> <span data-ttu-id="dba1d-245">Spojte se se svým předplatným Azure.</span><span class="sxs-lookup"><span data-stu-id="dba1d-245">Connect with your Azure subscription.</span></span> <span data-ttu-id="dba1d-246">Otevřete **služby App Services**.</span><span class="sxs-lookup"><span data-stu-id="dba1d-246">Open **App Services**.</span></span> <span data-ttu-id="dba1d-247">Klikněte pravým tlačítkem myši na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="dba1d-247">Right-click the app.</span></span> <span data-ttu-id="dba1d-248">Vyberte **možnost Stáhnout profil publikování**.</span><span class="sxs-lookup"><span data-stu-id="dba1d-248">Select **Download Publish Profile**.</span></span>
  * <span data-ttu-id="dba1d-249">Portál Azure: V panelu **Přehled** webové aplikace vyberte **Získat profil publikování.**</span><span class="sxs-lookup"><span data-stu-id="dba1d-249">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="dba1d-250">Následující příklad používá profil publikování s názvem *AzureWebApp - Web Deploy*:</span><span class="sxs-lookup"><span data-stu-id="dba1d-250">The following example uses a publish profile named *AzureWebApp - Web Deploy*:</span></span>

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="dba1d-251">Profil publikování lze také použít s příkazem [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) rozhraní .NET Core z příkazového prostředí systému Windows:</span><span class="sxs-lookup"><span data-stu-id="dba1d-251">A publish profile can also be used with the .NET Core CLI's [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command shell:</span></span>

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> <span data-ttu-id="dba1d-252">Příkaz `dotnet msbuild` je příkaz pro různé platformy a může kompilovat ASP.NET aplikace Core v systému macOS a Linux.</span><span class="sxs-lookup"><span data-stu-id="dba1d-252">The `dotnet msbuild` command is a cross-platform command and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="dba1d-253">MSBuild na macOS a Linux však není schopen nasazení aplikace do Azure nebo jiných koncových bodů MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="dba1d-253">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoints.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="dba1d-254">Nastavení prostředí</span><span class="sxs-lookup"><span data-stu-id="dba1d-254">Set the environment</span></span>

<span data-ttu-id="dba1d-255">Zahrňte `<EnvironmentName>` vlastnost do profilu publikování (*.pubxml*) nebo souboru projektu, abyste nastavili [prostředí](xref:fundamentals/environments)aplikace :</span><span class="sxs-lookup"><span data-stu-id="dba1d-255">Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="dba1d-256">Pokud požadujete transformace *web.config* (například nastavení proměnných prostředí na základě konfigurace, profilu nebo prostředí), přečtěte si viz <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="dba1d-256">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="dba1d-257">Vyloučit soubory</span><span class="sxs-lookup"><span data-stu-id="dba1d-257">Exclude files</span></span>

<span data-ttu-id="dba1d-258">Při publikování ASP.NET webových aplikací Core jsou zahrnuty následující datové zdroje:</span><span class="sxs-lookup"><span data-stu-id="dba1d-258">When publishing ASP.NET Core web apps, the following assets are included:</span></span>

* <span data-ttu-id="dba1d-259">Vytváření artefaktů</span><span class="sxs-lookup"><span data-stu-id="dba1d-259">Build artifacts</span></span>
* <span data-ttu-id="dba1d-260">Složky a soubory odpovídající následujícím vzorům globbingu:</span><span class="sxs-lookup"><span data-stu-id="dba1d-260">Folders and files matching the following globbing patterns:</span></span>
  * <span data-ttu-id="dba1d-261">`**\*.config`(například *web.config*)</span><span class="sxs-lookup"><span data-stu-id="dba1d-261">`**\*.config` (for example, *web.config*)</span></span>
  * <span data-ttu-id="dba1d-262">`**\*.json`(například *appsettings.json*)</span><span class="sxs-lookup"><span data-stu-id="dba1d-262">`**\*.json` (for example, *appsettings.json*)</span></span>
  * `wwwroot\**`

<span data-ttu-id="dba1d-263">MSBuild podporuje [globbing vzory](https://gruntjs.com/configuring-tasks#globbing-patterns).</span><span class="sxs-lookup"><span data-stu-id="dba1d-263">MSBuild supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="dba1d-264">Například následující `<Content>` prvek potlačí kopírování textových souborů (*TXT*) ve složce *wwwroot\content* a jejích podsložkách:</span><span class="sxs-lookup"><span data-stu-id="dba1d-264">For example, the following `<Content>` element suppresses the copying of text (*.txt*) files in the *wwwroot\content* folder and its subfolders:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="dba1d-265">Předchozí značky lze přidat do profilu publikování nebo do souboru *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="dba1d-265">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="dba1d-266">Po přidání do souboru *.csproj* je pravidlo přidáno do všech profilů publikování v projektu.</span><span class="sxs-lookup"><span data-stu-id="dba1d-266">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="dba1d-267">Následující `<MsDeploySkipRules>` prvek vylučuje všechny soubory ze složky *wwwroot\content:*</span><span class="sxs-lookup"><span data-stu-id="dba1d-267">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot\content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="dba1d-268">`<MsDeploySkipRules>`neodstraní *přeskakovací* cíle z lokality nasazení.</span><span class="sxs-lookup"><span data-stu-id="dba1d-268">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="dba1d-269">`<Content>`cílené soubory a složky jsou odstraněny z webu nasazení.</span><span class="sxs-lookup"><span data-stu-id="dba1d-269">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="dba1d-270">Předpokládejme například, že nasazená webová aplikace měla následující soubory:</span><span class="sxs-lookup"><span data-stu-id="dba1d-270">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="dba1d-271">*Zobrazení/Úvod/About1.cshtml*</span><span class="sxs-lookup"><span data-stu-id="dba1d-271">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="dba1d-272">*Zobrazení/Domů/About2.cshtml*</span><span class="sxs-lookup"><span data-stu-id="dba1d-272">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="dba1d-273">*Zobrazení/Úvod/About3.cshtml*</span><span class="sxs-lookup"><span data-stu-id="dba1d-273">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="dba1d-274">Pokud jsou `<MsDeploySkipRules>` přidány následující prvky, tyto soubory by nebyly odstraněny v lokalitě nasazení.</span><span class="sxs-lookup"><span data-stu-id="dba1d-274">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

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

<span data-ttu-id="dba1d-275">Předchozí `<MsDeploySkipRules>` prvky zabránit *přeskočené* soubory z nasazení.</span><span class="sxs-lookup"><span data-stu-id="dba1d-275">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="dba1d-276">Po nasazení tyto soubory neodstraní.</span><span class="sxs-lookup"><span data-stu-id="dba1d-276">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="dba1d-277">Následující `<Content>` prvek odstraní cílové soubory v lokalitě nasazení:</span><span class="sxs-lookup"><span data-stu-id="dba1d-277">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="dba1d-278">Použití nasazení příkazového řádku `<Content>` s předchozím prvkem dává variaci následujícího výstupu:</span><span class="sxs-lookup"><span data-stu-id="dba1d-278">Using command-line deployment with the preceding `<Content>` element yields a variation of the following output:</span></span>

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

## <a name="include-files"></a><span data-ttu-id="dba1d-279">Soubory k zahrnutí</span><span class="sxs-lookup"><span data-stu-id="dba1d-279">Include files</span></span>

<span data-ttu-id="dba1d-280">V následujících částech jsou popsány různé přístupy pro zahrnutí souborů v době publikování.</span><span class="sxs-lookup"><span data-stu-id="dba1d-280">The following sections outline different approaches for file inclusion at publish time.</span></span> <span data-ttu-id="dba1d-281">Část [Obecné zahrnutí souboru](#general-file-inclusion) používá položku, `DotNetPublishFiles` která je poskytována souborem cílů publikování v sada Web SDK.</span><span class="sxs-lookup"><span data-stu-id="dba1d-281">The [General file inclusion](#general-file-inclusion) section uses the `DotNetPublishFiles` item, which is provided by a publish targets file in the Web SDK.</span></span> <span data-ttu-id="dba1d-282">Sekce [Selektivní zahrnutí souboru](#selective-file-inclusion) používá položku, `ResolvedFileToPublish` která je poskytována souborem cílů publikování v souboru .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="dba1d-282">The [Selective file inclusion](#selective-file-inclusion) section uses the `ResolvedFileToPublish` item, which is provided by a publish targets file in the .NET Core SDK.</span></span> <span data-ttu-id="dba1d-283">Vzhledem k tomu, že sada Web SDK závisí na sdk jádra .NET, lze buď položku použít v projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dba1d-283">Because the Web SDK depends on the .NET Core SDK, either item can be used in an ASP.NET Core project.</span></span>

### <a name="general-file-inclusion"></a><span data-ttu-id="dba1d-284">Obecné zahrnutí souboru</span><span class="sxs-lookup"><span data-stu-id="dba1d-284">General file inclusion</span></span>

<span data-ttu-id="dba1d-285">`<ItemGroup>` Následující příklad je prvek ukazuje kopírování složky umístěné mimo adresář projektu do složky publikovaného webu.</span><span class="sxs-lookup"><span data-stu-id="dba1d-285">The following example's `<ItemGroup>` element demonstrates copying a folder located outside of the project directory to a folder of the published site.</span></span> <span data-ttu-id="dba1d-286">Všechny soubory přidané do následujících `<ItemGroup>` značek jsou zahrnuty ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="dba1d-286">Any files added to the following markup's `<ItemGroup>` are included by default.</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="dba1d-287">Předchozí značka:</span><span class="sxs-lookup"><span data-stu-id="dba1d-287">The preceding markup:</span></span>

* <span data-ttu-id="dba1d-288">Lze přidat do souboru *.csproj* nebo do profilu publikování.</span><span class="sxs-lookup"><span data-stu-id="dba1d-288">Can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="dba1d-289">Pokud je přidán do souboru *.csproj,* je součástí každého profilu publikování v projektu.</span><span class="sxs-lookup"><span data-stu-id="dba1d-289">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>
* <span data-ttu-id="dba1d-290">Deklaruje položku `_CustomFiles` `Include` pro uložení souborů odpovídajících vzoru globbing u atributu.</span><span class="sxs-lookup"><span data-stu-id="dba1d-290">Declares a `_CustomFiles` item to store files matching the `Include` attribute's globbing pattern.</span></span> <span data-ttu-id="dba1d-291">Složka *obrázků,* na kterou se odkazuje ve vzoru, je umístěna mimo adresář projektu.</span><span class="sxs-lookup"><span data-stu-id="dba1d-291">The *images* folder referenced in the pattern is located outside of the project directory.</span></span> <span data-ttu-id="dba1d-292">[Vyhrazená vlastnost](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties) `$(MSBuildProjectDirectory)`s názvem překládá na absolutní cestu souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="dba1d-292">A [reserved property](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), named `$(MSBuildProjectDirectory)`, resolves to the project file's absolute path.</span></span>
* <span data-ttu-id="dba1d-293">Obsahuje seznam souborů k `DotNetPublishFiles` položce.</span><span class="sxs-lookup"><span data-stu-id="dba1d-293">Provides a list of files to the `DotNetPublishFiles` item.</span></span> <span data-ttu-id="dba1d-294">Ve výchozím nastavení je `<DestinationRelativePath>` prvek položky prázdný.</span><span class="sxs-lookup"><span data-stu-id="dba1d-294">By default, the item's `<DestinationRelativePath>` element is empty.</span></span> <span data-ttu-id="dba1d-295">Výchozí hodnota je přepsána ve značkách a používá [známá metadata položek,](/visualstudio/msbuild/msbuild-well-known-item-metadata) například `%(RecursiveDir)`.</span><span class="sxs-lookup"><span data-stu-id="dba1d-295">The default value is overridden in the markup and uses [well-known item metadata](/visualstudio/msbuild/msbuild-well-known-item-metadata) such as `%(RecursiveDir)`.</span></span> <span data-ttu-id="dba1d-296">Vnitřní text představuje složku *wwwroot/images* publikovaného webu.</span><span class="sxs-lookup"><span data-stu-id="dba1d-296">The inner text represents the *wwwroot/images* folder of the published site.</span></span>

### <a name="selective-file-inclusion"></a><span data-ttu-id="dba1d-297">Selektivní zahrnutí souboru</span><span class="sxs-lookup"><span data-stu-id="dba1d-297">Selective file inclusion</span></span>

<span data-ttu-id="dba1d-298">Zvýrazněná značka v následujícím příkladu ukazuje:</span><span class="sxs-lookup"><span data-stu-id="dba1d-298">The highlighted markup in the following example demonstrates:</span></span>

* <span data-ttu-id="dba1d-299">Kopírování souboru umístěného mimo projekt do složky *wwwroot* publikovaného webu.</span><span class="sxs-lookup"><span data-stu-id="dba1d-299">Copying a file located outside of the project into the published site's *wwwroot* folder.</span></span> <span data-ttu-id="dba1d-300">Je zachován název souboru *ReadMe2.md.*</span><span class="sxs-lookup"><span data-stu-id="dba1d-300">The file name of *ReadMe2.md* is maintained.</span></span>
* <span data-ttu-id="dba1d-301">S výjimkou složky *wwwroot\Content.*</span><span class="sxs-lookup"><span data-stu-id="dba1d-301">Excluding the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="dba1d-302">S výjimkou *zobrazení\Domů\About2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="dba1d-302">Excluding *Views\Home\About2.cshtml*.</span></span>

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

<span data-ttu-id="dba1d-303">Předchozí příklad používá `ResolvedFileToPublish` položku, jejíž výchozí chování je vždy `Include` kopírovat soubory uvedené v atributu na publikovaný web.</span><span class="sxs-lookup"><span data-stu-id="dba1d-303">The preceding example uses the `ResolvedFileToPublish` item, whose default behavior is to always copy the files provided in the `Include` attribute to the published site.</span></span> <span data-ttu-id="dba1d-304">Výchozí chování přepište zahrnutím podřízeného `<CopyToPublishDirectory>` `Never` prvku `PreserveNewest`s vnitřním textem buď nebo .</span><span class="sxs-lookup"><span data-stu-id="dba1d-304">Override the default behavior by including a `<CopyToPublishDirectory>` child element with inner text of either `Never` or `PreserveNewest`.</span></span> <span data-ttu-id="dba1d-305">Příklad:</span><span class="sxs-lookup"><span data-stu-id="dba1d-305">For example:</span></span>

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

<span data-ttu-id="dba1d-306">Další ukázky nasazení naleznete v [úložišti úložiště web SDK Readme](https://github.com/aspnet/websdk).</span><span class="sxs-lookup"><span data-stu-id="dba1d-306">For more deployment samples, see the [Web SDK repository Readme](https://github.com/aspnet/websdk).</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="dba1d-307">Spuštění cíle před publikováním nebo po publikování</span><span class="sxs-lookup"><span data-stu-id="dba1d-307">Run a target before or after publishing</span></span>

<span data-ttu-id="dba1d-308">Předdefinované `BeforePublish` a `AfterPublish` cíle provést cíl před nebo po cíl publikování.</span><span class="sxs-lookup"><span data-stu-id="dba1d-308">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="dba1d-309">Přidejte do profilu publikování následující prvky pro zprávy v konzole protokolu před publikováním i po něm:</span><span class="sxs-lookup"><span data-stu-id="dba1d-309">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="dba1d-310">Publikování na serveru pomocí nedůvěryhodného certifikátu</span><span class="sxs-lookup"><span data-stu-id="dba1d-310">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="dba1d-311">Přidejte `<AllowUntrustedCertificate>` vlastnost s `True` hodnotou do profilu publikování:</span><span class="sxs-lookup"><span data-stu-id="dba1d-311">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="dba1d-312">Služba Kudu</span><span class="sxs-lookup"><span data-stu-id="dba1d-312">The Kudu service</span></span>

<span data-ttu-id="dba1d-313">Chcete-li zobrazit soubory v nasazení webové aplikace Služby Azure App Service, použijte [službu Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span><span class="sxs-lookup"><span data-stu-id="dba1d-313">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="dba1d-314">Připojte `scm` token k názvu webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="dba1d-314">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="dba1d-315">Příklad:</span><span class="sxs-lookup"><span data-stu-id="dba1d-315">For example:</span></span>

| <span data-ttu-id="dba1d-316">zprostředkovatele identity</span><span class="sxs-lookup"><span data-stu-id="dba1d-316">URL</span></span>                                    | <span data-ttu-id="dba1d-317">Výsledek</span><span class="sxs-lookup"><span data-stu-id="dba1d-317">Result</span></span>       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | <span data-ttu-id="dba1d-318">Webová aplikace</span><span class="sxs-lookup"><span data-stu-id="dba1d-318">Web App</span></span>      |
| `http://mysite.scm.azurewebsites.net/` | <span data-ttu-id="dba1d-319">Služba Kudu</span><span class="sxs-lookup"><span data-stu-id="dba1d-319">Kudu service</span></span> |

<span data-ttu-id="dba1d-320">Vyberte položku nabídky [Editorská/editor,](https://github.com/projectkudu/kudu/wiki/Kudu-console) kterou chcete zobrazit, upravit, odstranit nebo přidat soubory.</span><span class="sxs-lookup"><span data-stu-id="dba1d-320">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dba1d-321">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="dba1d-321">Additional resources</span></span>

* <span data-ttu-id="dba1d-322">[Nasazení webu](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) zjednodušuje nasazení webových aplikací a webů na servery služby IIS.</span><span class="sxs-lookup"><span data-stu-id="dba1d-322">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="dba1d-323">[Úložiště Web SDK GitHub](https://github.com/aspnet/websdk/issues): Problémy se soubory a funkce požadavků pro nasazení.</span><span class="sxs-lookup"><span data-stu-id="dba1d-323">[Web SDK GitHub repository](https://github.com/aspnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="dba1d-324">Publikování ASP.NET webové aplikace do virtuálního počítače Azure z Visual Studia</span><span class="sxs-lookup"><span data-stu-id="dba1d-324">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
