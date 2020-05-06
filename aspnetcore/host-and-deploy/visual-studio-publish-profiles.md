---
title: Publikační profily sady Visual Studio (. pubxml) pro nasazení aplikace ASP.NET Core
author: rick-anderson
description: Naučte se vytvářet profily publikování v aplikaci Visual Studio a používat je ke správě ASP.NET Core nasazení aplikací do různých cílů.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: 0de20b93929162f79d4d15fc4731959e48bb3b6c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776367"
---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a>Publikační profily sady Visual Studio (. pubxml) pro nasazení aplikace ASP.NET Core

Od [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) a [Rick Anderson](https://twitter.com/RickAndMSFT)

Tento dokument se zaměřuje na použití sady Visual Studio 2019 nebo novější k vytváření a používání profilů publikování. Profily publikování vytvořené pomocí sady Visual Studio lze použít s nástroji MSBuild a sadou Visual Studio. Pokyny k publikování do Azure najdete v tématu <xref:tutorials/publish-to-azure-webapp-using-vs>.

`dotnet new mvc` Příkaz vytvoří soubor projektu obsahující následující [ \<> element projektu](/visualstudio/msbuild/project-element-msbuild)na kořenové úrovni:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

Předchozí `<Project>` `Sdk` atribut elementu importuje [vlastnosti](/visualstudio/msbuild/msbuild-properties) a [cíle](/visualstudio/msbuild/msbuild-targets) nástroje MSBuild z *$ (MSBuildSDKsPath) \Microsoft.NET.SDK.Web\Sdk\Sdk.props* a *$ (MSBuildSDKsPath) \Microsoft.NET.SDK.Web\Sdk\Sdk.targets*, v uvedeném pořadí. Výchozím umístěním pro `$(MSBuildSDKsPath)` (se sadou visual Studio 2019 Enterprise) je složka *% ProgramFiles (x86)% \ Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* .

`Microsoft.NET.Sdk.Web`(Web SDK) závisí na jiných sadách SDK, `Microsoft.NET.Sdk` včetně (.NET Core SDK) `Microsoft.NET.Sdk.Razor` a ([Razor SDK](xref:razor-pages/sdk)). Naimportují se vlastnosti a cíle nástroje MSBuild přidružené ke každé závislé sadě SDK. Cíle publikování importují odpovídající sadu cílů na základě používané metody publikování.

Když nástroj MSBuild nebo Visual Studio načte projekt, dojde k následujícím akcím na nejvyšší úrovni:

* Sestavit projekt
* Soubory COMPUTE k publikování
* Publikovat soubory do cílového umístění

## <a name="compute-project-items"></a>Položky výpočetního projektu

Po načtení projektu jsou vypočítány [položky projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) (soubory). Typ položky určuje, jak je soubor zpracován. Ve výchozím nastavení jsou soubory *. cs* zahrnuty v seznamu `Compile` položek. Soubory v seznamu `Compile` položek jsou kompilovány.

Seznam `Content` položek obsahuje soubory, které jsou publikovány kromě výstupů sestavení. Ve výchozím nastavení jsou soubory, které `wwwroot\**`odpovídají `**\*.config`vzorům `**\*.json` , a zahrnuty v `Content` seznamu položek. Například `wwwroot\**` [vzor expanze názvů](https://gruntjs.com/configuring-tasks#globbing-patterns) odpovídá všem souborům ve složce *wwwroot* a jejích podsložkách.

::: moniker range=">= aspnetcore-3.0"

Sada Web SDK importuje [sadu Razor SDK](xref:razor-pages/sdk). V důsledku toho `**\*.cshtml` `**\*.razor` jsou soubory, které odpovídají vzorům, zahrnuty také v `Content` seznamu položek.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Sada Web SDK importuje [sadu Razor SDK](xref:razor-pages/sdk). V důsledku toho jsou soubory, které `**\*.cshtml` odpovídají vzoru, zahrnuty také v `Content` seznamu položek.

::: moniker-end

Chcete-li explicitně přidat soubor do seznamu publikování, přidejte ho přímo do souboru *. csproj* , jak je znázorněno v části [zahrnuté soubory](#include-files) .

Při výběru tlačítka **publikovat** v aplikaci Visual Studio nebo při publikování z příkazového řádku:

* Jsou vypočítány vlastnosti nebo položky (soubory, které jsou nutné k sestavení).
* **Pouze Visual Studio**: obnoveny balíčky NuGet. (Obnovení musí být uživatelem v rozhraní příkazového řádku explicitní.)
* Sestavení projektu.
* Položky publikování jsou vypočítány (soubory, které jsou nutné k publikování).
* Projekt je publikován (vypočítané soubory jsou zkopírovány do umístění pro publikování).

Pokud projekt ASP.NET Core odkazuje `Microsoft.NET.Sdk.Web` v souboru projektu, soubor *App_offline. htm* se umístí do kořenového adresáře webové aplikace. Když je soubor přítomen, modul ASP.NET Core aplikaci korektně ukončí a během nasazování zachová soubor *App_offline. htm* . Další informace najdete v referenčních informacích k [konfiguraci modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).

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

`dotnet publish` Příkaz vytvoří variaci následujícího výstupu:

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

Výchozí formát složky pro publikování je *bin\Debug\\{Target Framework MONIKER} \publish\\*. Například *bin\Debug\netcoreapp2.2\publish\\*.

Následující příkaz určuje `Release` sestavení a adresář pro publikování:

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

`dotnet publish` Příkaz volá MSBuild, který vyvolá `Publish` cíl. Všechny parametry předané `dotnet publish` do jsou předány do nástroje MSBuild. Parametry `-c` a `-o` se mapují do vlastností `Configuration` a `OutputPath` v uvedeném pořadí.

Vlastnosti nástroje MSBuild lze předat pomocí některého z následujících formátů:

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

Například následující příkaz publikuje `Release` sestavení do síťové sdílené složky. Sdílená síťová složka je určena s použitím lomítka (*//R8/*) a funguje na všech podporovaných platformách .NET Core.

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
* Azure Virtual Machines
* Složka
* IIS, FTP, Nasazení webu (pro libovolný webový server)
* Importovat profil

K určení nejvhodnějšího cíle publikování si přečtěte téma [co jsou možnosti publikování správné](/visualstudio/ide/not-in-toc/web-publish-options).

Když je vybraná možnost cíl publikování **složky** , zadejte cestu ke složce pro uložení publikovaných prostředků. Výchozí cesta ke složce je *bin\\{konfigurace projektu}\\{cílový MONIKER rozhraní} \publish\\*. Například *bin\Release\netcoreapp2.2\publish\\*. Kliknutím na tlačítko **vytvořit profil** dokončíte.

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

* `dotnet publish`a `dotnet build` podporují rozhraní API Kudu pro publikování do Azure z jakékoli platformy. Publikování Visual studia podporuje rozhraní API Kudu, ale podporuje je WebSDK pro publikování mezi platformami v Azure.
* Nepředávejte `DeployOnBuild` do `dotnet publish` příkazu.

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

Volání `msbuild` příkazu [dotnet sestavení](/dotnet/core/tools/dotnet-build) .NET Core CLI pro spuštění procesu sestavení a publikování. Příkazy `dotnet build` a `msbuild` jsou ekvivalentní při předávání do profilu složky. Při volání `msbuild` přímo ve Windows se používá .NET Framework verze nástroje MSBuild. Volání `dotnet build` na profil mimo složku:

* Vyvolá `msbuild`, který používá MSDeploy.
* Výsledkem je selhání (i při spuštění ve Windows). Chcete-li publikovat s profilem mimo složku, `msbuild` zavolejte přímo.

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

* `<ExcludeApp_Data>` Vlastnost je přítomna pouze pro splnění požadavku schématu XML. `<ExcludeApp_Data>` Vlastnost nemá žádný vliv na proces publikování, a to ani v případě, že je v kořenovém adresáři projektu *App_Data* složka. Složka *App_Data* neobdrží zvláštní zacházení, protože to funguje v projektech ASP.NET 4. x.

<!--

NOTE: Temporarily removed from 'Using the .NET Core CLI' below until https://github.com/aspnet/websdk/issues/888 is resolved.

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

-->

* `<LastUsedBuildConfiguration>` Vlastnost je nastavena na `Release`hodnotu. Při publikování ze sady Visual Studio je hodnota `<LastUsedBuildConfiguration>` nastavena pomocí hodnoty při spuštění procesu publikování. `<LastUsedBuildConfiguration>`má hodnotu Special a v importovaném souboru MSBuild by se neměl přepsat. Tato vlastnost se ale dá přepsat z příkazového řádku pomocí jednoho z následujících přístupů.
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

K nasazení aplikace pomocí profilu publikování spusťte `msbuild` příkaz ze sady Visual Studio **Developer Command Prompt**. Příkazový řádek je k dispozici ve složce sady *Visual Studio* v nabídce **Start** na hlavním panelu systému Windows. Pro snazší přístup můžete přidat příkazový řádek do nabídky **nástroje** v aplikaci Visual Studio. Další informace najdete v tématu [Developer Command Prompt pro Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).

Nástroj MSBuild používá následující syntaxi příkazu:

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* DÍLČÍ &ndash; Cesta k souboru projektu aplikace
* PROFILU &ndash; Název publikačního profilu.
* JMEN &ndash; Uživatelské jméno MSDeploy UŽIVATEL {USERNAME} se dá najít v profilu publikování.
* ZADÁNO &ndash; Heslo MSDeploy Získá {PASSWORD} z *{Profile}. Soubor PublishSettings* Stáhněte si *. Soubor PublishSettings* z některého z těchto:
  * **Průzkumník řešení**: vyberte **Zobrazit** > **Průzkumníka cloudu**. Spojte se s vaším předplatným Azure. Otevřete **App Services**. Klikněte pravým tlačítkem na aplikaci. Vyberte **Stáhnout profil publikování**.
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
> `dotnet msbuild` Příkaz je příkaz pro různé platformy a může kompilovat ASP.NET Core aplikace v systému MacOS a Linux. MSBuild v macOS a Linux ale neumožňuje nasazení aplikace do Azure nebo jiných koncových bodů MSDeploy.

## <a name="set-the-environment"></a>Nastavení prostředí

Zahrňte `<EnvironmentName>` vlastnost do profilu publikování (*. pubxml*) nebo soubor projektu pro nastavení [prostředí](xref:fundamentals/environments)aplikace:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

Pokud požadujete transformace *Web. config* (například nastavení proměnných prostředí na základě konfigurace, profilu nebo prostředí), přečtěte si téma <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="exclude-files"></a>Vyloučit soubory

Při publikování ASP.NET Core Web Apps jsou k dispozici následující prostředky:

* Sestavit artefakty
* Složky a soubory, které odpovídají následujícím vzorům pro expanzi názvů:
  * `**\*.config`(například *Web. config*)
  * `**\*.json`(například *appSettings. JSON*)
  * `wwwroot\**`

Nástroj MSBuild podporuje [vzory expanze](https://gruntjs.com/configuring-tasks#globbing-patterns). Například následující `<Content>` prvek potlačí kopírování souborů textu (*. txt*) ve složce *wwwroot\content* a jejích podsložkách:

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Předchozí kód lze přidat do publikačního profilu nebo souboru *. csproj* . Po přidání do souboru *. csproj* je pravidlo přidáno do všech profilů publikování v projektu.

Následující `<MsDeploySkipRules>` prvek vyloučí všechny soubory ze složky *wwwroot\content* :

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

`<MsDeploySkipRules>`neodstraní cíle *přeskočení* z webu nasazení. `<Content>`cílové soubory a složky se odstraní z lokality nasazení. Předpokládejme například, že nasazená webová aplikace obsahovala následující soubory:

* *Views/Home/About1. cshtml*
* *Views/Home/About2. cshtml*
* *Views/Home/About3. cshtml*

Pokud jsou přidány `<MsDeploySkipRules>` následující prvky, tyto soubory nebyly odstraněny na webu nasazení.

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

Předchozí `<MsDeploySkipRules>` prvky zabraňují nasazení *vynechaných* souborů. Po nasazení tyto soubory neodstraní.

Následující `<Content>` element odstraní cílové soubory v lokalitě nasazení:

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Použití nasazení příkazového řádku s předchozím `<Content>` elementem vrací variaci následujícího výstupu:

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

## <a name="include-files"></a>Soubory k zahrnutí

Následující oddíly popisují různé přístupy k zařazení souborů v době publikování. Oddíl [Obecné zahrnutí souborů](#general-file-inclusion) používá `DotNetPublishFiles` položku, která je poskytována souborem cílů publikování v sadě web SDK. Oddíl [zahrnutí selektivních souborů](#selective-file-inclusion) používá `ResolvedFileToPublish` položku, která je k dispozici v souboru cílů publikování v .NET Core SDK. Vzhledem k tomu, že webová sada SDK závisí na .NET Core SDK, může být každá položka použita v projektu ASP.NET Core.

### <a name="general-file-inclusion"></a>Obecné zahrnutí souborů

Následující příklad `<ItemGroup>` elementu ukazuje zkopírování složky nacházející se mimo adresář projektu do složky publikovaného webu. Ve výchozím nastavení `<ItemGroup>` jsou zahrnuty všechny soubory přidané do následujících značek.

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
* Deklaruje `_CustomFiles` položku pro ukládání souborů, které odpovídají vzoru `Include` rozexpanzení atributu. Složka *imagí* , na kterou se odkazuje ve vzoru, je umístěna mimo adresář projektu. [Vyhrazená vlastnost](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties)s názvem `$(MSBuildProjectDirectory)`se překládá na absolutní cestu souboru projektu.
* Poskytuje seznam souborů k `DotNetPublishFiles` položce. Ve výchozím nastavení je `<DestinationRelativePath>` element položky prázdný. Výchozí hodnota je přepsána ve značce a používá [dobře známá metadata položky](/visualstudio/msbuild/msbuild-well-known-item-metadata) , jako je `%(RecursiveDir)`například. Vnitřní text představuje složku *wwwroot/image* publikovaného webu.

### <a name="selective-file-inclusion"></a>Zahrnutí selektivních souborů

Zvýrazněný kód ukazuje následující příklad:

* Kopírování souboru, který se nachází mimo projekt, do složky *wwwroot* publikované lokality. Název souboru *ReadMe2.MD* se zachová.
* Vyloučení složky *wwwroot\Content*
* Kromě *Views\Home\About2.cshtml*.

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

Předchozí příklad používá `ResolvedFileToPublish` položku, jejíž výchozí chování je vždy kopírovat soubory, které jsou zadány v `Include` atributu do publikované lokality. Přepište výchozí chování zahrnutím `<CopyToPublishDirectory>` podřízeného prvku s vnitřním textem buď `Never` nebo. `PreserveNewest` Příklad:

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

Další ukázky nasazení najdete v [souboru Readme pro úložiště web SDK](https://github.com/aspnet/websdk).

## <a name="run-a-target-before-or-after-publishing"></a>Spustit cíl před nebo po publikování

Předdefinované `BeforePublish` a `AfterPublish` cíle provádějí cíl před nebo po cíli publikování. Přidejte následující prvky do profilu publikování pro protokolování zpráv konzoly před i po publikování:

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a>Publikování na serveru pomocí nedůvěryhodného certifikátu

Přidejte `<AllowUntrustedCertificate>` vlastnost s hodnotou `True` do profilu publikování:

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a>Služba Kudu

Pokud chcete zobrazit soubory ve Azure App Service nasazení webové aplikace, použijte [službu Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service). Přidejte `scm` token do názvu webové aplikace. Příklad:

| zprostředkovatele identity                                    | Výsledek       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | Webová aplikace      |
| `http://mysite.scm.azurewebsites.net/` | Služba Kudu |

Vyberte položku nabídky [konzoly ladění](https://github.com/projectkudu/kudu/wiki/Kudu-console) pro zobrazení, úpravy, odstranění nebo přidání souborů.

## <a name="additional-resources"></a>Další zdroje

* [Nasazení webu](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) zjednodušuje nasazení webových aplikací a webů na servery služby IIS.
* [Úložiště GitHub web SDK](https://github.com/aspnet/websdk/issues): problémy se soubory a žádosti pro nasazení.
* [Publikování webové aplikace v ASP.NET ve virtuálním počítači Azure ze sady Visual Studio](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
