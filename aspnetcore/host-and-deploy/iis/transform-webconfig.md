---
title: Transformace souboru web.config
author: guardrex
description: Naučte se, jak transformovat soubor Web. config při publikování aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: d28c362a200ad433e316bc1af710231a169a30a4
ms.sourcegitcommit: 3d082bd46e9e00a3297ea0314582b1ed2abfa830
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "72007312"
---
# <a name="transform-webconfig"></a><span data-ttu-id="408cc-103">Transformace souboru web.config</span><span class="sxs-lookup"><span data-stu-id="408cc-103">Transform web.config</span></span>

<span data-ttu-id="408cc-104">Od [Vijay Ramakrishnan](https://github.com/vijayrkn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="408cc-104">By [Vijay Ramakrishnan](https://github.com/vijayrkn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="408cc-105">Transformace do souboru *Web. config* lze použít automaticky při publikování aplikace na základě:</span><span class="sxs-lookup"><span data-stu-id="408cc-105">Transformations to the *web.config* file can be applied automatically when an app is published based on:</span></span>

* [<span data-ttu-id="408cc-106">Konfigurace sestavení</span><span class="sxs-lookup"><span data-stu-id="408cc-106">Build configuration</span></span>](#build-configuration)
* [<span data-ttu-id="408cc-107">Profilu</span><span class="sxs-lookup"><span data-stu-id="408cc-107">Profile</span></span>](#profile)
* [<span data-ttu-id="408cc-108">Prostředí</span><span class="sxs-lookup"><span data-stu-id="408cc-108">Environment</span></span>](#environment)
* [<span data-ttu-id="408cc-109">Vlastní</span><span class="sxs-lookup"><span data-stu-id="408cc-109">Custom</span></span>](#custom)

<span data-ttu-id="408cc-110">Tyto transformace se vyskytují pro některé z následujících scénářů generování *webu. config* :</span><span class="sxs-lookup"><span data-stu-id="408cc-110">These transformations occur for either of the following *web.config* generation scenarios:</span></span>

* <span data-ttu-id="408cc-111">Vygenerováno automaticky sadou `Microsoft.NET.Sdk.Web` SDK.</span><span class="sxs-lookup"><span data-stu-id="408cc-111">Generated automatically by the `Microsoft.NET.Sdk.Web` SDK.</span></span>
* <span data-ttu-id="408cc-112">Poskytl vývojář v [kořenu obsahu](xref:fundamentals/index#content-root) aplikace.</span><span class="sxs-lookup"><span data-stu-id="408cc-112">Provided by the developer in the [content root](xref:fundamentals/index#content-root) of the app.</span></span>

## <a name="build-configuration"></a><span data-ttu-id="408cc-113">Konfigurace sestavení</span><span class="sxs-lookup"><span data-stu-id="408cc-113">Build configuration</span></span>

<span data-ttu-id="408cc-114">Transformace konfigurace sestavení se spouštějí jako první.</span><span class="sxs-lookup"><span data-stu-id="408cc-114">Build configuration transforms are run first.</span></span>

<span data-ttu-id="408cc-115">Zahrnout *Web. { KONFIGURAČNÍ soubor. config* pro každou [konfiguraci sestavení (ladění | Release)](/dotnet/core/tools/dotnet-publish#options) , která vyžaduje transformaci *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="408cc-115">Include a *web.{CONFIGURATION}.config* file for each [build configuration (Debug|Release)](/dotnet/core/tools/dotnet-publish#options) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="408cc-116">V následujícím příkladu je proměnná prostředí specifická pro konfiguraci nastavena na *webu. Release. config*:</span><span class="sxs-lookup"><span data-stu-id="408cc-116">In the following example, a configuration-specific environment variable is set in *web.Release.config*:</span></span>

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

<span data-ttu-id="408cc-117">Transformace se použije, když je konfigurace nastavená na *release*:</span><span class="sxs-lookup"><span data-stu-id="408cc-117">The transform is applied when the configuration is set to *Release*:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="408cc-118">Vlastnost MSBuild pro konfiguraci je `$(Configuration)`.</span><span class="sxs-lookup"><span data-stu-id="408cc-118">The MSBuild property for the configuration is `$(Configuration)`.</span></span>

## <a name="profile"></a><span data-ttu-id="408cc-119">Profil</span><span class="sxs-lookup"><span data-stu-id="408cc-119">Profile</span></span>

<span data-ttu-id="408cc-120">Transformace profilů se spustí za sekundu po transformaci [konfigurace sestavení](#build-configuration) .</span><span class="sxs-lookup"><span data-stu-id="408cc-120">Profile transformations are run second, after [Build configuration](#build-configuration) transforms.</span></span>

<span data-ttu-id="408cc-121">Zahrnout *Web. { PROFIL}. config* soubor pro každou konfiguraci profilu vyžadující transformaci *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="408cc-121">Include a *web.{PROFILE}.config* file for each profile configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="408cc-122">V následujícím příkladu je proměnná prostředí pro konkrétní profil nastavena na *webu. FolderProfile. config* pro profil publikování složky:</span><span class="sxs-lookup"><span data-stu-id="408cc-122">In the following example, a profile-specific environment variable is set in *web.FolderProfile.config* for a folder publish profile:</span></span>

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

<span data-ttu-id="408cc-123">Transformace se použije, když je profil *FolderProfile*:</span><span class="sxs-lookup"><span data-stu-id="408cc-123">The transform is applied when the profile is *FolderProfile*:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

<span data-ttu-id="408cc-124">Vlastnost MSBuild pro název profilu je `$(PublishProfile)`.</span><span class="sxs-lookup"><span data-stu-id="408cc-124">The MSBuild property for the profile name is `$(PublishProfile)`.</span></span>

<span data-ttu-id="408cc-125">Pokud není předán žádný profil, výchozí název profilu je **systém souborů** a *Web. FileSystem. config* se použije, pokud se soubor nachází v kořenovém adresáři obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="408cc-125">If no profile is passed, the default profile name is **FileSystem** and *web.FileSystem.config* is applied if the file is present in the app's content root.</span></span>

## <a name="environment"></a><span data-ttu-id="408cc-126">Prostředí</span><span class="sxs-lookup"><span data-stu-id="408cc-126">Environment</span></span>

<span data-ttu-id="408cc-127">Transformace prostředí se spouští třetí, po [konfiguraci sestavení](#build-configuration) a transformacích [profilů](#profile) .</span><span class="sxs-lookup"><span data-stu-id="408cc-127">Environment transformations are run third, after [Build configuration](#build-configuration) and [Profile](#profile) transforms.</span></span>

<span data-ttu-id="408cc-128">Zahrnout *Web. { ENVIRONMENT}. config* soubor pro každé [prostředí](xref:fundamentals/environments) vyžaduje transformaci *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="408cc-128">Include a *web.{ENVIRONMENT}.config* file for each [environment](xref:fundamentals/environments) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="408cc-129">V následujícím příkladu je proměnná prostředí specifická pro prostředí nastavena na *webu. Provozní. config* v produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="408cc-129">In the following example, a environment-specific environment variable is set in *web.Production.config* for the Production environment:</span></span>

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

<span data-ttu-id="408cc-130">Transformace se použije, když je prostředí v *produkčním*prostředí:</span><span class="sxs-lookup"><span data-stu-id="408cc-130">The transform is applied when the environment is *Production*:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:EnvironmentName=Production
```

<span data-ttu-id="408cc-131">Vlastnost MSBuild pro prostředí je `$(EnvironmentName)`.</span><span class="sxs-lookup"><span data-stu-id="408cc-131">The MSBuild property for the environment is `$(EnvironmentName)`.</span></span>

<span data-ttu-id="408cc-132">Při publikování ze sady Visual Studio a používání profilu publikování, viz <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.</span><span class="sxs-lookup"><span data-stu-id="408cc-132">When publishing from Visual Studio and using a publish profile, see <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.</span></span>

<span data-ttu-id="408cc-133">Proměnná prostředí `ASPNETCORE_ENVIRONMENT` je automaticky přidána do souboru *Web. config* , pokud je zadán název prostředí.</span><span class="sxs-lookup"><span data-stu-id="408cc-133">The `ASPNETCORE_ENVIRONMENT` environment variable is automatically added to the *web.config* file when the environment name is specified.</span></span>

## <a name="custom"></a><span data-ttu-id="408cc-134">Vlastní</span><span class="sxs-lookup"><span data-stu-id="408cc-134">Custom</span></span>

<span data-ttu-id="408cc-135">Vlastní transformace jsou spouštěny jako poslední, po transformaci [konfigurace sestavení](#build-configuration), [profilu](#profile)a [prostředí](#environment) .</span><span class="sxs-lookup"><span data-stu-id="408cc-135">Custom transformations are run last, after [Build configuration](#build-configuration), [Profile](#profile), and [Environment](#environment) transforms.</span></span>

<span data-ttu-id="408cc-136">Zahrňte *{CUSTOM_NAME}. transformační* soubor pro každou vlastní konfiguraci, která vyžaduje transformaci *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="408cc-136">Include a *{CUSTOM_NAME}.transform* file for each custom configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="408cc-137">V následujícím příkladu je proměnná prostředí vlastní transformace nastavena v *Custom. transformaci*:</span><span class="sxs-lookup"><span data-stu-id="408cc-137">In the following example, a custom transform environment variable is set in *custom.transform*:</span></span>

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

<span data-ttu-id="408cc-138">Transformace je použita, pokud je vlastnost `CustomTransformFileName` předána příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) :</span><span class="sxs-lookup"><span data-stu-id="408cc-138">The transform is applied when the `CustomTransformFileName` property is passed to the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

<span data-ttu-id="408cc-139">Vlastnost MSBuild pro název profilu je `$(CustomTransformFileName)`.</span><span class="sxs-lookup"><span data-stu-id="408cc-139">The MSBuild property for the profile name is `$(CustomTransformFileName)`.</span></span>

## <a name="prevent-webconfig-transformation"></a><span data-ttu-id="408cc-140">Zabránit transformaci Web. config</span><span class="sxs-lookup"><span data-stu-id="408cc-140">Prevent web.config transformation</span></span>

<span data-ttu-id="408cc-141">Chcete-li zabránit transformaci souboru *Web. config* , nastavte vlastnost MSBuild `$(IsWebConfigTransformDisabled)`:</span><span class="sxs-lookup"><span data-stu-id="408cc-141">To prevent transformations of the *web.config* file, set the MSBuild property `$(IsWebConfigTransformDisabled)`:</span></span>

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a><span data-ttu-id="408cc-142">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="408cc-142">Additional resources</span></span>

* [<span data-ttu-id="408cc-143">Syntaxe transformace Web. config pro nasazení projektu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="408cc-143">Web.config Transformation Syntax for Web Application Project Deployment</span></span>](https://go.microsoft.com/fwlink/?LinkId=301874)
* <span data-ttu-id="408cc-144">[Syntaxe transformace Web. config pro nasazení webového projektu pomocí sady Visual Studio](https://docs.microsoft.com/previous-versions/aspnet/dd465326(v=vs.110))</span><span class="sxs-lookup"><span data-stu-id="408cc-144">[Web.config Transformation Syntax for Web Project Deployment Using Visual Studio](https://docs.microsoft.com/previous-versions/aspnet/dd465326(v=vs.110))</span></span>
