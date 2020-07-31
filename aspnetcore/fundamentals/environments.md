---
title: Používání více prostředí v ASP.NET Core
author: rick-anderson
description: Naučte se řídit chování aplikace v různých prostředích aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/1/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/environments
ms.openlocfilehash: 977d222ed61fa914bd4ffb70e192ef19d4da5c33
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2020
ms.locfileid: "87330616"
---
# <a name="use-multiple-environments-in-aspnet-core"></a>Používání více prostředí v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Kirka Larkin](https://twitter.com/serpent5)

ASP.NET Core konfiguruje chování aplikace na základě běhového prostředí pomocí proměnné prostředí.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Prostředí

Pro určení běhového prostředí ASP.NET Core čtení z následujících proměnných prostředí:

1. [DOTNET_ENVIRONMENT](xref:fundamentals/configuration/index#default-host-configuration)
1. `ASPNETCORE_ENVIRONMENT`Kdy <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> je volána metoda. Výchozí ASP.NET Core volání šablon webové aplikace `ConfigureWebHostDefaults` . `ASPNETCORE_ENVIRONMENT`Hodnota Přepisuje `DOTNET_ENVIRONMENT` .

`IHostEnvironment.EnvironmentName`lze nastavit na libovolnou hodnotu, ale následující hodnoty jsou poskytovány rozhraním:

* <xref:Microsoft.Extensions.Hosting.Environments.Development>: [launchSettings.jsv](#lsj) sadě souborů na `ASPNETCORE_ENVIRONMENT` `Development` místním počítači.
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <xref:Microsoft.Extensions.Hosting.Environments.Production>: Výchozí nastavení `DOTNET_ENVIRONMENT` , pokud a nebylo `ASPNETCORE_ENVIRONMENT` nastaveno.

Následující kód:

* Volá [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) , pokud `ASPNETCORE_ENVIRONMENT` je nastaven na `Development` .
* Volá [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) , pokud je hodnota `ASPNETCORE_ENVIRONMENT` nastavena na `Staging` , `Production` nebo `Staging_2` .
* Vloží <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do `Startup.Configure` . Tento přístup je užitečný, když aplikace vyžaduje úpravu jenom `Startup.Configure` pro několik prostředí s minimálními rozdíly v kódu na prostředí.
* Se podobá kódu generovanému šablonami ASP.NET Core.

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Startup.cs?name=snippet)]

[Pomocná značka prostředí](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) používá hodnotu [IHostEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) pro zahrnutí nebo vyloučení značek v elementu:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

[Stránka about](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) z [ukázkového kódu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) obsahuje předchozí kód a zobrazuje hodnotu `IWebHostEnvironment.EnvironmentName` .

V systému Windows a macOS se v proměnných a hodnotách prostředí nerozlišují velká a malá písmena. Ve výchozím nastavení se v proměnných a hodnotách prostředí Linux rozlišují **velká a malá písmena** .

### <a name="create-environmentssample"></a>Vytvořit EnvironmentsSample

[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) použitý v tomto dokumentu je založen na Razor projektu stránky s názvem *EnvironmentsSample*.

Následující kód vytvoří a spustí webovou aplikaci s názvem *EnvironmentsSample*:

```bash
dotnet new webapp -o EnvironmentsSample
cd EnvironmentsSample
dotnet run --verbosity normal
```

Po spuštění aplikace se zobrazí některé z následujících výstupů:

```bash
Using launch settings from c:\tmp\EnvironmentsSample\Properties\launchSettings.json
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: c:\tmp\EnvironmentsSample
```

<a name="lsj"></a>

### <a name="development-and-launchsettingsjson"></a>Vývoj a launchSettings.jsna

Vývojové prostředí může povolit funkce, které by se neměly zveřejnit v produkčním prostředí. Například šablony ASP.NET Core umožňují [stránku s výjimkou vývojáře](xref:fundamentals/error-handling#developer-exception-page) ve vývojovém prostředí.

Prostředí pro vývoj místních počítačů lze nastavit v *Properties\launchSettings.jsv* souboru projektu. Hodnoty prostředí nastavené v *launchSettings.jspři* přepisování hodnot nastavených v prostředí systému.

*launchSettings.jsv* souboru:

* Se používá pouze v místním vývojovém počítači.
* Není nasazen.
* obsahuje nastavení profilu.

Následující JSON zobrazuje *launchSettings.jsv* souboru pro ASP.NET Core webový projekt s názvem *EnvironmentsSample* vytvořený v aplikaci Visual Studio nebo `dotnet new` :

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettingsCopy.json)]

