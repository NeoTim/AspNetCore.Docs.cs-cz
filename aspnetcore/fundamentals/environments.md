---
title: Použití více prostředí v ASP.NET Core
author: rick-anderson
description: Naučte se řídit chování aplikace v různých prostředích aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/17/2019
uid: fundamentals/environments
ms.openlocfilehash: b0218b2c77c283c0849dca9491046534b88c5a77
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78656217"
---
# <a name="use-multiple-environments-in-aspnet-core"></a>Použití více prostředí v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core konfiguruje chování aplikace na základě běhového prostředí pomocí proměnné prostředí.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Prostředí

ASP.NET Core přečte proměnnou prostředí `ASPNETCORE_ENVIRONMENT` při spuštění aplikace a uloží hodnotu v [IWebHostEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName). `ASPNETCORE_ENVIRONMENT` lze nastavit na libovolnou hodnotu, ale rozhraní poskytuje tři hodnoty:

* <xref:Microsoft.Extensions.Hosting.Environments.Development>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <xref:Microsoft.Extensions.Hosting.Environments.Production> (výchozí)

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

Předchozí kód:

* Volá [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) , když je `ASPNETCORE_ENVIRONMENT` nastaveno na `Development`.
* Volá [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) , pokud je hodnota `ASPNETCORE_ENVIRONMENT` nastavena na jednu z následujících hodnot:

  * `Staging`
  * `Production`
  * `Staging_2`

[Pomocná značka prostředí](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) používá hodnotu `IHostingEnvironment.EnvironmentName` k zahrnutí nebo vyloučení značek v elementu:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

V systému Windows a macOS hodnoty proměnných prostředí a hodnot nerozlišují velká a malá písmena. Ve výchozím nastavení se v proměnných a hodnotách prostředí Linux **rozlišují velká a malá písmena** .

### <a name="development"></a>Vývoj

