---
title: Visual Studio publikuje profily (.pubxml) pro nasazení aplikace ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vytvořit profily publikování ve Visual Studiu a použít je pro správu nasazení aplikací ASP.NET Core na různé cíle.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: 274dd2cd528d3766aa07f69aac3470a131c79ffe
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659374"
---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a>Visual Studio publikuje profily (.pubxml) pro nasazení aplikace ASP.NET Core

Podle [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) a [Rick Anderson](https://twitter.com/RickAndMSFT)

Tento dokument se zaměřuje na použití Visual Studio 2019 nebo novější k vytvoření a použití profilů publikování. Profily publikování vytvořené pomocí sady Visual Studio lze použít s MSBuild a Visual Studio. Pokyny k publikování v <xref:tutorials/publish-to-azure-webapp-using-vs>Azure najdete v tématu .

Příkaz `dotnet new mvc` vytvoří soubor projektu obsahující následující prvek [ \<projektu>](/visualstudio/msbuild/project-element-msbuild)kořenové úrovně :

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

Atribut předchozího `<Project>` `Sdk` prvku importuje [vlastnosti](/visualstudio/msbuild/msbuild-properties) a [cíle](/visualstudio/msbuild/msbuild-targets) MSBuild z *aplikace $(MSBuildSDKSPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* a *$(MSBuildSDKSPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*. Výchozí umístění `$(MSBuildSDKsPath)` pro (s aplikací Visual Studio 2019 Enterprise) je složka *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks.*

`Microsoft.NET.Sdk.Web`(Web SDK) závisí na jiných sadách SDK, včetně `Microsoft.NET.Sdk` `Microsoft.NET.Sdk.Razor` (.NET Core SDK) a[(Razor SDK).](xref:razor-pages/sdk) Vlastnosti msbuild a cíle přidružené ke každé závislé sady SDK jsou importovány. Cíl publikování importuje příslušnou sadu cílů na základě použité metody publikování.

Když MSBuild nebo Visual Studio načte projekt, dojde k následujícím akcím vysoké úrovně:

* Sestavení projektu
* Výpočetní soubory k publikování
* Publikování souborů do cíle

## <a name="compute-project-items"></a>Vypočítat položky projektu

Při načtení projektu jsou vypočítány [položky projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) (soubory). Typ položky určuje způsob zpracování souboru. Ve výchozím nastavení jsou soubory `Compile` *.cs* zahrnuty v seznamu položek. Soubory v `Compile` seznamu položek jsou kompilovány.

Seznam `Content` položek obsahuje soubory, které jsou publikovány kromě výstupů sestavení. Ve výchozím nastavení jsou `wwwroot\**`soubory `**\*.config`odpovídající `**\*.json` vzorům `Content` a jsou zahrnuty do seznamu položek. Například `wwwroot\**` [globbing vzor](https://gruntjs.com/configuring-tasks#globbing-patterns) odpovídá všechny soubory ve složce *wwwroot* a jeho podsložky.

::: moniker range=">= aspnetcore-3.0"

Sada Web SDK importuje [sadu Razor SDK](xref:razor-pages/sdk). Výsledkem je, že soubory `**\*.cshtml` `**\*.razor` odpovídající vzorky `Content` a jsou také zahrnuty v seznamu položek.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Sada Web SDK importuje [sadu Razor SDK](xref:razor-pages/sdk). V důsledku toho jsou `**\*.cshtml` soubory odpovídající vzoru `Content` také zahrnuty do seznamu položek.

::: moniker-end

Chcete-li explicitně přidat soubor do seznamu publikování, přidejte jej přímo do souboru *.csproj,* jak je znázorněno v části [Zahrnout soubory.](#include-files)

Při výběru tlačítka **Publikovat** v sadě Visual Studio nebo při publikování z příkazového řádku:

* Vlastnosti nebo položky jsou vypočítány (soubory, které jsou potřebné k sestavení).
* **Pouze Visual Studio**: Balíčky NuGet jsou obnoveny. (Obnovení musí být explicitní uživatelem na rozhraní se konstruum.)
* Projekt se staví.
* Položky publikování jsou vypočítány (soubory, které jsou potřebné k publikování).
* Projekt je publikován (vypočítané soubory jsou zkopírovány do cíle publikování).

Když ASP.NET základní projekt `Microsoft.NET.Sdk.Web` odkazuje v souboru projektu, soubor *app_offline.htm* je umístěn v kořenovém adresáři webové aplikace. Když je soubor k dispozici, ASP.NET základní modul řádně vypne aplikaci a slouží *souboru app_offline.htm* během nasazení. Další informace naleznete v [odkazu na konfiguraci ASP.NET core modulem](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).

## <a name="basic-command-line-publishing"></a>Základní publikování příkazového řádku

Publikování příkazového řádku funguje na všech platformách podporovaných jádrem .NET a nevyžaduje visual studio. V následujících příkladech je příkaz publikování [dotnet](/dotnet/core/tools/dotnet-publish) rozhraní .NET Core CLI spuštěn z adresáře projektu (který obsahuje soubor *.csproj).* Pokud složka projektu není aktuální pracovní adresář, explicitně předat v cestě k souboru projektu. Příklad:

```dotnetcli
dotnet publish C:\Webs\Web1
```

Chcete-li vytvořit a publikovat webovou aplikaci, spusťte následující příkazy:

```dotnetcli
dotnet new mvc
dotnet publish
```

Příkaz `dotnet publish` vytvoří variantu následujícího výstupu:

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

Výchozí formát složky publikování je *bin\Debug\\{TARGET FRAMEWORK\\MONIKER}\publish*. Například *bin\Debug\netcoreapp2.2\publish\\*.

Následující příkaz určuje `Release` adresář sestavení a publikování:

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

Příkaz `dotnet publish` volá MSBuild, který `Publish` vyvolá cíl. Všechny parametry `dotnet publish` předané jsou předány MSBuild. A `-c` `-o` parametry mapovat na MSBuild `Configuration` a `OutputPath` vlastnosti, v uvedeném pořadí.

Vlastnosti MSBuild lze předat pomocí některého z následujících formátů:

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

Například následující příkaz publikuje `Release` sestavení do sdílené síťové složky. Sdílená síť je určena lomítkem *(//r8/*) a funguje na všech platformách podporovaných rozhraním .NET Core.

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

Zkontrolujte, že publikovaná aplikace pro nasazení není spuštěná. Soubory ve složce *publikování* jsou při spuštění aplikace uzamčeny. Nasazení nemůže dojít, protože uzamčené soubory nelze zkopírovat.

## <a name="publish-profiles"></a>Profily publikování

Tato část používá Visual Studio 2019 nebo novější k vytvoření profilu publikování. Po vytvoření profilu je k dispozici publikování z sady Visual Studio nebo příkazového řádku. Profily publikování mohou zjednodušit proces publikování a může existovat libovolný počet profilů.

Vytvořte profil publikování v sadě Visual Studio výběrem jedné z následujících cest:

* Klepněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte **publikovat**.
* V nabídce Sestavení vyberte **Publikovat {NÁZEV PROJEKTU}.** **Build**

Zobrazí se karta **Publikovat** na stránce možností aplikace. Pokud projekt nemá profil publikování, zobrazí se cílová stránka **Vyskladnění publikování.** Budete vyzváni k výběru jednoho z následujících cílů publikování:

* Azure App Service
* Služba Azure App Service na Linuxu
* Azure Virtual Machines
* Složka
* Nasazení služby IIS, FTP, web (pro libovolný webový server)
* Importovat profil

Chcete-li určit nejvhodnější cíl publikování, přečtěte [si téma Jaké možnosti publikování jsou pro mě vhodné](/visualstudio/ide/not-in-toc/web-publish-options).

Když je vybraný cíl publikování **složky,** zadejte cestu ke složce pro uložení publikovaných datových zdrojů. Výchozí cesta ke složce je *\\přihrádka {KONFIGURACE PROJEKTU}\\{TARGET FRAMEWORK MONIKER}\publish\\*. Například *bin\Release\netcoreapp2.2\publish\\*. K dokončení vyberte tlačítko **Vytvořit profil.**

Po vytvoření profilu publikování se obsah karty **Publikovat** změní. Nově vytvořený profil se zobrazí v rozevíracím seznamu. Pod rozevíracím seznamem vyberte **Vytvořit nový profil** a vytvořte další nový profil.

Nástroj pro publikování sady Visual Studio vytváří soubor *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild popisující profil publikování. Soubor *.pubxml:*

* Obsahuje nastavení konfigurace publikování a je spotřebována procesem publikování.
* Lze upravit přizpůsobit proces sestavení a publikování.

Při publikování na cíl Azure obsahuje soubor *PubXML* identifikátor předplatného Azure. U tohoto typu cíle se nedoporučuje přidávat tento soubor do správy zdrojového kódu. Při publikování na cíl, který není Azure, je bezpečné soubor *pubxml.*

Citlivé informace (například heslo pro publikování) jsou šifrovány na úrovni uživatele/počítače. Je uložen v souboru *Vlastnosti/PublishProfiles/{PROFILE NAME}.pubxml.user.* Vzhledem k tomu, že tento soubor může ukládat citlivé informace, neměl by být zaškrtávádo správy zdrojového kódu.

Přehled publikování webové aplikace ASP.NET Core najdete <xref:host-and-deploy/index>v tématu . Úlohy a cíle MSBuild nezbytné k publikování webové aplikace ASP.NET Core jsou open source v [úložišti aspnet/websdk](https://github.com/aspnet/websdk).

Následující příkazy mohou používat profily publikování složky, MSDeploy a [Kudu.](https://github.com/projectkudu/kudu/wiki) Vzhledem k tomu, že msdeploy postrádá podporu napříč platformami, jsou následující možnosti MSDeploy podporovány pouze v systému Windows.

**Složka (funguje napříč platformami):**

<!--

NOTE: Add back the following 'dotnet publish' folder publish example after https://github.com/aspnet/websdk/issues/888 is resolved.

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

-->

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

**MSDeploy:**

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

* `dotnet publish`a `dotnet build` podporujte kudu API pro publikování do Azure z libovolné platformy. Visual Studio publikování podporuje Kudu API, ale je podporované WebSDK pro publikování napříč platformami do Azure.
* `DeployOnBuild` Nepřecházejte `dotnet publish` na příkaz.

Další informace naleznete v tématu [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).

Přidejte profil publikování do složky *Vlastnosti/PublishProfiles* projektu s následujícím obsahem:

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

Při publikování pomocí profilu s názvem *FolderProfile*použijte některý z následujících příkazů:

<!--

NOTE: Temporarily removed until https://github.com/aspnet/websdk/issues/888 is resolved.

* `dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`

-->

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

Příkaz [dotnet sestavení](/dotnet/core/tools/dotnet-build) rozhraní .NET Core `msbuild` cli volání ke spuštění procesu sestavení a publikování. `dotnet build` Příkazy `msbuild` a jsou ekvivalentní při předávání v profilu složky. Při `msbuild` volání přímo v systému Windows se používá verze rozhraní .NET Framework msbuild. Volání `dotnet build` na profil bez složky:

* Vyvolá `msbuild`, který používá MSDeploy.
* Výsledkem je selhání (i při spuštění v systému Windows). Chcete-li publikovat s profilem `msbuild` bez složky, zavolejte přímo.

Následující profil publikování složky byl vytvořen pomocí sady Visual Studio a publikuje se do sdílené síťové složky:

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

* Vlastnost `<ExcludeApp_Data>` je k dispozici pouze pro splnění požadavku na schéma XML. Vlastnost `<ExcludeApp_Data>` nemá žádný vliv na proces publikování, i v případě, že je *App_Data* složky v kořenovém adresáři projektu. Složka *App_Data* nedostává zvláštní zacházení, jako tomu je v ASP.NET 4.x projekty.

<!--

NOTE: Temporarily removed from 'Using the .NET Core CLI' below until https://github.com/aspnet/websdk/issues/888 is resolved.

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

-->

* Vlastnost `<LastUsedBuildConfiguration>` je nastavena na `Release`. Při publikování z visual studia `<LastUsedBuildConfiguration>` je hodnota nastavena pomocí hodnoty při spuštění procesu publikování. `<LastUsedBuildConfiguration>`je zvláštní a v importovaném souboru MSBuild by neměl být přepsán. Tato vlastnost však může být přepsána z příkazového řádku pomocí jednoho z následujících přístupů.
  * Použití rozhraní CLI jádra .NET:

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * Pomocí msbuildu:

    ```console
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  Další informace naleznete v tématu [MSBuild: jak nastavit vlastnost konfigurace](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a>Publikování do koncového bodu MSDeploy z příkazového řádku

Následující příklad používá webovou aplikaci ASP.NET Core vytvořenou visual studio s názvem *AzureWebApp*. Profil publikování Aplikací Azure se přidá ve Visual Studiu. Další informace o vytvoření profilu naleznete v části [Publikovat profily.](#publish-profiles)

Chcete-li aplikaci nasadit pomocí `msbuild` profilu publikování, spusťte příkaz z **příkazového řádku pro vývojáře**sady Visual Studio . Příkazový řádek je k dispozici ve složce *Visual Studio* v nabídce **Start** na hlavním panelu systému Windows. Pro snadnější přístup můžete přidat příkazový řádek do nabídky **Nástroje** v sadě Visual Studio. Další informace naleznete v [tématu Developer Command Prompt Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).

MSBuild používá následující syntaxi příkazů:

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* {CESTA} &ndash; Cesta k souboru projektu aplikace.
* {PROFIL} &ndash; Název profilu publikování.
* {UŽIVATELSKÉ JMÉNO} &ndash; Uživatelské jméno MSDeploy. {USERNAME} lze nalézt v profilu publikování.
* {HESLO} &ndash; MSDeploy heslo. Získejte {PASSWORD} z *{PROFILE}. Soubor PublishSettings.* Stáhněte si *soubor . Soubor PublishSettings* z obou těchto:
  * **Průzkumník řešení**: Vyberte **zobrazit** > **Průzkumníka cloudu**. Spojte se se svým předplatným Azure. Otevřete **služby App Services**. Klikněte pravým tlačítkem myši na aplikaci. Vyberte **možnost Stáhnout profil publikování**.
  * Portál Azure: V panelu **Přehled** webové aplikace vyberte **Získat profil publikování.**

Následující příklad používá profil publikování s názvem *AzureWebApp - Web Deploy*:

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

Profil publikování lze také použít s příkazem [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) rozhraní .NET Core z příkazového prostředí systému Windows:

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> Příkaz `dotnet msbuild` je příkaz pro různé platformy a může kompilovat ASP.NET aplikace Core v systému macOS a Linux. MSBuild na macOS a Linux však není schopen nasazení aplikace do Azure nebo jiných koncových bodů MSDeploy.

## <a name="set-the-environment"></a>Nastavení prostředí

Zahrňte `<EnvironmentName>` vlastnost do profilu publikování (*.pubxml*) nebo souboru projektu, abyste nastavili [prostředí](xref:fundamentals/environments)aplikace :

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

Pokud požadujete transformace *web.config* (například nastavení proměnných prostředí na základě konfigurace, profilu nebo prostředí), přečtěte si viz <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="exclude-files"></a>Vyloučit soubory

Při publikování ASP.NET webových aplikací Core jsou zahrnuty následující datové zdroje:

* Vytváření artefaktů
* Složky a soubory odpovídající následujícím vzorům globbingu:
  * `**\*.config`(například *web.config*)
  * `**\*.json`(například *appsettings.json*)
  * `wwwroot\**`

MSBuild podporuje [globbing vzory](https://gruntjs.com/configuring-tasks#globbing-patterns). Například následující `<Content>` prvek potlačí kopírování textových souborů (*TXT*) ve složce *wwwroot\content* a jejích podsložkách:

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Předchozí značky lze přidat do profilu publikování nebo do souboru *.csproj.* Po přidání do souboru *.csproj* je pravidlo přidáno do všech profilů publikování v projektu.

Následující `<MsDeploySkipRules>` prvek vylučuje všechny soubory ze složky *wwwroot\content:*

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

`<MsDeploySkipRules>`neodstraní *přeskakovací* cíle z lokality nasazení. `<Content>`cílené soubory a složky jsou odstraněny z webu nasazení. Předpokládejme například, že nasazená webová aplikace měla následující soubory:

* *Zobrazení/Úvod/About1.cshtml*
* *Zobrazení/Domů/About2.cshtml*
* *Zobrazení/Úvod/About3.cshtml*

Pokud jsou `<MsDeploySkipRules>` přidány následující prvky, tyto soubory by nebyly odstraněny v lokalitě nasazení.

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

Předchozí `<MsDeploySkipRules>` prvky zabránit *přeskočené* soubory z nasazení. Po nasazení tyto soubory neodstraní.

Následující `<Content>` prvek odstraní cílové soubory v lokalitě nasazení:

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Použití nasazení příkazového řádku `<Content>` s předchozím prvkem dává variaci následujícího výstupu:

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

V následujících částech jsou popsány různé přístupy pro zahrnutí souborů v době publikování. Část [Obecné zahrnutí souboru](#general-file-inclusion) používá položku, `DotNetPublishFiles` která je poskytována souborem cílů publikování v sada Web SDK. Sekce [Selektivní zahrnutí souboru](#selective-file-inclusion) používá položku, `ResolvedFileToPublish` která je poskytována souborem cílů publikování v souboru .NET Core SDK. Vzhledem k tomu, že sada Web SDK závisí na sdk jádra .NET, lze buď položku použít v projektu ASP.NET Core.

### <a name="general-file-inclusion"></a>Obecné zahrnutí souboru

`<ItemGroup>` Následující příklad je prvek ukazuje kopírování složky umístěné mimo adresář projektu do složky publikovaného webu. Všechny soubory přidané do následujících `<ItemGroup>` značek jsou zahrnuty ve výchozím nastavení.

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

Předchozí značka:

* Lze přidat do souboru *.csproj* nebo do profilu publikování. Pokud je přidán do souboru *.csproj,* je součástí každého profilu publikování v projektu.
* Deklaruje položku `_CustomFiles` `Include` pro uložení souborů odpovídajících vzoru globbing u atributu. Složka *obrázků,* na kterou se odkazuje ve vzoru, je umístěna mimo adresář projektu. [Vyhrazená vlastnost](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties) `$(MSBuildProjectDirectory)`s názvem překládá na absolutní cestu souboru projektu.
* Obsahuje seznam souborů k `DotNetPublishFiles` položce. Ve výchozím nastavení je `<DestinationRelativePath>` prvek položky prázdný. Výchozí hodnota je přepsána ve značkách a používá [známá metadata položek,](/visualstudio/msbuild/msbuild-well-known-item-metadata) například `%(RecursiveDir)`. Vnitřní text představuje složku *wwwroot/images* publikovaného webu.

### <a name="selective-file-inclusion"></a>Selektivní zahrnutí souboru

Zvýrazněná značka v následujícím příkladu ukazuje:

* Kopírování souboru umístěného mimo projekt do složky *wwwroot* publikovaného webu. Je zachován název souboru *ReadMe2.md.*
* S výjimkou složky *wwwroot\Content.*
* S výjimkou *zobrazení\Domů\About2.cshtml*.

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

Předchozí příklad používá `ResolvedFileToPublish` položku, jejíž výchozí chování je vždy `Include` kopírovat soubory uvedené v atributu na publikovaný web. Výchozí chování přepište zahrnutím podřízeného `<CopyToPublishDirectory>` `Never` prvku `PreserveNewest`s vnitřním textem buď nebo . Příklad:

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

Další ukázky nasazení naleznete v [úložišti úložiště web SDK Readme](https://github.com/aspnet/websdk).

## <a name="run-a-target-before-or-after-publishing"></a>Spuštění cíle před publikováním nebo po publikování

Předdefinované `BeforePublish` a `AfterPublish` cíle provést cíl před nebo po cíl publikování. Přidejte do profilu publikování následující prvky pro zprávy v konzole protokolu před publikováním i po něm:

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a>Publikování na serveru pomocí nedůvěryhodného certifikátu

Přidejte `<AllowUntrustedCertificate>` vlastnost s `True` hodnotou do profilu publikování:

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a>Služba Kudu

Chcete-li zobrazit soubory v nasazení webové aplikace Služby Azure App Service, použijte [službu Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service). Připojte `scm` token k názvu webové aplikace. Příklad:

| zprostředkovatele identity                                    | Výsledek       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | Webová aplikace      |
| `http://mysite.scm.azurewebsites.net/` | Služba Kudu |

Vyberte položku nabídky [Editorská/editor,](https://github.com/projectkudu/kudu/wiki/Kudu-console) kterou chcete zobrazit, upravit, odstranit nebo přidat soubory.

## <a name="additional-resources"></a>Další zdroje

* [Nasazení webu](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) zjednodušuje nasazení webových aplikací a webů na servery služby IIS.
* [Úložiště Web SDK GitHub](https://github.com/aspnet/websdk/issues): Problémy se soubory a funkce požadavků pro nasazení.
* [Publikování ASP.NET webové aplikace do virtuálního počítače Azure z Visual Studia](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