Předchozí kód obsahuje dva profily:

* `IIS Express`: Výchozí profil, který se používá při spuštění aplikace ze sady Visual Studio. `"commandName"`Klíč má hodnotu `"IISExpress"` , proto je [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) webovým serverem. Můžete nastavit profil spuštění na projekt nebo jakýkoli jiný profil, který je součástí. Například na následujícím obrázku vyberte název projektu, který spustí [webový server Kestrel](xref:fundamentals/servers/kestrel).

  ![IIS Express spustit v nabídce](environments/_static/iisx2.png)
* `EnvironmentsSample`: Název profilu je název projektu. Tento profil se ve výchozím nastavení používá při spouštění aplikace pomocí `dotnet run` .  `"commandName"`Klíč má hodnotu `"Project"` , proto je spuštěn [webový server Kestrel](xref:fundamentals/servers/kestrel) .

Hodnota `commandName` může určovat webový server, který se má spustit. `commandName`může to být jedna z následujících:

* `IISExpress`: Spustí IIS Express.
* `IIS`: Nebyl spuštěn žádný webový server. Očekává se, že služba IIS bude k dispozici.
* `Project`: Spustí Kestrel.

Karta **ladění** vlastností projektu sady Visual Studio poskytuje grafické uživatelské rozhraní pro úpravu *launchSettings.jsv* souboru. Změny provedené v profilech projektu se neprojeví, dokud se webový server nerestartuje. Kestrel se musí restartovat, aby bylo možné zjistit změny provedené ve svém prostředí.

![Vlastnosti projektu nastavení proměnných prostředí](environments/_static/project-properties-debug.png)

Následující *launchSettings.js* souboru obsahuje několik profilů:

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettings.json)]

Můžete vybrat profily:

* Z uživatelského rozhraní sady Visual Studio.
* Pomocí [`dotnet run`](/dotnet/core/tools/dotnet-run) příkazu v příkazovém prostředí s `--launch-profile` možností nastavenou na název profilu. *Tento přístup podporuje jenom Kestrel profily.*

  ```dotnetcli
  dotnet run --launch-profile "SampleApp"
  ```

> [!WARNING]
> *launchSettings.js* by neměl ukládat tajné klíče. [Nástroj Správce tajných](xref:security/app-secrets) klíčů je možné použít k ukládání tajných kódů pro místní vývoj.

