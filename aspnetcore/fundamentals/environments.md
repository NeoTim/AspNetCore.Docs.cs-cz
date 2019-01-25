---
title: Používání více prostředí v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak řídit chování aplikace napříč několika prostředími v aplikacích ASP.NET Core.
ms.author: riande
ms.date: 01/22/2019
uid: fundamentals/environments
ms.openlocfilehash: 39e1b48481832a6d76de605b37410fe2e16dcd88
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836737"
---
# <a name="use-multiple-environments-in-aspnet-core"></a>Používání více prostředí v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core nakonfiguruje chování aplikace založené na prostředí modulu runtime pomocí proměnné prostředí.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Prostředí

ASP.NET Core načte proměnnou prostředí `ASPNETCORE_ENVIRONMENT` při spuštění aplikace a uloží hodnotu v [IHostingEnvironment.EnvironmentName](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname). Můžete nastavit `ASPNETCORE_ENVIRONMENT` na libovolnou hodnotu, ale [tří hodnot](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) jsou podporovány v rámci rozhraní: [Vývoj](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [pracovní](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging), a [produkční](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Pokud `ASPNETCORE_ENVIRONMENT` není nastavený, použije se výchozí `Production`.

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

Předchozí kód:

* Volání [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) při `ASPNETCORE_ENVIRONMENT` je nastavena na `Development`.
* Volání [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) při hodnotu `ASPNETCORE_ENVIRONMENT` nastavený jeden z následujících akcí:

    * `Staging`
    * `Production`
    * `Staging_2`

[Pomocná rutina značky prostředí](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) používá hodnotu `IHostingEnvironment.EnvironmentName` pro zahrnutí nebo vyloučení značek v elementu:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

Ve Windows a macOS proměnné prostředí a hodnoty nejsou velká a malá písmena. Proměnné prostředí Linux a hodnoty jsou **malá a velká písmena** ve výchozím nastavení.

### <a name="development"></a>Vývoj

Vývojové prostředí můžete povolit funkce, které by neměly být vystaveny v produkčním prostředí. Například šablony ASP.NET Core povolit [stránku výjimek pro vývojáře](xref:fundamentals/error-handling#the-developer-exception-page) ve vývojovém prostředí.

Je možné nastavit prostředí pro vývoj v místním počítači v *Properties\launchSettings.json* souboru projektu. Hodnoty prostředí nastavené *launchSettings.json* přepisují hodnoty nastavené v prostředí systému.

Následující kód JSON ukazuje tři profily *launchSettings.json* souboru:

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

::: moniker range=">= aspnetcore-2.1"

> [!NOTE]
> `applicationUrl` Vlastnost *launchSettings.json* můžete zadat seznam adres URL serveru. Použijte středník mezi adresy URL v seznamu:
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

::: moniker-end

Při spuštění aplikace s [dotnet spustit](/dotnet/core/tools/dotnet-run), první profil s `"commandName": "Project"` se používá. Hodnota `commandName` specifikuje webový server ke spuštění. `commandName` může být jedna z následujících akcí:

* `IISExpress`
* `IIS`
* `Project` (které spustí Kestrel)

Když je aplikace spuštěna s [dotnet spustit](/dotnet/core/tools/dotnet-run):

* *launchSettings.json* je pro čtení. Pokud je k dispozici. `environmentVariables` nastavení v *launchSettings.json* přepsání proměnných prostředí.
* Hostitelské prostředí se zobrazí.

Následující výstup zobrazuje aplikaci začít [dotnet spustit](/dotnet/core/tools/dotnet-run):

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

Vlastnosti projektu sady Visual Studio **ladění** karta poskytuje grafické uživatelské rozhraní pro úpravy *launchSettings.json* souboru:

![Proměnné prostředí nastavení vlastností projektu](environments/_static/project-properties-debug.png)

Změny provedené v projektu profily pravděpodobně projeví až po restartu webového serveru. Kestrel je nutné restartovat předtím, než může zjistit změny provedené v prostředí.

> [!WARNING]
> *launchSettings.json* neměli ukládat tajné kódy. [Nástroj tajný klíč správce](xref:security/app-secrets) slouží k ukládání tajných kódů pro místní vývoj.

Při použití [Visual Studio Code](https://code.visualstudio.com/), proměnné prostředí můžete nastavit v *.vscode/launch.json* souboru. Následující příklad nastaví prostředí `Development`:

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

A *.vscode/launch.json* soubor v projektu pro není čtení při spuštění aplikace s `dotnet run` stejným způsobem jako *Properties/launchSettings.json*. Při spuštění aplikace ve vývoji, která nemá *launchSettings.json* souboru buď nastavit prostředí pomocí proměnné prostředí nebo argument příkazového řádku k `dotnet run` příkazu.

### <a name="production"></a>Produkční

Produkčním prostředí by měl být povolen maximalizace zabezpečení, výkon a odolnost aplikace. Některé obecná nastavení, které se liší od vývoje patří:

* Ukládání do mezipaměti.
* Prostředky na straně klienta jsou spojeny minifikovaný a potenciálně obsluhovat z CDN.
* Chyba diagnostiky stránek zakázána.
* Chybové stránky povolena.
* Produkční protokolování a monitorování povoleno. Například [Application Insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Nastavení prostředí

Často je užitečné k nastavení konkrétního prostředí pro testování. Pokud prostředí není nastavená, použije se výchozí `Production`, která zakáže většina funkcí ladění. Metoda pro nastavení prostředí závisí na operačním systému.

### <a name="azure-app-service"></a>Azure App Service

Chcete-li nastavit prostředí [služby Azure App Service](https://azure.microsoft.com/services/app-service/), proveďte následující kroky:

1. Vyberte aplikaci, ze **App Services** okno.
1. V **nastavení** skupiny, vyberte **nastavení aplikace** okno.
1. V **nastavení aplikace** vyberte **přidat nové nastavení**.
1. Pro **zadejte název**, poskytují `ASPNETCORE_ENVIRONMENT`. Pro **zadejte hodnotu**, poskytovat prostředí (například `Staging`).
1. Vyberte **nastavení slotu** zaškrtávací políčko, pokud chcete nastavení prostředí, které zůstane s aktuálním slotu, když se Prohodit sloty nasazení. Další informace najdete v tématu [dokumentaci k Azure: Nastavení, které jsou přehozeny? ](/azure/app-service/web-sites-staged-publishing).
1. Vyberte **Uložit** v horní části okna.

Po nastavení aplikace (proměnnou prostředí) přidání, změně nebo odstranění na webu Azure Portal, Azure App Service automaticky restartuje aplikaci.

### <a name="windows"></a>Windows

Chcete-li nastavit `ASPNETCORE_ENVIRONMENT` pro aktuální relaci při spuštění aplikace pomocí [dotnet spustit](/dotnet/core/tools/dotnet-run), se používají následující příkazy:

**Příkazový řádek**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

Tyto příkazy platit jenom pro aktuální okno. Při zavření okna `ASPNETCORE_ENVIRONMENT` nastavení obnoví na výchozí nastavení nebo počítač hodnotu.

K nastavení hodnoty globálně ve Windows, použijte jednu z následujících postupů:

* Otevřít **ovládací panely** > **systému** > **Upřesnit nastavení systému** a přidat nebo upravit `ASPNETCORE_ENVIRONMENT` hodnotu:

  ![Systémové rozšířené vlastnosti](environments/_static/systemsetting_environment.png)

  ![Proměnná prostředí základní ASPNET](environments/_static/windows_aspnetcore_environment.png)

* Otevřete příkazový řádek pro správu a použít `setx` příkazu nebo otevření příkazového řádku pro správu Powershellu a použití `[Environment]::SetEnvironmentVariable`:

  **Příkazový řádek**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  `/M` Přepínač označuje k nastavení proměnné prostředí na úrovni systému. Pokud `/M` nastaví proměnnou prostředí pro uživatelský účet, přepínač se nepoužívá.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  `Machine` Označuje hodnotu možnosti k nastavení proměnné prostředí na úrovni systému. Pokud se změní hodnotu možnosti na `User`, je nastavit proměnné prostředí pro uživatelský účet.

Když `ASPNETCORE_ENVIRONMENT` proměnnou prostředí je nastavit globálně, projeví se `dotnet run` v jakékoli okno příkazového řádku otevřené po je hodnota nastavena.

**web.config**

Nastavit `ASPNETCORE_ENVIRONMENT` proměnné prostředí s *web.config*, najdete v článku *nastavení proměnných prostředí* část <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>. Když `ASPNETCORE_ENVIRONMENT` nastavení proměnné prostředí s *web.config*, jeho hodnota se přepíše nastavení na úrovni systému.

::: moniker range=">= aspnetcore-2.2"

**Soubor projektu nebo profil publikování**

**Pro nasazení Windows služby IIS:** Zahrnout `<EnvironmentName>` vlastnost v profilu publikování (*.pubxml*) nebo soubor projektu. Tento přístup nastaví prostředí *web.config* při publikování projektu:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

::: moniker-end

**Na jeden fond aplikací služby IIS**

Chcete-li nastavit `ASPNETCORE_ENVIRONMENT` proměnné prostředí pro aplikace běžící v izolované fondu aplikací (podporované službou IIS 10.0 nebo vyšší), najdete v tématu *AppCmd.exe příkaz* část [proměnné prostředí &lt; environmentVariables&gt; ](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) tématu. Když `ASPNETCORE_ENVIRONMENT` nastavení proměnné prostředí pro fond aplikací, jeho hodnota se přepíše nastavení na úrovni systému.

> [!IMPORTANT]
> Při hostování aplikace v IIS a přidání nebo změně `ASPNETCORE_ENVIRONMENT` prostředí proměnné, použijte jednu z následujících přístupů k mají novou hodnotu vyzvednou aplikace:
>
> * Spustit `net stop was /y` následovaný `net start w3svc` z příkazového řádku.
> * Restartujte server.

### <a name="macos"></a>macOS

Nastavení aktuální prostředí pro macOS může být prováděny v řádku při spuštění aplikace:

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

Můžete také nastavit prostředí pomocí `export` před spuštěním aplikace:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

Proměnné prostředí na úrovni počítače se nastavují *.bashrc* nebo *.bash_profile* souboru. Upravte soubor pomocí libovolného textového editoru. Přidejte následující příkaz:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

### <a name="linux"></a>Linux

Pro distribuce Linuxu, použijte `export` příkazu na příkazovém řádku pro nastavení proměnné na základě relace a *bash_profile* souboru pro nastavení prostředí na úrovni počítače.

### <a name="configuration-by-environment"></a>Konfigurace podle prostředí

Načtení konfigurace podle prostředí, doporučujeme:

* *appsettings* files (*appsettings.&lt;<Environment>&gt;.json). Zobrazit [konfigurace: Zprostředkovatel konfigurace souboru](xref:fundamentals/configuration/index#file-configuration-provider).
* proměnné prostředí (nastavený v každém systému je hostitelem aplikace). Zobrazit [konfigurace: Zprostředkovatel konfigurace souboru](xref:fundamentals/configuration/index#file-configuration-provider) a [bezpečné ukládání tajných kódů aplikace při vývoji: Proměnné prostředí](xref:security/app-secrets#environment-variables).
* Tajný klíč správce (ve vývojovém prostředí pouze). Viz <xref:security/app-secrets>.

## <a name="environment-based-startup-class-and-methods"></a>Na základě prostředí při spuštění třídy a metody

### <a name="startup-class-conventions"></a>Při spuštění třídy konvence

Při spuštění aplikace ASP.NET Core [třídu pro spuštění](xref:fundamentals/startup) bootstraps aplikace. Aplikace může definovat oddělenou `Startup` třídu pro různá prostředí (například `StartupDevelopment`). Odpovídající `Startup` třída je vybrána v době běhu. Třída, jejíž název má příponu odpovídající aktuálnímu prostředí, je upřednostněna. Pokud odpovídající `Startup{EnvironmentName}` třídy nenajde, `Startup` třída se používá.

K implementaci založeném na prostředí `Startup` třídy, vytvoří `Startup{EnvironmentName}` třídy pro každé prostředí, které se používají a záložní `Startup` třídy:

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}
```

Použití [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) přetížení, které přijímá název sestavení:

::: moniker range=">= aspnetcore-2.1"

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

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```csharp
public static void Main(string[] args)
{
    CreateWebHost(args).Run();
}

public static IWebHost CreateWebHost(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName)
        .Build();
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

        var host = new WebHostBuilder()
            .UseStartup(assemblyName)
            .Build();

        host.Run();
    }
}
```

::: moniker-end

### <a name="startup-method-conventions"></a>Po spuštění metody konvence

[Konfigurace](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) a [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) podpory verzí specifických pro prostředí formu `Configure<EnvironmentName>` a `Configure<EnvironmentName>Services`:

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,51)]

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* [IHostingEnvironment.EnvironmentName](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname)
