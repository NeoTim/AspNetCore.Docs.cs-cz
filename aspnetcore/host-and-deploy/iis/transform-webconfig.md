---
title: Transformace souboru web.config
author: rick-anderson
description: Naučte se, jak transformovat soubor web.config při publikování aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: a2f26f32d2a282189b391aa9bb8c4637723dc60a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634628"
---
# <a name="transform-webconfig"></a><span data-ttu-id="0f32a-103">Transformace souboru web.config</span><span class="sxs-lookup"><span data-stu-id="0f32a-103">Transform web.config</span></span>

<span data-ttu-id="0f32a-104">Od [Vijay Ramakrishnan](https://github.com/vijayrkn)</span><span class="sxs-lookup"><span data-stu-id="0f32a-104">By [Vijay Ramakrishnan](https://github.com/vijayrkn)</span></span>

<span data-ttu-id="0f32a-105">Transformace na soubor *web.config* lze použít automaticky při publikování aplikace na základě:</span><span class="sxs-lookup"><span data-stu-id="0f32a-105">Transformations to the *web.config* file can be applied automatically when an app is published based on:</span></span>

* [<span data-ttu-id="0f32a-106">Konfigurace sestavení</span><span class="sxs-lookup"><span data-stu-id="0f32a-106">Build configuration</span></span>](#build-configuration)
* [<span data-ttu-id="0f32a-107">Profil</span><span class="sxs-lookup"><span data-stu-id="0f32a-107">Profile</span></span>](#profile)
* [<span data-ttu-id="0f32a-108">Prostředí</span><span class="sxs-lookup"><span data-stu-id="0f32a-108">Environment</span></span>](#environment)
* [<span data-ttu-id="0f32a-109">Vlastní</span><span class="sxs-lookup"><span data-stu-id="0f32a-109">Custom</span></span>](#custom)

<span data-ttu-id="0f32a-110">K těmto transformacím dochází pro některé z následujících scénářů *web.config* generace:</span><span class="sxs-lookup"><span data-stu-id="0f32a-110">These transformations occur for either of the following *web.config* generation scenarios:</span></span>

* <span data-ttu-id="0f32a-111">Vygenerováno automaticky sadou `Microsoft.NET.Sdk.Web` SDK.</span><span class="sxs-lookup"><span data-stu-id="0f32a-111">Generated automatically by the `Microsoft.NET.Sdk.Web` SDK.</span></span>
* <span data-ttu-id="0f32a-112">Poskytl vývojář v [kořenu obsahu](xref:fundamentals/index#content-root) aplikace.</span><span class="sxs-lookup"><span data-stu-id="0f32a-112">Provided by the developer in the [content root](xref:fundamentals/index#content-root) of the app.</span></span>

## <a name="build-configuration"></a><span data-ttu-id="0f32a-113">Konfigurace sestavení</span><span class="sxs-lookup"><span data-stu-id="0f32a-113">Build configuration</span></span>

<span data-ttu-id="0f32a-114">Transformace konfigurace sestavení se spouštějí jako první.</span><span class="sxs-lookup"><span data-stu-id="0f32a-114">Build configuration transforms are run first.</span></span>

<span data-ttu-id="0f32a-115">Zahrnout *Web. { KONFIGURAČNÍ soubor. config* pro každou [konfiguraci sestavení (ladění | Release)](/dotnet/core/tools/dotnet-publish#options) , která vyžaduje transformaci *web.config* .</span><span class="sxs-lookup"><span data-stu-id="0f32a-115">Include a *web.{CONFIGURATION}.config* file for each [build configuration (Debug|Release)](/dotnet/core/tools/dotnet-publish#options) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="0f32a-116">V následujícím příkladu je proměnná prostředí specifická pro konfiguraci nastavena v *web.Release.config*:</span><span class="sxs-lookup"><span data-stu-id="0f32a-116">In the following example, a configuration-specific environment variable is set in *web.Release.config*:</span></span>

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

<span data-ttu-id="0f32a-117">Transformace se použije, když je konfigurace nastavená na *release*:</span><span class="sxs-lookup"><span data-stu-id="0f32a-117">The transform is applied when the configuration is set to *Release*:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="0f32a-118">Vlastnost MSBuild pro konfiguraci je `$(Configuration)` .</span><span class="sxs-lookup"><span data-stu-id="0f32a-118">The MSBuild property for the configuration is `$(Configuration)`.</span></span>

## <a name="profile"></a><span data-ttu-id="0f32a-119">Profil</span><span class="sxs-lookup"><span data-stu-id="0f32a-119">Profile</span></span>

<span data-ttu-id="0f32a-120">Transformace profilů se spustí za sekundu po transformaci [konfigurace sestavení](#build-configuration) .</span><span class="sxs-lookup"><span data-stu-id="0f32a-120">Profile transformations are run second, after [Build configuration](#build-configuration) transforms.</span></span>

<span data-ttu-id="0f32a-121">Zahrnout *Web. { Soubor PROFILe}. config* pro každou konfiguraci profilu vyžaduje transformaci *web.config* .</span><span class="sxs-lookup"><span data-stu-id="0f32a-121">Include a *web.{PROFILE}.config* file for each profile configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="0f32a-122">V následujícím příkladu je proměnná prostředí specifická pro daný profil nastavena v *web.FolderProfile.config* pro profil publikování složky:</span><span class="sxs-lookup"><span data-stu-id="0f32a-122">In the following example, a profile-specific environment variable is set in *web.FolderProfile.config* for a folder publish profile:</span></span>

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

<span data-ttu-id="0f32a-123">Transformace se použije, když je profil *FolderProfile*:</span><span class="sxs-lookup"><span data-stu-id="0f32a-123">The transform is applied when the profile is *FolderProfile*:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

<span data-ttu-id="0f32a-124">Vlastnost MSBuild pro název profilu je `$(PublishProfile)` .</span><span class="sxs-lookup"><span data-stu-id="0f32a-124">The MSBuild property for the profile name is `$(PublishProfile)`.</span></span>

<span data-ttu-id="0f32a-125">Pokud se nepředává žádný profil, výchozí název profilu je **systém souborů** a *web.FileSystem.config* se použije, pokud se soubor nachází v kořenu obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="0f32a-125">If no profile is passed, the default profile name is **FileSystem** and *web.FileSystem.config* is applied if the file is present in the app's content root.</span></span>

## <a name="environment"></a><span data-ttu-id="0f32a-126">Prostředí</span><span class="sxs-lookup"><span data-stu-id="0f32a-126">Environment</span></span>

<span data-ttu-id="0f32a-127">Transformace prostředí se spouští třetí, po [konfiguraci sestavení](#build-configuration) a transformacích [profilů](#profile) .</span><span class="sxs-lookup"><span data-stu-id="0f32a-127">Environment transformations are run third, after [Build configuration](#build-configuration) and [Profile](#profile) transforms.</span></span>

<span data-ttu-id="0f32a-128">Zahrnout *Web. { ENVIRONMENT}. config* soubor pro každé [prostředí](xref:fundamentals/environments) vyžaduje transformaci *web.config* .</span><span class="sxs-lookup"><span data-stu-id="0f32a-128">Include a *web.{ENVIRONMENT}.config* file for each [environment](xref:fundamentals/environments) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="0f32a-129">V následujícím příkladu je proměnná prostředí specifická pro prostředí nastavena v *web.Production.config* pro produkční prostředí:</span><span class="sxs-lookup"><span data-stu-id="0f32a-129">In the following example, a environment-specific environment variable is set in *web.Production.config* for the Production environment:</span></span>

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

<span data-ttu-id="0f32a-130">Transformace se použije, když je prostředí v *produkčním*prostředí:</span><span class="sxs-lookup"><span data-stu-id="0f32a-130">The transform is applied when the environment is *Production*:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:EnvironmentName=Production
```

<span data-ttu-id="0f32a-131">Vlastnost MSBuild pro prostředí je `$(EnvironmentName)` .</span><span class="sxs-lookup"><span data-stu-id="0f32a-131">The MSBuild property for the environment is `$(EnvironmentName)`.</span></span>

<span data-ttu-id="0f32a-132">Pokud publikujete ze sady Visual Studio a použijete profil publikování, přečtěte si téma <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment> .</span><span class="sxs-lookup"><span data-stu-id="0f32a-132">When publishing from Visual Studio and using a publish profile, see <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.</span></span>

<span data-ttu-id="0f32a-133">`ASPNETCORE_ENVIRONMENT`Proměnná prostředí je automaticky přidána do souboru *web.config* , pokud je zadán název prostředí.</span><span class="sxs-lookup"><span data-stu-id="0f32a-133">The `ASPNETCORE_ENVIRONMENT` environment variable is automatically added to the *web.config* file when the environment name is specified.</span></span>

## <a name="custom"></a><span data-ttu-id="0f32a-134">Vlastní</span><span class="sxs-lookup"><span data-stu-id="0f32a-134">Custom</span></span>

<span data-ttu-id="0f32a-135">Vlastní transformace jsou spouštěny jako poslední, po transformaci [konfigurace sestavení](#build-configuration), [profilu](#profile)a [prostředí](#environment) .</span><span class="sxs-lookup"><span data-stu-id="0f32a-135">Custom transformations are run last, after [Build configuration](#build-configuration), [Profile](#profile), and [Environment](#environment) transforms.</span></span>

<span data-ttu-id="0f32a-136">Zahrňte *{CUSTOM_NAME}. transformační* soubor pro každou vlastní konfiguraci, která vyžaduje transformaci *web.config* .</span><span class="sxs-lookup"><span data-stu-id="0f32a-136">Include a *{CUSTOM_NAME}.transform* file for each custom configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="0f32a-137">V následujícím příkladu je proměnná prostředí vlastní transformace nastavena v *Custom. transformaci*:</span><span class="sxs-lookup"><span data-stu-id="0f32a-137">In the following example, a custom transform environment variable is set in *custom.transform*:</span></span>

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

<span data-ttu-id="0f32a-138">Transformace je použita, pokud `CustomTransformFileName` je vlastnost předána příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) :</span><span class="sxs-lookup"><span data-stu-id="0f32a-138">The transform is applied when the `CustomTransformFileName` property is passed to the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

<span data-ttu-id="0f32a-139">Vlastnost MSBuild pro název profilu je `$(CustomTransformFileName)` .</span><span class="sxs-lookup"><span data-stu-id="0f32a-139">The MSBuild property for the profile name is `$(CustomTransformFileName)`.</span></span>

## <a name="prevent-webconfig-transformation"></a><span data-ttu-id="0f32a-140">Zabránit transformaci web.config</span><span class="sxs-lookup"><span data-stu-id="0f32a-140">Prevent web.config transformation</span></span>

<span data-ttu-id="0f32a-141">Chcete-li zabránit transformaci souboru *web.config* , nastavte vlastnost MSBuild `$(IsWebConfigTransformDisabled)` :</span><span class="sxs-lookup"><span data-stu-id="0f32a-141">To prevent transformations of the *web.config* file, set the MSBuild property `$(IsWebConfigTransformDisabled)`:</span></span>

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a><span data-ttu-id="0f32a-142">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="0f32a-142">Additional resources</span></span>

* <span data-ttu-id="0f32a-143">[Web.config syntaxe transformace pro nasazení projektu webové aplikace](/previous-versions/dd465326(v=vs.100))</span><span class="sxs-lookup"><span data-stu-id="0f32a-143">[Web.config Transformation Syntax for Web Application Project Deployment](/previous-versions/dd465326(v=vs.100))</span></span>
* <span data-ttu-id="0f32a-144">[Web.config syntaxe transformace pro nasazení webového projektu pomocí sady Visual Studio](/previous-versions/aspnet/dd465326(v=vs.110))</span><span class="sxs-lookup"><span data-stu-id="0f32a-144">[Web.config Transformation Syntax for Web Project Deployment Using Visual Studio](/previous-versions/aspnet/dd465326(v=vs.110))</span></span>
