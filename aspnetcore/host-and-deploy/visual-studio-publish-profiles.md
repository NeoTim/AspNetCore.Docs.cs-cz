---
title: Publikační profily sady Visual Studio (. pubxml) pro nasazení aplikace ASP.NET Core
author: rick-anderson
description: Naučte se vytvářet profily publikování v aplikaci Visual Studio a používat je ke správě ASP.NET Core nasazení aplikací do různých cílů.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: 274dd2cd528d3766aa07f69aac3470a131c79ffe
ms.sourcegitcommit: 67116718dc33a7a01696d41af38590fdbb58e014
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799354"
---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a>Publikační profily sady Visual Studio (. pubxml) pro nasazení aplikace ASP.NET Core

Od [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) a [Rick Anderson](https://twitter.com/RickAndMSFT)

Tento dokument se zaměřuje na použití sady Visual Studio 2019 nebo novější k vytváření a používání profilů publikování. Profily publikování vytvořené pomocí sady Visual Studio lze použít s nástroji MSBuild a sadou Visual Studio. Pokyny k publikování do Azure najdete v tématu <xref:tutorials/publish-to-azure-webapp-using-vs>.

Příkaz `dotnet new mvc` vytvoří soubor projektu obsahující následující [prvek \<Project >](/visualstudio/msbuild/project-element-msbuild)na kořenové úrovni:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

Předchozí atribut `Sdk` elementu `<Project>` importuje [vlastnosti](/visualstudio/msbuild/msbuild-properties) a [cíle](/visualstudio/msbuild/msbuild-targets) nástroje MSBuild z *$ (MSBuildSDKsPath) \Microsoft.NET.SDK.Web\Sdk\Sdk.props* a *$ (MSBuildSDKsPath) \Microsoft.NET.SDK.Web\Sdk\ Sada SDK. targets*, v uvedeném pořadí. Výchozím umístěním pro `$(MSBuildSDKsPath)` (se sadou Visual Studio 2019 Enterprise) je složka *% ProgramFiles (x86)% \ Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* .

`Microsoft.NET.Sdk.Web` (webová sada SDK) závisí na jiných sadách SDK, včetně `Microsoft.NET.Sdk` (.NET Core SDK) a `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)). Naimportují se vlastnosti a cíle nástroje MSBuild přidružené ke každé závislé sadě SDK. Cíle publikování importují odpovídající sadu cílů na základě používané metody publikování.

Když nástroj MSBuild nebo Visual Studio načte projekt, dojde k následujícím akcím na nejvyšší úrovni:

* Sestavit projekt
* Soubory COMPUTE k publikování
* Publikovat soubory do cílového umístění

## <a name="compute-project-items"></a>Položky výpočetního projektu

Po načtení projektu jsou vypočítány [položky projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) (soubory). Typ položky určuje, jak je soubor zpracován. Ve výchozím nastavení jsou soubory *. cs* obsaženy v seznamu položek `Compile`. Soubory v seznamu položek `Compile` jsou kompilovány.

Seznam položek `Content` obsahuje soubory, které jsou publikovány kromě výstupů sestavení. Ve výchozím nastavení jsou soubory, které odpovídají vzorům `wwwroot\**`, `**\*.config` a `**\*.json`, zahrnuty v seznamu položek `Content`. Například [vzor expanze](https://gruntjs.com/configuring-tasks#globbing-patterns) `wwwroot\**` odpovídá všem souborům ve složce *wwwroot* a jejích podsložkách.

::: moniker range=">= aspnetcore-3.0"

Sada Web SDK importuje [sadu Razor SDK](xref:razor-pages/sdk). V důsledku toho jsou soubory, které odpovídají vzorům `**\*.cshtml` a `**\*.razor`, zahrnuty také v seznamu položek `Content`.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Sada Web SDK importuje [sadu Razor SDK](xref:razor-pages/sdk). V důsledku toho jsou soubory, které odpovídají vzoru `**\*.cshtml`, zahrnuty také v seznamu položek `Content`.

::: moniker-end

Chcete-li explicitně přidat soubor do seznamu publikování, přidejte ho přímo do souboru *. csproj* , jak je znázorněno v části [zahrnuté soubory](#include-files) .

Při výběru tlačítka **publikovat** v aplikaci Visual Studio nebo při publikování z příkazového řádku:

* Jsou vypočítány vlastnosti nebo položky (soubory, které jsou nutné k sestavení).
* **Pouze Visual Studio**: obnoveny balíčky NuGet. (Obnovení musí být uživatelem v rozhraní příkazového řádku explicitní.)
* Sestavení projektu.
* Položky publikování jsou vypočítány (soubory, které jsou nutné k publikování).
* Projekt je publikován (vypočítané soubory jsou zkopírovány do umístění pro publikování).

Pokud ASP.NET Core projekt odkazuje `Microsoft.NET.Sdk.Web` v souboru projektu, je soubor *App_offline. htm* umístěn v kořenu adresáře webové aplikace. Když je soubor přítomen, modul ASP.NET Core aplikaci řádně ukončí a během nasazování zachová soubor *App_offline. htm* . Další informace najdete v referenčních informacích k [konfiguraci modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).

## <a name="basic-command-line-publishing"></a>Základní publikování z příkazového řádku

Publikování z příkazového řádku funguje na všech platformách podporovaných .NET Core a nevyžaduje sadu Visual Studio. V následujících příkladech se příkaz [dotnet publish](/dotnet/core/tools/dotnet-publish) .NET Core CLI spouští z adresáře projektu (který obsahuje soubor *. csproj* ). Pokud složka projektu není aktuálním pracovním adresářem, explicitně předejte cestu k souboru projektu. Příklad:

```dotnetcli
dotnet publish C:\Webs\Web1
```

Spuštěním následujících příkazů vytvořte a publikujte webovou aplikaci:

```dotnetcli
dotnet new mvc
dotnet publish
```

Příkaz `dotnet publish` vytvoří variaci následujícího výstupu:

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

Výchozí formát složky pro publikování je *bin\Debug\\{Target Framework MONIKER} \publish\\* . Například *bin\Debug\netcoreapp2.2\publish\\* .

Následující příkaz určuje `Release` sestavení a adresář pro publikování:

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

Příkaz `dotnet publish` volá nástroj MSBuild, který vyvolá cíl `Publish`. Všechny parametry předané do `dotnet publish` jsou předány do nástroje MSBuild. Parametry `-c` a `-o` se mapují na vlastnosti `Configuration` a `OutputPath` v nástroji MSBuild.

Vlastnosti nástroje MSBuild lze předat pomocí některého z následujících formátů:

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

Například následující příkaz publikuje sestavení `Release` do sdílené síťové složky. Sdílená síťová složka je určena s použitím lomítka ( *//R8/* ) a funguje na všech podporovaných platformách .NET Core.

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

Potvrďte, že publikovaná aplikace pro nasazení není spuštěná. Soubory ve složce pro *publikování* jsou při spuštění aplikace zamčené. Nasazení nelze provést, protože uzamčené soubory nelze zkopírovat.

## <a name="publish-profiles"></a>Profily publikování

V této části se používá Visual Studio 2019 nebo novější k vytvoření profilu publikování. Po vytvoření profilu, publikování ze sady Visual Studio nebo příkazového řádku je k dispozici. Publikování profilů může zjednodušit proces publikování a může existovat libovolný počet profilů.

Vytvořte profil publikování v aplikaci Visual Studio tak, že vyberete jednu z následujících cest:

* V **Průzkumník řešení** klikněte pravým tlačítkem na projekt a vyberte **publikovat**.
* V nabídce **sestavení** vyberte **publikovat {Project Name}** .

Zobrazí se karta **publikovat** stránky možnosti aplikace. Pokud projekt nemá profil publikování, zobrazí se stránka **vybrat cíl publikování** . Budete požádáni o výběr jednoho z následujících cílů publikování:

* Azure App Service
* Azure App Service v systému Linux
* Virtual Machines Azure
* Folder
* IIS, FTP, Nasazení webu (pro libovolný webový server)
* Importovat profil

K určení nejvhodnějšího cíle publikování si přečtěte téma [co jsou možnosti publikování správné](/visualstudio/ide/not-in-toc/web-publish-options).

Když je vybraná možnost cíl publikování **složky** , zadejte cestu ke složce pro uložení publikovaných prostředků. Výchozí cesta ke složce je *bin\\{Project Configuration}\\{Target Framework MONIKER} \publish\\* . Například *bin\Release\netcoreapp2.2\publish\\* . Kliknutím na tlačítko **vytvořit profil** dokončíte.

Po vytvoření profilu publikování se změní obsah karty **publikovat** . Nově vytvořený profil se zobrazí v rozevíracím seznamu. V rozevíracím seznamu vyberte **vytvořit nový profil** a vytvořte další nový profil.

Nástroj pro publikování sady Visual Studio vytvoří soubor. *PublishProfiles/{Profile}. pubxml* MSBuild popisující profil publikování. Soubor *. pubxml* :

* Obsahuje nastavení konfigurace publikování a je využito procesem publikování.
* Lze upravit pro přizpůsobení procesu sestavení a publikování.

Při publikování do cíle Azure obsahuje soubor *. pubxml* váš identifikátor předplatného Azure. U tohoto cílového typu se nedoporučuje přidat tento soubor do správy zdrojových kódů. Při publikování do cíle mimo Azure je bezpečné se vrátit se změnami soubor *. pubxml* .

Citlivé informace (například heslo pro publikování) jsou zašifrované na úrovni jednotlivých uživatelů nebo počítačů. Je uložený v souboru *Properties/PublishProfiles/{Profile}. pubxml. User* . Vzhledem k tomu, že tento soubor může ukládat citlivé informace, neměl by být zkontrolován do správy zdrojového kódu.

Přehled publikování ASP.NET Core webové aplikace najdete v tématu <xref:host-and-deploy/index>. Úlohy a cíle nástroje MSBuild potřebné k publikování ASP.NET Core webové aplikace jsou open source v [úložišti ASPNET/websdk](https://github.com/aspnet/websdk).

V následujících příkazech můžete použít profily pro publikování složek, MSDeploy a [Kudu](https://github.com/projectkudu/kudu/wiki) . Vzhledem k tomu, že MSDeploy chybí podpora pro různé platformy, jsou následující možnosti MSDeploy podporovány pouze v systému Windows.

**Složka (funguje pro různé platformy):**

<!--

NOTE: Add back the following 'dotnet publish' folder publish example after https://github.com/aspnet/websdk/issues/888 is resolved.

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

-->

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

**MSDeploy**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

**Balíček MSDeploy:**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

V předchozích příkladech:

* `dotnet publish` a `dotnet build` podporují rozhraní API Kudu pro publikování do Azure z jakékoli platformy. Publikování Visual studia podporuje rozhraní API Kudu, ale podporuje je WebSDK pro publikování mezi platformami v Azure.
* Nepředávejte `DeployOnBuild` do příkazu `dotnet publish`.

Další informace najdete v tématu [Microsoft. NET. SDK. Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).

Přidejte profil publikování do složky *vlastností projektu/PublishProfiles* s následujícím obsahem:

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

## <a name="folder-publish-example"></a>Příklad publikování složky

Při publikování s profilem s názvem *FolderProfile*použijte některý z následujících příkazů:

<!--

NOTE: Temporarily removed until https://github.com/aspnet/websdk/issues/888 is resolved.

* `dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`

-->

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

Příkaz [dotnet buildu](/dotnet/core/tools/dotnet-build) .NET Core CLI volá `msbuild` pro spuštění procesu sestavení a publikování. Příkazy `dotnet build` a `msbuild` jsou ekvivalentní při předávání do profilu složky. Při volání `msbuild` přímo v systému Windows je použita verze nástroje MSBuild .NET Framework. Volání `dotnet build` v profilu mimo složku:

* Vyvolá `msbuild`, který používá MSDeploy.
* Výsledkem je selhání (i při spuštění ve Windows). Chcete-li publikovat s profilem mimo složku, zavolejte `msbuild` přímo.

V aplikaci Visual Studio se vytvořila následující složka publikovat profil a publikuje se do sdílené síťové složky:

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

V předchozím příkladu:

* Vlastnost `<ExcludeApp_Data>` je přítomna pouze pro splnění požadavku schématu XML. Vlastnost `<ExcludeApp_Data>` nemá žádný vliv na proces publikování, a to ani v případě, že je v kořenovém adresáři projektu složka *App_Data* . Složka *App_Data* neobdrží zvláštní zacházení, protože to funguje v projektech ASP.NET 4. x.

<!--

NOTE: Temporarily removed from 'Using the .NET Core CLI' below until https://github.com/aspnet/websdk/issues/888 is resolved.

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

-->

* Vlastnost `<LastUsedBuildConfiguration>` je nastavena na hodnotu `Release`. Při publikování ze sady Visual Studio je hodnota `<LastUsedBuildConfiguration>` nastavena pomocí hodnoty při spuštění procesu publikování. `<LastUsedBuildConfiguration>` je zvláštní a v importovaném souboru MSBuild by se neměla přepsat. Tato vlastnost se ale dá přepsat z příkazového řádku pomocí jednoho z následujících přístupů.
  * Použití .NET Core CLI:

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * Pomocí nástroje MSBuild:

    ```console
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  Další informace naleznete v tématu [MSBuild: jak nastavit vlastnost konfigurace](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a>Publikování na koncový bod MSDeploy z příkazového řádku

V následujícím příkladu se používá webová aplikace ASP.NET Core vytvořená v aplikaci Visual Studio s názvem *AzureWebApp*. Do sady Visual Studio se přidá profil publikování aplikací Azure. Další informace o tom, jak vytvořit profil, najdete v části [publikování profilů](#publish-profiles) .

K nasazení aplikace pomocí profilu publikování spusťte příkaz `msbuild` z **Developer Command Prompt**sady Visual Studio. Příkazový řádek je k dispozici ve složce sady *Visual Studio* v nabídce **Start** na hlavním panelu systému Windows. Pro snazší přístup můžete přidat příkazový řádek do nabídky **nástroje** v aplikaci Visual Studio. Další informace najdete v tématu [Developer Command Prompt pro Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).

Nástroj MSBuild používá následující syntaxi příkazu:

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* {PATH} &ndash; cesta k souboru projektu aplikace.
* {Profil} &ndash; název publikačního profilu.
* {USERNAME} &ndash; uživatelské jméno MSDeploy. UŽIVATEL {USERNAME} se dá najít v profilu publikování.
* {PASSWORD} &ndash; heslo MSDeploy. Získá {PASSWORD} z *{Profile}. Soubor PublishSettings* Stáhněte si *. Soubor PublishSettings* z některého z těchto:
  * **Průzkumník řešení**: vyberte **Zobrazit** > **Cloud Explorer**. Spojte se s vaším předplatným Azure. Otevřete **App Services**. Klikněte pravým tlačítkem na aplikaci. Vyberte **Stáhnout profil publikování**.
  * Azure Portal: na panelu **přehledu** webové aplikace vyberte **získat profil publikování** .

Následující příklad používá profil publikování s názvem *AzureWebApp-nasazení webu*:

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

Profil publikování lze také použít s příkazem .NET Core CLI [dotnet MSBuild](/dotnet/core/tools/dotnet-msbuild) z příkazového prostředí systému Windows:

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> Příkaz `dotnet msbuild` je příkaz pro různé platformy a může kompilovat ASP.NET Core aplikace v macOS a Linux. MSBuild v macOS a Linux ale neumožňuje nasazení aplikace do Azure nebo jiných koncových bodů MSDeploy.

## <a name="set-the-environment"></a>Nastavení prostředí

Zahrňte vlastnost `<EnvironmentName>` v profilu publikování ( *. pubxml*) nebo souboru projektu pro nastavení [prostředí](xref:fundamentals/environments)aplikace:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

Pokud potřebujete transformace *Web. config* (například nastavení proměnných prostředí na základě konfigurace, profilu nebo prostředí), přečtěte si téma <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="exclude-files"></a>Vyloučit soubory

Při publikování ASP.NET Core Web Apps jsou k dispozici následující prostředky:

* Sestavit artefakty
* Složky a soubory, které odpovídají následujícím vzorům pro expanzi názvů:
  * `**\*.config` (například *Web. config*)
  * `**\*.json` (například *appSettings. JSON*)
  * `wwwroot\**`

Nástroj MSBuild podporuje [vzory expanze](https://gruntjs.com/configuring-tasks#globbing-patterns). Například následující prvek `<Content>` potlačí kopírování souborů textu ( *. txt*) ve složce *wwwroot\content* a jejích podsložkách:

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Předchozí kód lze přidat do publikačního profilu nebo souboru *. csproj* . Po přidání do souboru *. csproj* je pravidlo přidáno do všech profilů publikování v projektu.

Následující prvek `<MsDeploySkipRules>` vyloučí všechny soubory ze složky *wwwroot\content* :

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

`<MsDeploySkipRules>` neodstraní cíle *přeskočení* z webu nasazení. `<Content>` cílové soubory a složky jsou odstraněny z lokality nasazení. Předpokládejme například, že nasazená webová aplikace obsahovala následující soubory:

* *Views/Home/About1. cshtml*
* *Views/Home/About2. cshtml*
* *Views/Home/About3. cshtml*

Pokud jsou přidány následující prvky `<MsDeploySkipRules>`, neodstraní se tyto soubory na webu nasazení.

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

Předchozí prvky `<MsDeploySkipRules>` zabraňují nasazení *vynechaných* souborů. Po nasazení tyto soubory neodstraní.

Následující prvek `<Content>` odstraní cílové soubory v lokalitě nasazení:

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Použití nasazení příkazového řádku s předchozím prvkem `<Content>` vrací variaci následujícího výstupu:

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

## <a name="include-files"></a>Zahrnuté soubory

Následující oddíly popisují různé přístupy k zařazení souborů v době publikování. Oddíl [Obecné zahrnutí souborů](#general-file-inclusion) používá položku `DotNetPublishFiles`, která je k dispozici v souboru cílů publikování v sadě web SDK. Oddíl [zahrnutí selektivních souborů](#selective-file-inclusion) používá položku `ResolvedFileToPublish`, která je k dispozici v souboru cílů publikování v .NET Core SDK. Vzhledem k tomu, že webová sada SDK závisí na .NET Core SDK, může být každá položka použita v projektu ASP.NET Core.

### <a name="general-file-inclusion"></a>Obecné zahrnutí souborů

Následující příklad elementu `<ItemGroup>` ukazuje kopírování složky nacházející se mimo adresář projektu do složky publikovaného webu. Ve výchozím nastavení jsou zahrnuty všechny soubory přidané do následujících značek `<ItemGroup>`.

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

Předchozí kód:

* Lze přidat do souboru *. csproj* nebo do profilu publikování. Pokud je přidán do souboru *. csproj* , je zahrnut do každého profilu publikování v projektu.
* Deklaruje `_CustomFiles` položku pro ukládání souborů, které odpovídají vzoru rozexpanzení atributu `Include`. Složka *imagí* , na kterou se odkazuje ve vzoru, je umístěna mimo adresář projektu. [Rezervovaná vlastnost](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties)s názvem `$(MSBuildProjectDirectory)` se překládá na absolutní cestu souboru projektu.
* Poskytuje seznam souborů pro položku `DotNetPublishFiles`. Ve výchozím nastavení je prvek `<DestinationRelativePath>` položky prázdný. Výchozí hodnota je přepsána ve značce a používá [dobře známá metadata položky](/visualstudio/msbuild/msbuild-well-known-item-metadata) , například `%(RecursiveDir)`. Vnitřní text představuje složku *wwwroot/image* publikovaného webu.

### <a name="selective-file-inclusion"></a>Zahrnutí selektivních souborů

Zvýrazněný kód ukazuje následující příklad:

* Kopírování souboru, který se nachází mimo projekt, do složky *wwwroot* publikované lokality. Název souboru *ReadMe2.MD* se zachová.
* Vyloučení složky *wwwroot\Content*
* Kromě *Views\Home\About2.cshtml*.

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

Předchozí příklad používá položku `ResolvedFileToPublish`, jejíž výchozí chování je vždy kopírovat soubory zadané v atributu `Include` do publikované lokality. Přepište výchozí chování zahrnutím podřízeného prvku `<CopyToPublishDirectory>` s vnitřním textem buď `Never` nebo `PreserveNewest`. Příklad:

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

Další ukázky nasazení najdete v [souboru Readme pro úložiště web SDK](https://github.com/aspnet/websdk).

## <a name="run-a-target-before-or-after-publishing"></a>Spustit cíl před nebo po publikování

Vestavěné cíle `BeforePublish` a `AfterPublish` spouštějí cíl před nebo po cíli publikování. Přidejte následující prvky do profilu publikování pro protokolování zpráv konzoly před i po publikování:

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a>Publikování na serveru pomocí nedůvěryhodného certifikátu

Do profilu publikování přidejte vlastnost `<AllowUntrustedCertificate>` s hodnotou `True`:

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a>Služba Kudu

Pokud chcete zobrazit soubory ve Azure App Service nasazení webové aplikace, použijte [službu Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service). K názvu webové aplikace přidejte token `scm`. Příklad:

| Adresa URL                                    | Výsledek       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | Webová aplikace      |
| `http://mysite.scm.azurewebsites.net/` | Služba Kudu |

Vyberte položku nabídky [konzoly ladění](https://github.com/projectkudu/kudu/wiki/Kudu-console) pro zobrazení, úpravy, odstranění nebo přidání souborů.

## <a name="additional-resources"></a>Další zdroje

* [Nasazení webu](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) zjednodušuje nasazení webových aplikací a webů na servery služby IIS.
* [Úložiště GitHub web SDK](https://github.com/aspnet/websdk/issues): problémy se soubory a žádosti pro nasazení.
* [Publikování webové aplikace v ASP.NET ve virtuálním počítači Azure ze sady Visual Studio](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
