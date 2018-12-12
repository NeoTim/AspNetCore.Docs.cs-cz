---
title: Visual Studio publikačních profilů pro nasazení aplikace ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vytvořit profily publikování v sadě Visual Studio a jejich použití pro správu nasazení aplikací ASP.NET Core do různých cílů.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: 3d24cd2cd4697e8e7cf7e4bdf4d076a09b6a6a23
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284705"
---
# <a name="visual-studio-publish-profiles-for-aspnet-core-app-deployment"></a>Visual Studio publikačních profilů pro nasazení aplikace ASP.NET Core

Podle [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) a [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range="<= aspnetcore-1.1"

1.1 verzi tohoto tématu, stáhněte si [sady Visual Studio publikačních profilů pro nasazení aplikace ASP.NET Core (verze 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/VS_Publish_Profiles_1.1.pdf).

::: moniker-end

Tento dokument se zaměřuje na pomocí sady Visual Studio 2017 nebo později k vytvoření a použití publikačních profilů. Profily publikování vytvořené pomocí sady Visual Studio můžete spustit z nástroje MSBuild a sadě Visual Studio. Zobrazit [publikovat webovou aplikaci ASP.NET Core do služby Azure App Service pomocí sady Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs) pokyny k publikování do Azure.

Následující soubor projektu byl vytvořen pomocí příkazu `dotnet new mvc`:

::: moniker range=">= aspnetcore-2.2"

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

::: moniker-end

`<Project>` Elementu `Sdk` atribut provádí následující úlohy:

* Importuje soubor vlastnosti z *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.Props* na začátku.
* Importuje soubor cílů z *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets* na konci.

Výchozím umístěním pro `MSBuildSDKsPath` (pomocí sady Visual Studio 2017 Enterprise) je *% programfiles (x86) %\Microsoft Visual Studio\2017\Enterprise\MSBuild\Sdks* složky.

`Microsoft.NET.Sdk.Web` SDK závisí na:

* *Microsoft.NET.Sdk.Web.ProjectSystem*
* *Microsoft.NET.Sdk.Publish*

Což způsobí, že následující vlastnosti a cíle, které se mají naimportovat:

* *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.Props*
* *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.targets*
* *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.Props*
* *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.targets*

Publikování import cílů vpravo sadu cílů založené na metodě publikování použít.

Při načtení projektu nástroje MSBuild nebo Visual Studio provedou tyto akce vysoké úrovně:

* Sestavit projekt
* COMPUTE souborů k publikování
* Publikování souborů do cíle

## <a name="compute-project-items"></a>COMPUTE položky projektu

Při načtení projektu jsou vypočítány položky projektu (soubory). `item type` Atribut určuje, jak se zpracovávají souboru. Ve výchozím nastavení *.cs* soubory jsou zahrnuty v `Compile` seznam položek. Soubory `Compile` jsou zkompilovány seznam položek.

`Content` Seznamu položek obsahuje soubory, které jsou publikovány kromě výstupy sestavení. Ve výchozím nastavení, soubory odpovídající vzoru `wwwroot/**` jsou součástí `Content` položky. `wwwroot/\*\*` [Model podpory zástupných znaků](https://gruntjs.com/configuring-tasks#globbing-patterns) vyhledá všechny soubory v *wwwroot* složky **a** podsložky. Pokud chcete explicitně přidat soubor do seznamu publikovat, přidejte přímo v souboru *.csproj* sdílené, jak je znázorněno v [vložených souborů](#include-files).

Při výběru **publikovat** tlačítko v sadě Visual Studio nebo při publikování z příkazového řádku:

* Jsou vypočítané vlastnosti/položky (soubory, které jsou potřebné k sestavení).
* **Visual Studio pouze**: Obnovení balíčků NuGet. (Obnovit musí být explicitní uživatelem na rozhraní příkazového řádku).
* Sestavení projektu.
* Publikovat položky se zpracovávají (soubory, které jsou potřebné k publikování).
* Publikování projektu (vypočítané soubory se zkopírují do cílového umístění publikování).

Když se odkazuje projekt ASP.NET Core `Microsoft.NET.Sdk.Web` v souboru projektu *app_offline.htm* soubor umístěn v kořenovém adresáři webové aplikace. Pokud soubor existuje, modul ASP.NET Core řádně ukončí aplikaci, slouží *app_offline.htm* souboru během nasazení. Další informace najdete v tématu [odkaz Konfigurace modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).

## <a name="basic-command-line-publishing"></a>Publikování základních příkazového řádku

Příkazový řádek publikování funguje na všech platformách .NET Core podporovány a nevyžaduje, aby Visual Studio. Níže [dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkaz spustí z adresáře projektu (která obsahuje *.csproj* souboru). Pokud není ve složce projektu explicitně předávat v cestě k souboru projektu. Příklad:

```console
dotnet publish C:\Webs\Web1
```

Spusťte následující příkazy k vytvoření a publikování webové aplikace:

```console
dotnet new mvc
dotnet publish
```

[Dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkaz vytváří výstup podobný následujícímu:

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version 15.3.409.57025 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp2.0\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp2.0\publish\
```

Výchozí nastavení složky pro publikování je `bin\$(Configuration)\netcoreapp<version>\publish`. Výchozí nastavení pro `$(Configuration)` je *ladění*. V předchozím příkladu `<TargetFramework>` je `netcoreapp2.0`.

`dotnet publish -h` Zobrazí nápovědu pro publikování.

Následující příkaz určuje `Release` sestavení a publikování adresáře:

```console
dotnet publish -c Release -o C:\MyWebs\test
```

[Dotnet publikovat](/dotnet/core/tools/dotnet-publish) volání MSBuild, který vyvolá příkaz `Publish` cíl. Žádné parametry předány `dotnet publish` jsou předávaným do MSBuild. `-c` Parametr se mapuje `Configuration` vlastnosti Msbuildu. `-o` Parametr mapuje na `OutputPath`.

Vlastnosti nástroje MSBuild mohou být předány některou z následujících formátů:

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

Následující příkaz publikuje `Release` od sestavení k síťové sdílené složky:

`dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb`

Sdílené síťové složky se zadaným lomítka (*//r8/*) a funguje na všech platformách .NET Core podporovány.

Potvrďte, že publikované aplikace pro nasazení neběží. Soubory *publikovat* složky jsou zamčené, když je aplikace spuštěna. Nasazení nebyla vytvořena, protože uzamčena, nelze zkopírovat soubory.

## <a name="publish-profiles"></a>Profily publikování

Tato část používá k vytvoření profilu publikování sady Visual Studio 2017 nebo novější. Po vytvoření profilu publikování ze sady Visual Studio nebo příkazového řádku je k dispozici.

Publikování profilů může zjednodušit proces publikování, a může existovat libovolný počet profilů. Vytvořte profil publikování v sadě Visual Studio výběrem jedné z následujících cest:

* Klikněte pravým tlačítkem na projekt v Průzkumníku řešení a vyberte **publikovat**.
* Vyberte **publikovat {název projektu}** z **sestavení** nabídky.

**Publikovat** zobrazuje kartu na stránce kapacity aplikace. Pokud projekt nemá profil publikování, zobrazí se následující stránka:

![Na kartě Publikovat na stránce kapacity aplikace](visual-studio-publish-profiles/_static/az.png)

Když **složky** je vybrána, zadejte cestu ke složce pro ukládání publikovaných prostředků. Výchozí složkou je *bin\Release\PublishOutput*. Klikněte na tlačítko **vytvořit profil** tlačítko Dokončit.

Po vytvoření profilu publikování **publikovat** kartě změny. V rozevíracím seznamu se zobrazí nově vytvořený profil. Klikněte na tlačítko **vytvořit nový profil** vytvořte další nový profil.

![Na kartě Publikovat na stránce kapacity aplikace zobrazuje FolderProfile](visual-studio-publish-profiles/_static/create_new.png)

Průvodce publikováním podporuje následující cíle publikování:

* Azure App Service
* Azure Virtual Machines
* Služba IIS, FTP atd., (pro všechny webové servery)
* Folder
* Importovat profil

Další informace najdete v tématu [jaké možnosti publikování jsou pro mě nejlepší](/visualstudio/ide/not-in-toc/web-publish-options).

Při vytváření profilu publikování pomocí sady Visual Studio *vlastnosti/PublishProfiles / {název profilu} .pubxml* se vytvoří soubor MSBuild. *.Pubxml* soubor je soubor MSBuild a obsahuje konfigurační nastavení publikování. Tento soubor můžete změnit na vlastní nastavení sestavení a publikování procesu. Tento soubor je pro čtení procesem publikování. `<LastUsedBuildConfiguration>` je speciální, protože globální vlastností a nemohou být zapsány všech souborů, které se importují v sestavení. Zobrazit [MSBuild: jak nastavit vlastnost konfigurace](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx) Další informace.

Při publikování do Azure cíl, *.pubxml* soubor obsahuje identifikátor vašeho předplatného Azure. S cílovým typem přidává se tento soubor do správy zdrojového kódu se nedoporučuje. Při publikování na cíl mimo Azure, je k vrácení se změnami *.pubxml* souboru.

Se šifrují citlivé údaje (třeba publikovat heslo) na úrovni uživatele nebo počítače. Je uložený v *vlastnosti/PublishProfiles / {název profilu}.pubxml.user* souboru. Protože tento soubor můžete uložit citlivé informace, by neměla být zaškrtnuta do správy zdrojového kódu.

Přehled o tom, jak publikovat webovou aplikaci v ASP.NET Core, najdete v části [hostitele a nasadit](xref:host-and-deploy/index). Úlohy MSBuild a cíle plánování pro publikování aplikace ASP.NET Core je open source na https://github.com/aspnet/websdk.

`dotnet publish` můžete použít složku, MSDeploy, a [Kudu](https://github.com/projectkudu/kudu/wiki) publikační profily:

Složka (funguje napříč platformami):

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

MSDeploy (aktuálně toto funguje pouze ve Windows, protože není MSDeploy napříč platformami):

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

Balíček MSDeploy (aktuálně toto funguje pouze ve Windows, protože není MSDeploy napříč platformami):

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

V předchozích ukázkách **není** předat `deployonbuild` k `dotnet publish`.

Další informace najdete v tématu [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).

`dotnet publish` podporuje rozhraní API Kudu k publikování do Azure z jakékoliv platformy. Visual Studio publikování podporuje Kudu rozhraní API, ale je podporovaný modulem WebSDK pro různé platformy publikovat do Azure.

Přidat profil publikování *vlastnosti/PublishProfiles* složka s následujícím obsahem:

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

Spusťte následující příkaz, který zip obsah publikovat a publikujete ji do Azure pomocí rozhraní API Kudu:

```console
dotnet publish /p:PublishProfile=Azure /p:Configuration=Release
```

Při použití profil publikování, nastavte následující vlastnosti MSBuild:

* `DeployOnBuild=true`
* `PublishProfile={PUBLISH PROFILE}`

Při publikování tak, že profil s názvem *FolderProfile*, lze provést jednu z následujících příkazů:

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild      /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

Při vyvolání [dotnet sestavení](/dotnet/core/tools/dotnet-build), volá `msbuild` ke spuštění sestavení a publikování procesu. Volání na buď `dotnet build` nebo `msbuild` je ekvivalentní při předávání ve složce profilu. Při volání nástroje MSBuild přímo na Windows, se používá rozhraní .NET Framework verze nástroje MSBuild. Je aktuálně omezená na počítače s Windows pro publikování MSDeploy. Volání `dotnet build` do jiné složky profil vyvolá MSBuild a nástroj MSBuild používá MSDeploy v jiné složce profily. Volání `dotnet build` v jiné složce profilu vyvolá MSBuild (pomocí MSDeploy) a vede k chybě (i když spuštěn na platformě Windows). Pokud chcete publikovat pomocí profilu bez složky, přímo voláním funkce MSBuild.

Profil publikování následující složka byla vytvořena pomocí sady Visual Studio a publikuje do sdílené síťové složky:

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

Poznámka: `<LastUsedBuildConfiguration>` je nastavena na `Release`. Při publikování ze sady Visual Studio `<LastUsedBuildConfiguration>` hodnota vlastnosti konfigurace je nastavena pomocí hodnoty, když se spustí proces publikování. `<LastUsedBuildConfiguration>` Vlastnost konfigurace je speciální a nesmí být přepsána nastaveními v importovaný soubor MSBuild. Tato vlastnost může být přepsána z příkazového řádku.

Použití .NET Core CLI:

```console
dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

Použití nástroje MSBuild:

```console
msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a>Publikování do koncového bodu MSDeploy z příkazového řádku

Následující příklad používá webovou aplikaci ASP.NET Core, vytvořené pomocí sady Visual Studio s názvem *AzureWebApp*. Profil publikování aplikací Azure se přidá s Visual Studio. Další informace o tom, jak vytvořit profil, najdete v článku [publikační profily](#publish-profiles) oddílu.

Chcete-li nasadit aplikaci pomocí profilu publikování, spusťte `msbuild` příkaz ze sady Visual Studio **Developer Command Prompt**. Je k dispozici v příkazovém řádku *sady Visual Studio* složky **Start** nabídky na hlavním panelu Windows. Pro snadnější přístup, můžete přidat do příkazového řádku **nástroje** nabídky v sadě Visual Studio. Další informace najdete v tématu [Developer Command Prompt pro sadu Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).

Nástroj MSBuild používá následující syntaxe příkazu:

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* POLOŽKY {PATH} &ndash; Cestu k souboru projektu vaší aplikace.
* {PROFIL} &ndash; Název profilu publikování.
* {USERNAME} &ndash; MSDeploy uživatelské jméno. {USERNAME} najdete v profilu publikování.
* {HESLO} &ndash; MSDeploy heslo. Získat {heslo} z *{profil}. PublishSettings* souboru. Stáhněte si *. PublishSettings* soubor z aplikace:
  * Průzkumník řešení: Vyberte **zobrazení** > **Cloud Explorer**. Připojení k vašemu předplatnému Azure. Otevřít **App Services**. Klikněte pravým tlačítkem na aplikaci. Vyberte **stáhnout profil publikování**.
  * Azure portal: Vyberte **získat profil publikování** ve webové aplikaci **přehled** panelu.

Následující příklad používá profil publikování s názvem *AzureWebApp – nasazení webu*:

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

Profil publikování, je také možné pomocí rozhraní příkazového řádku .NET Core [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) příkazu z příkazového řádku Windows:

```console
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!NOTE]
> [Dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) příkaz je k dispozici napříč platformami a můžete zkompilovat aplikace ASP.NET Core v macOS a Linuxu. Však není schopné nasadit aplikaci do Azure nebo jiný koncový bod MSDeploy MSBuild v systému macOS a Linux. MSDeploy je dostupná jenom ve Windows.

## <a name="exclude-files"></a>Vyloučení souborů

Při publikování webové aplikace ASP.NET Core, artefakty sestavení a obsah *wwwroot* složky jsou zahrnuty. `msbuild` podporuje [vzorů podpory zástupných znaků](https://gruntjs.com/configuring-tasks#globbing-patterns). Například následující `<Content>` element vyloučí veškerý text (*.txt*) souborů z doručené pošty *wwwroot/obsah* složce a jejích podsložkách.

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Předchozí kód lze přidat do profilu publikování nebo *.csproj* souboru. Když se přidá *.csproj* , pravidlo se přidá soubor všechny profily publikování v projektu.

Následující `<MsDeploySkipRules>` element vyloučí všechny soubory *wwwroot/obsah* složky:

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

`<MsDeploySkipRules>` nedojde k odstranění *přeskočit* cílů nasazení webu. `<Content>` cílové soubory a složky, jsou odstraněny z nasazení webu. Předpokládejme například, že nasazené webové aplikace má následující soubory:

* *Views/Home/About1.cshtml*
* *Views/Home/About2.cshtml*
* *Views/Home/About3.cshtml*

Pokud následující `<MsDeploySkipRules>` prvky jsou přidány, by dojít k odstranění těchto souborů na web pro nasazení.

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

Předchozí `<MsDeploySkipRules>` zabránit prvky *přeskočeno* soubory z nasazení. Tyto soubory neodstraní po nasazení.

Následující `<Content>` element odstraní cílové soubory na web pro nasazení:

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Pomocí příkazového řádku nasazení s předchozím `<Content>` element provede následující výstup:

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\netcoreapp1.1\PubTmp\Web1.SourceManifest.
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

Obsahuje následující kód *image* mimo adresář projektu do složky *wwwroot/imagí* složku publikování webu:

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotnetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotnetPublishFiles>
</ItemGroup>
```

Značky mohou být přidány do *.csproj* souboru nebo profil publikování. Pokud je přidána do *.csproj* souboru, je zahrnutý v každé profilu publikování v projektu.

Následující zvýrazněný kód ukazuje jak na:

* Zkopírovat soubor z mimo projekt sketchflow *wwwroot* složky.
* Vyloučit *wwwroot\Content* složky.
* Vyloučit *Views\Home\About2.cshtml*.

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
    <PublishFramework />
    <ProjectGuid>afa9f185-7ce0-4935-9da1-ab676229d68a</ProjectGuid>
    <publishUrl>bin\Release\PublishOutput</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
  <ItemGroup>
    <ResolvedFileToPublish Include="..\ReadMe2.MD">
      <RelativePath>wwwroot\ReadMe2.MD</RelativePath>
    </ResolvedFileToPublish>

    <Content Update="wwwroot\Content\**\*" CopyToPublishDirectory="Never" />
    <Content Update="Views\Home\About2.cshtml" CopyToPublishDirectory="Never" />

  </ItemGroup>
</Project>
```

Zobrazit [WebSDK Readme](https://github.com/aspnet/websdk) pro další nasazení ukázky.

## <a name="run-a-target-before-or-after-publishing"></a>Spustit cíl před nebo po publikování

Předdefinované `BeforePublish` a `AfterPublish` cíle spustit cíl před nebo za cíl publikování. Přidáte do profilu publikování k protokolování zpráv konzoly před i po publikování následující prvky:

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a>Publikovat na server pomocí nedůvěryhodného certifikátu

Přidat `<AllowUntrustedCertificate>` vlastnost s hodnotou `True` do profilu publikování:

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a>Kudu service

Chcete-li zobrazit soubory v nasazení služby Azure App Service webovou aplikaci, použijte [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service). Připojit `scm` tokenu název webové aplikace. Příklad:

| Adresa URL                                    | Výsledek       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | Webové aplikace      |
| `http://mysite.scm.azurewebsites.net/` | Kudu service |

Vyberte [ladění konzoly](https://github.com/projectkudu/kudu/wiki/Kudu-console) položka nabídky zobrazit, upravit, odstranit nebo přidat soubory.

## <a name="additional-resources"></a>Další zdroje

* [Webu nasadit](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) zjednodušuje nasazení webové aplikace a weby pro servery služby IIS.
* [https://github.com/aspnet/websdk](https://github.com/aspnet/websdk/issues): Soubor problémů a požádat o funkce pro nasazení.
* [Publikování webové aplikace v ASP.NET do virtuálního počítače Azure ze sady Visual Studio](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
