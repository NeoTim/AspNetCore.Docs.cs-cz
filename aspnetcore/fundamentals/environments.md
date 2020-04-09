---
title: Používání více prostředí v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak řídit chování aplikací ve více prostředích v ASP.NET základních aplikacích.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/17/2019
uid: fundamentals/environments
ms.openlocfilehash: b0218b2c77c283c0849dca9491046534b88c5a77
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656217"
---
# <a name="use-multiple-environments-in-aspnet-core"></a>Používání více prostředí v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core konfiguruje chování aplikací na základě runtime prostředí pomocí proměnné prostředí.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="environments"></a>Prostředí

ASP.NET Core přečte `ASPNETCORE_ENVIRONMENT` proměnnou prostředí při spuštění aplikace a uloží hodnotu do [iWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName). `ASPNETCORE_ENVIRONMENT`lze nastavit na libovolnou hodnotu, ale rámci jsou uvedeny tři hodnoty:

* <xref:Microsoft.Extensions.Hosting.Environments.Development>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <xref:Microsoft.Extensions.Hosting.Environments.Production>(výchozí)

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

Předcházející kód:

* Volání [UseDeveloperExceptionPage,](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) `ASPNETCORE_ENVIRONMENT` pokud `Development`je nastavena na .
* Volá [UseExceptionHandler,](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) pokud `ASPNETCORE_ENVIRONMENT` je nastavena hodnota jedné z následujících možností:

  * `Staging`
  * `Production`
  * `Staging_2`

Pomocník [značky prostředí](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) používá `IHostingEnvironment.EnvironmentName` hodnotu zahrnout nebo vyloučit značky v prvku:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

Ve Windows a macOS nerozlišují proměnné a hodnoty prostředí. Proměnné a hodnoty prostředí Linuxu jsou ve výchozím nastavení **rozlišovány malá a velká písmena.**

### <a name="development"></a>Vývoj

