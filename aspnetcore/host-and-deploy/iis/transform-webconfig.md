---
title: Transformace souboru web.config
author: guardrex
description: Naučte se, jak transformovat soubor Web. config při publikování aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2019
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: 32e66007d527f7f7b7cfd88d3bebc9b808251941
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081450"
---
# <a name="transform-webconfig"></a>Transformace souboru web.config

Od [Vijay Ramakrishnan](https://github.com/vijayrkn) a [Luke Latham](https://github.com/guardrex)

Transformace do souboru *Web. config* lze použít automaticky při publikování aplikace na základě:

* [Konfigurace sestavení](#build-configuration)
* [Profilu](#profile)
* [Prostředí](#environment)
* [Vlastní](#custom)

Tyto transformace se vyskytují pro některé z následujících scénářů generování *webu. config* :

* Vygenerováno automaticky `Microsoft.NET.Sdk.Web` sadou SDK.
* Poskytl vývojář v kořenu obsahu aplikace.

## <a name="build-configuration"></a>Konfigurace sestavení

Transformace konfigurace sestavení se spouštějí jako první.

Zahrnout *Web. { KONFIGURAČNÍ soubor. config* pro každou [konfiguraci sestavení (ladění | Release)](/dotnet/core/tools/dotnet-publish#options) , která vyžaduje transformaci *Web. config* .

V následujícím příkladu je proměnná prostředí specifická pro konfiguraci nastavena na *webu. Release. config*:

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

Transformace se použije, když je konfigurace nastavená na *release*:

```dotnetcli
dotnet publish --configuration Release
```

Vlastnost MSBuild pro konfiguraci je `$(Configuration)`.

## <a name="profile"></a>Profil

Transformace profilů se spustí za sekundu po transformaci [konfigurace sestavení](#build-configuration) .

Zahrnout *Web. { PROFIL}. config* soubor pro každou konfiguraci profilu vyžadující transformaci *Web. config* .

V následujícím příkladu je proměnná prostředí pro konkrétní profil nastavena na *webu. FolderProfile. config* pro profil publikování složky:

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

Vlastnost MSBuild pro název profilu je `$(PublishProfile)`.

Pokud není předán žádný profil, výchozí název profilu je **systém souborů** a *Web. FileSystem. config* se použije, pokud se soubor nachází v kořenovém adresáři obsahu aplikace.

## <a name="environment"></a>Prostředí

Transformace prostředí se spouští třetí, po [konfiguraci sestavení](#build-configuration) a transformacích [profilů](#profile) .

Zahrnout *Web. { ENVIRONMENT}. config* soubor pro každé [prostředí](xref:fundamentals/environments) vyžaduje transformaci *Web. config* .

V následujícím příkladu je proměnná prostředí specifická pro prostředí nastavena na *webu. Provozní. config* v produkčním prostředí:

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

Transformace se použije, když je prostředí v *produkčním*prostředí:

```dotnetcli
dotnet publish --configuration Release /p:EnvironmentName=Production
```

Vlastnost MSBuild pro prostředí je `$(EnvironmentName)`.

Pokud publikujete ze sady Visual Studio a použijete profil publikování <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>, přečtěte si téma.

Proměnná prostředí je automaticky přidána do souboru *Web. config* , pokud je zadán název prostředí. `ASPNETCORE_ENVIRONMENT`

## <a name="custom"></a>Vlastní

Vlastní transformace jsou spouštěny jako poslední, po transformaci [konfigurace sestavení](#build-configuration), [profilu](#profile)a [prostředí](#environment) .

Zahrňte *{CUSTOM_NAME}. transformační* soubor pro každou vlastní konfiguraci, která vyžaduje transformaci *Web. config* .

V následujícím příkladu je proměnná prostředí vlastní transformace nastavena v *Custom. transformaci*:

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

Transformace je použita, pokud `CustomTransformFileName` je vlastnost předána příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) :

```dotnetcli
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

Vlastnost MSBuild pro název profilu je `$(CustomTransformFileName)`.

## <a name="prevent-webconfig-transformation"></a>Zabránit transformaci Web. config

Chcete-li zabránit transformaci souboru *Web. config* , nastavte vlastnost `$(IsWebConfigTransformDisabled)`MSBuild:

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a>Další zdroje

* [Syntaxe transformace Web. config pro nasazení projektu webové aplikace](https://go.microsoft.com/fwlink/?LinkId=301874)
* [Syntaxe transformace Web. config pro nasazení webového projektu pomocí sady Visual Studio](https://docs.microsoft.com/previous-versions/aspnet/dd465326(v=vs.110))
