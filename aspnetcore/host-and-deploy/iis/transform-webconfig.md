---
title: Transformace souboru web.config
author: guardrex
description: Zjistěte, jak transformace souboru web.config, při publikování aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2019
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: bd8cf7d8515e874eefd2c326727f56d0a4b502a7
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087081"
---
# <a name="transform-webconfig"></a><span data-ttu-id="1c0ba-103">Transformace souboru web.config</span><span class="sxs-lookup"><span data-stu-id="1c0ba-103">Transform web.config</span></span>

<span data-ttu-id="1c0ba-104">Podle [Vijay Ramakrishnan](https://github.com/vijayrkn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1c0ba-104">By [Vijay Ramakrishnan](https://github.com/vijayrkn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1c0ba-105">Transformace tak, *web.config* souboru můžete použít automaticky, když je při publikování na základě:</span><span class="sxs-lookup"><span data-stu-id="1c0ba-105">Transformations to the *web.config* file can be applied automatically when an app is published based on:</span></span>

* [<span data-ttu-id="1c0ba-106">Konfigurace sestavení</span><span class="sxs-lookup"><span data-stu-id="1c0ba-106">Build configuration</span></span>](#build-configuration)
* [<span data-ttu-id="1c0ba-107">Profile</span><span class="sxs-lookup"><span data-stu-id="1c0ba-107">Profile</span></span>](#profile)
* [<span data-ttu-id="1c0ba-108">Prostředí</span><span class="sxs-lookup"><span data-stu-id="1c0ba-108">Environment</span></span>](#environment)
* [<span data-ttu-id="1c0ba-109">Vlastní</span><span class="sxs-lookup"><span data-stu-id="1c0ba-109">Custom</span></span>](#custom)

<span data-ttu-id="1c0ba-110">Tyto transformace dojít z některého z následujících *web.config* generování scénáře:</span><span class="sxs-lookup"><span data-stu-id="1c0ba-110">These transformations occur for either of the following *web.config* generation scenarios:</span></span>

* <span data-ttu-id="1c0ba-111">Automaticky generované `Microsoft.NET.Sdk.Web` SDK.</span><span class="sxs-lookup"><span data-stu-id="1c0ba-111">Generated automatically by the `Microsoft.NET.Sdk.Web` SDK.</span></span>
* <span data-ttu-id="1c0ba-112">Poskytuje pro vývojáře v obsahu kořenovém adresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="1c0ba-112">Provided by the developer in the content root of the app.</span></span>

## <a name="build-configuration"></a><span data-ttu-id="1c0ba-113">Konfigurace sestavení</span><span class="sxs-lookup"><span data-stu-id="1c0ba-113">Build configuration</span></span>

<span data-ttu-id="1c0ba-114">Transformace konfigurace sestavení se nejprve spustí.</span><span class="sxs-lookup"><span data-stu-id="1c0ba-114">Build configuration transforms are run first.</span></span>

<span data-ttu-id="1c0ba-115">Zahrnout *webové. { KONFIGURACE} .config* souboru pro každý [konfiguraci sestavení (ladění | Vydaná verze)](/dotnet/core/tools/dotnet-publish#options) vyžadování *web.config* transformace.</span><span class="sxs-lookup"><span data-stu-id="1c0ba-115">Include a *web.{CONFIGURATION}.config* file for each [build configuration (Debug|Release)](/dotnet/core/tools/dotnet-publish#options) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="1c0ba-116">V následujícím příkladu je nastavena proměnná prostředí určených pro konfigurace *web. Release.config*:</span><span class="sxs-lookup"><span data-stu-id="1c0ba-116">In the following example, a configuration-specific environment variable is set in *web.Release.config*:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Configuration_Specific" 
                               value="Configuration_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="1c0ba-117">Při konfiguraci nastavená na je použita transformace *vydání*:</span><span class="sxs-lookup"><span data-stu-id="1c0ba-117">The transform is applied when the configuration is set to *Release*:</span></span>

```console
dotnet publish --configuration Release
```

<span data-ttu-id="1c0ba-118">Vlastnost MSBuild pro danou konfiguraci je `$(Configuration)`.</span><span class="sxs-lookup"><span data-stu-id="1c0ba-118">The MSBuild property for the configuration is `$(Configuration)`.</span></span>

## <a name="profile"></a><span data-ttu-id="1c0ba-119">Profil</span><span class="sxs-lookup"><span data-stu-id="1c0ba-119">Profile</span></span>

<span data-ttu-id="1c0ba-120">Transformace profilu spuštění druhého po [konfiguraci sestavení](#build-configuration) transformace.</span><span class="sxs-lookup"><span data-stu-id="1c0ba-120">Profile transformations are run second, after [Build configuration](#build-configuration) transforms.</span></span>

<span data-ttu-id="1c0ba-121">Zahrnout *webové. { PROFIL} .config* souboru pro každý profil konfigurace vyžadování *web.config* transformace.</span><span class="sxs-lookup"><span data-stu-id="1c0ba-121">Include a *web.{PROFILE}.config* file for each profile configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="1c0ba-122">V následujícím příkladu je nastavena proměnná prostředí pro konkrétní profil *web. FolderProfile.config* profil publikování pro složku:</span><span class="sxs-lookup"><span data-stu-id="1c0ba-122">In the following example, a profile-specific environment variable is set in *web.FolderProfile.config* for a folder publish profile:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Profile_Specific" 
                               value="Profile_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="1c0ba-123">Pokud je profil, který je použita transformace *FolderProfile*:</span><span class="sxs-lookup"><span data-stu-id="1c0ba-123">The transform is applied when the profile is *FolderProfile*:</span></span>

```console
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

<span data-ttu-id="1c0ba-124">Vlastnost MSBuild pro název profilu je `$(PublishProfile)`.</span><span class="sxs-lookup"><span data-stu-id="1c0ba-124">The MSBuild property for the profile name is `$(PublishProfile)`.</span></span>

<span data-ttu-id="1c0ba-125">Pokud není předán žádný profil, výchozí název profilu je **systému souborů** a *web. FileSystem.config* se použije, pokud se soubor nachází v kořenovém adresáři obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="1c0ba-125">If no profile is passed, the default profile name is **FileSystem** and *web.FileSystem.config* is applied if the file is present in the app's content root.</span></span>

## <a name="environment"></a><span data-ttu-id="1c0ba-126">Prostředí</span><span class="sxs-lookup"><span data-stu-id="1c0ba-126">Environment</span></span>

<span data-ttu-id="1c0ba-127">Transformace prostředí jsou spouštěny třetí po [konfiguraci sestavení](#build-configuration) a [profilu](#profile) transformace.</span><span class="sxs-lookup"><span data-stu-id="1c0ba-127">Environment transformations are run third, after [Build configuration](#build-configuration) and [Profile](#profile) transforms.</span></span>

<span data-ttu-id="1c0ba-128">Zahrnout *webové. { PROSTŘEDÍ} .config* souboru pro každý [prostředí](xref:fundamentals/environments) vyžadování *web.config* transformace.</span><span class="sxs-lookup"><span data-stu-id="1c0ba-128">Include a *web.{ENVIRONMENT}.config* file for each [environment](xref:fundamentals/environments) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="1c0ba-129">V následujícím příkladu je nastavena proměnná prostředí pro konkrétní prostředí *web. Production.config* pro produkční prostředí:</span><span class="sxs-lookup"><span data-stu-id="1c0ba-129">In the following example, a environment-specific environment variable is set in *web.Production.config* for the Production environment:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Environment_Specific" 
                               value="Environment_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="1c0ba-130">Pokud je prostředí, je použita transformace *produkční*:</span><span class="sxs-lookup"><span data-stu-id="1c0ba-130">The transform is applied when the environment is *Production*:</span></span>

```console
dotnet publish --configuration Release /p:EnvironmentName=Production
```

<span data-ttu-id="1c0ba-131">Vlastnost MSBuild pro prostředí je `$(EnvironmentName)`.</span><span class="sxs-lookup"><span data-stu-id="1c0ba-131">The MSBuild property for the environment is `$(EnvironmentName)`.</span></span>

<span data-ttu-id="1c0ba-132">Při publikování ze sady Visual Studio a používání profilu publikování, najdete v článku <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.</span><span class="sxs-lookup"><span data-stu-id="1c0ba-132">When publishing from Visual Studio and using a publish profile, see <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.</span></span>

<span data-ttu-id="1c0ba-133">`ASPNETCORE_ENVIRONMENT` Proměnné prostředí se automaticky přidá do *web.config* souboru, když je zadaný název prostředí.</span><span class="sxs-lookup"><span data-stu-id="1c0ba-133">The `ASPNETCORE_ENVIRONMENT` environment variable is automatically added to the *web.config* file when the environment name is specified.</span></span>

## <a name="custom"></a><span data-ttu-id="1c0ba-134">Vlastní</span><span class="sxs-lookup"><span data-stu-id="1c0ba-134">Custom</span></span>

<span data-ttu-id="1c0ba-135">Vlastní transformace jsou spuštěny po poslední [konfiguraci sestavení](#build-configuration), [profilu](#profile), a [prostředí](#environment) transformace.</span><span class="sxs-lookup"><span data-stu-id="1c0ba-135">Custom transformations are run last, after [Build configuration](#build-configuration), [Profile](#profile), and [Environment](#environment) transforms.</span></span>

<span data-ttu-id="1c0ba-136">Zahrnout *{CUSTOM_NAME} .transform* souboru pro každý vlastní konfigurace vyžadování *web.config* transformace.</span><span class="sxs-lookup"><span data-stu-id="1c0ba-136">Include a *{CUSTOM_NAME}.transform* file for each custom configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="1c0ba-137">V následujícím příkladu je nastavena proměnná prostředí vlastní transformace *custom.transform*:</span><span class="sxs-lookup"><span data-stu-id="1c0ba-137">In the following example, a custom transform environment variable is set in *custom.transform*:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Custom_Specific" 
                               value="Custom_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="1c0ba-138">Je použita transformace při `CustomTransformFileName` vlastnost předána [dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkaz:</span><span class="sxs-lookup"><span data-stu-id="1c0ba-138">The transform is applied when the `CustomTransformFileName` property is passed to the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

```console
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

<span data-ttu-id="1c0ba-139">Vlastnost MSBuild pro název profilu je `$(CustomTransformFileName)`.</span><span class="sxs-lookup"><span data-stu-id="1c0ba-139">The MSBuild property for the profile name is `$(CustomTransformFileName)`.</span></span>

## <a name="prevent-webconfig-transformation"></a><span data-ttu-id="1c0ba-140">Zabránit transformace web.config</span><span class="sxs-lookup"><span data-stu-id="1c0ba-140">Prevent web.config transformation</span></span>

<span data-ttu-id="1c0ba-141">Aby se zabránilo transformace *web.config* souboru, nastavte vlastnost MSBuild `$(IsWebConfigTransformDisabled)`:</span><span class="sxs-lookup"><span data-stu-id="1c0ba-141">To prevent transformations of the *web.config* file, set the MSBuild property `$(IsWebConfigTransformDisabled)`:</span></span>

```console
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a><span data-ttu-id="1c0ba-142">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="1c0ba-142">Additional resources</span></span>

* [<span data-ttu-id="1c0ba-143">Syntaxe transformace souboru Web.config pro nasazení projektu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="1c0ba-143">Web.config Transformation Syntax for Web Application Project Deployment</span></span>](http://go.microsoft.com/fwlink/?LinkId=301874)
* <span data-ttu-id="1c0ba-144">[Syntaxe transformace souboru Web.config pro projekt nasazení webu pomocí sady Visual Studio](https://docs.microsoft.com/previous-versions/aspnet/dd465326(v=vs.110))</span><span class="sxs-lookup"><span data-stu-id="1c0ba-144">[Web.config Transformation Syntax for Web Project Deployment Using Visual Studio](https://docs.microsoft.com/previous-versions/aspnet/dd465326(v=vs.110))</span></span>