Při použití [Visual Studio Code](https://code.visualstudio.com/)lze proměnné prostředí nastavit v souboru *. VSCode/launch.js* . Následující příklad nastaví několik [hodnot konfigurace hostitele proměnné prostředí](xref:fundamentals/host/web-host#host-configuration-values):

[!code-json[](environments/3.1sample/EnvironmentsSample/.vscode/launch.json?range=4-10,32-38)]

Soubor *. VSCode/launch.js* je používán pouze Visual Studio Code.

### <a name="production"></a>Produkce

Provozní prostředí by mělo být nakonfigurované tak, aby maximalizovalo zabezpečení, [výkon](xref:performance/performance-best-practices)a odolnost aplikací. Mezi běžná nastavení, která se liší od vývoje, patří:

* [Ukládání do mezipaměti](xref:performance/caching/memory).
* Prostředky na straně klienta jsou seskupené, minifikovaného a potenciálně poskytované z CDN.
* Chybové stránky diagnostiky jsou zakázané.
* Jsou povoleny popisné chybové stránky.
* [Protokolování](xref:fundamentals/logging/index) a sledování výroby je povolené. Například pomocí [Application Insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Nastavení prostředí

Je často užitečné nastavit konkrétní prostředí pro testování pomocí proměnné prostředí nebo nastavení platformy. Pokud prostředí není nastavené, nastaví se jako výchozí `Production` , což zakáže většinu funkcí ladění. Metoda nastavení prostředí závisí na operačním systému.

Po sestavení hostitele určuje poslední nastavení prostředí načtené aplikací prostředí aplikace. Prostředí aplikace se nedá změnit, když je aplikace spuštěná.

[Stránka about](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) z [ukázkového kódu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) zobrazí hodnotu `IWebHostEnvironment.EnvironmentName` .

### <a name="azure-app-service"></a>Azure App Service

Pokud chcete nastavit prostředí v [Azure App Service](https://azure.microsoft.com/services/app-service/), proveďte následující kroky:

1. Vyberte aplikaci z okna **App Services** .
1. Ve skupině **Nastavení** vyberte okno **Konfigurace** .
1. Na kartě **nastavení aplikace** vyberte možnost **nové nastavení aplikace**.
1. V okně **Přidat nebo upravit nastavení aplikace** zadejte `ASPNETCORE_ENVIRONMENT` **název**. V poli **hodnota**zadejte prostředí (například `Staging` ).
1. Zaškrtněte políčko **nastavení slotu nasazení** , pokud chcete, aby nastavení prostředí zůstalo v aktuální pozici, když jsou sloty nasazení prohozeny. Další informace najdete v tématu [Nastavení přípravného prostředí v Azure App Service](/azure/app-service/web-sites-staged-publishing) v dokumentaci k Azure.
1. Výběrem **OK** zavřete okno **Přidat/upravit nastavení aplikace** .
1. V horní části okna **Konfigurace** vyberte **Uložit** .

Azure App Service automaticky restartuje aplikaci po přidání, změně nebo odstranění nastavení aplikace v Azure Portal.

### <a name="windows"></a>Windows

Hodnoty prostředí v *launchSettings.jspři* přepisování hodnot nastavených v prostředí systému.

K nastavení `ASPNETCORE_ENVIRONMENT` pro aktuální relaci při zahájení aplikace pomocí [spuštění dotnet](/dotnet/core/tools/dotnet-run)se použijí následující příkazy:

**Příkazový řádek**

```console
set ASPNETCORE_ENVIRONMENT=Staging
dotnet run --no-launch-profile
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Staging"
dotnet run --no-launch-profile
```

Předchozí příkaz nastaví `ASPNETCORE_ENVIRONMENT` pouze procesy spouštěné z tohoto příkazového okna.

K nastavení hodnoty globálně ve Windows použijte některý z následujících přístupů:

* Otevřete **ovládací panel** > **System** > **Upřesnit systémová nastavení** a přidejte nebo upravte `ASPNETCORE_ENVIRONMENT` hodnotu:

  ![Rozšířené vlastnosti systému](environments/_static/systemsetting_environment.png)

  ![Proměnná prostředí ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* Otevřete příkazový řádek pro správu a použijte `setx` příkaz nebo otevřete příkazový řádek prostředí PowerShell pro správu a použijte příkaz `[Environment]::SetEnvironmentVariable` :

  **Příkazový řádek**

  ```console
  setx ASPNETCORE_ENVIRONMENT Staging /M
  ```

  `/M`Přepínač označuje nastavení proměnné prostředí na úrovni systému. Pokud `/M` přepínač není použit, je pro uživatelský účet nastavena proměnná prostředí.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Staging", "Machine")
  ```

  `Machine`Hodnota možnosti určuje nastavení proměnné prostředí na úrovni systému. Pokud je hodnota možnosti změněna na `User` , je pro uživatelský účet nastavena proměnná prostředí.

Pokud `ASPNETCORE_ENVIRONMENT` je proměnná prostředí nastavena globálně, projeví se `dotnet run` v jakémkoli příkazovém okně otevřeném po nastavení hodnoty. Hodnoty prostředí v *launchSettings.jspři* přepisování hodnot nastavených v prostředí systému.

**web.config**

Chcete-li nastavit `ASPNETCORE_ENVIRONMENT` proměnnou prostředí pomocí *web.config*, přečtěte si část *nastavení proměnných prostředí* v tématu <xref:host-and-deploy/aspnet-core-module#setting-environment-variables> .

**Projektový soubor nebo profil publikování**

**Pro nasazení služby Windows IIS:** Zahrňte `<EnvironmentName>` vlastnost do [profilu publikování (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu. Tento přístup nastaví prostředí v *web.config* při publikování projektu:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**Pro fond aplikací služby IIS**

Pokud chcete nastavit `ASPNETCORE_ENVIRONMENT` proměnnou prostředí pro aplikaci spuštěnou v izolovaném fondu aplikací (podporovaném ve službě IIS 10,0 nebo novější), přečtěte si část *AppCmd.exe příkazu* v tématu [ &lt; environmentVariables &gt; Variables (prostředí](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) ). Když `ASPNETCORE_ENVIRONMENT` je pro fond aplikací nastavená proměnná prostředí, přepíše její hodnota nastavení na úrovni systému.

Při hostování aplikace ve službě IIS a přidání nebo změny `ASPNETCORE_ENVIRONMENT` proměnné prostředí použijte libovolný z následujících přístupů, aby byla nová hodnota vyzvednuta aplikacemi:

* Spustí `net stop was /y` a pak `net start w3svc` z příkazového řádku.
* Restartujte server.

#### <a name="macos"></a>macOS

Nastavení aktuálního prostředí pro macOS se dá při spuštění aplikace provést na řádku:

```bash
ASPNETCORE_ENVIRONMENT=Staging dotnet run
```

Případně můžete nastavit prostředí pomocí `export` před spuštěním aplikace:

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

Proměnné prostředí na úrovni počítače jsou nastaveny v souboru *. bashrc* nebo *. bash_profile* . Upravte soubor pomocí libovolného textového editoru. Přidejte následující příkaz:

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

#### <a name="linux"></a>Linux

U distribucí systému Linux použijte `export` příkaz na příkazovém řádku pro nastavení proměnné založené na relaci a soubor *bash_profile* pro nastavení prostředí na úrovni počítače.

### <a name="set-the-environment-in-code"></a>Nastavení prostředí v kódu

Zavolejte <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> při sestavování hostitele. Viz třída <xref:fundamentals/host/generic-host#environmentname>.

### <a name="configuration-by-environment"></a>Konfigurace podle prostředí

Pokud chcete načíst konfiguraci podle prostředí, přečtěte si téma <xref:fundamentals/configuration/index#json-configuration-provider> .

## <a name="environment-based-startup-class-and-methods"></a>Třída a metody spouštění založené na prostředí

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a>Vložení IWebHostEnvironment do spouštěcí třídy

Vložit <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do `Startup` konstruktoru. Tento přístup je užitečný, když aplikace vyžaduje konfiguraci `Startup` jenom pro několik prostředí s minimálními rozdíly v kódu pro každé prostředí.

V následujícím příkladu:

* Prostředí se uchovává v `_env` poli.
* `_env`se používá v `ConfigureServices` `Configure` systémech a k aplikování konfigurace spuštění na základě prostředí aplikace.

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupInject.cs?name=snippet&highlight=3-11)]

### <a name="startup-class-conventions"></a>Konvence třídy spouštění

Při spuštění aplikace ASP.NET Core spustí [spouštěcí třída](xref:fundamentals/startup) aplikaci. Aplikace může definovat více `Startup` tříd pro různá prostředí. Příslušná `Startup` Třída je vybrána za běhu. Určuje prioritu třídy, jejíž přípona názvu odpovídá aktuálnímu prostředí. Pokud `Startup{EnvironmentName}` není nalezena shodná třída, `Startup` je použita třída. Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly v kódu pro každé prostředí. Typické aplikace nebudou tento přístup potřebovat.

Chcete-li implementovat třídy založené na prostředí `Startup` , vytvořte `Startup{EnvironmentName}` třídy a záložní `Startup` třídu:

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupClassConventions.cs?name=snippet)]

Použijte přetížení [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) , které přijímá název sestavení:

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Program.cs?name=snippet)]

### <a name="startup-method-conventions"></a>Konvence metody spuštění

[Nakonfigurujte](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) a [ConfigureServiceste](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) podporu pro konkrétní verze formuláře a prostředí `Configure<EnvironmentName>` `Configure<EnvironmentName>Services` . Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly v kódu na prostředí:

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupMethodConventions.cs?name=snippet)]

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* <xref:blazor/fundamentals/environments>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core konfiguruje chování aplikace na základě běhového prostředí pomocí proměnné prostředí.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Prostředí

ASP.NET Core přečte proměnnou prostředí `ASPNETCORE_ENVIRONMENT` při spuštění aplikace a uloží hodnotu do [IHostingEnvironment. Environment](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName). `ASPNETCORE_ENVIRONMENT`lze nastavit na libovolnou hodnotu, ale rozhraní poskytuje tři hodnoty:

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production>výchozí

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

Předcházející kód:

* Volá [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) , pokud `ASPNETCORE_ENVIRONMENT` je nastaven na `Development` .
* Volá [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) , pokud je hodnota `ASPNETCORE_ENVIRONMENT` nastavená na jednu z následujících hodnot:

  * `Staging`
  * `Production`
  * `Staging_2`

[Pomocná značka prostředí](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) používá hodnotu `IHostingEnvironment.EnvironmentName` pro zahrnutí nebo vyloučení značek v elementu:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

V systému Windows a macOS se v proměnných a hodnotách prostředí nerozlišují velká a malá písmena. Ve výchozím nastavení se v proměnných a hodnotách prostředí Linux rozlišují velká a malá písmena.

### <a name="development"></a>Vývoj

Vývojové prostředí může povolit funkce, které by se neměly zveřejnit v produkčním prostředí. Například šablony ASP.NET Core umožňují [stránku s výjimkou vývojáře](xref:fundamentals/error-handling#developer-exception-page) ve vývojovém prostředí.

Prostředí pro vývoj místních počítačů lze nastavit v *Properties\launchSettings.jsv* souboru projektu. Hodnoty prostředí nastavené v *launchSettings.jspři* přepisování hodnot nastavených v prostředí systému.

Následující JSON zobrazuje tři profily z *launchSettings.jsv* souboru:

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> `applicationUrl`Vlastnost v *launchSettings.jsv* může určovat seznam adres URL serveru. Použijte středník mezi adresami URL v seznamu:
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

Když se aplikace spustí pomocí [příkazu dotnet](/dotnet/core/tools/dotnet-run), použije se první profil `"commandName": "Project"` . Hodnota `commandName` Určuje webový server, který se má spustit. `commandName`může to být jedna z následujících:

* `IISExpress`
* `IIS`
* `Project`(která spouští Kestrel)

Když se aplikace spustí s použitím [příkazu dotnet](/dotnet/core/tools/dotnet-run):

* *launchSettings.jszapnuto* je přečteno, pokud je k dispozici. `environmentVariables`nastavení v *launchSettings.jspři* přepisu proměnných prostředí.
* Zobrazí se hostující prostředí.

Následující výstup ukazuje aplikaci spuštěnou pomocí [příkazu dotnet](/dotnet/core/tools/dotnet-run):

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

Karta **ladění** vlastností projektu sady Visual Studio poskytuje grafické uživatelské rozhraní pro úpravu *launchSettings.jsv* souboru:

![Vlastnosti projektu nastavení proměnných prostředí](environments/_static/project-properties-debug.png)

Změny provedené v profilech projektu se neprojeví, dokud se webový server nerestartuje. Kestrel se musí restartovat, aby bylo možné zjistit změny provedené ve svém prostředí.

> [!WARNING]
> *launchSettings.js* by neměl ukládat tajné klíče. [Nástroj Správce tajných](xref:security/app-secrets) klíčů je možné použít k ukládání tajných kódů pro místní vývoj.

Při použití [Visual Studio Code](https://code.visualstudio.com/)lze proměnné prostředí nastavit v souboru *. VSCode/launch.js* . Následující příklad nastaví prostředí na `Development` :

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

A *. VSCode/launch.js* v souboru v projektu se při spouštění aplikace `dotnet run` stejným způsobem jako *vlastnosti/launchSettings.js*nenačte. Při spouštění aplikace ve vývoji, která nemá *launchSettings.jsv* souboru, buď nastavte prostředí s proměnnou prostředí nebo argumentem příkazového řádku pro `dotnet run` příkaz.

### <a name="production"></a>Produkce

Provozní prostředí by mělo být nakonfigurované tak, aby maximalizovalo zabezpečení, výkon a odolnost aplikací. Mezi běžná nastavení, která se liší od vývoje, patří:

* Vyrovnávací.
* Prostředky na straně klienta jsou seskupené, minifikovaného a potenciálně poskytované z CDN.
* Chybové stránky diagnostiky jsou zakázané.
* Jsou povoleny popisné chybové stránky.
* Protokolování a sledování výroby je povolené. Například [Application Insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Nastavení prostředí

Je často užitečné nastavit konkrétní prostředí pro testování pomocí proměnné prostředí nebo nastavení platformy. Pokud prostředí není nastavené, nastaví se jako výchozí `Production` , což zakáže většinu funkcí ladění. Metoda nastavení prostředí závisí na operačním systému.

Po sestavení hostitele určuje poslední nastavení prostředí načtené aplikací prostředí aplikace. Prostředí aplikace se nedá změnit, když je aplikace spuštěná.

### <a name="environment-variable-or-platform-setting"></a>Nastavení proměnné prostředí nebo platformy

#### <a name="azure-app-service"></a>Azure App Service

Pokud chcete nastavit prostředí v [Azure App Service](https://azure.microsoft.com/services/app-service/), proveďte následující kroky:

1. Vyberte aplikaci z okna **App Services** .
1. Ve skupině **Nastavení** vyberte okno **Konfigurace** .
1. Na kartě **nastavení aplikace** vyberte možnost **nové nastavení aplikace**.
1. V okně **Přidat nebo upravit nastavení aplikace** zadejte `ASPNETCORE_ENVIRONMENT` **název**. V poli **hodnota**zadejte prostředí (například `Staging` ).
1. Zaškrtněte políčko **nastavení slotu nasazení** , pokud chcete, aby nastavení prostředí zůstalo v aktuální pozici, když jsou sloty nasazení prohozeny. Další informace najdete v tématu [Nastavení přípravného prostředí v Azure App Service](/azure/app-service/web-sites-staged-publishing) v dokumentaci k Azure.
1. Výběrem **OK** zavřete okno **Přidat/upravit nastavení aplikace** .
1. V horní části okna **Konfigurace** vyberte **Uložit** .

Azure App Service automaticky restartuje aplikaci po přidání, změně nebo odstranění nastavení aplikace (proměnná prostředí) v Azure Portal.

#### <a name="windows"></a>Windows

K nastavení `ASPNETCORE_ENVIRONMENT` pro aktuální relaci při zahájení aplikace pomocí [spuštění dotnet](/dotnet/core/tools/dotnet-run)se použijí následující příkazy:

**Příkazový řádek**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

Tyto příkazy se projeví pouze pro aktuální okno. Po zavření okna se `ASPNETCORE_ENVIRONMENT` nastavení vrátí k výchozímu nastavení nebo hodnotě počítače.

K nastavení hodnoty globálně ve Windows použijte některý z následujících přístupů:

* Otevřete **ovládací panel** > **System** > **Upřesnit systémová nastavení** a přidejte nebo upravte `ASPNETCORE_ENVIRONMENT` hodnotu:

  ![Rozšířené vlastnosti systému](environments/_static/systemsetting_environment.png)

  ![Proměnná prostředí ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* Otevřete příkazový řádek pro správu a použijte `setx` příkaz nebo otevřete příkazový řádek prostředí PowerShell pro správu a použijte příkaz `[Environment]::SetEnvironmentVariable` :

  **Příkazový řádek**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  `/M`Přepínač označuje nastavení proměnné prostředí na úrovni systému. Pokud `/M` přepínač není použit, je pro uživatelský účet nastavena proměnná prostředí.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  `Machine`Hodnota možnosti určuje nastavení proměnné prostředí na úrovni systému. Pokud je hodnota možnosti změněna na `User` , je pro uživatelský účet nastavena proměnná prostředí.

Pokud `ASPNETCORE_ENVIRONMENT` je proměnná prostředí nastavena globálně, projeví se `dotnet run` v jakémkoli příkazovém okně otevřeném po nastavení hodnoty.

**web.config**

Chcete-li nastavit `ASPNETCORE_ENVIRONMENT` proměnnou prostředí pomocí *web.config*, přečtěte si část *nastavení proměnných prostředí* v tématu <xref:host-and-deploy/aspnet-core-module#setting-environment-variables> .

**Projektový soubor nebo profil publikování**

**Pro nasazení služby Windows IIS:** Zahrňte `<EnvironmentName>` vlastnost do [profilu publikování (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu. Tento přístup nastaví prostředí v *web.config* při publikování projektu:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**Pro fond aplikací služby IIS**

Pokud chcete nastavit `ASPNETCORE_ENVIRONMENT` proměnnou prostředí pro aplikaci spuštěnou v izolovaném fondu aplikací (podporovaném ve službě IIS 10,0 nebo novější), přečtěte si část *AppCmd.exe příkazu* v tématu [ &lt; environmentVariables &gt; Variables (prostředí](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) ). Když `ASPNETCORE_ENVIRONMENT` je pro fond aplikací nastavená proměnná prostředí, přepíše její hodnota nastavení na úrovni systému.

> [!IMPORTANT]
> Při hostování aplikace ve službě IIS a přidání nebo změny `ASPNETCORE_ENVIRONMENT` proměnné prostředí použijte libovolný z následujících přístupů, aby byla nová hodnota vyzvednuta aplikacemi:
>
> * Spustí `net stop was /y` a pak `net start w3svc` z příkazového řádku.
> * Restartujte server.

#### <a name="macos"></a>macOS

Nastavení aktuálního prostředí pro macOS se dá při spuštění aplikace provést na řádku:

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

Případně můžete nastavit prostředí pomocí `export` před spuštěním aplikace:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

Proměnné prostředí na úrovni počítače jsou nastaveny v souboru *. bashrc* nebo *. bash_profile* . Upravte soubor pomocí libovolného textového editoru. Přidejte následující příkaz:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a>Linux

U distribucí systému Linux použijte `export` příkaz na příkazovém řádku pro nastavení proměnné založené na relaci a soubor *bash_profile* pro nastavení prostředí na úrovni počítače.

### <a name="set-the-environment-in-code"></a>Nastavení prostředí v kódu

Zavolejte <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> při sestavování hostitele. Viz třída <xref:fundamentals/host/web-host#environment>.

### <a name="configuration-by-environment"></a>Konfigurace podle prostředí

K načtení konfigurace podle prostředí doporučujeme:

* soubory *appSettings* (*appSettings. { Environment}. JSON*. Viz třída <xref:fundamentals/configuration/index#json-configuration-provider>.
* Proměnné prostředí (nastavené v každém systému, ve kterém je aplikace hostovaná). Viz <xref:fundamentals/host/web-host#environment> a <xref:security/app-secrets#environment-variables> .
* Správce tajných klíčů (jenom ve vývojovém prostředí). Viz třída <xref:security/app-secrets>.

## <a name="environment-based-startup-class-and-methods"></a>Třída a metody spouštění založené na prostředí

### <a name="inject-ihostingenvironment-into-startupconfigure"></a>Vložení IHostingEnvironment do Startup.Configurovat

Vložit <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> do `Startup.Configure` . Tento přístup je užitečný, když aplikace vyžaduje jenom konfiguraci `Startup.Configure` jenom pro několik prostředí s minimálními rozdíly v kódu pro každé prostředí.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-ihostingenvironment-into-the-startup-class"></a>Vložení IHostingEnvironment do spouštěcí třídy

Vložení <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> do `Startup` konstruktoru a přiřazení služby k poli pro použití v rámci `Startup` třídy. Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spouštění jenom pro několik prostředí s minimálními rozdíly v kódu pro každé prostředí.

V následujícím příkladu:

* Prostředí se uchovává v `_env` poli.
* `_env`se používá v `ConfigureServices` `Configure` systémech a k aplikování konfigurace spuštění na základě prostředí aplikace.

```csharp
public class Startup
{
    private readonly IHostingEnvironment _env;

    public Startup(IHostingEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```

### <a name="startup-class-conventions"></a>Konvence třídy spouštění

Při spuštění aplikace ASP.NET Core spustí [spouštěcí třída](xref:fundamentals/startup) aplikaci. Aplikace může definovat samostatné `Startup` třídy pro různá prostředí (například `StartupDevelopment` ). Příslušná `Startup` Třída je vybrána za běhu. Určuje prioritu třídy, jejíž přípona názvu odpovídá aktuálnímu prostředí. Pokud `Startup{EnvironmentName}` není nalezena shodná třída, `Startup` je použita třída. Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly v kódu pro každé prostředí.

Chcete-li implementovat třídy založené na prostředí `Startup` , vytvořte `Startup{EnvironmentName}` třídu pro každé používané prostředí a záložní `Startup` třídu:

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

Použijte přetížení [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) , které přijímá název sestavení:

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a>Konvence metody spuštění

[Nakonfigurujte](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) a [ConfigureServiceste](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) podporu pro konkrétní verze formuláře a prostředí `Configure<EnvironmentName>` `Configure<EnvironmentName>Services` . Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly v kódu pro každé prostředí.

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>Další materiály

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
