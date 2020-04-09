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
# <a name="transform-webconfig"></a>Transformace souboru web.config

Podle [Vijay Ramakrishnan](https://github.com/vijayrkn)

Transformace do souboru *web.config* lze použít automaticky při publikování aplikace na základě:

* [Konfigurace sestavení](#build-configuration)
* [Profil](#profile)
* [Prostředí](#environment)
* [Vlastní](#custom)

K těmto transformacídochází v některém z následujících scénářů generování *web.config:*

* Generováno automaticky `Microsoft.NET.Sdk.Web` sadou SDK.
* Poskytované vývojářem v [kořenovém adresáři obsahu](xref:fundamentals/index#content-root) aplikace.

## <a name="build-configuration"></a>Konfigurace sestavení

Transformace konfigurace sestavení jsou spuštěny jako první.

Zahrnout *web.{ SOUBOR CONFIGURATION}.config* pro každou [konfiguraci sestavení (Ladění| Release)](/dotnet/core/tools/dotnet-publish#options) vyžadující transformaci *web.config.*

V následujícím příkladu je proměnná prostředí specifická pro konfiguraci nastavena na *webu. Release.config*:

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

Transformace se použije, když je konfigurace nastavena na *release*:

```dotnetcli
dotnet publish --configuration Release
```

Vlastnost MSBuild pro konfiguraci je `$(Configuration)`.

## <a name="profile"></a>Profil

Transformace profilu jsou spuštěny druhé, po [transformace konfigurace sestavení.](#build-configuration)

Zahrnout *web.{ PROFILE}.config* pro každou konfiguraci profilu, která vyžaduje transformaci *web.config.*

V následujícím příkladu je proměnná prostředí specifická pro profil nastavena na *webu. FolderProfile.config* pro profil publikování složky:

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

Transformace se použije, když je profil *FolderProfile*:

```dotnetcli
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

Vlastnost MSBuild pro název `$(PublishProfile)`profilu je .

Pokud není předán žádný profil, výchozí název profilu je **FileSystem** a *web. Soubor FileSystem.config* se použije, pokud je soubor přítomen v kořenovém adresáři obsahu aplikace.

## <a name="environment"></a>Prostředí

Transformace prostředí jsou spuštěny třetí, po [sestavení konfigurace](#build-configuration) a [profil](#profile) transformace.

Zahrnout *web.{ ENVIRONMENT}.config* pro každé [prostředí,](xref:fundamentals/environments) které vyžaduje transformaci *web.config.*

V následujícím příkladu je proměnná prostředí specifická pro prostředí nastavena na *webu. Production.config* pro produkční prostředí:

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

Transformace se použije, když je prostředí *Výroba*:

```dotnetcli
dotnet publish --configuration Release /p:EnvironmentName=Production
```

Vlastnost MSBuild pro prostředí `$(EnvironmentName)`je .

Při publikování z Visual Studia a <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>pomocí profilu publikování, najdete v tématu .

Proměnná `ASPNETCORE_ENVIRONMENT` prostředí je automaticky přidána do souboru *web.config,* když je zadán název prostředí.

## <a name="custom"></a>Vlastní

Vlastní transformace jsou spuštěny jako poslední, po [sestavení konfigurace](#build-configuration), [profil](#profile)a [prostředí](#environment) transformace.

Pro každou vlastní konfiguraci vyžadující transformaci *web.config* zahrňte soubor *{CUSTOM_NAME}.transform.*

V následujícím příkladu je proměnná prostředí vlastní transformace nastavena v *souboru custom.transform*:

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

Transformace se použije, `CustomTransformFileName` když je vlastnost předána příkazu [dotnet publish:](/dotnet/core/tools/dotnet-publish)

```dotnetcli
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

Vlastnost MSBuild pro název `$(CustomTransformFileName)`profilu je .

## <a name="prevent-webconfig-transformation"></a>Zabránit transformaci web.config

Chcete-li zabránit transformací souboru *web.config,* nastavte vlastnost `$(IsWebConfigTransformDisabled)`MSBuild :

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a>Další zdroje

* [Syntaxe transformace web.config pro nasazení projektu webové aplikace](/previous-versions/dd465326(v=vs.100))
* [Syntaxe transformace Web.config pro nasazení webového projektu pomocí sady Visual Studio](/previous-versions/aspnet/dd465326(v=vs.110))
