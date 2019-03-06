---
title: Používání více prostředí v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak řídit chování aplikace napříč několika prostředími v aplikacích ASP.NET Core.
ms.author: riande
ms.date: 01/22/2019
uid: fundamentals/environments
ms.openlocfilehash: 4120453c36d1fc2a2303c2175df30ef9777bc54f
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345947"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="bfea4-103">Používání více prostředí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bfea4-103">Use multiple environments in ASP.NET Core</span></span>

<span data-ttu-id="bfea4-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bfea4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bfea4-105">ASP.NET Core nakonfiguruje chování aplikace založené na prostředí modulu runtime pomocí proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="bfea4-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="bfea4-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bfea4-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="bfea4-107">Prostředí</span><span class="sxs-lookup"><span data-stu-id="bfea4-107">Environments</span></span>

<span data-ttu-id="bfea4-108">ASP.NET Core načte proměnnou prostředí `ASPNETCORE_ENVIRONMENT` při spuštění aplikace a uloží hodnotu v [IHostingEnvironment.EnvironmentName](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname).</span><span class="sxs-lookup"><span data-stu-id="bfea4-108">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname).</span></span> <span data-ttu-id="bfea4-109">Můžete nastavit `ASPNETCORE_ENVIRONMENT` na libovolnou hodnotu, ale [tří hodnot](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) jsou podporovány v rámci rozhraní: [Vývoj](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [pracovní](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging), a [produkční](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production).</span><span class="sxs-lookup"><span data-stu-id="bfea4-109">You can set `ASPNETCORE_ENVIRONMENT` to any value, but [three values](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) are supported by the framework: [Development](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [Staging](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging), and [Production](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production).</span></span> <span data-ttu-id="bfea4-110">Pokud `ASPNETCORE_ENVIRONMENT` není nastavený, použije se výchozí `Production`.</span><span class="sxs-lookup"><span data-stu-id="bfea4-110">If `ASPNETCORE_ENVIRONMENT` isn't set, it defaults to `Production`.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="bfea4-111">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="bfea4-111">The preceding code:</span></span>

* <span data-ttu-id="bfea4-112">Volání [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) při `ASPNETCORE_ENVIRONMENT` je nastavena na `Development`.</span><span class="sxs-lookup"><span data-stu-id="bfea4-112">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="bfea4-113">Volání [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) při hodnotu `ASPNETCORE_ENVIRONMENT` nastavený jeden z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="bfea4-113">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

    * `Staging`
    * `Production`
    * `Staging_2`

<span data-ttu-id="bfea4-114">[Pomocná rutina značky prostředí](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) používá hodnotu `IHostingEnvironment.EnvironmentName` pro zahrnutí nebo vyloučení značek v elementu:</span><span class="sxs-lookup"><span data-stu-id="bfea4-114">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="bfea4-115">Ve Windows a macOS proměnné prostředí a hodnoty nejsou velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="bfea4-115">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="bfea4-116">Proměnné prostředí Linux a hodnoty jsou **malá a velká písmena** ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="bfea4-116">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="bfea4-117">Vývoj</span><span class="sxs-lookup"><span data-stu-id="bfea4-117">Development</span></span>

<span data-ttu-id="bfea4-118">Vývojové prostředí můžete povolit funkce, které by neměly být vystaveny v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="bfea4-118">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="bfea4-119">Například šablony ASP.NET Core povolit [stránku výjimek pro vývojáře](xref:fundamentals/error-handling#developer-exception-page) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="bfea4-119">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="bfea4-120">Je možné nastavit prostředí pro vývoj v místním počítači v *Properties\launchSettings.json* souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="bfea4-120">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="bfea4-121">Hodnoty prostředí nastavené *launchSettings.json* přepisují hodnoty nastavené v prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="bfea4-121">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="bfea4-122">Následující kód JSON ukazuje tři profily *launchSettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="bfea4-122">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

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
> <span data-ttu-id="bfea4-123">`applicationUrl` Vlastnost *launchSettings.json* můžete zadat seznam adres URL serveru.</span><span class="sxs-lookup"><span data-stu-id="bfea4-123">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="bfea4-124">Použijte středník mezi adresy URL v seznamu:</span><span class="sxs-lookup"><span data-stu-id="bfea4-124">Use a semicolon between the URLs in the list:</span></span>
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

<span data-ttu-id="bfea4-125">Při spuštění aplikace s [dotnet spustit](/dotnet/core/tools/dotnet-run), první profil s `"commandName": "Project"` se používá.</span><span class="sxs-lookup"><span data-stu-id="bfea4-125">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="bfea4-126">Hodnota `commandName` specifikuje webový server ke spuštění.</span><span class="sxs-lookup"><span data-stu-id="bfea4-126">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="bfea4-127">`commandName` může být jedna z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="bfea4-127">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="bfea4-128">`Project` (které spustí Kestrel)</span><span class="sxs-lookup"><span data-stu-id="bfea4-128">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="bfea4-129">Když je aplikace spuštěna s [dotnet spustit](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="bfea4-129">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="bfea4-130">*launchSettings.json* je pro čtení. Pokud je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="bfea4-130">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="bfea4-131">`environmentVariables` nastavení v *launchSettings.json* přepsání proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="bfea4-131">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="bfea4-132">Hostitelské prostředí se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="bfea4-132">The hosting environment is displayed.</span></span>

<span data-ttu-id="bfea4-133">Následující výstup zobrazuje aplikaci začít [dotnet spustit](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="bfea4-133">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="bfea4-134">Vlastnosti projektu sady Visual Studio **ladění** karta poskytuje grafické uživatelské rozhraní pro úpravy *launchSettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="bfea4-134">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Proměnné prostředí nastavení vlastností projektu](environments/_static/project-properties-debug.png)

<span data-ttu-id="bfea4-136">Změny provedené v projektu profily pravděpodobně projeví až po restartu webového serveru.</span><span class="sxs-lookup"><span data-stu-id="bfea4-136">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="bfea4-137">Kestrel je nutné restartovat předtím, než může zjistit změny provedené v prostředí.</span><span class="sxs-lookup"><span data-stu-id="bfea4-137">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="bfea4-138">*launchSettings.json* neměli ukládat tajné kódy.</span><span class="sxs-lookup"><span data-stu-id="bfea4-138">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="bfea4-139">[Nástroj tajný klíč správce](xref:security/app-secrets) slouží k ukládání tajných kódů pro místní vývoj.</span><span class="sxs-lookup"><span data-stu-id="bfea4-139">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="bfea4-140">Při použití [Visual Studio Code](https://code.visualstudio.com/), proměnné prostředí můžete nastavit v *.vscode/launch.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="bfea4-140">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="bfea4-141">Následující příklad nastaví prostředí `Development`:</span><span class="sxs-lookup"><span data-stu-id="bfea4-141">The following example sets the environment to `Development`:</span></span>

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

<span data-ttu-id="bfea4-142">A *.vscode/launch.json* soubor v projektu pro není čtení při spuštění aplikace s `dotnet run` stejným způsobem jako *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="bfea4-142">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="bfea4-143">Při spuštění aplikace ve vývoji, která nemá *launchSettings.json* souboru buď nastavit prostředí pomocí proměnné prostředí nebo argument příkazového řádku k `dotnet run` příkazu.</span><span class="sxs-lookup"><span data-stu-id="bfea4-143">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="bfea4-144">Produkční</span><span class="sxs-lookup"><span data-stu-id="bfea4-144">Production</span></span>

<span data-ttu-id="bfea4-145">Produkčním prostředí by měl být povolen maximalizace zabezpečení, výkon a odolnost aplikace.</span><span class="sxs-lookup"><span data-stu-id="bfea4-145">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="bfea4-146">Některé obecná nastavení, které se liší od vývoje patří:</span><span class="sxs-lookup"><span data-stu-id="bfea4-146">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="bfea4-147">Ukládání do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="bfea4-147">Caching.</span></span>
* <span data-ttu-id="bfea4-148">Prostředky na straně klienta jsou spojeny minifikovaný a potenciálně obsluhovat z CDN.</span><span class="sxs-lookup"><span data-stu-id="bfea4-148">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="bfea4-149">Chyba diagnostiky stránek zakázána.</span><span class="sxs-lookup"><span data-stu-id="bfea4-149">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="bfea4-150">Chybové stránky povolena.</span><span class="sxs-lookup"><span data-stu-id="bfea4-150">Friendly error pages enabled.</span></span>
* <span data-ttu-id="bfea4-151">Produkční protokolování a monitorování povoleno.</span><span class="sxs-lookup"><span data-stu-id="bfea4-151">Production logging and monitoring enabled.</span></span> <span data-ttu-id="bfea4-152">Například [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="bfea4-152">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="bfea4-153">Nastavení prostředí</span><span class="sxs-lookup"><span data-stu-id="bfea4-153">Set the environment</span></span>

<span data-ttu-id="bfea4-154">Často je užitečné k nastavení konkrétního prostředí pro testování.</span><span class="sxs-lookup"><span data-stu-id="bfea4-154">It's often useful to set a specific environment for testing.</span></span> <span data-ttu-id="bfea4-155">Pokud prostředí není nastavená, použije se výchozí `Production`, která zakáže většina funkcí ladění.</span><span class="sxs-lookup"><span data-stu-id="bfea4-155">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="bfea4-156">Metoda pro nastavení prostředí závisí na operačním systému.</span><span class="sxs-lookup"><span data-stu-id="bfea4-156">The method for setting the environment depends on the operating system.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="bfea4-157">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="bfea4-157">Azure App Service</span></span>

<span data-ttu-id="bfea4-158">Chcete-li nastavit prostředí [služby Azure App Service](https://azure.microsoft.com/services/app-service/), proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="bfea4-158">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="bfea4-159">Vyberte aplikaci, ze **App Services** okno.</span><span class="sxs-lookup"><span data-stu-id="bfea4-159">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="bfea4-160">V **nastavení** skupiny, vyberte **nastavení aplikace** okno.</span><span class="sxs-lookup"><span data-stu-id="bfea4-160">In the **SETTINGS** group, select the **Application settings** blade.</span></span>
1. <span data-ttu-id="bfea4-161">V **nastavení aplikace** vyberte **přidat nové nastavení**.</span><span class="sxs-lookup"><span data-stu-id="bfea4-161">In the **Application settings** area, select **Add new setting**.</span></span>
1. <span data-ttu-id="bfea4-162">Pro **zadejte název**, poskytují `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="bfea4-162">For **Enter a name**, provide `ASPNETCORE_ENVIRONMENT`.</span></span> <span data-ttu-id="bfea4-163">Pro **zadejte hodnotu**, poskytovat prostředí (například `Staging`).</span><span class="sxs-lookup"><span data-stu-id="bfea4-163">For **Enter a value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="bfea4-164">Vyberte **nastavení slotu** zaškrtávací políčko, pokud chcete nastavení prostředí, které zůstane s aktuálním slotu, když se Prohodit sloty nasazení.</span><span class="sxs-lookup"><span data-stu-id="bfea4-164">Select the **Slot Setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="bfea4-165">Další informace najdete v tématu [dokumentaci k Azure: Nastavení, které jsou přehozeny? ](/azure/app-service/web-sites-staged-publishing).</span><span class="sxs-lookup"><span data-stu-id="bfea4-165">For more information, see [Azure Documentation: Which settings are swapped?](/azure/app-service/web-sites-staged-publishing).</span></span>
1. <span data-ttu-id="bfea4-166">Vyberte **Uložit** v horní části okna.</span><span class="sxs-lookup"><span data-stu-id="bfea4-166">Select **Save** at the top of the blade.</span></span>

<span data-ttu-id="bfea4-167">Po nastavení aplikace (proměnnou prostředí) přidání, změně nebo odstranění na webu Azure Portal, Azure App Service automaticky restartuje aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bfea4-167">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

### <a name="windows"></a><span data-ttu-id="bfea4-168">Windows</span><span class="sxs-lookup"><span data-stu-id="bfea4-168">Windows</span></span>

<span data-ttu-id="bfea4-169">Chcete-li nastavit `ASPNETCORE_ENVIRONMENT` pro aktuální relaci při spuštění aplikace pomocí [dotnet spustit](/dotnet/core/tools/dotnet-run), se používají následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="bfea4-169">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="bfea4-170">**Příkazový řádek**</span><span class="sxs-lookup"><span data-stu-id="bfea4-170">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="bfea4-171">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="bfea4-171">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="bfea4-172">Tyto příkazy platit jenom pro aktuální okno.</span><span class="sxs-lookup"><span data-stu-id="bfea4-172">These commands only take effect for the current window.</span></span> <span data-ttu-id="bfea4-173">Při zavření okna `ASPNETCORE_ENVIRONMENT` nastavení obnoví na výchozí nastavení nebo počítač hodnotu.</span><span class="sxs-lookup"><span data-stu-id="bfea4-173">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="bfea4-174">K nastavení hodnoty globálně ve Windows, použijte jednu z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="bfea4-174">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="bfea4-175">Otevřít **ovládací panely** > **systému** > **Upřesnit nastavení systému** a přidat nebo upravit `ASPNETCORE_ENVIRONMENT` hodnotu:</span><span class="sxs-lookup"><span data-stu-id="bfea4-175">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Systémové rozšířené vlastnosti](environments/_static/systemsetting_environment.png)

  ![Proměnná prostředí základní ASPNET](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="bfea4-178">Otevřete příkazový řádek pro správu a použít `setx` příkazu nebo otevření příkazového řádku pro správu Powershellu a použití `[Environment]::SetEnvironmentVariable`:</span><span class="sxs-lookup"><span data-stu-id="bfea4-178">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="bfea4-179">**Příkazový řádek**</span><span class="sxs-lookup"><span data-stu-id="bfea4-179">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="bfea4-180">`/M` Přepínač označuje k nastavení proměnné prostředí na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="bfea4-180">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="bfea4-181">Pokud `/M` nastaví proměnnou prostředí pro uživatelský účet, přepínač se nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="bfea4-181">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="bfea4-182">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="bfea4-182">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="bfea4-183">`Machine` Označuje hodnotu možnosti k nastavení proměnné prostředí na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="bfea4-183">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="bfea4-184">Pokud se změní hodnotu možnosti na `User`, je nastavit proměnné prostředí pro uživatelský účet.</span><span class="sxs-lookup"><span data-stu-id="bfea4-184">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="bfea4-185">Když `ASPNETCORE_ENVIRONMENT` proměnnou prostředí je nastavit globálně, projeví se `dotnet run` v jakékoli okno příkazového řádku otevřené po je hodnota nastavena.</span><span class="sxs-lookup"><span data-stu-id="bfea4-185">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="bfea4-186">**web.config**</span><span class="sxs-lookup"><span data-stu-id="bfea4-186">**web.config**</span></span>

<span data-ttu-id="bfea4-187">Nastavit `ASPNETCORE_ENVIRONMENT` proměnné prostředí s *web.config*, najdete v článku *nastavení proměnných prostředí* část <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="bfea4-187">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span> <span data-ttu-id="bfea4-188">Když `ASPNETCORE_ENVIRONMENT` nastavení proměnné prostředí s *web.config*, jeho hodnota se přepíše nastavení na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="bfea4-188">When the `ASPNETCORE_ENVIRONMENT` environment variable is set with *web.config*, its value overrides a setting at the system level.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="bfea4-189">**Soubor projektu nebo profil publikování**</span><span class="sxs-lookup"><span data-stu-id="bfea4-189">**Project file or publish profile**</span></span>

<span data-ttu-id="bfea4-190">**Pro nasazení Windows služby IIS:** Zahrnout `<EnvironmentName>` vlastnost v profilu publikování (*.pubxml*) nebo soubor projektu.</span><span class="sxs-lookup"><span data-stu-id="bfea4-190">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="bfea4-191">Tento přístup nastaví prostředí *web.config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="bfea4-191">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

::: moniker-end

<span data-ttu-id="bfea4-192">**Na jeden fond aplikací služby IIS**</span><span class="sxs-lookup"><span data-stu-id="bfea4-192">**Per IIS Application Pool**</span></span>

<span data-ttu-id="bfea4-193">Chcete-li nastavit `ASPNETCORE_ENVIRONMENT` proměnné prostředí pro aplikace běžící v izolované fondu aplikací (podporované službou IIS 10.0 nebo vyšší), najdete v tématu *AppCmd.exe příkaz* část [proměnné prostředí &lt; environmentVariables&gt; ](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) tématu.</span><span class="sxs-lookup"><span data-stu-id="bfea4-193">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="bfea4-194">Když `ASPNETCORE_ENVIRONMENT` nastavení proměnné prostředí pro fond aplikací, jeho hodnota se přepíše nastavení na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="bfea4-194">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="bfea4-195">Při hostování aplikace v IIS a přidání nebo změně `ASPNETCORE_ENVIRONMENT` prostředí proměnné, použijte jednu z následujících přístupů k mají novou hodnotu vyzvednou aplikace:</span><span class="sxs-lookup"><span data-stu-id="bfea4-195">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="bfea4-196">Spustit `net stop was /y` následovaný `net start w3svc` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="bfea4-196">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="bfea4-197">Restartujte server.</span><span class="sxs-lookup"><span data-stu-id="bfea4-197">Restart the server.</span></span>

### <a name="macos"></a><span data-ttu-id="bfea4-198">macOS</span><span class="sxs-lookup"><span data-stu-id="bfea4-198">macOS</span></span>

<span data-ttu-id="bfea4-199">Nastavení aktuální prostředí pro macOS může být prováděny v řádku při spuštění aplikace:</span><span class="sxs-lookup"><span data-stu-id="bfea4-199">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="bfea4-200">Můžete také nastavit prostředí pomocí `export` před spuštěním aplikace:</span><span class="sxs-lookup"><span data-stu-id="bfea4-200">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="bfea4-201">Proměnné prostředí na úrovni počítače se nastavují *.bashrc* nebo *.bash_profile* souboru.</span><span class="sxs-lookup"><span data-stu-id="bfea4-201">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="bfea4-202">Upravte soubor pomocí libovolného textového editoru.</span><span class="sxs-lookup"><span data-stu-id="bfea4-202">Edit the file using any text editor.</span></span> <span data-ttu-id="bfea4-203">Přidejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="bfea4-203">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

### <a name="linux"></a><span data-ttu-id="bfea4-204">Linux</span><span class="sxs-lookup"><span data-stu-id="bfea4-204">Linux</span></span>

<span data-ttu-id="bfea4-205">Pro distribuce Linuxu, použijte `export` příkazu na příkazovém řádku pro nastavení proměnné na základě relace a *bash_profile* souboru pro nastavení prostředí na úrovni počítače.</span><span class="sxs-lookup"><span data-stu-id="bfea4-205">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="bfea4-206">Konfigurace podle prostředí</span><span class="sxs-lookup"><span data-stu-id="bfea4-206">Configuration by environment</span></span>

<span data-ttu-id="bfea4-207">Načtení konfigurace podle prostředí, doporučujeme:</span><span class="sxs-lookup"><span data-stu-id="bfea4-207">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="bfea4-208">*appsettings* files (\*appsettings.&lt;<Environment>&gt;.json).</span><span class="sxs-lookup"><span data-stu-id="bfea4-208">*appsettings* files (\*appsettings.&lt;<Environment>&gt;.json).</span></span> <span data-ttu-id="bfea4-209">Zobrazit [konfigurace: Zprostředkovatel konfigurace souboru](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="bfea4-209">See [Configuration: File configuration provider](xref:fundamentals/configuration/index#file-configuration-provider).</span></span>
* <span data-ttu-id="bfea4-210">proměnné prostředí (nastavený v každém systému je hostitelem aplikace).</span><span class="sxs-lookup"><span data-stu-id="bfea4-210">environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="bfea4-211">Zobrazit [konfigurace: Zprostředkovatel konfigurace souboru](xref:fundamentals/configuration/index#file-configuration-provider) a [bezpečné ukládání tajných kódů aplikace při vývoji: Proměnné prostředí](xref:security/app-secrets#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="bfea4-211">See [Configuration: File configuration provider](xref:fundamentals/configuration/index#file-configuration-provider) and [Safe storage of app secrets in development: Environment variables](xref:security/app-secrets#environment-variables).</span></span>
* <span data-ttu-id="bfea4-212">Tajný klíč správce (ve vývojovém prostředí pouze).</span><span class="sxs-lookup"><span data-stu-id="bfea4-212">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="bfea4-213">Viz <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="bfea4-213">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="bfea4-214">Na základě prostředí při spuštění třídy a metody</span><span class="sxs-lookup"><span data-stu-id="bfea4-214">Environment-based Startup class and methods</span></span>

### <a name="startup-class-conventions"></a><span data-ttu-id="bfea4-215">Při spuštění třídy konvence</span><span class="sxs-lookup"><span data-stu-id="bfea4-215">Startup class conventions</span></span>

<span data-ttu-id="bfea4-216">Při spuštění aplikace ASP.NET Core [třídu pro spuštění](xref:fundamentals/startup) bootstraps aplikace.</span><span class="sxs-lookup"><span data-stu-id="bfea4-216">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="bfea4-217">Aplikace může definovat oddělenou `Startup` třídu pro různá prostředí (například `StartupDevelopment`). Odpovídající `Startup` třída je vybrána v době běhu.</span><span class="sxs-lookup"><span data-stu-id="bfea4-217">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`), and the appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="bfea4-218">Třída, jejíž název má příponu odpovídající aktuálnímu prostředí, je upřednostněna.</span><span class="sxs-lookup"><span data-stu-id="bfea4-218">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="bfea4-219">Pokud odpovídající `Startup{EnvironmentName}` třídy nenajde, `Startup` třída se používá.</span><span class="sxs-lookup"><span data-stu-id="bfea4-219">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span>

<span data-ttu-id="bfea4-220">K implementaci založeném na prostředí `Startup` třídy, vytvoří `Startup{EnvironmentName}` třídy pro každé prostředí, které se používají a záložní `Startup` třídy:</span><span class="sxs-lookup"><span data-stu-id="bfea4-220">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

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

<span data-ttu-id="bfea4-221">Použití [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) přetížení, které přijímá název sestavení:</span><span class="sxs-lookup"><span data-stu-id="bfea4-221">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

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

### <a name="startup-method-conventions"></a><span data-ttu-id="bfea4-222">Po spuštění metody konvence</span><span class="sxs-lookup"><span data-stu-id="bfea4-222">Startup method conventions</span></span>

<span data-ttu-id="bfea4-223">[Konfigurace](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) a [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) podpory verzí specifických pro prostředí formu `Configure<EnvironmentName>` a `Configure<EnvironmentName>Services`:</span><span class="sxs-lookup"><span data-stu-id="bfea4-223">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`:</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,51)]

## <a name="additional-resources"></a><span data-ttu-id="bfea4-224">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="bfea4-224">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="bfea4-225">IHostingEnvironment.EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="bfea4-225">IHostingEnvironment.EnvironmentName</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname)
