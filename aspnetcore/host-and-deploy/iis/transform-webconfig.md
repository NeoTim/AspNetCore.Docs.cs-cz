---
title: Transformace souboru web.config
author: rick-anderson
description: Přečtěte si, jak transformovat soubor web.config při publikování aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: 069b9bb516644a1a722235b33d4916460488ebf2
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657932"
---
# <a name="transform-webconfig"></a><span data-ttu-id="da223-103">Transformace souboru web.config</span><span class="sxs-lookup"><span data-stu-id="da223-103">Transform web.config</span></span>

<span data-ttu-id="da223-104">Podle [Vijay Ramakrishnan](https://github.com/vijayrkn)</span><span class="sxs-lookup"><span data-stu-id="da223-104">By [Vijay Ramakrishnan](https://github.com/vijayrkn)</span></span>

<span data-ttu-id="da223-105">Transformace do souboru *web.config* lze použít automaticky při publikování aplikace na základě:</span><span class="sxs-lookup"><span data-stu-id="da223-105">Transformations to the *web.config* file can be applied automatically when an app is published based on:</span></span>

* [<span data-ttu-id="da223-106">Konfigurace sestavení</span><span class="sxs-lookup"><span data-stu-id="da223-106">Build configuration</span></span>](#build-configuration)
* [<span data-ttu-id="da223-107">Profil</span><span class="sxs-lookup"><span data-stu-id="da223-107">Profile</span></span>](#profile)
* [<span data-ttu-id="da223-108">Prostředí</span><span class="sxs-lookup"><span data-stu-id="da223-108">Environment</span></span>](#environment)
* [<span data-ttu-id="da223-109">Vlastní</span><span class="sxs-lookup"><span data-stu-id="da223-109">Custom</span></span>](#custom)

<span data-ttu-id="da223-110">K těmto transformacídochází v některém z následujících scénářů generování *web.config:*</span><span class="sxs-lookup"><span data-stu-id="da223-110">These transformations occur for either of the following *web.config* generation scenarios:</span></span>

* <span data-ttu-id="da223-111">Generováno automaticky `Microsoft.NET.Sdk.Web` sadou SDK.</span><span class="sxs-lookup"><span data-stu-id="da223-111">Generated automatically by the `Microsoft.NET.Sdk.Web` SDK.</span></span>
* <span data-ttu-id="da223-112">Poskytované vývojářem v [kořenovém adresáři obsahu](xref:fundamentals/index#content-root) aplikace.</span><span class="sxs-lookup"><span data-stu-id="da223-112">Provided by the developer in the [content root](xref:fundamentals/index#content-root) of the app.</span></span>

## <a name="build-configuration"></a><span data-ttu-id="da223-113">Konfigurace sestavení</span><span class="sxs-lookup"><span data-stu-id="da223-113">Build configuration</span></span>

<span data-ttu-id="da223-114">Transformace konfigurace sestavení jsou spuštěny jako první.</span><span class="sxs-lookup"><span data-stu-id="da223-114">Build configuration transforms are run first.</span></span>

<span data-ttu-id="da223-115">Zahrnout *web.{ SOUBOR CONFIGURATION}.config* pro každou [konfiguraci sestavení (Ladění| Release)](/dotnet/core/tools/dotnet-publish#options) vyžadující transformaci *web.config.*</span><span class="sxs-lookup"><span data-stu-id="da223-115">Include a *web.{CONFIGURATION}.config* file for each [build configuration (Debug|Release)](/dotnet/core/tools/dotnet-publish#options) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="da223-116">V následujícím příkladu je proměnná prostředí specifická pro konfiguraci nastavena na *webu. Release.config*:</span><span class="sxs-lookup"><span data-stu-id="da223-116">In the following example, a configuration-specific environment variable is set in *web.Release.config*:</span></span>

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

<span data-ttu-id="da223-117">Transformace se použije, když je konfigurace nastavena na *release*:</span><span class="sxs-lookup"><span data-stu-id="da223-117">The transform is applied when the configuration is set to *Release*:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="da223-118">Vlastnost MSBuild pro konfiguraci je `$(Configuration)`.</span><span class="sxs-lookup"><span data-stu-id="da223-118">The MSBuild property for the configuration is `$(Configuration)`.</span></span>

## <a name="profile"></a><span data-ttu-id="da223-119">Profil</span><span class="sxs-lookup"><span data-stu-id="da223-119">Profile</span></span>

<span data-ttu-id="da223-120">Transformace profilu jsou spuštěny druhé, po [transformace konfigurace sestavení.](#build-configuration)</span><span class="sxs-lookup"><span data-stu-id="da223-120">Profile transformations are run second, after [Build configuration](#build-configuration) transforms.</span></span>

<span data-ttu-id="da223-121">Zahrnout *web.{ PROFILE}.config* pro každou konfiguraci profilu, která vyžaduje transformaci *web.config.*</span><span class="sxs-lookup"><span data-stu-id="da223-121">Include a *web.{PROFILE}.config* file for each profile configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="da223-122">V následujícím příkladu je proměnná prostředí specifická pro profil nastavena na *webu. FolderProfile.config* pro profil publikování složky:</span><span class="sxs-lookup"><span data-stu-id="da223-122">In the following example, a profile-specific environment variable is set in *web.FolderProfile.config* for a folder publish profile:</span></span>

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

<span data-ttu-id="da223-123">Transformace se použije, když je profil *FolderProfile*:</span><span class="sxs-lookup"><span data-stu-id="da223-123">The transform is applied when the profile is *FolderProfile*:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

<span data-ttu-id="da223-124">Vlastnost MSBuild pro název `$(PublishProfile)`profilu je .</span><span class="sxs-lookup"><span data-stu-id="da223-124">The MSBuild property for the profile name is `$(PublishProfile)`.</span></span>

<span data-ttu-id="da223-125">Pokud není předán žádný profil, výchozí název profilu je **FileSystem** a *web. Soubor FileSystem.config* se použije, pokud je soubor přítomen v kořenovém adresáři obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="da223-125">If no profile is passed, the default profile name is **FileSystem** and *web.FileSystem.config* is applied if the file is present in the app's content root.</span></span>

## <a name="environment"></a><span data-ttu-id="da223-126">Prostředí</span><span class="sxs-lookup"><span data-stu-id="da223-126">Environment</span></span>

<span data-ttu-id="da223-127">Transformace prostředí jsou spuštěny třetí, po [sestavení konfigurace](#build-configuration) a [profil](#profile) transformace.</span><span class="sxs-lookup"><span data-stu-id="da223-127">Environment transformations are run third, after [Build configuration](#build-configuration) and [Profile](#profile) transforms.</span></span>

<span data-ttu-id="da223-128">Zahrnout *web.{ ENVIRONMENT}.config* pro každé [prostředí,](xref:fundamentals/environments) které vyžaduje transformaci *web.config.*</span><span class="sxs-lookup"><span data-stu-id="da223-128">Include a *web.{ENVIRONMENT}.config* file for each [environment](xref:fundamentals/environments) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="da223-129">V následujícím příkladu je proměnná prostředí specifická pro prostředí nastavena na *webu. Production.config* pro produkční prostředí:</span><span class="sxs-lookup"><span data-stu-id="da223-129">In the following example, a environment-specific environment variable is set in *web.Production.config* for the Production environment:</span></span>

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

<span data-ttu-id="da223-130">Transformace se použije, když je prostředí *Výroba*:</span><span class="sxs-lookup"><span data-stu-id="da223-130">The transform is applied when the environment is *Production*:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:EnvironmentName=Production
```

<span data-ttu-id="da223-131">Vlastnost MSBuild pro prostředí `$(EnvironmentName)`je .</span><span class="sxs-lookup"><span data-stu-id="da223-131">The MSBuild property for the environment is `$(EnvironmentName)`.</span></span>

<span data-ttu-id="da223-132">Při publikování z Visual Studia a <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>pomocí profilu publikování, najdete v tématu .</span><span class="sxs-lookup"><span data-stu-id="da223-132">When publishing from Visual Studio and using a publish profile, see <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.</span></span>

<span data-ttu-id="da223-133">Proměnná `ASPNETCORE_ENVIRONMENT` prostředí je automaticky přidána do souboru *web.config,* když je zadán název prostředí.</span><span class="sxs-lookup"><span data-stu-id="da223-133">The `ASPNETCORE_ENVIRONMENT` environment variable is automatically added to the *web.config* file when the environment name is specified.</span></span>

## <a name="custom"></a><span data-ttu-id="da223-134">Vlastní</span><span class="sxs-lookup"><span data-stu-id="da223-134">Custom</span></span>

<span data-ttu-id="da223-135">Vlastní transformace jsou spuštěny jako poslední, po [sestavení konfigurace](#build-configuration), [profil](#profile)a [prostředí](#environment) transformace.</span><span class="sxs-lookup"><span data-stu-id="da223-135">Custom transformations are run last, after [Build configuration](#build-configuration), [Profile](#profile), and [Environment](#environment) transforms.</span></span>

<span data-ttu-id="da223-136">Pro každou vlastní konfiguraci vyžadující transformaci *web.config* zahrňte soubor *{CUSTOM_NAME}.transform.*</span><span class="sxs-lookup"><span data-stu-id="da223-136">Include a *{CUSTOM_NAME}.transform* file for each custom configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="da223-137">V následujícím příkladu je proměnná prostředí vlastní transformace nastavena v *souboru custom.transform*:</span><span class="sxs-lookup"><span data-stu-id="da223-137">In the following example, a custom transform environment variable is set in *custom.transform*:</span></span>

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

<span data-ttu-id="da223-138">Transformace se použije, `CustomTransformFileName` když je vlastnost předána příkazu [dotnet publish:](/dotnet/core/tools/dotnet-publish)</span><span class="sxs-lookup"><span data-stu-id="da223-138">The transform is applied when the `CustomTransformFileName` property is passed to the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

<span data-ttu-id="da223-139">Vlastnost MSBuild pro název `$(CustomTransformFileName)`profilu je .</span><span class="sxs-lookup"><span data-stu-id="da223-139">The MSBuild property for the profile name is `$(CustomTransformFileName)`.</span></span>

## <a name="prevent-webconfig-transformation"></a><span data-ttu-id="da223-140">Zabránit transformaci web.config</span><span class="sxs-lookup"><span data-stu-id="da223-140">Prevent web.config transformation</span></span>

<span data-ttu-id="da223-141">Chcete-li zabránit transformací souboru *web.config,* nastavte vlastnost `$(IsWebConfigTransformDisabled)`MSBuild :</span><span class="sxs-lookup"><span data-stu-id="da223-141">To prevent transformations of the *web.config* file, set the MSBuild property `$(IsWebConfigTransformDisabled)`:</span></span>

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a><span data-ttu-id="da223-142">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="da223-142">Additional resources</span></span>

* <span data-ttu-id="da223-143">[Syntaxe transformace web.config pro nasazení projektu webové aplikace](/previous-versions/dd465326(v=vs.100))</span><span class="sxs-lookup"><span data-stu-id="da223-143">[Web.config Transformation Syntax for Web Application Project Deployment](/previous-versions/dd465326(v=vs.100))</span></span>
* <span data-ttu-id="da223-144">[Syntaxe transformace Web.config pro nasazení webového projektu pomocí sady Visual Studio](/previous-versions/aspnet/dd465326(v=vs.110))</span><span class="sxs-lookup"><span data-stu-id="da223-144">[Web.config Transformation Syntax for Web Project Deployment Using Visual Studio](/previous-versions/aspnet/dd465326(v=vs.110))</span></span>
