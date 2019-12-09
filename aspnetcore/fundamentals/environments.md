---
title: Použití více prostředí v ASP.NET Core
author: rick-anderson
description: Naučte se řídit chování aplikace v různých prostředích aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: fundamentals/environments
ms.openlocfilehash: affbb95273c91fe5bf452e0e1ebefa669297304c
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944318"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="18ad4-103">Použití více prostředí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18ad4-103">Use multiple environments in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="18ad4-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="18ad4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="18ad4-105">ASP.NET Core konfiguruje chování aplikace na základě běhového prostředí pomocí proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="18ad4-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="18ad4-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="18ad4-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="18ad4-107">Prostředí</span><span class="sxs-lookup"><span data-stu-id="18ad4-107">Environments</span></span>

<span data-ttu-id="18ad4-108">ASP.NET Core přečte proměnnou prostředí `ASPNETCORE_ENVIRONMENT` při spuštění aplikace a uloží hodnotu v [IWebHostEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="18ad4-108">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span></span> <span data-ttu-id="18ad4-109">`ASPNETCORE_ENVIRONMENT` lze nastavit na libovolnou hodnotu, ale rozhraní poskytuje tři hodnoty:</span><span class="sxs-lookup"><span data-stu-id="18ad4-109">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.Extensions.Hosting.Environments.Development>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="18ad4-110"><xref:Microsoft.Extensions.Hosting.Environments.Production> (výchozí)</span><span class="sxs-lookup"><span data-stu-id="18ad4-110"><xref:Microsoft.Extensions.Hosting.Environments.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="18ad4-111">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="18ad4-111">The preceding code:</span></span>

* <span data-ttu-id="18ad4-112">Volá [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) , když je `ASPNETCORE_ENVIRONMENT` nastaveno na `Development`.</span><span class="sxs-lookup"><span data-stu-id="18ad4-112">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="18ad4-113">Volá [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) , pokud je hodnota `ASPNETCORE_ENVIRONMENT` nastavena na jednu z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="18ad4-113">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="18ad4-114">[Pomocná značka prostředí](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) používá hodnotu `IHostingEnvironment.EnvironmentName` k zahrnutí nebo vyloučení značek v elementu:</span><span class="sxs-lookup"><span data-stu-id="18ad4-114">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="18ad4-115">V systému Windows a macOS hodnoty proměnných prostředí a hodnot nerozlišují velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="18ad4-115">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="18ad4-116">Ve výchozím nastavení se v proměnných a hodnotách prostředí Linux **rozlišují velká a malá písmena** .</span><span class="sxs-lookup"><span data-stu-id="18ad4-116">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="18ad4-117">Vývoj</span><span class="sxs-lookup"><span data-stu-id="18ad4-117">Development</span></span>

<span data-ttu-id="18ad4-118">Vývojové prostředí může povolit funkce, které by se neměly zveřejnit v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="18ad4-118">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="18ad4-119">Například šablony ASP.NET Core umožňují [stránku s výjimkou vývojáře](xref:fundamentals/error-handling#developer-exception-page) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="18ad4-119">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="18ad4-120">Prostředí pro vývoj místních počítačů lze nastavit v souboru *Properties\launchSettings.JSON* projektu.</span><span class="sxs-lookup"><span data-stu-id="18ad4-120">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="18ad4-121">Hodnoty prostředí nastavené v hodnotách přepisu *launchSettings. JSON* nastavené v prostředí systému</span><span class="sxs-lookup"><span data-stu-id="18ad4-121">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="18ad4-122">Následující JSON zobrazuje tři profily ze souboru *launchSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="18ad4-122">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

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
> <span data-ttu-id="18ad4-123">Vlastnost `applicationUrl` v *launchSettings. JSON* může určovat seznam adres URL serveru.</span><span class="sxs-lookup"><span data-stu-id="18ad4-123">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="18ad4-124">Použijte středník mezi adresami URL v seznamu:</span><span class="sxs-lookup"><span data-stu-id="18ad4-124">Use a semicolon between the URLs in the list:</span></span>
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

<span data-ttu-id="18ad4-125">Když se aplikace spustí pomocí [příkazu dotnet](/dotnet/core/tools/dotnet-run), použije se první profil s `"commandName": "Project"`.</span><span class="sxs-lookup"><span data-stu-id="18ad4-125">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="18ad4-126">Hodnota `commandName` určuje webový server, který se má spustit.</span><span class="sxs-lookup"><span data-stu-id="18ad4-126">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="18ad4-127">`commandName` může být jedna z následujících:</span><span class="sxs-lookup"><span data-stu-id="18ad4-127">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="18ad4-128">`Project` (spouští Kestrel)</span><span class="sxs-lookup"><span data-stu-id="18ad4-128">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="18ad4-129">Když se aplikace spustí s použitím [příkazu dotnet](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="18ad4-129">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="18ad4-130">*launchSettings. JSON* je přečtený, pokud je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="18ad4-130">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="18ad4-131">nastavení `environmentVariables` v proměnných prostředí přepsat *launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="18ad4-131">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="18ad4-132">Zobrazí se hostující prostředí.</span><span class="sxs-lookup"><span data-stu-id="18ad4-132">The hosting environment is displayed.</span></span>

<span data-ttu-id="18ad4-133">Následující výstup ukazuje aplikaci spuštěnou pomocí [příkazu dotnet](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="18ad4-133">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="18ad4-134">Karta **ladění** vlastností projektu sady Visual Studio poskytuje grafické uživatelské rozhraní pro úpravy souboru *launchSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="18ad4-134">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Vlastnosti projektu nastavení proměnných prostředí](environments/_static/project-properties-debug.png)

<span data-ttu-id="18ad4-136">Změny provedené v profilech projektu se neprojeví, dokud se webový server nerestartuje.</span><span class="sxs-lookup"><span data-stu-id="18ad4-136">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="18ad4-137">Kestrel se musí restartovat, aby bylo možné zjistit změny provedené ve svém prostředí.</span><span class="sxs-lookup"><span data-stu-id="18ad4-137">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="18ad4-138">*launchSettings. JSON* by neměl ukládat tajné klíče.</span><span class="sxs-lookup"><span data-stu-id="18ad4-138">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="18ad4-139">[Nástroj Správce tajných](xref:security/app-secrets) klíčů je možné použít k ukládání tajných kódů pro místní vývoj.</span><span class="sxs-lookup"><span data-stu-id="18ad4-139">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="18ad4-140">Při použití [Visual Studio Code](https://code.visualstudio.com/)lze proměnné prostředí nastavit v souboru *. VSCode/Launch. JSON* .</span><span class="sxs-lookup"><span data-stu-id="18ad4-140">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="18ad4-141">Následující příklad nastaví prostředí na `Development`:</span><span class="sxs-lookup"><span data-stu-id="18ad4-141">The following example sets the environment to `Development`:</span></span>

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

<span data-ttu-id="18ad4-142">Soubor *. VSCode/Launch. JSON* v projektu není čten při spuštění aplikace s `dotnet run` stejným způsobem jako *vlastnosti/launchSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="18ad4-142">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="18ad4-143">Při spouštění aplikace ve vývoji, která nemá soubor *launchSettings. JSON* , buď nastavte prostředí s proměnnou prostředí nebo argumentem příkazového řádku na příkaz `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="18ad4-143">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="18ad4-144">Provoz</span><span class="sxs-lookup"><span data-stu-id="18ad4-144">Production</span></span>

<span data-ttu-id="18ad4-145">Provozní prostředí by mělo být nakonfigurované tak, aby maximalizovalo zabezpečení, výkon a odolnost aplikací.</span><span class="sxs-lookup"><span data-stu-id="18ad4-145">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="18ad4-146">Mezi běžná nastavení, která se liší od vývoje, patří:</span><span class="sxs-lookup"><span data-stu-id="18ad4-146">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="18ad4-147">Vyrovnávací.</span><span class="sxs-lookup"><span data-stu-id="18ad4-147">Caching.</span></span>
* <span data-ttu-id="18ad4-148">Prostředky na straně klienta jsou seskupené, minifikovaného a potenciálně poskytované z CDN.</span><span class="sxs-lookup"><span data-stu-id="18ad4-148">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="18ad4-149">Chybové stránky diagnostiky jsou zakázané.</span><span class="sxs-lookup"><span data-stu-id="18ad4-149">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="18ad4-150">Jsou povoleny popisné chybové stránky.</span><span class="sxs-lookup"><span data-stu-id="18ad4-150">Friendly error pages enabled.</span></span>
* <span data-ttu-id="18ad4-151">Protokolování a sledování výroby je povolené.</span><span class="sxs-lookup"><span data-stu-id="18ad4-151">Production logging and monitoring enabled.</span></span> <span data-ttu-id="18ad4-152">Například [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="18ad4-152">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="18ad4-153">Nastavení prostředí</span><span class="sxs-lookup"><span data-stu-id="18ad4-153">Set the environment</span></span>

<span data-ttu-id="18ad4-154">Je často užitečné nastavit konkrétní prostředí pro testování pomocí proměnné prostředí nebo nastavení platformy.</span><span class="sxs-lookup"><span data-stu-id="18ad4-154">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="18ad4-155">Pokud prostředí není nastavené, nastaví se jako výchozí `Production`, což zakáže většinu funkcí ladění.</span><span class="sxs-lookup"><span data-stu-id="18ad4-155">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="18ad4-156">Metoda nastavení prostředí závisí na operačním systému.</span><span class="sxs-lookup"><span data-stu-id="18ad4-156">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="18ad4-157">Po sestavení hostitele určuje poslední nastavení prostředí načtené aplikací prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="18ad4-157">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="18ad4-158">Prostředí aplikace se nedá změnit, když je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="18ad4-158">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="18ad4-159">Nastavení proměnné prostředí nebo platformy</span><span class="sxs-lookup"><span data-stu-id="18ad4-159">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="18ad4-160">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="18ad4-160">Azure App Service</span></span>

<span data-ttu-id="18ad4-161">Pokud chcete nastavit prostředí v [Azure App Service](https://azure.microsoft.com/services/app-service/), proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="18ad4-161">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="18ad4-162">Vyberte aplikaci z okna **App Services** .</span><span class="sxs-lookup"><span data-stu-id="18ad4-162">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="18ad4-163">Ve skupině **Nastavení** vyberte okno **nastavení aplikace** .</span><span class="sxs-lookup"><span data-stu-id="18ad4-163">In the **SETTINGS** group, select the **Application settings** blade.</span></span>
1. <span data-ttu-id="18ad4-164">V oblasti **nastavení aplikace** vyberte **Přidat nové nastavení**.</span><span class="sxs-lookup"><span data-stu-id="18ad4-164">In the **Application settings** area, select **Add new setting**.</span></span>
1. <span data-ttu-id="18ad4-165">Pro **Zadejte název zadejte**`ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="18ad4-165">For **Enter a name**, provide `ASPNETCORE_ENVIRONMENT`.</span></span> <span data-ttu-id="18ad4-166">V poli **zadat hodnotu zadejte**prostředí (například `Staging`).</span><span class="sxs-lookup"><span data-stu-id="18ad4-166">For **Enter a value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="18ad4-167">Zaškrtněte políčko **nastavení slotu** , pokud chcete, aby nastavení prostředí zůstalo v aktuální pozici, když budou sloty nasazení prohozeny.</span><span class="sxs-lookup"><span data-stu-id="18ad4-167">Select the **Slot Setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="18ad4-168">Další informace najdete v tématu [dokumentace k Azure: která nastavení jsou Zahozená?](/azure/app-service/web-sites-staged-publishing).</span><span class="sxs-lookup"><span data-stu-id="18ad4-168">For more information, see [Azure Documentation: Which settings are swapped?](/azure/app-service/web-sites-staged-publishing).</span></span>
1. <span data-ttu-id="18ad4-169">V horní části okna vyberte **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="18ad4-169">Select **Save** at the top of the blade.</span></span>

<span data-ttu-id="18ad4-170">Azure App Service automaticky restartuje aplikaci po přidání, změně nebo odstranění nastavení aplikace (proměnná prostředí) v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="18ad4-170">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="18ad4-171">Windows</span><span class="sxs-lookup"><span data-stu-id="18ad4-171">Windows</span></span>

<span data-ttu-id="18ad4-172">Chcete-li nastavit `ASPNETCORE_ENVIRONMENT` pro aktuální relaci při zahájení aplikace pomocí [příkazu dotnet](/dotnet/core/tools/dotnet-run), jsou použity následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="18ad4-172">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="18ad4-173">**Příkazový řádek**</span><span class="sxs-lookup"><span data-stu-id="18ad4-173">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="18ad4-174">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="18ad4-174">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="18ad4-175">Tyto příkazy se projeví pouze pro aktuální okno.</span><span class="sxs-lookup"><span data-stu-id="18ad4-175">These commands only take effect for the current window.</span></span> <span data-ttu-id="18ad4-176">Po zavření okna se nastavení `ASPNETCORE_ENVIRONMENT` vrátí k výchozímu nastavení nebo hodnotě počítače.</span><span class="sxs-lookup"><span data-stu-id="18ad4-176">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="18ad4-177">K nastavení hodnoty globálně ve Windows použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="18ad4-177">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="18ad4-178">Otevřete **Ovládací panely** > **systémové** > **Pokročilé nastavení systému** a přidejte nebo upravte `ASPNETCORE_ENVIRONMENT`ovou hodnotu:</span><span class="sxs-lookup"><span data-stu-id="18ad4-178">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Rozšířené vlastnosti systému](environments/_static/systemsetting_environment.png)

  ![Proměnná prostředí ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="18ad4-181">Otevřete příkazový řádek pro správu a použijte příkaz `setx` nebo otevřete příkazový řádek prostředí PowerShell pro správu a použijte `[Environment]::SetEnvironmentVariable`:</span><span class="sxs-lookup"><span data-stu-id="18ad4-181">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="18ad4-182">**Příkazový řádek**</span><span class="sxs-lookup"><span data-stu-id="18ad4-182">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="18ad4-183">Přepínač `/M` označuje nastavení proměnné prostředí na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="18ad4-183">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="18ad4-184">Pokud není použit přepínač `/M`, je pro uživatelský účet nastavena proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="18ad4-184">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="18ad4-185">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="18ad4-185">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="18ad4-186">Hodnota možnosti `Machine` označuje, že se má nastavit proměnná prostředí na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="18ad4-186">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="18ad4-187">Pokud je hodnota možnosti změněna na `User`, je pro uživatelský účet nastavena proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="18ad4-187">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="18ad4-188">Pokud je proměnná prostředí `ASPNETCORE_ENVIRONMENT` nastavena globálně, projeví se `dotnet run` v jakémkoli příkazovém okně otevřeném po nastavení hodnoty.</span><span class="sxs-lookup"><span data-stu-id="18ad4-188">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="18ad4-189">**web.config**</span><span class="sxs-lookup"><span data-stu-id="18ad4-189">**web.config**</span></span>

<span data-ttu-id="18ad4-190">Chcete-li nastavit proměnnou prostředí `ASPNETCORE_ENVIRONMENT` pomocí *souboru Web. config*, přečtěte si část *nastavení proměnných prostředí* v <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="18ad4-190">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="18ad4-191">**Projektový soubor nebo profil publikování**</span><span class="sxs-lookup"><span data-stu-id="18ad4-191">**Project file or publish profile**</span></span>

<span data-ttu-id="18ad4-192">**Pro nasazení služby Windows IIS:** Do [profilu publikování (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu zahrňte vlastnost `<EnvironmentName>`.</span><span class="sxs-lookup"><span data-stu-id="18ad4-192">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="18ad4-193">Tento přístup nastaví prostředí v *souboru Web. config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="18ad4-193">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="18ad4-194">**Pro fond aplikací služby IIS**</span><span class="sxs-lookup"><span data-stu-id="18ad4-194">**Per IIS Application Pool**</span></span>

<span data-ttu-id="18ad4-195">Pokud chcete nastavit proměnnou prostředí `ASPNETCORE_ENVIRONMENT` pro aplikaci spuštěnou v izolovaném fondu aplikací (podporovaném ve službě IIS 10,0 nebo novější), přečtěte si část *příkazového řádku Appcmd. exe* v tématu [proměnné prostředí &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) .</span><span class="sxs-lookup"><span data-stu-id="18ad4-195">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="18ad4-196">Pokud je pro fond aplikací nastavená proměnná prostředí `ASPNETCORE_ENVIRONMENT`, přepíše její hodnota nastavení na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="18ad4-196">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="18ad4-197">Při hostování aplikace ve službě IIS a přidání nebo změny proměnné prostředí `ASPNETCORE_ENVIRONMENT` použijte libovolný z následujících přístupů, aby byla nová hodnota vyzvednuta aplikacemi:</span><span class="sxs-lookup"><span data-stu-id="18ad4-197">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="18ad4-198">Spustí `net stop was /y` následovaný `net start w3svc` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="18ad4-198">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="18ad4-199">Restartujte server.</span><span class="sxs-lookup"><span data-stu-id="18ad4-199">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="18ad4-200">macOS</span><span class="sxs-lookup"><span data-stu-id="18ad4-200">macOS</span></span>

<span data-ttu-id="18ad4-201">Nastavení aktuálního prostředí pro macOS se dá při spuštění aplikace provést na řádku:</span><span class="sxs-lookup"><span data-stu-id="18ad4-201">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="18ad4-202">Případně můžete nastavit prostředí pomocí `export` před spuštěním aplikace:</span><span class="sxs-lookup"><span data-stu-id="18ad4-202">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="18ad4-203">Proměnné prostředí na úrovni počítače jsou nastaveny v souboru *. bashrc* nebo *. bash_profile* .</span><span class="sxs-lookup"><span data-stu-id="18ad4-203">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="18ad4-204">Upravte soubor pomocí libovolného textového editoru.</span><span class="sxs-lookup"><span data-stu-id="18ad4-204">Edit the file using any text editor.</span></span> <span data-ttu-id="18ad4-205">Přidejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="18ad4-205">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="18ad4-206">Linux</span><span class="sxs-lookup"><span data-stu-id="18ad4-206">Linux</span></span>

<span data-ttu-id="18ad4-207">Pro Linux distribuce použijte na příkazovém řádku příkaz `export` pro nastavení proměnné založené na relaci a soubor *bash_profile* pro nastavení prostředí na úrovni počítače.</span><span class="sxs-lookup"><span data-stu-id="18ad4-207">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="18ad4-208">Nastavení prostředí v kódu</span><span class="sxs-lookup"><span data-stu-id="18ad4-208">Set the environment in code</span></span>

<span data-ttu-id="18ad4-209">Při sestavování hostitele volejte <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*>.</span><span class="sxs-lookup"><span data-stu-id="18ad4-209">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="18ad4-210">Podívejte se na téma <xref:fundamentals/host/generic-host#environmentname>.</span><span class="sxs-lookup"><span data-stu-id="18ad4-210">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>


### <a name="configuration-by-environment"></a><span data-ttu-id="18ad4-211">Konfigurace podle prostředí</span><span class="sxs-lookup"><span data-stu-id="18ad4-211">Configuration by environment</span></span>

<span data-ttu-id="18ad4-212">K načtení konfigurace podle prostředí doporučujeme:</span><span class="sxs-lookup"><span data-stu-id="18ad4-212">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="18ad4-213">soubory *appSettings* (*appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="18ad4-213">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="18ad4-214">Podívejte se na téma <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="18ad4-214">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="18ad4-215">Proměnné prostředí (nastavené v každém systému, ve kterém je aplikace hostovaná).</span><span class="sxs-lookup"><span data-stu-id="18ad4-215">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="18ad4-216">Viz témata <xref:fundamentals/host/generic-host#environmentname> a <xref:security/app-secrets#environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="18ad4-216">See <xref:fundamentals/host/generic-host#environmentname> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="18ad4-217">Správce tajných klíčů (jenom ve vývojovém prostředí).</span><span class="sxs-lookup"><span data-stu-id="18ad4-217">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="18ad4-218">Podívejte se na téma <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="18ad4-218">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="18ad4-219">Třída a metody spouštění založené na prostředí</span><span class="sxs-lookup"><span data-stu-id="18ad4-219">Environment-based Startup class and methods</span></span>

### <a name="inject-iwebhostenvironment-into-startupconfigure"></a><span data-ttu-id="18ad4-220">Vloží IWebHostEnvironment do Startup. Configure</span><span class="sxs-lookup"><span data-stu-id="18ad4-220">Inject IWebHostEnvironment into Startup.Configure</span></span>

<span data-ttu-id="18ad4-221">Vloží <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="18ad4-221">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="18ad4-222">Tento přístup je užitečný, když aplikace vyžaduje úpravu `Startup.Configure` jenom pro několik prostředí s minimálními rozdíly v kódu na prostředí.</span><span class="sxs-lookup"><span data-stu-id="18ad4-222">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>

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

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="18ad4-223">Vložení IWebHostEnvironment do spouštěcí třídy</span><span class="sxs-lookup"><span data-stu-id="18ad4-223">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="18ad4-224">Vloží <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do konstruktoru `Startup`.</span><span class="sxs-lookup"><span data-stu-id="18ad4-224">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="18ad4-225">Tento přístup je užitečný, když aplikace vyžaduje konfiguraci `Startup` jenom pro několik prostředí s minimálními rozdíly v kódu pro každé prostředí.</span><span class="sxs-lookup"><span data-stu-id="18ad4-225">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="18ad4-226">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="18ad4-226">In the following example:</span></span>

* <span data-ttu-id="18ad4-227">Prostředí se uchovává v poli `_env`.</span><span class="sxs-lookup"><span data-stu-id="18ad4-227">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="18ad4-228">`_env` se používá v `ConfigureServices` a `Configure` pro použití konfigurace spuštění na základě prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="18ad4-228">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

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
### <a name="startup-class-conventions"></a><span data-ttu-id="18ad4-229">Konvence třídy spouštění</span><span class="sxs-lookup"><span data-stu-id="18ad4-229">Startup class conventions</span></span>

<span data-ttu-id="18ad4-230">Při spuštění aplikace ASP.NET Core spustí [spouštěcí třída](xref:fundamentals/startup) aplikaci.</span><span class="sxs-lookup"><span data-stu-id="18ad4-230">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="18ad4-231">Aplikace může definovat samostatné třídy `Startup` pro různá prostředí (například `StartupDevelopment`).</span><span class="sxs-lookup"><span data-stu-id="18ad4-231">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="18ad4-232">Příslušná třída `Startup` je vybrána za běhu.</span><span class="sxs-lookup"><span data-stu-id="18ad4-232">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="18ad4-233">Třída, jejíž název má příponu odpovídající aktuálnímu prostředí, je upřednostněna.</span><span class="sxs-lookup"><span data-stu-id="18ad4-233">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="18ad4-234">Pokud není nalezena shodná `Startup{EnvironmentName}` třída, je použita třída `Startup`.</span><span class="sxs-lookup"><span data-stu-id="18ad4-234">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="18ad4-235">Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly v kódu pro každé prostředí.</span><span class="sxs-lookup"><span data-stu-id="18ad4-235">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="18ad4-236">Chcete-li implementovat třídy `Startup` založené na prostředí, vytvořte třídu `Startup{EnvironmentName}` pro každé používané prostředí a záložní třídu `Startup`:</span><span class="sxs-lookup"><span data-stu-id="18ad4-236">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

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

<span data-ttu-id="18ad4-237">Použijte přetížení [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) , které přijímá název sestavení:</span><span class="sxs-lookup"><span data-stu-id="18ad4-237">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

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

### <a name="startup-method-conventions"></a><span data-ttu-id="18ad4-238">Konvence metody spuštění</span><span class="sxs-lookup"><span data-stu-id="18ad4-238">Startup method conventions</span></span>

<span data-ttu-id="18ad4-239">[Nakonfigurujte](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) a [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) , které podporují verze formuláře `Configure<EnvironmentName>` a `Configure<EnvironmentName>Services`na konkrétní prostředí.</span><span class="sxs-lookup"><span data-stu-id="18ad4-239">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="18ad4-240">Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly v kódu pro každé prostředí.</span><span class="sxs-lookup"><span data-stu-id="18ad4-240">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="18ad4-241">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="18ad4-241">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="18ad4-242">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="18ad4-242">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="18ad4-243">ASP.NET Core konfiguruje chování aplikace na základě běhového prostředí pomocí proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="18ad4-243">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="18ad4-244">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="18ad4-244">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="18ad4-245">Prostředí</span><span class="sxs-lookup"><span data-stu-id="18ad4-245">Environments</span></span>

<span data-ttu-id="18ad4-246">ASP.NET Core přečte proměnnou prostředí `ASPNETCORE_ENVIRONMENT` při spuštění aplikace a uloží hodnotu v [IHostingEnvironment. Environment](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="18ad4-246">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="18ad4-247">`ASPNETCORE_ENVIRONMENT` lze nastavit na libovolnou hodnotu, ale rozhraní poskytuje tři hodnoty:</span><span class="sxs-lookup"><span data-stu-id="18ad4-247">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="18ad4-248"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (výchozí)</span><span class="sxs-lookup"><span data-stu-id="18ad4-248"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="18ad4-249">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="18ad4-249">The preceding code:</span></span>

* <span data-ttu-id="18ad4-250">Volá [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) , když je `ASPNETCORE_ENVIRONMENT` nastaveno na `Development`.</span><span class="sxs-lookup"><span data-stu-id="18ad4-250">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="18ad4-251">Volá [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) , pokud je hodnota `ASPNETCORE_ENVIRONMENT` nastavena na jednu z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="18ad4-251">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="18ad4-252">[Pomocná značka prostředí](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) používá hodnotu `IHostingEnvironment.EnvironmentName` k zahrnutí nebo vyloučení značek v elementu:</span><span class="sxs-lookup"><span data-stu-id="18ad4-252">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="18ad4-253">V systému Windows a macOS hodnoty proměnných prostředí a hodnot nerozlišují velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="18ad4-253">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="18ad4-254">Ve výchozím nastavení se v proměnných a hodnotách prostředí Linux **rozlišují velká a malá písmena** .</span><span class="sxs-lookup"><span data-stu-id="18ad4-254">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="18ad4-255">Vývoj</span><span class="sxs-lookup"><span data-stu-id="18ad4-255">Development</span></span>

<span data-ttu-id="18ad4-256">Vývojové prostředí může povolit funkce, které by se neměly zveřejnit v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="18ad4-256">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="18ad4-257">Například šablony ASP.NET Core umožňují [stránku s výjimkou vývojáře](xref:fundamentals/error-handling#developer-exception-page) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="18ad4-257">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="18ad4-258">Prostředí pro vývoj místních počítačů lze nastavit v souboru *Properties\launchSettings.JSON* projektu.</span><span class="sxs-lookup"><span data-stu-id="18ad4-258">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="18ad4-259">Hodnoty prostředí nastavené v hodnotách přepisu *launchSettings. JSON* nastavené v prostředí systému</span><span class="sxs-lookup"><span data-stu-id="18ad4-259">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="18ad4-260">Následující JSON zobrazuje tři profily ze souboru *launchSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="18ad4-260">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

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
> <span data-ttu-id="18ad4-261">Vlastnost `applicationUrl` v *launchSettings. JSON* může určovat seznam adres URL serveru.</span><span class="sxs-lookup"><span data-stu-id="18ad4-261">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="18ad4-262">Použijte středník mezi adresami URL v seznamu:</span><span class="sxs-lookup"><span data-stu-id="18ad4-262">Use a semicolon between the URLs in the list:</span></span>
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

<span data-ttu-id="18ad4-263">Když se aplikace spustí pomocí [příkazu dotnet](/dotnet/core/tools/dotnet-run), použije se první profil s `"commandName": "Project"`.</span><span class="sxs-lookup"><span data-stu-id="18ad4-263">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="18ad4-264">Hodnota `commandName` určuje webový server, který se má spustit.</span><span class="sxs-lookup"><span data-stu-id="18ad4-264">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="18ad4-265">`commandName` může být jedna z následujících:</span><span class="sxs-lookup"><span data-stu-id="18ad4-265">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="18ad4-266">`Project` (spouští Kestrel)</span><span class="sxs-lookup"><span data-stu-id="18ad4-266">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="18ad4-267">Když se aplikace spustí s použitím [příkazu dotnet](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="18ad4-267">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="18ad4-268">*launchSettings. JSON* je přečtený, pokud je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="18ad4-268">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="18ad4-269">nastavení `environmentVariables` v proměnných prostředí přepsat *launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="18ad4-269">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="18ad4-270">Zobrazí se hostující prostředí.</span><span class="sxs-lookup"><span data-stu-id="18ad4-270">The hosting environment is displayed.</span></span>

<span data-ttu-id="18ad4-271">Následující výstup ukazuje aplikaci spuštěnou pomocí [příkazu dotnet](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="18ad4-271">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="18ad4-272">Karta **ladění** vlastností projektu sady Visual Studio poskytuje grafické uživatelské rozhraní pro úpravy souboru *launchSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="18ad4-272">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Vlastnosti projektu nastavení proměnných prostředí](environments/_static/project-properties-debug.png)

<span data-ttu-id="18ad4-274">Změny provedené v profilech projektu se neprojeví, dokud se webový server nerestartuje.</span><span class="sxs-lookup"><span data-stu-id="18ad4-274">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="18ad4-275">Kestrel se musí restartovat, aby bylo možné zjistit změny provedené ve svém prostředí.</span><span class="sxs-lookup"><span data-stu-id="18ad4-275">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="18ad4-276">*launchSettings. JSON* by neměl ukládat tajné klíče.</span><span class="sxs-lookup"><span data-stu-id="18ad4-276">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="18ad4-277">[Nástroj Správce tajných](xref:security/app-secrets) klíčů je možné použít k ukládání tajných kódů pro místní vývoj.</span><span class="sxs-lookup"><span data-stu-id="18ad4-277">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="18ad4-278">Při použití [Visual Studio Code](https://code.visualstudio.com/)lze proměnné prostředí nastavit v souboru *. VSCode/Launch. JSON* .</span><span class="sxs-lookup"><span data-stu-id="18ad4-278">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="18ad4-279">Následující příklad nastaví prostředí na `Development`:</span><span class="sxs-lookup"><span data-stu-id="18ad4-279">The following example sets the environment to `Development`:</span></span>

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

<span data-ttu-id="18ad4-280">Soubor *. VSCode/Launch. JSON* v projektu není čten při spuštění aplikace s `dotnet run` stejným způsobem jako *vlastnosti/launchSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="18ad4-280">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="18ad4-281">Při spouštění aplikace ve vývoji, která nemá soubor *launchSettings. JSON* , buď nastavte prostředí s proměnnou prostředí nebo argumentem příkazového řádku na příkaz `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="18ad4-281">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="18ad4-282">Provoz</span><span class="sxs-lookup"><span data-stu-id="18ad4-282">Production</span></span>

<span data-ttu-id="18ad4-283">Provozní prostředí by mělo být nakonfigurované tak, aby maximalizovalo zabezpečení, výkon a odolnost aplikací.</span><span class="sxs-lookup"><span data-stu-id="18ad4-283">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="18ad4-284">Mezi běžná nastavení, která se liší od vývoje, patří:</span><span class="sxs-lookup"><span data-stu-id="18ad4-284">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="18ad4-285">Vyrovnávací.</span><span class="sxs-lookup"><span data-stu-id="18ad4-285">Caching.</span></span>
* <span data-ttu-id="18ad4-286">Prostředky na straně klienta jsou seskupené, minifikovaného a potenciálně poskytované z CDN.</span><span class="sxs-lookup"><span data-stu-id="18ad4-286">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="18ad4-287">Chybové stránky diagnostiky jsou zakázané.</span><span class="sxs-lookup"><span data-stu-id="18ad4-287">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="18ad4-288">Jsou povoleny popisné chybové stránky.</span><span class="sxs-lookup"><span data-stu-id="18ad4-288">Friendly error pages enabled.</span></span>
* <span data-ttu-id="18ad4-289">Protokolování a sledování výroby je povolené.</span><span class="sxs-lookup"><span data-stu-id="18ad4-289">Production logging and monitoring enabled.</span></span> <span data-ttu-id="18ad4-290">Například [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="18ad4-290">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="18ad4-291">Nastavení prostředí</span><span class="sxs-lookup"><span data-stu-id="18ad4-291">Set the environment</span></span>

<span data-ttu-id="18ad4-292">Je často užitečné nastavit konkrétní prostředí pro testování pomocí proměnné prostředí nebo nastavení platformy.</span><span class="sxs-lookup"><span data-stu-id="18ad4-292">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="18ad4-293">Pokud prostředí není nastavené, nastaví se jako výchozí `Production`, což zakáže většinu funkcí ladění.</span><span class="sxs-lookup"><span data-stu-id="18ad4-293">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="18ad4-294">Metoda nastavení prostředí závisí na operačním systému.</span><span class="sxs-lookup"><span data-stu-id="18ad4-294">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="18ad4-295">Po sestavení hostitele určuje poslední nastavení prostředí načtené aplikací prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="18ad4-295">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="18ad4-296">Prostředí aplikace se nedá změnit, když je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="18ad4-296">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="18ad4-297">Nastavení proměnné prostředí nebo platformy</span><span class="sxs-lookup"><span data-stu-id="18ad4-297">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="18ad4-298">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="18ad4-298">Azure App Service</span></span>

<span data-ttu-id="18ad4-299">Pokud chcete nastavit prostředí v [Azure App Service](https://azure.microsoft.com/services/app-service/), proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="18ad4-299">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="18ad4-300">Vyberte aplikaci z okna **App Services** .</span><span class="sxs-lookup"><span data-stu-id="18ad4-300">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="18ad4-301">Ve skupině **Nastavení** vyberte okno **nastavení aplikace** .</span><span class="sxs-lookup"><span data-stu-id="18ad4-301">In the **SETTINGS** group, select the **Application settings** blade.</span></span>
1. <span data-ttu-id="18ad4-302">V oblasti **nastavení aplikace** vyberte **Přidat nové nastavení**.</span><span class="sxs-lookup"><span data-stu-id="18ad4-302">In the **Application settings** area, select **Add new setting**.</span></span>
1. <span data-ttu-id="18ad4-303">Pro **Zadejte název zadejte**`ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="18ad4-303">For **Enter a name**, provide `ASPNETCORE_ENVIRONMENT`.</span></span> <span data-ttu-id="18ad4-304">V poli **zadat hodnotu zadejte**prostředí (například `Staging`).</span><span class="sxs-lookup"><span data-stu-id="18ad4-304">For **Enter a value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="18ad4-305">Zaškrtněte políčko **nastavení slotu** , pokud chcete, aby nastavení prostředí zůstalo v aktuální pozici, když budou sloty nasazení prohozeny.</span><span class="sxs-lookup"><span data-stu-id="18ad4-305">Select the **Slot Setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="18ad4-306">Další informace najdete v tématu [dokumentace k Azure: která nastavení jsou Zahozená?](/azure/app-service/web-sites-staged-publishing).</span><span class="sxs-lookup"><span data-stu-id="18ad4-306">For more information, see [Azure Documentation: Which settings are swapped?](/azure/app-service/web-sites-staged-publishing).</span></span>
1. <span data-ttu-id="18ad4-307">V horní části okna vyberte **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="18ad4-307">Select **Save** at the top of the blade.</span></span>

<span data-ttu-id="18ad4-308">Azure App Service automaticky restartuje aplikaci po přidání, změně nebo odstranění nastavení aplikace (proměnná prostředí) v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="18ad4-308">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="18ad4-309">Windows</span><span class="sxs-lookup"><span data-stu-id="18ad4-309">Windows</span></span>

<span data-ttu-id="18ad4-310">Chcete-li nastavit `ASPNETCORE_ENVIRONMENT` pro aktuální relaci při zahájení aplikace pomocí [příkazu dotnet](/dotnet/core/tools/dotnet-run), jsou použity následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="18ad4-310">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="18ad4-311">**Příkazový řádek**</span><span class="sxs-lookup"><span data-stu-id="18ad4-311">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="18ad4-312">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="18ad4-312">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="18ad4-313">Tyto příkazy se projeví pouze pro aktuální okno.</span><span class="sxs-lookup"><span data-stu-id="18ad4-313">These commands only take effect for the current window.</span></span> <span data-ttu-id="18ad4-314">Po zavření okna se nastavení `ASPNETCORE_ENVIRONMENT` vrátí k výchozímu nastavení nebo hodnotě počítače.</span><span class="sxs-lookup"><span data-stu-id="18ad4-314">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="18ad4-315">K nastavení hodnoty globálně ve Windows použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="18ad4-315">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="18ad4-316">Otevřete **Ovládací panely** > **systémové** > **Pokročilé nastavení systému** a přidejte nebo upravte `ASPNETCORE_ENVIRONMENT`ovou hodnotu:</span><span class="sxs-lookup"><span data-stu-id="18ad4-316">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Rozšířené vlastnosti systému](environments/_static/systemsetting_environment.png)

  ![Proměnná prostředí ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="18ad4-319">Otevřete příkazový řádek pro správu a použijte příkaz `setx` nebo otevřete příkazový řádek prostředí PowerShell pro správu a použijte `[Environment]::SetEnvironmentVariable`:</span><span class="sxs-lookup"><span data-stu-id="18ad4-319">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="18ad4-320">**Příkazový řádek**</span><span class="sxs-lookup"><span data-stu-id="18ad4-320">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="18ad4-321">Přepínač `/M` označuje nastavení proměnné prostředí na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="18ad4-321">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="18ad4-322">Pokud není použit přepínač `/M`, je pro uživatelský účet nastavena proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="18ad4-322">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="18ad4-323">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="18ad4-323">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="18ad4-324">Hodnota možnosti `Machine` označuje, že se má nastavit proměnná prostředí na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="18ad4-324">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="18ad4-325">Pokud je hodnota možnosti změněna na `User`, je pro uživatelský účet nastavena proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="18ad4-325">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="18ad4-326">Pokud je proměnná prostředí `ASPNETCORE_ENVIRONMENT` nastavena globálně, projeví se `dotnet run` v jakémkoli příkazovém okně otevřeném po nastavení hodnoty.</span><span class="sxs-lookup"><span data-stu-id="18ad4-326">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="18ad4-327">**web.config**</span><span class="sxs-lookup"><span data-stu-id="18ad4-327">**web.config**</span></span>

<span data-ttu-id="18ad4-328">Chcete-li nastavit proměnnou prostředí `ASPNETCORE_ENVIRONMENT` pomocí *souboru Web. config*, přečtěte si část *nastavení proměnných prostředí* v <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="18ad4-328">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="18ad4-329">**Projektový soubor nebo profil publikování**</span><span class="sxs-lookup"><span data-stu-id="18ad4-329">**Project file or publish profile**</span></span>

<span data-ttu-id="18ad4-330">**Pro nasazení služby Windows IIS:** Do [profilu publikování (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu zahrňte vlastnost `<EnvironmentName>`.</span><span class="sxs-lookup"><span data-stu-id="18ad4-330">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="18ad4-331">Tento přístup nastaví prostředí v *souboru Web. config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="18ad4-331">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="18ad4-332">**Pro fond aplikací služby IIS**</span><span class="sxs-lookup"><span data-stu-id="18ad4-332">**Per IIS Application Pool**</span></span>

<span data-ttu-id="18ad4-333">Pokud chcete nastavit proměnnou prostředí `ASPNETCORE_ENVIRONMENT` pro aplikaci spuštěnou v izolovaném fondu aplikací (podporovaném ve službě IIS 10,0 nebo novější), přečtěte si část *příkazového řádku Appcmd. exe* v tématu [proměnné prostředí &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) .</span><span class="sxs-lookup"><span data-stu-id="18ad4-333">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="18ad4-334">Pokud je pro fond aplikací nastavená proměnná prostředí `ASPNETCORE_ENVIRONMENT`, přepíše její hodnota nastavení na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="18ad4-334">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="18ad4-335">Při hostování aplikace ve službě IIS a přidání nebo změny proměnné prostředí `ASPNETCORE_ENVIRONMENT` použijte libovolný z následujících přístupů, aby byla nová hodnota vyzvednuta aplikacemi:</span><span class="sxs-lookup"><span data-stu-id="18ad4-335">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="18ad4-336">Spustí `net stop was /y` následovaný `net start w3svc` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="18ad4-336">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="18ad4-337">Restartujte server.</span><span class="sxs-lookup"><span data-stu-id="18ad4-337">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="18ad4-338">macOS</span><span class="sxs-lookup"><span data-stu-id="18ad4-338">macOS</span></span>

<span data-ttu-id="18ad4-339">Nastavení aktuálního prostředí pro macOS se dá při spuštění aplikace provést na řádku:</span><span class="sxs-lookup"><span data-stu-id="18ad4-339">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="18ad4-340">Případně můžete nastavit prostředí pomocí `export` před spuštěním aplikace:</span><span class="sxs-lookup"><span data-stu-id="18ad4-340">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="18ad4-341">Proměnné prostředí na úrovni počítače jsou nastaveny v souboru *. bashrc* nebo *. bash_profile* .</span><span class="sxs-lookup"><span data-stu-id="18ad4-341">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="18ad4-342">Upravte soubor pomocí libovolného textového editoru.</span><span class="sxs-lookup"><span data-stu-id="18ad4-342">Edit the file using any text editor.</span></span> <span data-ttu-id="18ad4-343">Přidejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="18ad4-343">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="18ad4-344">Linux</span><span class="sxs-lookup"><span data-stu-id="18ad4-344">Linux</span></span>

<span data-ttu-id="18ad4-345">Pro Linux distribuce použijte na příkazovém řádku příkaz `export` pro nastavení proměnné založené na relaci a soubor *bash_profile* pro nastavení prostředí na úrovni počítače.</span><span class="sxs-lookup"><span data-stu-id="18ad4-345">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="18ad4-346">Nastavení prostředí v kódu</span><span class="sxs-lookup"><span data-stu-id="18ad4-346">Set the environment in code</span></span>

<span data-ttu-id="18ad4-347">Při sestavování hostitele volejte <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*>.</span><span class="sxs-lookup"><span data-stu-id="18ad4-347">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="18ad4-348">Podívejte se na téma <xref:fundamentals/host/web-host#environment>.</span><span class="sxs-lookup"><span data-stu-id="18ad4-348">See <xref:fundamentals/host/web-host#environment>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="18ad4-349">Konfigurace podle prostředí</span><span class="sxs-lookup"><span data-stu-id="18ad4-349">Configuration by environment</span></span>

<span data-ttu-id="18ad4-350">K načtení konfigurace podle prostředí doporučujeme:</span><span class="sxs-lookup"><span data-stu-id="18ad4-350">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="18ad4-351">soubory *appSettings* (*appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="18ad4-351">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="18ad4-352">Podívejte se na téma <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="18ad4-352">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="18ad4-353">Proměnné prostředí (nastavené v každém systému, ve kterém je aplikace hostovaná).</span><span class="sxs-lookup"><span data-stu-id="18ad4-353">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="18ad4-354">Viz témata <xref:fundamentals/host/web-host#environment> a <xref:security/app-secrets#environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="18ad4-354">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="18ad4-355">Správce tajných klíčů (jenom ve vývojovém prostředí).</span><span class="sxs-lookup"><span data-stu-id="18ad4-355">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="18ad4-356">Podívejte se na téma <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="18ad4-356">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="18ad4-357">Třída a metody spouštění založené na prostředí</span><span class="sxs-lookup"><span data-stu-id="18ad4-357">Environment-based Startup class and methods</span></span>

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="18ad4-358">Vloží IHostingEnvironment do Startup. Configure</span><span class="sxs-lookup"><span data-stu-id="18ad4-358">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="18ad4-359">Vloží <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> do `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="18ad4-359">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="18ad4-360">Tento přístup je užitečný, když aplikace vyžaduje jenom konfiguraci `Startup.Configure` jenom pro několik prostředí s minimálními rozdíly v kódu pro každé prostředí.</span><span class="sxs-lookup"><span data-stu-id="18ad4-360">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

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

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="18ad4-361">Vložení IHostingEnvironment do spouštěcí třídy</span><span class="sxs-lookup"><span data-stu-id="18ad4-361">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="18ad4-362">Vložení <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> do konstruktoru `Startup` a přiřazení služby k poli pro použití v rámci třídy `Startup`.</span><span class="sxs-lookup"><span data-stu-id="18ad4-362">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="18ad4-363">Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spouštění jenom pro několik prostředí s minimálními rozdíly v kódu pro každé prostředí.</span><span class="sxs-lookup"><span data-stu-id="18ad4-363">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="18ad4-364">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="18ad4-364">In the following example:</span></span>

* <span data-ttu-id="18ad4-365">Prostředí se uchovává v poli `_env`.</span><span class="sxs-lookup"><span data-stu-id="18ad4-365">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="18ad4-366">`_env` se používá v `ConfigureServices` a `Configure` pro použití konfigurace spuštění na základě prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="18ad4-366">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

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

### <a name="startup-class-conventions"></a><span data-ttu-id="18ad4-367">Konvence třídy spouštění</span><span class="sxs-lookup"><span data-stu-id="18ad4-367">Startup class conventions</span></span>

<span data-ttu-id="18ad4-368">Při spuštění aplikace ASP.NET Core spustí [spouštěcí třída](xref:fundamentals/startup) aplikaci.</span><span class="sxs-lookup"><span data-stu-id="18ad4-368">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="18ad4-369">Aplikace může definovat samostatné třídy `Startup` pro různá prostředí (například `StartupDevelopment`).</span><span class="sxs-lookup"><span data-stu-id="18ad4-369">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="18ad4-370">Příslušná třída `Startup` je vybrána za běhu.</span><span class="sxs-lookup"><span data-stu-id="18ad4-370">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="18ad4-371">Třída, jejíž název má příponu odpovídající aktuálnímu prostředí, je upřednostněna.</span><span class="sxs-lookup"><span data-stu-id="18ad4-371">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="18ad4-372">Pokud není nalezena shodná `Startup{EnvironmentName}` třída, je použita třída `Startup`.</span><span class="sxs-lookup"><span data-stu-id="18ad4-372">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="18ad4-373">Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly v kódu pro každé prostředí.</span><span class="sxs-lookup"><span data-stu-id="18ad4-373">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="18ad4-374">Chcete-li implementovat třídy `Startup` založené na prostředí, vytvořte třídu `Startup{EnvironmentName}` pro každé používané prostředí a záložní třídu `Startup`:</span><span class="sxs-lookup"><span data-stu-id="18ad4-374">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

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

<span data-ttu-id="18ad4-375">Použijte přetížení [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) , které přijímá název sestavení:</span><span class="sxs-lookup"><span data-stu-id="18ad4-375">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

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

### <a name="startup-method-conventions"></a><span data-ttu-id="18ad4-376">Konvence metody spuštění</span><span class="sxs-lookup"><span data-stu-id="18ad4-376">Startup method conventions</span></span>

<span data-ttu-id="18ad4-377">[Nakonfigurujte](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) a [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) , které podporují verze formuláře `Configure<EnvironmentName>` a `Configure<EnvironmentName>Services`na konkrétní prostředí.</span><span class="sxs-lookup"><span data-stu-id="18ad4-377">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="18ad4-378">Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly v kódu pro každé prostředí.</span><span class="sxs-lookup"><span data-stu-id="18ad4-378">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="18ad4-379">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="18ad4-379">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end