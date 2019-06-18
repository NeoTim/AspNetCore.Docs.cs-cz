---
title: Visual Studio publikačních profilů pro nasazení aplikace ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vytvořit profily publikování v sadě Visual Studio a jejich použití pro správu nasazení aplikací ASP.NET Core do různých cílů.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/18/2019
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: 7006b73ea9a424a3c9530cf526727bc71a88838f
ms.sourcegitcommit: 516f166c5f7cec54edf3d9c71e6e2ba53fb3b0e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67196317"
---
# <a name="visual-studio-publish-profiles-for-aspnet-core-app-deployment"></a><span data-ttu-id="5c16f-103">Visual Studio publikačních profilů pro nasazení aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5c16f-103">Visual Studio publish profiles for ASP.NET Core app deployment</span></span>

<span data-ttu-id="5c16f-104">Podle [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5c16f-104">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5c16f-105">Tento dokument se zaměřuje na pomocí sady Visual Studio 2017 nebo později k vytvoření a použití publikačních profilů.</span><span class="sxs-lookup"><span data-stu-id="5c16f-105">This document focuses on using Visual Studio 2017 or later to create and use publish profiles.</span></span> <span data-ttu-id="5c16f-106">Profily publikování vytvořené pomocí sady Visual Studio můžete spustit z nástroje MSBuild a sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5c16f-106">The publish profiles created with Visual Studio can be run from MSBuild and Visual Studio.</span></span> <span data-ttu-id="5c16f-107">Zobrazit [publikovat webovou aplikaci ASP.NET Core do služby Azure App Service pomocí sady Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs) pokyny k publikování do Azure.</span><span class="sxs-lookup"><span data-stu-id="5c16f-107">See [Publish an ASP.NET Core web app to Azure App Service using Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs) for instructions on publishing to Azure.</span></span>

<span data-ttu-id="5c16f-108">`dotnet new mvc` Příkaz vytvoří soubor projektu, která obsahuje následující nejvyšší úrovně `<Project>` element:</span><span class="sxs-lookup"><span data-stu-id="5c16f-108">The `dotnet new mvc` command produces a project file containing the following top-level `<Project>` element:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

<span data-ttu-id="5c16f-109">Předchozí `<Project>` elementu `Sdk` atribut importuje MSBuild [vlastnosti](/visualstudio/msbuild/msbuild-properties) a [cíle](/visualstudio/msbuild/msbuild-targets) z *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\ SDK.props* a *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="5c16f-109">The preceding `<Project>` element's `Sdk` attribute imports the MSBuild [properties](/visualstudio/msbuild/msbuild-properties) and [targets](/visualstudio/msbuild/msbuild-targets) from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* and *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectively.</span></span> <span data-ttu-id="5c16f-110">Výchozím umístěním pro `$(MSBuildSDKsPath)` (s Visual Studio Enterprise. 2019) je *% programfiles (x86) %\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* složky.</span><span class="sxs-lookup"><span data-stu-id="5c16f-110">The default location for `$(MSBuildSDKsPath)` (with Visual Studio 2019 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="5c16f-111">`Microsoft.NET.Sdk.Web` (Sada web SDK) závisí na dalších sad SDK, včetně `Microsoft.NET.Sdk` (.NET Core SDK) a `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span><span class="sxs-lookup"><span data-stu-id="5c16f-111">`Microsoft.NET.Sdk.Web` (Web SDK) depends on other SDKs, including `Microsoft.NET.Sdk` (.NET Core SDK) and `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span></span> <span data-ttu-id="5c16f-112">Vlastnosti nástroje MSBuild a cíle, které jsou spojené s každou závislé sady SDK jsou importovány.</span><span class="sxs-lookup"><span data-stu-id="5c16f-112">The MSBuild properties and targets associated with each dependent SDK are imported.</span></span> <span data-ttu-id="5c16f-113">Publikujte cíle importu příslušné sady cíle založené na metodě publikování použít.</span><span class="sxs-lookup"><span data-stu-id="5c16f-113">Publish targets import the appropriate set of targets based on the publish method used.</span></span>

<span data-ttu-id="5c16f-114">Při načtení projektu nástroje MSBuild nebo Visual Studio provedou tyto akce vysoké úrovně:</span><span class="sxs-lookup"><span data-stu-id="5c16f-114">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="5c16f-115">Sestavit projekt</span><span class="sxs-lookup"><span data-stu-id="5c16f-115">Build project</span></span>
* <span data-ttu-id="5c16f-116">COMPUTE souborů k publikování</span><span class="sxs-lookup"><span data-stu-id="5c16f-116">Compute files to publish</span></span>
* <span data-ttu-id="5c16f-117">Publikování souborů do cíle</span><span class="sxs-lookup"><span data-stu-id="5c16f-117">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="5c16f-118">COMPUTE položky projektu</span><span class="sxs-lookup"><span data-stu-id="5c16f-118">Compute project items</span></span>

<span data-ttu-id="5c16f-119">Při načtení projektu [položky projektu nástroje MSBuild](/visualstudio/msbuild/common-msbuild-project-items) jsou vypočítány (soubory).</span><span class="sxs-lookup"><span data-stu-id="5c16f-119">When the project is loaded, the [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) (files) are computed.</span></span> <span data-ttu-id="5c16f-120">Typ položky určuje způsob zpracování souboru.</span><span class="sxs-lookup"><span data-stu-id="5c16f-120">The item type determines how the file is processed.</span></span> <span data-ttu-id="5c16f-121">Ve výchozím nastavení *.cs* soubory jsou zahrnuty v `Compile` seznam položek.</span><span class="sxs-lookup"><span data-stu-id="5c16f-121">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="5c16f-122">Soubory `Compile` jsou zkompilovány seznam položek.</span><span class="sxs-lookup"><span data-stu-id="5c16f-122">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="5c16f-123">`Content` Seznamu položek obsahuje soubory, které jsou publikovány kromě výstupy sestavení.</span><span class="sxs-lookup"><span data-stu-id="5c16f-123">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="5c16f-124">Ve výchozím nastavení, soubory odpovídající vzorům `wwwroot\**`, `**\*.config`, a `**\*.json` jsou součástí `Content` seznam položek.</span><span class="sxs-lookup"><span data-stu-id="5c16f-124">By default, files matching the patterns `wwwroot\**`, `**\*.config`, and `**\*.json` are included in the `Content` item list.</span></span> <span data-ttu-id="5c16f-125">Například `wwwroot\**` [model podpory zástupných znaků](https://gruntjs.com/configuring-tasks#globbing-patterns) vyhledá všechny soubory v *wwwroot* složky **a** jejích podsložkách.</span><span class="sxs-lookup"><span data-stu-id="5c16f-125">For example, the `wwwroot\**` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder **and** its subfolders.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5c16f-126">Importuje Web SDK [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="5c16f-126">The Web SDK imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="5c16f-127">V důsledku toho soubory odpovídající vzorům `**\*.cshtml` a `**\*.razor` jsou taky součástí `Content` seznam položek.</span><span class="sxs-lookup"><span data-stu-id="5c16f-127">As a result, files matching the patterns `**\*.cshtml` and `**\*.razor` are also included in the `Content` item list.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="5c16f-128">Importuje Web SDK [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="5c16f-128">The Web SDK imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="5c16f-129">V důsledku toho soubory odpovídající `**\*.cshtml` vzoru jsou taky součástí `Content` seznam položek.</span><span class="sxs-lookup"><span data-stu-id="5c16f-129">As a result, files matching the `**\*.cshtml` pattern are also included in the `Content` item list.</span></span>

::: moniker-end

<span data-ttu-id="5c16f-130">Pokud chcete explicitně přidat soubor do seznamu publikovat, přidejte přímo v souboru *.csproj* sdílené, jak je znázorněno [vložených souborů](#include-files) části.</span><span class="sxs-lookup"><span data-stu-id="5c16f-130">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in the [Include Files](#include-files) section.</span></span>

<span data-ttu-id="5c16f-131">Při výběru **publikovat** tlačítko v sadě Visual Studio nebo při publikování z příkazového řádku:</span><span class="sxs-lookup"><span data-stu-id="5c16f-131">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="5c16f-132">Jsou vypočítané vlastnosti/položky (soubory, které jsou potřebné k sestavení).</span><span class="sxs-lookup"><span data-stu-id="5c16f-132">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="5c16f-133">**Visual Studio pouze**: Obnovení balíčků NuGet.</span><span class="sxs-lookup"><span data-stu-id="5c16f-133">**Visual Studio only**: NuGet packages are restored.</span></span> <span data-ttu-id="5c16f-134">(Obnovit musí být explicitní uživatelem na rozhraní příkazového řádku).</span><span class="sxs-lookup"><span data-stu-id="5c16f-134">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="5c16f-135">Sestavení projektu.</span><span class="sxs-lookup"><span data-stu-id="5c16f-135">The project builds.</span></span>
* <span data-ttu-id="5c16f-136">Publikovat položky se zpracovávají (soubory, které jsou potřebné k publikování).</span><span class="sxs-lookup"><span data-stu-id="5c16f-136">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="5c16f-137">Publikování projektu (vypočítané soubory se zkopírují do cílového umístění publikování).</span><span class="sxs-lookup"><span data-stu-id="5c16f-137">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="5c16f-138">Když se odkazuje projekt ASP.NET Core `Microsoft.NET.Sdk.Web` v souboru projektu *app_offline.htm* soubor umístěn v kořenovém adresáři webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="5c16f-138">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="5c16f-139">Pokud soubor existuje, modul ASP.NET Core řádně ukončí aplikaci, slouží *app_offline.htm* souboru během nasazení.</span><span class="sxs-lookup"><span data-stu-id="5c16f-139">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="5c16f-140">Další informace najdete v tématu [odkaz Konfigurace modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="5c16f-140">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="5c16f-141">Publikování základních příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="5c16f-141">Basic command-line publishing</span></span>

<span data-ttu-id="5c16f-142">Příkazový řádek publikování funguje na všech platformách .NET Core podporovány a nevyžaduje, aby Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5c16f-142">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="5c16f-143">V následujících ukázkách [dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkaz spustí z adresáře projektu (obsahující *.csproj* souboru).</span><span class="sxs-lookup"><span data-stu-id="5c16f-143">In the following samples, the [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="5c16f-144">Pokud není ve složce projektu explicitně předávat v cestě k souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="5c16f-144">If not in the project folder, explicitly pass in the project file path.</span></span> <span data-ttu-id="5c16f-145">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5c16f-145">For example:</span></span>

```console
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="5c16f-146">Spusťte následující příkazy k vytvoření a publikování webové aplikace:</span><span class="sxs-lookup"><span data-stu-id="5c16f-146">Run the following commands to create and publish a web app:</span></span>

```console
dotnet new mvc
dotnet publish
```

<span data-ttu-id="5c16f-147">[Dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkaz vytváří výstup podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="5c16f-147">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command produces output similar to the following:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {version} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp{X.Y}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp{X.Y}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp{X.Y}\publish\
```

<span data-ttu-id="5c16f-148">Výchozí nastavení složky pro publikování je `bin\$(Configuration)\netcoreapp<version>\publish`.</span><span class="sxs-lookup"><span data-stu-id="5c16f-148">The default publish folder is `bin\$(Configuration)\netcoreapp<version>\publish`.</span></span> <span data-ttu-id="5c16f-149">Výchozí nastavení pro `$(Configuration)` je *ladění*.</span><span class="sxs-lookup"><span data-stu-id="5c16f-149">The default for `$(Configuration)` is *Debug*.</span></span> <span data-ttu-id="5c16f-150">V předchozím příkladu `<TargetFramework>` je `netcoreapp{X.Y}`.</span><span class="sxs-lookup"><span data-stu-id="5c16f-150">In the preceding sample, the `<TargetFramework>` is `netcoreapp{X.Y}`.</span></span>

<span data-ttu-id="5c16f-151">`dotnet publish -h` Zobrazí nápovědu pro publikování.</span><span class="sxs-lookup"><span data-stu-id="5c16f-151">`dotnet publish -h` displays help information for publish.</span></span>

<span data-ttu-id="5c16f-152">Následující příkaz určuje `Release` sestavení a publikování adresáře:</span><span class="sxs-lookup"><span data-stu-id="5c16f-152">The following command specifies a `Release` build and the publishing directory:</span></span>

```console
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="5c16f-153">[Dotnet publikovat](/dotnet/core/tools/dotnet-publish) volání MSBuild, který vyvolá příkaz `Publish` cíl.</span><span class="sxs-lookup"><span data-stu-id="5c16f-153">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="5c16f-154">Žádné parametry předány `dotnet publish` jsou předávaným do MSBuild.</span><span class="sxs-lookup"><span data-stu-id="5c16f-154">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="5c16f-155">`-c` Parametr se mapuje `Configuration` vlastnosti Msbuildu.</span><span class="sxs-lookup"><span data-stu-id="5c16f-155">The `-c` parameter maps to the `Configuration` MSBuild property.</span></span> <span data-ttu-id="5c16f-156">`-o` Parametr mapuje na `OutputPath`.</span><span class="sxs-lookup"><span data-stu-id="5c16f-156">The `-o` parameter maps to `OutputPath`.</span></span>

<span data-ttu-id="5c16f-157">Vlastnosti nástroje MSBuild mohou být předány některou z následujících formátů:</span><span class="sxs-lookup"><span data-stu-id="5c16f-157">MSBuild properties can be passed using either of the following formats:</span></span>

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="5c16f-158">Následující příkaz publikuje `Release` od sestavení k síťové sdílené složky:</span><span class="sxs-lookup"><span data-stu-id="5c16f-158">The following command publishes a `Release` build to a network share:</span></span>

`dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb`

<span data-ttu-id="5c16f-159">Sdílené síťové složky se zadaným lomítka ( *//r8/* ) a funguje na všech platformách .NET Core podporovány.</span><span class="sxs-lookup"><span data-stu-id="5c16f-159">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

<span data-ttu-id="5c16f-160">Potvrďte, že publikované aplikace pro nasazení neběží.</span><span class="sxs-lookup"><span data-stu-id="5c16f-160">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="5c16f-161">Soubory *publikovat* složky jsou zamčené, když je aplikace spuštěna.</span><span class="sxs-lookup"><span data-stu-id="5c16f-161">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="5c16f-162">Nasazení nebyla vytvořena, protože uzamčena, nelze zkopírovat soubory.</span><span class="sxs-lookup"><span data-stu-id="5c16f-162">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="5c16f-163">Profily publikování</span><span class="sxs-lookup"><span data-stu-id="5c16f-163">Publish profiles</span></span>

<span data-ttu-id="5c16f-164">Tato část používá k vytvoření profilu publikování sady Visual Studio 2017 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="5c16f-164">This section uses Visual Studio 2017 or later to create a publishing profile.</span></span> <span data-ttu-id="5c16f-165">Po vytvoření profilu publikování ze sady Visual Studio nebo příkazového řádku je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="5c16f-165">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span>

<span data-ttu-id="5c16f-166">Publikování profilů může zjednodušit proces publikování, a může existovat libovolný počet profilů.</span><span class="sxs-lookup"><span data-stu-id="5c16f-166">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span> <span data-ttu-id="5c16f-167">Vytvořte profil publikování v sadě Visual Studio výběrem jedné z následujících cest:</span><span class="sxs-lookup"><span data-stu-id="5c16f-167">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="5c16f-168">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="5c16f-168">Right-click the project in **Solution Explorer** and select **Publish**.</span></span>
* <span data-ttu-id="5c16f-169">Vyberte **publikovat {název projektu}** z **sestavení** nabídky.</span><span class="sxs-lookup"><span data-stu-id="5c16f-169">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="5c16f-170">**Publikovat** zobrazuje kartu na stránce kapacity aplikace.</span><span class="sxs-lookup"><span data-stu-id="5c16f-170">The **Publish** tab of the app capacities page is displayed.</span></span> <span data-ttu-id="5c16f-171">Pokud projekt nemá profil publikování, zobrazí se následující stránka:</span><span class="sxs-lookup"><span data-stu-id="5c16f-171">If the project lacks a publish profile, the following page is displayed:</span></span>

![Na kartě Publikovat na stránce kapacity aplikace](visual-studio-publish-profiles/_static/az.png)

<span data-ttu-id="5c16f-173">Když **složky** je vybrána, zadejte cestu ke složce pro ukládání publikovaných prostředků.</span><span class="sxs-lookup"><span data-stu-id="5c16f-173">When **Folder** is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="5c16f-174">Výchozí složkou je *bin\Release\PublishOutput*.</span><span class="sxs-lookup"><span data-stu-id="5c16f-174">The default folder is *bin\Release\PublishOutput*.</span></span> <span data-ttu-id="5c16f-175">Klikněte na tlačítko **vytvořit profil** tlačítko Dokončit.</span><span class="sxs-lookup"><span data-stu-id="5c16f-175">Click the **Create Profile** button to finish.</span></span>

<span data-ttu-id="5c16f-176">Po vytvoření profilu publikování **publikovat** kartě změny.</span><span class="sxs-lookup"><span data-stu-id="5c16f-176">Once a publish profile is created, the **Publish** tab changes.</span></span> <span data-ttu-id="5c16f-177">V rozevíracím seznamu se zobrazí nově vytvořený profil.</span><span class="sxs-lookup"><span data-stu-id="5c16f-177">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="5c16f-178">Klikněte na tlačítko **vytvořit nový profil** vytvořte další nový profil.</span><span class="sxs-lookup"><span data-stu-id="5c16f-178">Click **Create new profile** to create another new profile.</span></span>

![Na kartě Publikovat na stránce kapacity aplikace zobrazuje FolderProfile](visual-studio-publish-profiles/_static/create_new.png)

<span data-ttu-id="5c16f-180">Průvodce publikováním podporuje následující cíle publikování:</span><span class="sxs-lookup"><span data-stu-id="5c16f-180">The Publish wizard supports the following publish targets:</span></span>

* <span data-ttu-id="5c16f-181">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="5c16f-181">Azure App Service</span></span>
* <span data-ttu-id="5c16f-182">Azure Virtual Machines</span><span class="sxs-lookup"><span data-stu-id="5c16f-182">Azure Virtual Machines</span></span>
* <span data-ttu-id="5c16f-183">Služba IIS, FTP atd., (pro všechny webové servery)</span><span class="sxs-lookup"><span data-stu-id="5c16f-183">IIS, FTP, etc. (for any web server)</span></span>
* <span data-ttu-id="5c16f-184">Folder</span><span class="sxs-lookup"><span data-stu-id="5c16f-184">Folder</span></span>
* <span data-ttu-id="5c16f-185">Importovat profil</span><span class="sxs-lookup"><span data-stu-id="5c16f-185">Import Profile</span></span>

<span data-ttu-id="5c16f-186">Další informace najdete v tématu [jaké možnosti publikování jsou pro mě nejlepší](/visualstudio/ide/not-in-toc/web-publish-options).</span><span class="sxs-lookup"><span data-stu-id="5c16f-186">For more information, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="5c16f-187">Při vytváření profilu publikování pomocí sady Visual Studio *vlastnosti/PublishProfiles / {název profilu} .pubxml* se vytvoří soubor MSBuild.</span><span class="sxs-lookup"><span data-stu-id="5c16f-187">When creating a publish profile with Visual Studio, a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file is created.</span></span> <span data-ttu-id="5c16f-188">*.Pubxml* soubor je soubor MSBuild a obsahuje konfigurační nastavení publikování.</span><span class="sxs-lookup"><span data-stu-id="5c16f-188">The *.pubxml* file is a MSBuild file and contains publish configuration settings.</span></span> <span data-ttu-id="5c16f-189">Tento soubor můžete změnit na vlastní nastavení sestavení a publikování procesu.</span><span class="sxs-lookup"><span data-stu-id="5c16f-189">This file can be changed to customize the build and publish process.</span></span> <span data-ttu-id="5c16f-190">Tento soubor je pro čtení procesem publikování.</span><span class="sxs-lookup"><span data-stu-id="5c16f-190">This file is read by the publishing process.</span></span> <span data-ttu-id="5c16f-191">`<LastUsedBuildConfiguration>` je speciální, protože globální vlastností a nemohou být zapsány všech souborů, které se importují v sestavení.</span><span class="sxs-lookup"><span data-stu-id="5c16f-191">`<LastUsedBuildConfiguration>` is special because it's a global property and shouldn't be in any file that's imported in the build.</span></span> <span data-ttu-id="5c16f-192">Zobrazit [MSBuild: jak nastavit vlastnost konfigurace](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx) Další informace.</span><span class="sxs-lookup"><span data-stu-id="5c16f-192">See [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx) for more information.</span></span>

<span data-ttu-id="5c16f-193">Při publikování do Azure cíl, *.pubxml* soubor obsahuje identifikátor vašeho předplatného Azure.</span><span class="sxs-lookup"><span data-stu-id="5c16f-193">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="5c16f-194">S cílovým typem přidává se tento soubor do správy zdrojového kódu se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="5c16f-194">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="5c16f-195">Při publikování na cíl mimo Azure, je k vrácení se změnami *.pubxml* souboru.</span><span class="sxs-lookup"><span data-stu-id="5c16f-195">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="5c16f-196">Se šifrují citlivé údaje (třeba publikovat heslo) na úrovni uživatele nebo počítače.</span><span class="sxs-lookup"><span data-stu-id="5c16f-196">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="5c16f-197">Je uložený v *vlastnosti/PublishProfiles / {název profilu}.pubxml.user* souboru.</span><span class="sxs-lookup"><span data-stu-id="5c16f-197">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="5c16f-198">Protože tento soubor můžete uložit citlivé informace, by neměla být zaškrtnuta do správy zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="5c16f-198">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="5c16f-199">Přehled o tom, jak publikovat webovou aplikaci v ASP.NET Core, najdete v části [hostitele a nasadit](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="5c16f-199">For an overview of how to publish a web app on ASP.NET Core, see [Host and deploy](xref:host-and-deploy/index).</span></span> <span data-ttu-id="5c16f-200">Úlohy MSBuild a cíle plánování pro publikování aplikace ASP.NET Core je open source na [aspnet/websdk úložiště](https://github.com/aspnet/websdk).</span><span class="sxs-lookup"><span data-stu-id="5c16f-200">The MSBuild tasks and targets necessary to publish an ASP.NET Core app are open-source at the [aspnet/websdk repository](https://github.com/aspnet/websdk).</span></span>

<span data-ttu-id="5c16f-201">`dotnet publish` můžete použít složku, MSDeploy, a [Kudu](https://github.com/projectkudu/kudu/wiki) publikační profily:</span><span class="sxs-lookup"><span data-stu-id="5c16f-201">`dotnet publish` can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles:</span></span>

<span data-ttu-id="5c16f-202">Složka (funguje napříč platformami):</span><span class="sxs-lookup"><span data-stu-id="5c16f-202">Folder (works cross-platform):</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="5c16f-203">MSDeploy (aktuálně toto funguje pouze ve Windows, protože není MSDeploy napříč platformami):</span><span class="sxs-lookup"><span data-stu-id="5c16f-203">MSDeploy (currently this only works in Windows since MSDeploy isn't cross-platform):</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="5c16f-204">Balíček MSDeploy (aktuálně toto funguje pouze ve Windows, protože není MSDeploy napříč platformami):</span><span class="sxs-lookup"><span data-stu-id="5c16f-204">MSDeploy package (currently this only works in Windows since MSDeploy isn't cross-platform):</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="5c16f-205">V předchozích ukázkách, nepředávejte `deployonbuild` k `dotnet publish`.</span><span class="sxs-lookup"><span data-stu-id="5c16f-205">In the preceding examples, don't pass `deployonbuild` to `dotnet publish`.</span></span>

<span data-ttu-id="5c16f-206">Další informace najdete v tématu [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span><span class="sxs-lookup"><span data-stu-id="5c16f-206">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="5c16f-207">`dotnet publish` podporuje rozhraní API Kudu k publikování do Azure z jakékoliv platformy.</span><span class="sxs-lookup"><span data-stu-id="5c16f-207">`dotnet publish` supports Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="5c16f-208">Visual Studio publikování podporuje Kudu rozhraní API, ale je podporovaný modulem WebSDK pro různé platformy publikovat do Azure.</span><span class="sxs-lookup"><span data-stu-id="5c16f-208">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>

<span data-ttu-id="5c16f-209">Přidat profil publikování *vlastnosti/PublishProfiles* složka s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="5c16f-209">Add a publish profile to the *Properties/PublishProfiles* folder with the following content:</span></span>

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

<span data-ttu-id="5c16f-210">Spusťte následující příkaz, který zip obsah publikovat a publikujete ji do Azure pomocí rozhraní API Kudu:</span><span class="sxs-lookup"><span data-stu-id="5c16f-210">Run the following command to zip up the publish contents and publish it to Azure using the Kudu APIs:</span></span>

```console
dotnet publish /p:PublishProfile=Azure /p:Configuration=Release
```

<span data-ttu-id="5c16f-211">Při použití profil publikování, nastavte následující vlastnosti MSBuild:</span><span class="sxs-lookup"><span data-stu-id="5c16f-211">Set the following MSBuild properties when using a publish profile:</span></span>

* `DeployOnBuild=true`
* `PublishProfile={PUBLISH PROFILE}`

<span data-ttu-id="5c16f-212">Při publikování tak, že profil s názvem *FolderProfile*, lze provést jednu z následujících příkazů:</span><span class="sxs-lookup"><span data-stu-id="5c16f-212">When publishing with a profile named *FolderProfile*, either of the commands below can be executed:</span></span>

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild      /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="5c16f-213">Při vyvolání [dotnet sestavení](/dotnet/core/tools/dotnet-build), volá `msbuild` ke spuštění sestavení a publikování procesu.</span><span class="sxs-lookup"><span data-stu-id="5c16f-213">When invoking [dotnet build](/dotnet/core/tools/dotnet-build), it calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="5c16f-214">Volání na buď `dotnet build` nebo `msbuild` je ekvivalentní při předávání ve složce profilu.</span><span class="sxs-lookup"><span data-stu-id="5c16f-214">Calling either `dotnet build` or `msbuild` is equivalent when passing in a folder profile.</span></span> <span data-ttu-id="5c16f-215">Při volání nástroje MSBuild přímo na Windows, se používá rozhraní .NET Framework verze nástroje MSBuild.</span><span class="sxs-lookup"><span data-stu-id="5c16f-215">When calling MSBuild directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="5c16f-216">Je aktuálně omezená na počítače s Windows pro publikování MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="5c16f-216">MSDeploy is currently limited to Windows machines for publishing.</span></span> <span data-ttu-id="5c16f-217">Volání `dotnet build` do jiné složky profil vyvolá MSBuild a nástroj MSBuild používá MSDeploy v jiné složce profily.</span><span class="sxs-lookup"><span data-stu-id="5c16f-217">Calling `dotnet build` on a non-folder profile invokes MSBuild, and MSBuild uses MSDeploy on non-folder profiles.</span></span> <span data-ttu-id="5c16f-218">Volání `dotnet build` v jiné složce profilu vyvolá MSBuild (pomocí MSDeploy) a vede k chybě (i když spuštěn na platformě Windows).</span><span class="sxs-lookup"><span data-stu-id="5c16f-218">Calling `dotnet build` on a non-folder profile invokes MSBuild (using MSDeploy) and results in a failure (even when running on a Windows platform).</span></span> <span data-ttu-id="5c16f-219">Pokud chcete publikovat pomocí profilu bez složky, přímo voláním funkce MSBuild.</span><span class="sxs-lookup"><span data-stu-id="5c16f-219">To publish with a non-folder profile, call MSBuild directly.</span></span>

<span data-ttu-id="5c16f-220">Profil publikování následující složka byla vytvořena pomocí sady Visual Studio a publikuje do sdílené síťové složky:</span><span class="sxs-lookup"><span data-stu-id="5c16f-220">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

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

<span data-ttu-id="5c16f-221">V předchozím příkladu `<LastUsedBuildConfiguration>` je nastavena na `Release`.</span><span class="sxs-lookup"><span data-stu-id="5c16f-221">In the preceding example, `<LastUsedBuildConfiguration>` is set to `Release`.</span></span> <span data-ttu-id="5c16f-222">Při publikování ze sady Visual Studio `<LastUsedBuildConfiguration>` hodnota vlastnosti konfigurace je nastavena pomocí hodnoty, když se spustí proces publikování.</span><span class="sxs-lookup"><span data-stu-id="5c16f-222">When publishing from Visual Studio, the `<LastUsedBuildConfiguration>` configuration property value is set using the value when the publish process is started.</span></span> <span data-ttu-id="5c16f-223">`<LastUsedBuildConfiguration>` Vlastnost konfigurace je speciální a nesmí být přepsána nastaveními v importovaný soubor MSBuild.</span><span class="sxs-lookup"><span data-stu-id="5c16f-223">The `<LastUsedBuildConfiguration>` configuration property is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="5c16f-224">Tato vlastnost může být přepsána z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="5c16f-224">This property can be overridden from the command line.</span></span>

<span data-ttu-id="5c16f-225">Použití .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="5c16f-225">Using the .NET Core CLI:</span></span>

```console
dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

<span data-ttu-id="5c16f-226">Použití nástroje MSBuild:</span><span class="sxs-lookup"><span data-stu-id="5c16f-226">Using MSBuild:</span></span>

```console
msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="5c16f-227">Publikování do koncového bodu MSDeploy z příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="5c16f-227">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="5c16f-228">Následující příklad používá webovou aplikaci ASP.NET Core, vytvořené pomocí sady Visual Studio s názvem *AzureWebApp*.</span><span class="sxs-lookup"><span data-stu-id="5c16f-228">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp*.</span></span> <span data-ttu-id="5c16f-229">Profil publikování aplikací Azure se přidá s Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5c16f-229">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="5c16f-230">Další informace o tom, jak vytvořit profil, najdete v článku [publikační profily](#publish-profiles) oddílu.</span><span class="sxs-lookup"><span data-stu-id="5c16f-230">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="5c16f-231">Chcete-li nasadit aplikaci pomocí profilu publikování, spusťte `msbuild` příkaz ze sady Visual Studio **Developer Command Prompt**.</span><span class="sxs-lookup"><span data-stu-id="5c16f-231">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt**.</span></span> <span data-ttu-id="5c16f-232">Je k dispozici v příkazovém řádku *sady Visual Studio* složky **Start** nabídky na hlavním panelu Windows.</span><span class="sxs-lookup"><span data-stu-id="5c16f-232">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="5c16f-233">Pro snadnější přístup, můžete přidat do příkazového řádku **nástroje** nabídky v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5c16f-233">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="5c16f-234">Další informace najdete v tématu [Developer Command Prompt pro sadu Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="5c16f-234">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="5c16f-235">Nástroj MSBuild používá následující syntaxe příkazu:</span><span class="sxs-lookup"><span data-stu-id="5c16f-235">MSBuild uses the following command syntax:</span></span>

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="5c16f-236">POLOŽKY {PATH} &ndash; Cestu k souboru projektu vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="5c16f-236">{PATH} &ndash; Path to the app's project file.</span></span>
* <span data-ttu-id="5c16f-237">{PROFIL} &ndash; Název profilu publikování.</span><span class="sxs-lookup"><span data-stu-id="5c16f-237">{PROFILE} &ndash; Name of the publish profile.</span></span>
* <span data-ttu-id="5c16f-238">{USERNAME} &ndash; MSDeploy uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="5c16f-238">{USERNAME} &ndash; MSDeploy username.</span></span> <span data-ttu-id="5c16f-239">{USERNAME} najdete v profilu publikování.</span><span class="sxs-lookup"><span data-stu-id="5c16f-239">The {USERNAME} can be found in the publish profile.</span></span>
* <span data-ttu-id="5c16f-240">{HESLO} &ndash; MSDeploy heslo.</span><span class="sxs-lookup"><span data-stu-id="5c16f-240">{PASSWORD} &ndash; MSDeploy password.</span></span> <span data-ttu-id="5c16f-241">Získat {heslo} z *{profil}. PublishSettings* souboru.</span><span class="sxs-lookup"><span data-stu-id="5c16f-241">Obtain the {PASSWORD} from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="5c16f-242">Stáhněte si *. PublishSettings* soubor z aplikace:</span><span class="sxs-lookup"><span data-stu-id="5c16f-242">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="5c16f-243">Průzkumník řešení: Vyberte **zobrazení** > **Cloud Explorer**.</span><span class="sxs-lookup"><span data-stu-id="5c16f-243">Solution Explorer: Select **View** > **Cloud Explorer**.</span></span> <span data-ttu-id="5c16f-244">Připojení k vašemu předplatnému Azure.</span><span class="sxs-lookup"><span data-stu-id="5c16f-244">Connect with your Azure subscription.</span></span> <span data-ttu-id="5c16f-245">Otevřít **App Services**.</span><span class="sxs-lookup"><span data-stu-id="5c16f-245">Open **App Services**.</span></span> <span data-ttu-id="5c16f-246">Klikněte pravým tlačítkem na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5c16f-246">Right-click the app.</span></span> <span data-ttu-id="5c16f-247">Vyberte **stáhnout profil publikování**.</span><span class="sxs-lookup"><span data-stu-id="5c16f-247">Select **Download Publish Profile**.</span></span>
  * <span data-ttu-id="5c16f-248">Azure portal: Vyberte **získat profil publikování** ve webové aplikaci **přehled** panelu.</span><span class="sxs-lookup"><span data-stu-id="5c16f-248">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="5c16f-249">Následující příklad používá profil publikování s názvem *AzureWebApp – nasazení webu*:</span><span class="sxs-lookup"><span data-stu-id="5c16f-249">The following example uses a publish profile named *AzureWebApp - Web Deploy*:</span></span>

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="5c16f-250">Profil publikování, je také možné pomocí rozhraní příkazového řádku .NET Core [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) příkazu z příkazového řádku Windows:</span><span class="sxs-lookup"><span data-stu-id="5c16f-250">A publish profile can also be used with the .NET Core CLI [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command prompt:</span></span>

```console
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!NOTE]
> <span data-ttu-id="5c16f-251">[Dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) příkaz je k dispozici napříč platformami a můžete zkompilovat aplikace ASP.NET Core v macOS a Linuxu.</span><span class="sxs-lookup"><span data-stu-id="5c16f-251">The [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command is available cross-platform and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="5c16f-252">Však není schopné nasadit aplikaci do Azure nebo jiný koncový bod MSDeploy MSBuild v systému macOS a Linux.</span><span class="sxs-lookup"><span data-stu-id="5c16f-252">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoint.</span></span> <span data-ttu-id="5c16f-253">MSDeploy je dostupná jenom ve Windows.</span><span class="sxs-lookup"><span data-stu-id="5c16f-253">MSDeploy is only available on Windows.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="5c16f-254">Nastavení prostředí</span><span class="sxs-lookup"><span data-stu-id="5c16f-254">Set the environment</span></span>

<span data-ttu-id="5c16f-255">Zahrnout `<EnvironmentName>` vlastnost v profilu publikování ( *.pubxml*) nebo soubor projektu a nastavení aplikace [prostředí](xref:fundamentals/environments):</span><span class="sxs-lookup"><span data-stu-id="5c16f-255">Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="5c16f-256">Pokud budete potřebovat *web.config* transformace (například nastavení proměnné prostředí na základě konfigurace, profil nebo prostředí), najdete v článku <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="5c16f-256">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="5c16f-257">Vyloučení souborů</span><span class="sxs-lookup"><span data-stu-id="5c16f-257">Exclude files</span></span>

<span data-ttu-id="5c16f-258">Při publikování webové aplikace ASP.NET Core, jsou zahrnuty následující prostředky:</span><span class="sxs-lookup"><span data-stu-id="5c16f-258">When publishing ASP.NET Core web apps, the following assets are included:</span></span>

* <span data-ttu-id="5c16f-259">Artefakty sestavení</span><span class="sxs-lookup"><span data-stu-id="5c16f-259">Build artifacts</span></span>
* <span data-ttu-id="5c16f-260">Složky a soubory odpovídající následujících vzorů podpory zástupných znaků:</span><span class="sxs-lookup"><span data-stu-id="5c16f-260">Folders and files matching the following globbing patterns:</span></span>
  * <span data-ttu-id="5c16f-261">`**\*.config` (například *web.config*)</span><span class="sxs-lookup"><span data-stu-id="5c16f-261">`**\*.config` (for example, *web.config*)</span></span>
  * <span data-ttu-id="5c16f-262">`**\*.json` (například *appsettings.json*)</span><span class="sxs-lookup"><span data-stu-id="5c16f-262">`**\*.json` (for example, *appsettings.json*)</span></span>
  * `wwwroot\**`

<span data-ttu-id="5c16f-263">Podporuje MSBuild [vzorů podpory zástupných znaků](https://gruntjs.com/configuring-tasks#globbing-patterns).</span><span class="sxs-lookup"><span data-stu-id="5c16f-263">MSBuild supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="5c16f-264">Například následující `<Content>` element potlačí kopírování textu ( *.txt*) soubory *wwwroot\content* složce a jejích podsložkách:</span><span class="sxs-lookup"><span data-stu-id="5c16f-264">For example, the following `<Content>` element suppresses the copying of text (*.txt*) files in the *wwwroot\content* folder and its subfolders:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="5c16f-265">Předchozí kód lze přidat do profilu publikování nebo *.csproj* souboru.</span><span class="sxs-lookup"><span data-stu-id="5c16f-265">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="5c16f-266">Když se přidá *.csproj* , pravidlo se přidá soubor všechny profily publikování v projektu.</span><span class="sxs-lookup"><span data-stu-id="5c16f-266">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="5c16f-267">Následující `<MsDeploySkipRules>` element vyloučí všechny soubory *wwwroot\content* složky:</span><span class="sxs-lookup"><span data-stu-id="5c16f-267">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot\content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="5c16f-268">`<MsDeploySkipRules>` nedojde k odstranění *přeskočit* cílů nasazení webu.</span><span class="sxs-lookup"><span data-stu-id="5c16f-268">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="5c16f-269">`<Content>` cílové soubory a složky, jsou odstraněny z nasazení webu.</span><span class="sxs-lookup"><span data-stu-id="5c16f-269">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="5c16f-270">Předpokládejme například, že nasazené webové aplikace má následující soubory:</span><span class="sxs-lookup"><span data-stu-id="5c16f-270">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="5c16f-271">*Views/Home/About1.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5c16f-271">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="5c16f-272">*Views/Home/About2.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5c16f-272">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="5c16f-273">*Views/Home/About3.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5c16f-273">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="5c16f-274">Pokud následující `<MsDeploySkipRules>` prvky jsou přidány, by dojít k odstranění těchto souborů na web pro nasazení.</span><span class="sxs-lookup"><span data-stu-id="5c16f-274">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

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

<span data-ttu-id="5c16f-275">Předchozí `<MsDeploySkipRules>` zabránit prvky *přeskočeno* soubory z nasazení.</span><span class="sxs-lookup"><span data-stu-id="5c16f-275">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="5c16f-276">Tyto soubory neodstraní po nasazení.</span><span class="sxs-lookup"><span data-stu-id="5c16f-276">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="5c16f-277">Následující `<Content>` element odstraní cílové soubory na web pro nasazení:</span><span class="sxs-lookup"><span data-stu-id="5c16f-277">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="5c16f-278">Pomocí příkazového řádku nasazení s předchozím `<Content>` element provede následující výstup:</span><span class="sxs-lookup"><span data-stu-id="5c16f-278">Using command-line deployment with the preceding `<Content>` element yields the following output:</span></span>

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\netcoreapp1.1\PubTmp\Web1.SourceManifest.
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

## <a name="include-files"></a><span data-ttu-id="5c16f-279">Soubory k zahrnutí</span><span class="sxs-lookup"><span data-stu-id="5c16f-279">Include files</span></span>

<span data-ttu-id="5c16f-280">Obsahuje následující kód *image* mimo adresář projektu do složky *wwwroot/imagí* složku publikování webu:</span><span class="sxs-lookup"><span data-stu-id="5c16f-280">The following markup includes an *images* folder outside the project directory to the *wwwroot/images* folder of the publish site:</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotnetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotnetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="5c16f-281">Značky mohou být přidány do *.csproj* souboru nebo profil publikování.</span><span class="sxs-lookup"><span data-stu-id="5c16f-281">The markup can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="5c16f-282">Pokud je přidána do *.csproj* souboru, je zahrnutý v každé profilu publikování v projektu.</span><span class="sxs-lookup"><span data-stu-id="5c16f-282">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>

<span data-ttu-id="5c16f-283">Následující zvýrazněný kód ukazuje jak na:</span><span class="sxs-lookup"><span data-stu-id="5c16f-283">The following highlighted markup shows how to:</span></span>

* <span data-ttu-id="5c16f-284">Zkopírovat soubor z mimo projekt sketchflow *wwwroot* složky.</span><span class="sxs-lookup"><span data-stu-id="5c16f-284">Copy a file from outside the project into the *wwwroot* folder.</span></span>
* <span data-ttu-id="5c16f-285">Vyloučit *wwwroot\Content* složky.</span><span class="sxs-lookup"><span data-stu-id="5c16f-285">Exclude the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="5c16f-286">Vyloučit *Views\Home\About2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="5c16f-286">Exclude *Views\Home\About2.cshtml*.</span></span>

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
    <PublishFramework />
    <ProjectGuid>afa9f185-7ce0-4935-9da1-ab676229d68a</ProjectGuid>
    <publishUrl>bin\Release\PublishOutput</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
  <ItemGroup>
    <ResolvedFileToPublish Include="..\ReadMe2.MD">
      <RelativePath>wwwroot\ReadMe2.MD</RelativePath>
    </ResolvedFileToPublish>

    <Content Update="wwwroot\Content\**\*" CopyToPublishDirectory="Never" />
    <Content Update="Views\Home\About2.cshtml" CopyToPublishDirectory="Never" />

  </ItemGroup>
</Project>
```

<span data-ttu-id="5c16f-287">Zobrazit [WebSDK Readme](https://github.com/aspnet/websdk) pro další nasazení ukázky.</span><span class="sxs-lookup"><span data-stu-id="5c16f-287">See the [WebSDK Readme](https://github.com/aspnet/websdk) for more deployment samples.</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="5c16f-288">Spustit cíl před nebo po publikování</span><span class="sxs-lookup"><span data-stu-id="5c16f-288">Run a target before or after publishing</span></span>

<span data-ttu-id="5c16f-289">Předdefinované `BeforePublish` a `AfterPublish` cíle spustit cíl před nebo za cíl publikování.</span><span class="sxs-lookup"><span data-stu-id="5c16f-289">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="5c16f-290">Přidáte do profilu publikování k protokolování zpráv konzoly před i po publikování následující prvky:</span><span class="sxs-lookup"><span data-stu-id="5c16f-290">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="5c16f-291">Publikovat na server pomocí nedůvěryhodného certifikátu</span><span class="sxs-lookup"><span data-stu-id="5c16f-291">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="5c16f-292">Přidat `<AllowUntrustedCertificate>` vlastnost s hodnotou `True` do profilu publikování:</span><span class="sxs-lookup"><span data-stu-id="5c16f-292">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="5c16f-293">Kudu service</span><span class="sxs-lookup"><span data-stu-id="5c16f-293">The Kudu service</span></span>

<span data-ttu-id="5c16f-294">Chcete-li zobrazit soubory v nasazení služby Azure App Service webovou aplikaci, použijte [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span><span class="sxs-lookup"><span data-stu-id="5c16f-294">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="5c16f-295">Připojit `scm` tokenu název webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="5c16f-295">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="5c16f-296">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5c16f-296">For example:</span></span>

| <span data-ttu-id="5c16f-297">Adresa URL</span><span class="sxs-lookup"><span data-stu-id="5c16f-297">URL</span></span>                                    | <span data-ttu-id="5c16f-298">Výsledek</span><span class="sxs-lookup"><span data-stu-id="5c16f-298">Result</span></span>       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | <span data-ttu-id="5c16f-299">Webové aplikace</span><span class="sxs-lookup"><span data-stu-id="5c16f-299">Web App</span></span>      |
| `http://mysite.scm.azurewebsites.net/` | <span data-ttu-id="5c16f-300">Kudu service</span><span class="sxs-lookup"><span data-stu-id="5c16f-300">Kudu service</span></span> |

<span data-ttu-id="5c16f-301">Vyberte [ladění konzoly](https://github.com/projectkudu/kudu/wiki/Kudu-console) položka nabídky zobrazit, upravit, odstranit nebo přidat soubory.</span><span class="sxs-lookup"><span data-stu-id="5c16f-301">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5c16f-302">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5c16f-302">Additional resources</span></span>

* <span data-ttu-id="5c16f-303">[Webu nasadit](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) zjednodušuje nasazení webové aplikace a weby pro servery služby IIS.</span><span class="sxs-lookup"><span data-stu-id="5c16f-303">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="5c16f-304">[https://github.com/aspnet/websdk](https://github.com/aspnet/websdk/issues): Soubor problémů a požádat o funkce pro nasazení.</span><span class="sxs-lookup"><span data-stu-id="5c16f-304">[https://github.com/aspnet/websdk](https://github.com/aspnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="5c16f-305">Publikování webové aplikace v ASP.NET do virtuálního počítače Azure ze sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c16f-305">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