Vývojové prostředí může povolit funkce, které by neměly být vystaveny v produkčním prostředí. Například ASP.NET základní šablony umožňují [stránku výjimky pro vývojáře](xref:fundamentals/error-handling#developer-exception-page) ve vývojovém prostředí.

Prostředí pro vývoj místního počítače lze nastavit v souboru *Properties\launchSettings.json* projektu. Hodnoty prostředí nastavené v *launchSettings.json* přepíší hodnoty nastavené v systémovém prostředí.

Následující JSON zobrazuje tři profily ze souboru *launchSettings.json:*

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
> Vlastnost `applicationUrl` v *launchSettings.json* můžete zadat seznam adres URL serveru. Použijte středník mezi adresami URL v seznamu:
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

Při spuštění aplikace s [dotnet spustit](/dotnet/core/tools/dotnet-run), `"commandName": "Project"` první profil s se používá. Hodnota `commandName` určuje webový server, který má být spuštěn. `commandName`může být některý z následujících:

* `IISExpress`
* `IIS`
* `Project`(který spouští Kestrel)

Při spuštění aplikace s [dotnet spustit](/dotnet/core/tools/dotnet-run):

* *launchSettings.json* je přečten, pokud je k dispozici. `environmentVariables`nastavení v *launchSettings.json* přepsat proměnné prostředí.
* Zobrazí se hostitelské prostředí.

Následující výstup ukazuje aplikaci spuštěnou [s dotnet run](/dotnet/core/tools/dotnet-run):

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

Karta **Ladění** vlastností projektu sady Visual Studio poskytuje grafické uživatelské rozhraní pro úpravu souboru *launchSettings.json:*

![Vlastnosti projektu Nastavení proměnných prostředí](environments/_static/project-properties-debug.png)

Změny provedené v profilech projektu se nemusí projevit, dokud nebude webový server restartován. Kestrel musí být restartován předtím, než může rozpoznat změny provedené v jeho prostředí.

> [!WARNING]
> *launchSettings.json* by neměl ukládat tajné klíče. [Nástroj Správce tajných barev](xref:security/app-secrets) lze použít k ukládání tajných kódů pro místní vývoj.

Při použití [kódu sady Visual Studio](https://code.visualstudio.com/)lze proměnné prostředí nastavit v souboru *.vscode/launch.json.* Následující příklad nastaví `Development`prostředí na :

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

Soubor *.vscode/launch.json* v projektu se nečte při `dotnet run` spuštění aplikace stejným způsobem jako *Vlastnosti/launchSettings.json*. Při spouštění aplikace ve vývoji, která nemá soubor *launchSettings.json,* nastavte prostředí s proměnnou `dotnet run` prostředí nebo argument příkazového řádku na příkaz.

### <a name="production"></a>Výroba

Produkční prostředí by mělo být nakonfigurováno tak, aby maximalizovalo zabezpečení, výkon a robustnost aplikací. Některá běžná nastavení, která se liší od vývoje, zahrnují:

* Mezipaměti.
* Prostředky na straně klienta jsou sdružené, minifikovány a potenciálně obsluhovány z cdn.
* Diagnostické chybové stránky jsou zakázány.
* Jsou povoleny popisné chybové stránky.
* Povoleno protokolování a monitorování výroby. Například [Application Insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Nastavení prostředí

Často je užitečné nastavit konkrétní prostředí pro testování s proměnnou prostředí nebo nastavení platformy. Pokud prostředí není nastaveno, je `Production`výchozí na , který zakáže většinu funkcí ladění. Způsob nastavení prostředí závisí na operačním systému.

Při vytváření hostitele určuje prostředí aplikace poslední nastavení prostředí přečtené aplikací. Prostředí aplikace nelze změnit, když je aplikace spuštěná.

### <a name="environment-variable-or-platform-setting"></a>Nastavení proměnné prostředí nebo platformy

#### <a name="azure-app-service"></a>Azure App Service

Chcete-li nastavit prostředí ve [službě Azure App Service](https://azure.microsoft.com/services/app-service/), proveďte následující kroky:

1. Vyberte aplikaci z okna **Služby aplikací.**
1. Ve skupině **Nastavení** vyberte okno **Konfigurace.**
1. Na kartě **Nastavení aplikace** vyberte Nastavení **nové aplikace**.
1. V okně Nastavení aplikace Přidat `ASPNETCORE_ENVIRONMENT` nebo **upravit** zadejte **název**. Pro **hodnotu**zadejte prostředí `Staging`(například).
1. Pokud chcete, aby nastavení prostředí zůstalo s aktuální mašleí při výměně slotů nasazení, zaškrtněte políčko **Nastavení patice nasazení.** Další informace najdete v tématu [Nastavení pracovních prostředí ve službě Azure App Service](/azure/app-service/web-sites-staged-publishing) v dokumentaci k Azure.
1. Výběrem **možnosti OK** zavřete okno **Nastavení aplikace Přidat nebo upravit.**
1. V horní části okna **Konfigurace** vyberte **Uložit.**

Služba Azure App Service aplikaci automaticky restartuje po přidání, změně nebo odstranění na webu Azure Portal (proměnná prostředí).

#### <a name="windows"></a>Windows

Chcete-li `ASPNETCORE_ENVIRONMENT` nastavit aktuální relaci při spuštění aplikace pomocí [dotnet run](/dotnet/core/tools/dotnet-run), použijí se následující příkazy:

**Příkazového řádku**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

Tyto příkazy se projeví pouze pro aktuální okno. Když je okno zavřené, `ASPNETCORE_ENVIRONMENT` nastavení se vrátí na výchozí nastavení nebo hodnotu počítače.

Chcete-li nastavit hodnotu globálně v systému Windows, použijte některý z následujících přístupů:

* Otevřete `ASPNETCORE_ENVIRONMENT` nastavení > **systému Ovládací** **panely:** > **System**

  ![Rozšířené vlastnosti systému](environments/_static/systemsetting_environment.png)

  ![Proměnná jádrového prostředí ASPNET](environments/_static/windows_aspnetcore_environment.png)

* Otevřete příkazový řádek `setx` pro správu a použijte příkaz `[Environment]::SetEnvironmentVariable`nebo příkaz PowerShell pro správu a použijte :

  **Příkazového řádku**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  Přepínač `/M` označuje nastavení proměnné prostředí na úrovni systému. Pokud `/M` se přepínač nepoužívá, je pro uživatelský účet nastavena proměnná prostředí.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  Hodnota `Machine` možnosti označuje nastavení proměnné prostředí na úrovni systému. Pokud se hodnota možnosti změní na `User`, je pro uživatelský účet nastavena proměnná prostředí.

Když `ASPNETCORE_ENVIRONMENT` je proměnná prostředí nastavena globálně, projeví se v `dotnet run` libovolném příkazovém okně otevřeném po nastavení hodnoty.

**Souboru web.config**

Chcete-li `ASPNETCORE_ENVIRONMENT` nastavit proměnnou prostředí pomocí *souboru web.config*, přečtěte si část *Nastavení proměnných prostředí* v aplikaci <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.

**Soubor projektu nebo profil publikování**

**Pro nasazení služby Windows IIS:** Zahrňte `<EnvironmentName>` vlastnost do [profilu publikování (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo do souboru projektu. Tento přístup nastaví prostředí v *web.config* při publikování projektu:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**Na fond aplikací služby IIS**

Pokud chcete `ASPNETCORE_ENVIRONMENT` nastavit proměnnou prostředí pro aplikaci spuštěnou v izolovaném fondu aplikací (podporovaná ve službě IIS 10.0 nebo novější), přečtěte si část *příkazu AppCmd.exe* v tématu Proměnné [ &lt;&gt; prostředí proměnných](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) prostředí. Když `ASPNETCORE_ENVIRONMENT` je proměnná prostředí nastavená pro fond aplikací, její hodnota přepíše nastavení na úrovni systému.

> [!IMPORTANT]
> Při hostování aplikace ve službě IIS `ASPNETCORE_ENVIRONMENT` a přidávání nebo změně proměnné prostředí použijte některý z následujících přístupů, aby se nová hodnota zvedla aplikacemi:
>
> * Provedení `net stop was /y` následované `net start w3svc` příkazového řádku.
> * Restartujte server.

#### <a name="macos"></a>macOS

Nastavení aktuálního prostředí pro macOS lze provést in-line při spuštění aplikace:

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

Případně nastavte prostředí s `export` před spuštěním aplikace:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

Proměnné prostředí na úrovni počítače jsou nastaveny v souboru *%.bashrc* nebo *.bash_profile.* Upravte soubor pomocí libovolného textového editoru. Přidejte následující příkaz:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a>Linux

Pro distribuce Linuxu `export` použijte příkaz na příkazovém řádku pro nastavení proměnných na základě relace a *bash_profile* soubor pro nastavení prostředí na úrovni počítače.

### <a name="set-the-environment-in-code"></a>Nastavení prostředí v kódu

Zavolejte <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> při budování hostitele. Viz třída <xref:fundamentals/host/generic-host#environmentname>.


### <a name="configuration-by-environment"></a>Konfigurace podle prostředí

Chcete-li načíst konfiguraci podle prostředí, doporučujeme:

* *soubory nastavení aplikace* (*appsettings.{ prostředí}.json*). Viz třída <xref:fundamentals/configuration/index#json-configuration-provider>.
* Proměnné prostředí (nastavené v každém systému, kde je aplikace hostována). Zobrazit <xref:fundamentals/host/generic-host#environmentname> <xref:security/app-secrets#environment-variables>a .
* Správce tajných barev (pouze ve vývojovém prostředí). Viz třída <xref:security/app-secrets>.

## <a name="environment-based-startup-class-and-methods"></a>Třída a metody startupu založené na prostředí

### <a name="inject-iwebhostenvironment-into-startupconfigure"></a>Vložte prostředí IWebHostEnvironment do startupu.Konfigurace

<xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> Aplikujte `Startup.Configure`do něj . Tento přístup je užitečný, když `Startup.Configure` aplikace vyžaduje pouze úpravu pro několik prostředí s minimálními rozdíly kódu na prostředí.

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

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a>Vstříkněte prostředí IWebHostEnvironment do třídy Startup

Vstříkněte <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do konstruktoru. `Startup` Tento přístup je užitečný, když `Startup` aplikace vyžaduje konfiguraci pouze pro několik prostředí s minimálními rozdíly kódu na prostředí.

V následujícím příkladu:

* Prostředí je drženo `_env` v terénu.
* `_env`se používá `ConfigureServices` `Configure` v a použít konfiguraci při spuštění na základě prostředí aplikace.

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
### <a name="startup-class-conventions"></a>Konvence spouštěcí třídy

Když se spustí ASP.NET základní aplikace, [spustí se třída Startup.](xref:fundamentals/startup) Aplikace může definovat `Startup` samostatné třídy pro různá prostředí `StartupDevelopment`(například). Příslušná `Startup` třída je vybrána za běhu. Třída, jejíž přípona názvu odpovídá aktuálnímu prostředí, je upřednostněna. Pokud odpovídající `Startup{EnvironmentName}` třída nebyla nalezena, třída se `Startup` používá. Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly kódu na prostředí.

Chcete-li implementovat třídy založené na `Startup` prostředí, vytvořte třídu `Startup{EnvironmentName}` pro každé prostředí, které se používá, a záložní `Startup` třídu:

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

Použijte [přetížení UseStartup(IWebHostBuilder, String),](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) které přijímá název sestavení:

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

### <a name="startup-method-conventions"></a>Konvence metod spouštění

[Konfigurace](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) a [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) podporují verze formuláře `Configure<EnvironmentName>` a `Configure<EnvironmentName>Services`. Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly kódu na prostředí.

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core konfiguruje chování aplikací na základě runtime prostředí pomocí proměnné prostředí.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="environments"></a>Prostředí

ASP.NET Core přečte `ASPNETCORE_ENVIRONMENT` proměnnou prostředí při spuštění aplikace a uloží hodnotu do [iHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName). `ASPNETCORE_ENVIRONMENT`lze nastavit na libovolnou hodnotu, ale rámci jsou uvedeny tři hodnoty:

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production>(výchozí)

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

Předcházející kód:

* Volání [UseDeveloperExceptionPage,](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) `ASPNETCORE_ENVIRONMENT` pokud `Development`je nastavena na .
* Volá [UseExceptionHandler,](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) pokud `ASPNETCORE_ENVIRONMENT` je nastavena hodnota jedné z následujících možností:

  * `Staging`
  * `Production`
  * `Staging_2`

Pomocník [značky prostředí](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) používá `IHostingEnvironment.EnvironmentName` hodnotu zahrnout nebo vyloučit značky v prvku:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

Ve Windows a macOS nerozlišují proměnné a hodnoty prostředí. Proměnné a hodnoty prostředí Linuxu jsou ve výchozím nastavení **rozlišovány malá a velká písmena.**

### <a name="development"></a>Vývoj

Vývojové prostředí může povolit funkce, které by neměly být vystaveny v produkčním prostředí. Například ASP.NET základní šablony umožňují [stránku výjimky pro vývojáře](xref:fundamentals/error-handling#developer-exception-page) ve vývojovém prostředí.

Prostředí pro vývoj místního počítače lze nastavit v souboru *Properties\launchSettings.json* projektu. Hodnoty prostředí nastavené v *launchSettings.json* přepíší hodnoty nastavené v systémovém prostředí.

Následující JSON zobrazuje tři profily ze souboru *launchSettings.json:*

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
> Vlastnost `applicationUrl` v *launchSettings.json* můžete zadat seznam adres URL serveru. Použijte středník mezi adresami URL v seznamu:
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

Při spuštění aplikace s [dotnet spustit](/dotnet/core/tools/dotnet-run), `"commandName": "Project"` první profil s se používá. Hodnota `commandName` určuje webový server, který má být spuštěn. `commandName`může být některý z následujících:

* `IISExpress`
* `IIS`
* `Project`(který spouští Kestrel)

Při spuštění aplikace s [dotnet spustit](/dotnet/core/tools/dotnet-run):

* *launchSettings.json* je přečten, pokud je k dispozici. `environmentVariables`nastavení v *launchSettings.json* přepsat proměnné prostředí.
* Zobrazí se hostitelské prostředí.

Následující výstup ukazuje aplikaci spuštěnou [s dotnet run](/dotnet/core/tools/dotnet-run):

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

Karta **Ladění** vlastností projektu sady Visual Studio poskytuje grafické uživatelské rozhraní pro úpravu souboru *launchSettings.json:*

![Vlastnosti projektu Nastavení proměnných prostředí](environments/_static/project-properties-debug.png)

Změny provedené v profilech projektu se nemusí projevit, dokud nebude webový server restartován. Kestrel musí být restartován předtím, než může rozpoznat změny provedené v jeho prostředí.

> [!WARNING]
> *launchSettings.json* by neměl ukládat tajné klíče. [Nástroj Správce tajných barev](xref:security/app-secrets) lze použít k ukládání tajných kódů pro místní vývoj.

Při použití [kódu sady Visual Studio](https://code.visualstudio.com/)lze proměnné prostředí nastavit v souboru *.vscode/launch.json.* Následující příklad nastaví `Development`prostředí na :

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

Soubor *.vscode/launch.json* v projektu se nečte při `dotnet run` spuštění aplikace stejným způsobem jako *Vlastnosti/launchSettings.json*. Při spouštění aplikace ve vývoji, která nemá soubor *launchSettings.json,* nastavte prostředí s proměnnou `dotnet run` prostředí nebo argument příkazového řádku na příkaz.

### <a name="production"></a>Výroba

Produkční prostředí by mělo být nakonfigurováno tak, aby maximalizovalo zabezpečení, výkon a robustnost aplikací. Některá běžná nastavení, která se liší od vývoje, zahrnují:

* Mezipaměti.
* Prostředky na straně klienta jsou sdružené, minifikovány a potenciálně obsluhovány z cdn.
* Diagnostické chybové stránky jsou zakázány.
* Jsou povoleny popisné chybové stránky.
* Povoleno protokolování a monitorování výroby. Například [Application Insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Nastavení prostředí

Často je užitečné nastavit konkrétní prostředí pro testování s proměnnou prostředí nebo nastavení platformy. Pokud prostředí není nastaveno, je `Production`výchozí na , který zakáže většinu funkcí ladění. Způsob nastavení prostředí závisí na operačním systému.

Při vytváření hostitele určuje prostředí aplikace poslední nastavení prostředí přečtené aplikací. Prostředí aplikace nelze změnit, když je aplikace spuštěná.

### <a name="environment-variable-or-platform-setting"></a>Nastavení proměnné prostředí nebo platformy

#### <a name="azure-app-service"></a>Azure App Service

Chcete-li nastavit prostředí ve [službě Azure App Service](https://azure.microsoft.com/services/app-service/), proveďte následující kroky:

1. Vyberte aplikaci z okna **Služby aplikací.**
1. Ve skupině **Nastavení** vyberte okno **Konfigurace.**
1. Na kartě **Nastavení aplikace** vyberte Nastavení **nové aplikace**.
1. V okně Nastavení aplikace Přidat `ASPNETCORE_ENVIRONMENT` nebo **upravit** zadejte **název**. Pro **hodnotu**zadejte prostředí `Staging`(například).
1. Pokud chcete, aby nastavení prostředí zůstalo s aktuální mašleí při výměně slotů nasazení, zaškrtněte políčko **Nastavení patice nasazení.** Další informace najdete v tématu [Nastavení pracovních prostředí ve službě Azure App Service](/azure/app-service/web-sites-staged-publishing) v dokumentaci k Azure.
1. Výběrem **možnosti OK** zavřete okno **Nastavení aplikace Přidat nebo upravit.**
1. V horní části okna **Konfigurace** vyberte **Uložit.**

Služba Azure App Service aplikaci automaticky restartuje po přidání, změně nebo odstranění na webu Azure Portal (proměnná prostředí).

#### <a name="windows"></a>Windows

Chcete-li `ASPNETCORE_ENVIRONMENT` nastavit aktuální relaci při spuštění aplikace pomocí [dotnet run](/dotnet/core/tools/dotnet-run), použijí se následující příkazy:

**Příkazového řádku**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

Tyto příkazy se projeví pouze pro aktuální okno. Když je okno zavřené, `ASPNETCORE_ENVIRONMENT` nastavení se vrátí na výchozí nastavení nebo hodnotu počítače.

Chcete-li nastavit hodnotu globálně v systému Windows, použijte některý z následujících přístupů:

* Otevřete `ASPNETCORE_ENVIRONMENT` nastavení > **systému Ovládací** **panely:** > **System**

  ![Rozšířené vlastnosti systému](environments/_static/systemsetting_environment.png)

  ![Proměnná jádrového prostředí ASPNET](environments/_static/windows_aspnetcore_environment.png)

* Otevřete příkazový řádek `setx` pro správu a použijte příkaz `[Environment]::SetEnvironmentVariable`nebo příkaz PowerShell pro správu a použijte :

  **Příkazového řádku**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  Přepínač `/M` označuje nastavení proměnné prostředí na úrovni systému. Pokud `/M` se přepínač nepoužívá, je pro uživatelský účet nastavena proměnná prostředí.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  Hodnota `Machine` možnosti označuje nastavení proměnné prostředí na úrovni systému. Pokud se hodnota možnosti změní na `User`, je pro uživatelský účet nastavena proměnná prostředí.

Když `ASPNETCORE_ENVIRONMENT` je proměnná prostředí nastavena globálně, projeví se v `dotnet run` libovolném příkazovém okně otevřeném po nastavení hodnoty.

**Souboru web.config**

Chcete-li `ASPNETCORE_ENVIRONMENT` nastavit proměnnou prostředí pomocí *souboru web.config*, přečtěte si část *Nastavení proměnných prostředí* v aplikaci <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.

**Soubor projektu nebo profil publikování**

**Pro nasazení služby Windows IIS:** Zahrňte `<EnvironmentName>` vlastnost do [profilu publikování (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo do souboru projektu. Tento přístup nastaví prostředí v *web.config* při publikování projektu:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**Na fond aplikací služby IIS**

Pokud chcete `ASPNETCORE_ENVIRONMENT` nastavit proměnnou prostředí pro aplikaci spuštěnou v izolovaném fondu aplikací (podporovaná ve službě IIS 10.0 nebo novější), přečtěte si část *příkazu AppCmd.exe* v tématu Proměnné [ &lt;&gt; prostředí proměnných](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) prostředí. Když `ASPNETCORE_ENVIRONMENT` je proměnná prostředí nastavená pro fond aplikací, její hodnota přepíše nastavení na úrovni systému.

> [!IMPORTANT]
> Při hostování aplikace ve službě IIS `ASPNETCORE_ENVIRONMENT` a přidávání nebo změně proměnné prostředí použijte některý z následujících přístupů, aby se nová hodnota zvedla aplikacemi:
>
> * Provedení `net stop was /y` následované `net start w3svc` příkazového řádku.
> * Restartujte server.

#### <a name="macos"></a>macOS

Nastavení aktuálního prostředí pro macOS lze provést in-line při spuštění aplikace:

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

Případně nastavte prostředí s `export` před spuštěním aplikace:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

Proměnné prostředí na úrovni počítače jsou nastaveny v souboru *%.bashrc* nebo *.bash_profile.* Upravte soubor pomocí libovolného textového editoru. Přidejte následující příkaz:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a>Linux

Pro distribuce Linuxu `export` použijte příkaz na příkazovém řádku pro nastavení proměnných na základě relace a *bash_profile* soubor pro nastavení prostředí na úrovni počítače.

### <a name="set-the-environment-in-code"></a>Nastavení prostředí v kódu

Zavolejte <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> při budování hostitele. Viz třída <xref:fundamentals/host/web-host#environment>.

### <a name="configuration-by-environment"></a>Konfigurace podle prostředí

Chcete-li načíst konfiguraci podle prostředí, doporučujeme:

* *soubory nastavení aplikace* (*appsettings.{ prostředí}.json*). Viz třída <xref:fundamentals/configuration/index#json-configuration-provider>.
* Proměnné prostředí (nastavené v každém systému, kde je aplikace hostována). Zobrazit <xref:fundamentals/host/web-host#environment> <xref:security/app-secrets#environment-variables>a .
* Správce tajných barev (pouze ve vývojovém prostředí). Viz třída <xref:security/app-secrets>.

## <a name="environment-based-startup-class-and-methods"></a>Třída a metody startupu založené na prostředí

### <a name="inject-ihostingenvironment-into-startupconfigure"></a>Vložte iHostingEnvironment do startup.Configure

<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> Aplikujte `Startup.Configure`do něj . Tento přístup je užitečný, když `Startup.Configure` aplikace vyžaduje konfiguraci pouze pro několik prostředí s minimálními rozdíly kódu na prostředí.

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

### <a name="inject-ihostingenvironment-into-the-startup-class"></a>Vstříkněte iHostingEnvironment do třídy Startup

Vstříkněte <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> do konstruktoru `Startup` a přiřaďte službu k poli pro použití v `Startup` celé třídě. Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pouze pro několik prostředí s minimálními rozdíly kódu na prostředí.

V následujícím příkladu:

* Prostředí je drženo `_env` v terénu.
* `_env`se používá `ConfigureServices` `Configure` v a použít konfiguraci při spuštění na základě prostředí aplikace.

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

### <a name="startup-class-conventions"></a>Konvence spouštěcí třídy

Když se spustí ASP.NET základní aplikace, [spustí se třída Startup.](xref:fundamentals/startup) Aplikace může definovat `Startup` samostatné třídy pro různá prostředí `StartupDevelopment`(například). Příslušná `Startup` třída je vybrána za běhu. Třída, jejíž přípona názvu odpovídá aktuálnímu prostředí, je upřednostněna. Pokud odpovídající `Startup{EnvironmentName}` třída nebyla nalezena, třída se `Startup` používá. Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly kódu na prostředí.

Chcete-li implementovat třídy založené na `Startup` prostředí, vytvořte třídu `Startup{EnvironmentName}` pro každé prostředí, které se používá, a záložní `Startup` třídu:

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

Použijte [přetížení UseStartup(IWebHostBuilder, String),](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) které přijímá název sestavení:

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

### <a name="startup-method-conventions"></a>Konvence metod spouštění

[Konfigurace](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) a [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) podporují verze formuláře `Configure<EnvironmentName>` a `Configure<EnvironmentName>Services`. Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly kódu na prostředí.

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