Vývojové prostředí může povolit funkce, které by se neměly zveřejnit v produkčním prostředí. Například šablony ASP.NET Core umožňují [stránku s výjimkou vývojáře](xref:fundamentals/error-handling#developer-exception-page) ve vývojovém prostředí.

Prostředí pro vývoj místních počítačů lze nastavit v souboru *Properties\launchSettings.JSON* projektu. Hodnoty prostředí nastavené v hodnotách přepisu *launchSettings. JSON* nastavené v prostředí systému

Následující JSON zobrazuje tři profily ze souboru *launchSettings. JSON* :

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
> Vlastnost `applicationUrl` v *launchSettings. JSON* může určovat seznam adres URL serveru. Použijte středník mezi adresami URL v seznamu:
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

Když se aplikace spustí pomocí [příkazu dotnet](/dotnet/core/tools/dotnet-run), použije se první profil s `"commandName": "Project"`. Hodnota `commandName` určuje webový server, který se má spustit. `commandName` může být jedna z následujících:

* `IISExpress`
* `IIS`
* `Project` (spouští Kestrel)

Když se aplikace spustí s použitím [příkazu dotnet](/dotnet/core/tools/dotnet-run):

* *launchSettings. JSON* je přečtený, pokud je k dispozici. nastavení `environmentVariables` v proměnných prostředí přepsat *launchSettings. JSON* .
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

Karta **ladění** vlastností projektu sady Visual Studio poskytuje grafické uživatelské rozhraní pro úpravy souboru *launchSettings. JSON* :

![Vlastnosti projektu nastavení proměnných prostředí](environments/_static/project-properties-debug.png)

Změny provedené v profilech projektu se neprojeví, dokud se webový server nerestartuje. Kestrel se musí restartovat, aby bylo možné zjistit změny provedené ve svém prostředí.

> [!WARNING]
> *launchSettings. JSON* by neměl ukládat tajné klíče. [Nástroj Správce tajných](xref:security/app-secrets) klíčů je možné použít k ukládání tajných kódů pro místní vývoj.

Při použití [Visual Studio Code](https://code.visualstudio.com/)lze proměnné prostředí nastavit v souboru *. VSCode/Launch. JSON* . Následující příklad nastaví prostředí na `Development`:

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

Soubor *. VSCode/Launch. JSON* v projektu není čten při spuštění aplikace s `dotnet run` stejným způsobem jako *vlastnosti/launchSettings. JSON*. Při spouštění aplikace ve vývoji, která nemá soubor *launchSettings. JSON* , buď nastavte prostředí s proměnnou prostředí nebo argumentem příkazového řádku na příkaz `dotnet run`.

### <a name="production"></a>Výroba

Provozní prostředí by mělo být nakonfigurované tak, aby maximalizovalo zabezpečení, výkon a odolnost aplikací. Mezi běžná nastavení, která se liší od vývoje, patří:

* Vyrovnávací.
* Prostředky na straně klienta jsou seskupené, minifikovaného a potenciálně poskytované z CDN.
* Chybové stránky diagnostiky jsou zakázané.
* Jsou povoleny popisné chybové stránky.
* Protokolování a sledování výroby je povolené. Například [Application Insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Nastavení prostředí

Je často užitečné nastavit konkrétní prostředí pro testování pomocí proměnné prostředí nebo nastavení platformy. Pokud prostředí není nastavené, nastaví se jako výchozí `Production`, což zakáže většinu funkcí ladění. Metoda nastavení prostředí závisí na operačním systému.

Po sestavení hostitele určuje poslední nastavení prostředí načtené aplikací prostředí aplikace. Prostředí aplikace se nedá změnit, když je aplikace spuštěná.

### <a name="environment-variable-or-platform-setting"></a>Nastavení proměnné prostředí nebo platformy

#### <a name="azure-app-service"></a>Azure App Service

Pokud chcete nastavit prostředí v [Azure App Service](https://azure.microsoft.com/services/app-service/), proveďte následující kroky:

1. Vyberte aplikaci z okna **App Services** .
1. Ve skupině **Nastavení** vyberte okno **Konfigurace** .
1. Na kartě **nastavení aplikace** vyberte možnost **nové nastavení aplikace**.
1. V okně **Přidat/upravit nastavení aplikace** zadejte `ASPNETCORE_ENVIRONMENT` pro **název**. V poli **hodnota**zadejte prostředí (například `Staging`).
1. Zaškrtněte políčko **nastavení slotu nasazení** , pokud chcete, aby nastavení prostředí zůstalo v aktuální pozici, když jsou sloty nasazení prohozeny. Další informace najdete v tématu [Nastavení přípravného prostředí v Azure App Service](/azure/app-service/web-sites-staged-publishing) v dokumentaci k Azure.
1. Výběrem **OK** zavřete okno **Přidat/upravit nastavení aplikace** .
1. V horní části okna **Konfigurace** vyberte **Uložit** .

Azure App Service automaticky restartuje aplikaci po přidání, změně nebo odstranění nastavení aplikace (proměnná prostředí) v Azure Portal.

#### <a name="windows"></a>Windows

Chcete-li nastavit `ASPNETCORE_ENVIRONMENT` pro aktuální relaci při zahájení aplikace pomocí [příkazu dotnet](/dotnet/core/tools/dotnet-run), jsou použity následující příkazy:

**Příkazový řádek**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

Tyto příkazy se projeví pouze pro aktuální okno. Po zavření okna se nastavení `ASPNETCORE_ENVIRONMENT` vrátí k výchozímu nastavení nebo hodnotě počítače.

K nastavení hodnoty globálně ve Windows použijte některý z následujících přístupů:

* Otevřete **Ovládací panely** > **systémové** > **Pokročilé nastavení systému** a přidejte nebo upravte `ASPNETCORE_ENVIRONMENT`ovou hodnotu:

  ![Rozšířené vlastnosti systému](environments/_static/systemsetting_environment.png)

  ![Proměnná prostředí ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* Otevřete příkazový řádek pro správu a použijte příkaz `setx` nebo otevřete příkazový řádek prostředí PowerShell pro správu a použijte `[Environment]::SetEnvironmentVariable`:

  **Příkazový řádek**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  Přepínač `/M` označuje nastavení proměnné prostředí na úrovni systému. Pokud není použit přepínač `/M`, je pro uživatelský účet nastavena proměnná prostředí.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  Hodnota možnosti `Machine` označuje, že se má nastavit proměnná prostředí na úrovni systému. Pokud je hodnota možnosti změněna na `User`, je pro uživatelský účet nastavena proměnná prostředí.

Pokud je proměnná prostředí `ASPNETCORE_ENVIRONMENT` nastavena globálně, projeví se `dotnet run` v jakémkoli příkazovém okně otevřeném po nastavení hodnoty.

**Web. config**

Chcete-li nastavit proměnnou prostředí `ASPNETCORE_ENVIRONMENT` pomocí *souboru Web. config*, přečtěte si část *nastavení proměnných prostředí* v <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.

**Projektový soubor nebo profil publikování**

**Pro nasazení služby Windows IIS:** Do [profilu publikování (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu zahrňte vlastnost `<EnvironmentName>`. Tento přístup nastaví prostředí v *souboru Web. config* při publikování projektu:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**Pro fond aplikací služby IIS**

Pokud chcete nastavit proměnnou prostředí `ASPNETCORE_ENVIRONMENT` pro aplikaci spuštěnou v izolovaném fondu aplikací (podporovaném ve službě IIS 10,0 nebo novější), přečtěte si část *příkazového řádku Appcmd. exe* v tématu [proměnné prostředí &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) . Pokud je pro fond aplikací nastavená proměnná prostředí `ASPNETCORE_ENVIRONMENT`, přepíše její hodnota nastavení na úrovni systému.

> [!IMPORTANT]
> Při hostování aplikace ve službě IIS a přidání nebo změny proměnné prostředí `ASPNETCORE_ENVIRONMENT` použijte libovolný z následujících přístupů, aby byla nová hodnota vyzvednuta aplikacemi:
>
> * Spustí `net stop was /y` následovaný `net start w3svc` z příkazového řádku.
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

Pro Linux distribuce použijte na příkazovém řádku příkaz `export` pro nastavení proměnné založené na relaci a soubor *bash_profile* pro nastavení prostředí na úrovni počítače.

### <a name="set-the-environment-in-code"></a>Nastavení prostředí v kódu

Při sestavování hostitele volejte <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*>. Viz třída <xref:fundamentals/host/generic-host#environmentname>.


### <a name="configuration-by-environment"></a>Konfigurace podle prostředí

K načtení konfigurace podle prostředí doporučujeme:

* soubory *appSettings* (*appSettings. { Environment}. JSON*. Viz třída <xref:fundamentals/configuration/index#json-configuration-provider>.
* Proměnné prostředí (nastavené v každém systému, ve kterém je aplikace hostovaná). Viz témata <xref:fundamentals/host/generic-host#environmentname> a <xref:security/app-secrets#environment-variables>.
* Správce tajných klíčů (jenom ve vývojovém prostředí). Viz třída <xref:security/app-secrets>.

## <a name="environment-based-startup-class-and-methods"></a>Třída a metody spouštění založené na prostředí

### <a name="inject-iwebhostenvironment-into-startupconfigure"></a>Vloží IWebHostEnvironment do Startup. Configure

Vloží <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do `Startup.Configure`. Tento přístup je užitečný, když aplikace vyžaduje úpravu `Startup.Configure` jenom pro několik prostředí s minimálními rozdíly v kódu na prostředí.

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
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

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a>Vložení IWebHostEnvironment do spouštěcí třídy

Vloží <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do konstruktoru `Startup`. Tento přístup je užitečný, když aplikace vyžaduje konfiguraci `Startup` jenom pro několik prostředí s minimálními rozdíly v kódu pro každé prostředí.

V následujícím příkladu:

* Prostředí se uchovává v poli `_env`.
* `_env` se používá v `ConfigureServices` a `Configure` pro použití konfigurace spuštění na základě prostředí aplikace.

```csharp
public class Startup
{
    private readonly IWebHostEnvironment _env;

    public Startup(IWebHostEnvironment env)
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

Při spuštění aplikace ASP.NET Core spustí [spouštěcí třída](xref:fundamentals/startup) aplikaci. Aplikace může definovat samostatné třídy `Startup` pro různá prostředí (například `StartupDevelopment`). Příslušná třída `Startup` je vybrána za běhu. Třída, jejíž název má příponu odpovídající aktuálnímu prostředí, je upřednostněna. Pokud není nalezena shodná `Startup{EnvironmentName}` třída, je použita třída `Startup`. Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly v kódu pro každé prostředí.

Chcete-li implementovat třídy `Startup` založené na prostředí, vytvořte třídu `Startup{EnvironmentName}` pro každé používané prostředí a záložní třídu `Startup`:

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

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

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

[Nakonfigurujte](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) a [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) , které podporují verze formuláře `Configure<EnvironmentName>` a `Configure<EnvironmentName>Services`na konkrétní prostředí. Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly v kódu pro každé prostředí.

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core konfiguruje chování aplikace na základě běhového prostředí pomocí proměnné prostředí.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Prostředí

ASP.NET Core přečte proměnnou prostředí `ASPNETCORE_ENVIRONMENT` při spuštění aplikace a uloží hodnotu v [IHostingEnvironment. Environment](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName). `ASPNETCORE_ENVIRONMENT` lze nastavit na libovolnou hodnotu, ale rozhraní poskytuje tři hodnoty:

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (výchozí)

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

Předchozí kód:

* Volá [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) , když je `ASPNETCORE_ENVIRONMENT` nastaveno na `Development`.
* Volá [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) , pokud je hodnota `ASPNETCORE_ENVIRONMENT` nastavena na jednu z následujících hodnot:

  * `Staging`
  * `Production`
  * `Staging_2`

[Pomocná značka prostředí](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) používá hodnotu `IHostingEnvironment.EnvironmentName` k zahrnutí nebo vyloučení značek v elementu:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

V systému Windows a macOS hodnoty proměnných prostředí a hodnot nerozlišují velká a malá písmena. Ve výchozím nastavení se v proměnných a hodnotách prostředí Linux **rozlišují velká a malá písmena** .

### <a name="development"></a>Vývoj

Vývojové prostředí může povolit funkce, které by se neměly zveřejnit v produkčním prostředí. Například šablony ASP.NET Core umožňují [stránku s výjimkou vývojáře](xref:fundamentals/error-handling#developer-exception-page) ve vývojovém prostředí.

Prostředí pro vývoj místních počítačů lze nastavit v souboru *Properties\launchSettings.JSON* projektu. Hodnoty prostředí nastavené v hodnotách přepisu *launchSettings. JSON* nastavené v prostředí systému

Následující JSON zobrazuje tři profily ze souboru *launchSettings. JSON* :

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
> Vlastnost `applicationUrl` v *launchSettings. JSON* může určovat seznam adres URL serveru. Použijte středník mezi adresami URL v seznamu:
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

Když se aplikace spustí pomocí [příkazu dotnet](/dotnet/core/tools/dotnet-run), použije se první profil s `"commandName": "Project"`. Hodnota `commandName` určuje webový server, který se má spustit. `commandName` může být jedna z následujících:

* `IISExpress`
* `IIS`
* `Project` (spouští Kestrel)

Když se aplikace spustí s použitím [příkazu dotnet](/dotnet/core/tools/dotnet-run):

* *launchSettings. JSON* je přečtený, pokud je k dispozici. nastavení `environmentVariables` v proměnných prostředí přepsat *launchSettings. JSON* .
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

Karta **ladění** vlastností projektu sady Visual Studio poskytuje grafické uživatelské rozhraní pro úpravy souboru *launchSettings. JSON* :

![Vlastnosti projektu nastavení proměnných prostředí](environments/_static/project-properties-debug.png)

Změny provedené v profilech projektu se neprojeví, dokud se webový server nerestartuje. Kestrel se musí restartovat, aby bylo možné zjistit změny provedené ve svém prostředí.

> [!WARNING]
> *launchSettings. JSON* by neměl ukládat tajné klíče. [Nástroj Správce tajných](xref:security/app-secrets) klíčů je možné použít k ukládání tajných kódů pro místní vývoj.

Při použití [Visual Studio Code](https://code.visualstudio.com/)lze proměnné prostředí nastavit v souboru *. VSCode/Launch. JSON* . Následující příklad nastaví prostředí na `Development`:

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

Soubor *. VSCode/Launch. JSON* v projektu není čten při spuštění aplikace s `dotnet run` stejným způsobem jako *vlastnosti/launchSettings. JSON*. Při spouštění aplikace ve vývoji, která nemá soubor *launchSettings. JSON* , buď nastavte prostředí s proměnnou prostředí nebo argumentem příkazového řádku na příkaz `dotnet run`.

### <a name="production"></a>Výroba

Provozní prostředí by mělo být nakonfigurované tak, aby maximalizovalo zabezpečení, výkon a odolnost aplikací. Mezi běžná nastavení, která se liší od vývoje, patří:

* Vyrovnávací.
* Prostředky na straně klienta jsou seskupené, minifikovaného a potenciálně poskytované z CDN.
* Chybové stránky diagnostiky jsou zakázané.
* Jsou povoleny popisné chybové stránky.
* Protokolování a sledování výroby je povolené. Například [Application Insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Nastavení prostředí

Je často užitečné nastavit konkrétní prostředí pro testování pomocí proměnné prostředí nebo nastavení platformy. Pokud prostředí není nastavené, nastaví se jako výchozí `Production`, což zakáže většinu funkcí ladění. Metoda nastavení prostředí závisí na operačním systému.

Po sestavení hostitele určuje poslední nastavení prostředí načtené aplikací prostředí aplikace. Prostředí aplikace se nedá změnit, když je aplikace spuštěná.

### <a name="environment-variable-or-platform-setting"></a>Nastavení proměnné prostředí nebo platformy

#### <a name="azure-app-service"></a>Azure App Service

Pokud chcete nastavit prostředí v [Azure App Service](https://azure.microsoft.com/services/app-service/), proveďte následující kroky:

1. Vyberte aplikaci z okna **App Services** .
1. Ve skupině **Nastavení** vyberte okno **Konfigurace** .
1. Na kartě **nastavení aplikace** vyberte možnost **nové nastavení aplikace**.
1. V okně **Přidat/upravit nastavení aplikace** zadejte `ASPNETCORE_ENVIRONMENT` pro **název**. V poli **hodnota**zadejte prostředí (například `Staging`).
1. Zaškrtněte políčko **nastavení slotu nasazení** , pokud chcete, aby nastavení prostředí zůstalo v aktuální pozici, když jsou sloty nasazení prohozeny. Další informace najdete v tématu [Nastavení přípravného prostředí v Azure App Service](/azure/app-service/web-sites-staged-publishing) v dokumentaci k Azure.
1. Výběrem **OK** zavřete okno **Přidat/upravit nastavení aplikace** .
1. V horní části okna **Konfigurace** vyberte **Uložit** .

Azure App Service automaticky restartuje aplikaci po přidání, změně nebo odstranění nastavení aplikace (proměnná prostředí) v Azure Portal.

#### <a name="windows"></a>Windows

Chcete-li nastavit `ASPNETCORE_ENVIRONMENT` pro aktuální relaci při zahájení aplikace pomocí [příkazu dotnet](/dotnet/core/tools/dotnet-run), jsou použity následující příkazy:

**Příkazový řádek**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

Tyto příkazy se projeví pouze pro aktuální okno. Po zavření okna se nastavení `ASPNETCORE_ENVIRONMENT` vrátí k výchozímu nastavení nebo hodnotě počítače.

K nastavení hodnoty globálně ve Windows použijte některý z následujících přístupů:

* Otevřete **Ovládací panely** > **systémové** > **Pokročilé nastavení systému** a přidejte nebo upravte `ASPNETCORE_ENVIRONMENT`ovou hodnotu:

  ![Rozšířené vlastnosti systému](environments/_static/systemsetting_environment.png)

  ![Proměnná prostředí ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* Otevřete příkazový řádek pro správu a použijte příkaz `setx` nebo otevřete příkazový řádek prostředí PowerShell pro správu a použijte `[Environment]::SetEnvironmentVariable`:

  **Příkazový řádek**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  Přepínač `/M` označuje nastavení proměnné prostředí na úrovni systému. Pokud není použit přepínač `/M`, je pro uživatelský účet nastavena proměnná prostředí.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  Hodnota možnosti `Machine` označuje, že se má nastavit proměnná prostředí na úrovni systému. Pokud je hodnota možnosti změněna na `User`, je pro uživatelský účet nastavena proměnná prostředí.

Pokud je proměnná prostředí `ASPNETCORE_ENVIRONMENT` nastavena globálně, projeví se `dotnet run` v jakémkoli příkazovém okně otevřeném po nastavení hodnoty.

**Web. config**

Chcete-li nastavit proměnnou prostředí `ASPNETCORE_ENVIRONMENT` pomocí *souboru Web. config*, přečtěte si část *nastavení proměnných prostředí* v <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.

**Projektový soubor nebo profil publikování**

**Pro nasazení služby Windows IIS:** Do [profilu publikování (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu zahrňte vlastnost `<EnvironmentName>`. Tento přístup nastaví prostředí v *souboru Web. config* při publikování projektu:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**Pro fond aplikací služby IIS**

Pokud chcete nastavit proměnnou prostředí `ASPNETCORE_ENVIRONMENT` pro aplikaci spuštěnou v izolovaném fondu aplikací (podporovaném ve službě IIS 10,0 nebo novější), přečtěte si část *příkazového řádku Appcmd. exe* v tématu [proměnné prostředí &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) . Pokud je pro fond aplikací nastavená proměnná prostředí `ASPNETCORE_ENVIRONMENT`, přepíše její hodnota nastavení na úrovni systému.

> [!IMPORTANT]
> Při hostování aplikace ve službě IIS a přidání nebo změny proměnné prostředí `ASPNETCORE_ENVIRONMENT` použijte libovolný z následujících přístupů, aby byla nová hodnota vyzvednuta aplikacemi:
>
> * Spustí `net stop was /y` následovaný `net start w3svc` z příkazového řádku.
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

Pro Linux distribuce použijte na příkazovém řádku příkaz `export` pro nastavení proměnné založené na relaci a soubor *bash_profile* pro nastavení prostředí na úrovni počítače.

### <a name="set-the-environment-in-code"></a>Nastavení prostředí v kódu

Při sestavování hostitele volejte <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*>. Viz třída <xref:fundamentals/host/web-host#environment>.

### <a name="configuration-by-environment"></a>Konfigurace podle prostředí

K načtení konfigurace podle prostředí doporučujeme:

* soubory *appSettings* (*appSettings. { Environment}. JSON*. Viz třída <xref:fundamentals/configuration/index#json-configuration-provider>.
* Proměnné prostředí (nastavené v každém systému, ve kterém je aplikace hostovaná). Viz témata <xref:fundamentals/host/web-host#environment> a <xref:security/app-secrets#environment-variables>.
* Správce tajných klíčů (jenom ve vývojovém prostředí). Viz třída <xref:security/app-secrets>.

## <a name="environment-based-startup-class-and-methods"></a>Třída a metody spouštění založené na prostředí

### <a name="inject-ihostingenvironment-into-startupconfigure"></a>Vloží IHostingEnvironment do Startup. Configure

Vloží <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> do `Startup.Configure`. Tento přístup je užitečný, když aplikace vyžaduje jenom konfiguraci `Startup.Configure` jenom pro několik prostředí s minimálními rozdíly v kódu pro každé prostředí.

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

Vložení <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> do konstruktoru `Startup` a přiřazení služby k poli pro použití v rámci třídy `Startup`. Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spouštění jenom pro několik prostředí s minimálními rozdíly v kódu pro každé prostředí.

V následujícím příkladu:

* Prostředí se uchovává v poli `_env`.
* `_env` se používá v `ConfigureServices` a `Configure` pro použití konfigurace spuštění na základě prostředí aplikace.

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

Při spuštění aplikace ASP.NET Core spustí [spouštěcí třída](xref:fundamentals/startup) aplikaci. Aplikace může definovat samostatné třídy `Startup` pro různá prostředí (například `StartupDevelopment`). Příslušná třída `Startup` je vybrána za běhu. Třída, jejíž název má příponu odpovídající aktuálnímu prostředí, je upřednostněna. Pokud není nalezena shodná `Startup{EnvironmentName}` třída, je použita třída `Startup`. Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly v kódu pro každé prostředí.

Chcete-li implementovat třídy `Startup` založené na prostředí, vytvořte třídu `Startup{EnvironmentName}` pro každé používané prostředí a záložní třídu `Startup`:

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

[Nakonfigurujte](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) a [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) , které podporují verze formuláře `Configure<EnvironmentName>` a `Configure<EnvironmentName>Services`na konkrétní prostředí. Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly v kódu pro každé prostředí.

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
