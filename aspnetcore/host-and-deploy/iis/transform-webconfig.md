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
# <a name="transform-webconfig"></a>Transformace souboru web.config

Podle [Vijay Ramakrishnan](https://github.com/vijayrkn) a [Luke Latham](https://github.com/guardrex)

Transformace tak, *web.config* souboru můžete použít automaticky, když je při publikování na základě:

* [Konfigurace sestavení](#build-configuration)
* [Profile](#profile)
* [Prostředí](#environment)
* [Vlastní](#custom)

Tyto transformace dojít z některého z následujících *web.config* generování scénáře:

* Automaticky generované `Microsoft.NET.Sdk.Web` SDK.
* Poskytuje pro vývojáře v obsahu kořenovém adresáři aplikace.

## <a name="build-configuration"></a>Konfigurace sestavení

Transformace konfigurace sestavení se nejprve spustí.

Zahrnout *webové. { KONFIGURACE} .config* souboru pro každý [konfiguraci sestavení (ladění | Vydaná verze)](/dotnet/core/tools/dotnet-publish#options) vyžadování *web.config* transformace.

V následujícím příkladu je nastavena proměnná prostředí určených pro konfigurace *web. Release.config*:

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

Při konfiguraci nastavená na je použita transformace *vydání*:

```console
dotnet publish --configuration Release
```

Vlastnost MSBuild pro danou konfiguraci je `$(Configuration)`.

## <a name="profile"></a>Profil

Transformace profilu spuštění druhého po [konfiguraci sestavení](#build-configuration) transformace.

Zahrnout *webové. { PROFIL} .config* souboru pro každý profil konfigurace vyžadování *web.config* transformace.

V následujícím příkladu je nastavena proměnná prostředí pro konkrétní profil *web. FolderProfile.config* profil publikování pro složku:

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

Pokud je profil, který je použita transformace *FolderProfile*:

```console
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

Vlastnost MSBuild pro název profilu je `$(PublishProfile)`.

Pokud není předán žádný profil, výchozí název profilu je **systému souborů** a *web. FileSystem.config* se použije, pokud se soubor nachází v kořenovém adresáři obsahu aplikace.

## <a name="environment"></a>Prostředí

Transformace prostředí jsou spouštěny třetí po [konfiguraci sestavení](#build-configuration) a [profilu](#profile) transformace.

Zahrnout *webové. { PROSTŘEDÍ} .config* souboru pro každý [prostředí](xref:fundamentals/environments) vyžadování *web.config* transformace.

V následujícím příkladu je nastavena proměnná prostředí pro konkrétní prostředí *web. Production.config* pro produkční prostředí:

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

Pokud je prostředí, je použita transformace *produkční*:

```console
dotnet publish --configuration Release /p:EnvironmentName=Production
```

Vlastnost MSBuild pro prostředí je `$(EnvironmentName)`.

Při publikování ze sady Visual Studio a používání profilu publikování, najdete v článku <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.

`ASPNETCORE_ENVIRONMENT` Proměnné prostředí se automaticky přidá do *web.config* souboru, když je zadaný název prostředí.

## <a name="custom"></a>Vlastní

Vlastní transformace jsou spuštěny po poslední [konfiguraci sestavení](#build-configuration), [profilu](#profile), a [prostředí](#environment) transformace.

Zahrnout *{CUSTOM_NAME} .transform* souboru pro každý vlastní konfigurace vyžadování *web.config* transformace.

V následujícím příkladu je nastavena proměnná prostředí vlastní transformace *custom.transform*:

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

Je použita transformace při `CustomTransformFileName` vlastnost předána [dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkaz:

```console
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

Vlastnost MSBuild pro název profilu je `$(CustomTransformFileName)`.

## <a name="prevent-webconfig-transformation"></a>Zabránit transformace web.config

Aby se zabránilo transformace *web.config* souboru, nastavte vlastnost MSBuild `$(IsWebConfigTransformDisabled)`:

```console
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a>Další zdroje

* [Syntaxe transformace souboru Web.config pro nasazení projektu webové aplikace](http://go.microsoft.com/fwlink/?LinkId=301874)
* [Syntaxe transformace souboru Web.config pro projekt nasazení webu pomocí sady Visual Studio](https://docs.microsoft.com/previous-versions/aspnet/dd465326(v=vs.110))
