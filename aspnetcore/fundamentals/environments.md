---
title: Použití více prostředí v ASP.NET Core
author: rick-anderson
description: Naučte se řídit chování aplikace v různých prostředích aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/17/2019
uid: fundamentals/environments
ms.openlocfilehash: 30e2771c0a24fcbf6490d08c7028566314b6c011
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75358718"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="4b5de-103">Použití více prostředí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b5de-103">Use multiple environments in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4b5de-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4b5de-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4b5de-105">ASP.NET Core konfiguruje chování aplikace na základě běhového prostředí pomocí proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="4b5de-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="4b5de-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4b5de-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="4b5de-107">Prostředí</span><span class="sxs-lookup"><span data-stu-id="4b5de-107">Environments</span></span>

<span data-ttu-id="4b5de-108">ASP.NET Core přečte proměnnou prostředí `ASPNETCORE_ENVIRONMENT` při spuštění aplikace a uloží hodnotu v [IWebHostEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="4b5de-108">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span></span> <span data-ttu-id="4b5de-109">`ASPNETCORE_ENVIRONMENT` lze nastavit na libovolnou hodnotu, ale rozhraní poskytuje tři hodnoty:</span><span class="sxs-lookup"><span data-stu-id="4b5de-109">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.Extensions.Hosting.Environments.Development>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="4b5de-110"><xref:Microsoft.Extensions.Hosting.Environments.Production> (výchozí)</span><span class="sxs-lookup"><span data-stu-id="4b5de-110"><xref:Microsoft.Extensions.Hosting.Environments.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="4b5de-111">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="4b5de-111">The preceding code:</span></span>

* <span data-ttu-id="4b5de-112">Volá [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) , když je `ASPNETCORE_ENVIRONMENT` nastaveno na `Development`.</span><span class="sxs-lookup"><span data-stu-id="4b5de-112">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="4b5de-113">Volá [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) , pokud je hodnota `ASPNETCORE_ENVIRONMENT` nastavena na jednu z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="4b5de-113">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="4b5de-114">[Pomocná značka prostředí](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) používá hodnotu `IHostingEnvironment.EnvironmentName` k zahrnutí nebo vyloučení značek v elementu:</span><span class="sxs-lookup"><span data-stu-id="4b5de-114">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="4b5de-115">V systému Windows a macOS hodnoty proměnných prostředí a hodnot nerozlišují velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="4b5de-115">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="4b5de-116">Ve výchozím nastavení se v proměnných a hodnotách prostředí Linux **rozlišují velká a malá písmena** .</span><span class="sxs-lookup"><span data-stu-id="4b5de-116">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="4b5de-117">Vývoj</span><span class="sxs-lookup"><span data-stu-id="4b5de-117">Development</span></span>

<span data-ttu-id="4b5de-118">Vývojové prostředí může povolit funkce, které by se neměly zveřejnit v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="4b5de-118">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="4b5de-119">Například šablony ASP.NET Core umožňují [stránku s výjimkou vývojáře](xref:fundamentals/error-handling#developer-exception-page) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="4b5de-119">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="4b5de-120">Prostředí pro vývoj místních počítačů lze nastavit v souboru *Properties\launchSettings.JSON* projektu.</span><span class="sxs-lookup"><span data-stu-id="4b5de-120">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="4b5de-121">Hodnoty prostředí nastavené v hodnotách přepisu *launchSettings. JSON* nastavené v prostředí systému</span><span class="sxs-lookup"><span data-stu-id="4b5de-121">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="4b5de-122">Následující JSON zobrazuje tři profily ze souboru *launchSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="4b5de-122">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

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
> <span data-ttu-id="4b5de-123">Vlastnost `applicationUrl` v *launchSettings. JSON* může určovat seznam adres URL serveru.</span><span class="sxs-lookup"><span data-stu-id="4b5de-123">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="4b5de-124">Použijte středník mezi adresami URL v seznamu:</span><span class="sxs-lookup"><span data-stu-id="4b5de-124">Use a semicolon between the URLs in the list:</span></span>
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

<span data-ttu-id="4b5de-125">Když se aplikace spustí pomocí [příkazu dotnet](/dotnet/core/tools/dotnet-run), použije se první profil s `"commandName": "Project"`.</span><span class="sxs-lookup"><span data-stu-id="4b5de-125">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="4b5de-126">Hodnota `commandName` určuje webový server, který se má spustit.</span><span class="sxs-lookup"><span data-stu-id="4b5de-126">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="4b5de-127">`commandName` může být jedna z následujících:</span><span class="sxs-lookup"><span data-stu-id="4b5de-127">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="4b5de-128">`Project` (spouští Kestrel)</span><span class="sxs-lookup"><span data-stu-id="4b5de-128">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="4b5de-129">Když se aplikace spustí s použitím [příkazu dotnet](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="4b5de-129">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="4b5de-130">*launchSettings. JSON* je přečtený, pokud je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="4b5de-130">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="4b5de-131">nastavení `environmentVariables` v proměnných prostředí přepsat *launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="4b5de-131">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="4b5de-132">Zobrazí se hostující prostředí.</span><span class="sxs-lookup"><span data-stu-id="4b5de-132">The hosting environment is displayed.</span></span>

<span data-ttu-id="4b5de-133">Následující výstup ukazuje aplikaci spuštěnou pomocí [příkazu dotnet](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="4b5de-133">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="4b5de-134">Karta **ladění** vlastností projektu sady Visual Studio poskytuje grafické uživatelské rozhraní pro úpravy souboru *launchSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="4b5de-134">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Vlastnosti projektu nastavení proměnných prostředí](environments/_static/project-properties-debug.png)

<span data-ttu-id="4b5de-136">Změny provedené v profilech projektu se neprojeví, dokud se webový server nerestartuje.</span><span class="sxs-lookup"><span data-stu-id="4b5de-136">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="4b5de-137">Kestrel se musí restartovat, aby bylo možné zjistit změny provedené ve svém prostředí.</span><span class="sxs-lookup"><span data-stu-id="4b5de-137">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="4b5de-138">*launchSettings. JSON* by neměl ukládat tajné klíče.</span><span class="sxs-lookup"><span data-stu-id="4b5de-138">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="4b5de-139">[Nástroj Správce tajných](xref:security/app-secrets) klíčů je možné použít k ukládání tajných kódů pro místní vývoj.</span><span class="sxs-lookup"><span data-stu-id="4b5de-139">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="4b5de-140">Při použití [Visual Studio Code](https://code.visualstudio.com/)lze proměnné prostředí nastavit v souboru *. VSCode/Launch. JSON* .</span><span class="sxs-lookup"><span data-stu-id="4b5de-140">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="4b5de-141">Následující příklad nastaví prostředí na `Development`:</span><span class="sxs-lookup"><span data-stu-id="4b5de-141">The following example sets the environment to `Development`:</span></span>

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

<span data-ttu-id="4b5de-142">Soubor *. VSCode/Launch. JSON* v projektu není čten při spuštění aplikace s `dotnet run` stejným způsobem jako *vlastnosti/launchSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="4b5de-142">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="4b5de-143">Při spouštění aplikace ve vývoji, která nemá soubor *launchSettings. JSON* , buď nastavte prostředí s proměnnou prostředí nebo argumentem příkazového řádku na příkaz `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="4b5de-143">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="4b5de-144">Provoz</span><span class="sxs-lookup"><span data-stu-id="4b5de-144">Production</span></span>

<span data-ttu-id="4b5de-145">Provozní prostředí by mělo být nakonfigurované tak, aby maximalizovalo zabezpečení, výkon a odolnost aplikací.</span><span class="sxs-lookup"><span data-stu-id="4b5de-145">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="4b5de-146">Mezi běžná nastavení, která se liší od vývoje, patří:</span><span class="sxs-lookup"><span data-stu-id="4b5de-146">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="4b5de-147">Vyrovnávací.</span><span class="sxs-lookup"><span data-stu-id="4b5de-147">Caching.</span></span>
* <span data-ttu-id="4b5de-148">Prostředky na straně klienta jsou seskupené, minifikovaného a potenciálně poskytované z CDN.</span><span class="sxs-lookup"><span data-stu-id="4b5de-148">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="4b5de-149">Chybové stránky diagnostiky jsou zakázané.</span><span class="sxs-lookup"><span data-stu-id="4b5de-149">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="4b5de-150">Jsou povoleny popisné chybové stránky.</span><span class="sxs-lookup"><span data-stu-id="4b5de-150">Friendly error pages enabled.</span></span>
* <span data-ttu-id="4b5de-151">Protokolování a sledování výroby je povolené.</span><span class="sxs-lookup"><span data-stu-id="4b5de-151">Production logging and monitoring enabled.</span></span> <span data-ttu-id="4b5de-152">Například [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="4b5de-152">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="4b5de-153">Nastavení prostředí</span><span class="sxs-lookup"><span data-stu-id="4b5de-153">Set the environment</span></span>

<span data-ttu-id="4b5de-154">Je často užitečné nastavit konkrétní prostředí pro testování pomocí proměnné prostředí nebo nastavení platformy.</span><span class="sxs-lookup"><span data-stu-id="4b5de-154">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="4b5de-155">Pokud prostředí není nastavené, nastaví se jako výchozí `Production`, což zakáže většinu funkcí ladění.</span><span class="sxs-lookup"><span data-stu-id="4b5de-155">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="4b5de-156">Metoda nastavení prostředí závisí na operačním systému.</span><span class="sxs-lookup"><span data-stu-id="4b5de-156">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="4b5de-157">Po sestavení hostitele určuje poslední nastavení prostředí načtené aplikací prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b5de-157">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="4b5de-158">Prostředí aplikace se nedá změnit, když je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="4b5de-158">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="4b5de-159">Nastavení proměnné prostředí nebo platformy</span><span class="sxs-lookup"><span data-stu-id="4b5de-159">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="4b5de-160">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="4b5de-160">Azure App Service</span></span>

<span data-ttu-id="4b5de-161">Pokud chcete nastavit prostředí v [Azure App Service](https://azure.microsoft.com/services/app-service/), proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="4b5de-161">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="4b5de-162">Vyberte aplikaci z okna **App Services** .</span><span class="sxs-lookup"><span data-stu-id="4b5de-162">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="4b5de-163">Ve skupině **Nastavení** vyberte okno **Konfigurace** .</span><span class="sxs-lookup"><span data-stu-id="4b5de-163">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="4b5de-164">Na kartě **nastavení aplikace** vyberte možnost **nové nastavení aplikace**.</span><span class="sxs-lookup"><span data-stu-id="4b5de-164">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="4b5de-165">V okně **Přidat/upravit nastavení aplikace** zadejte `ASPNETCORE_ENVIRONMENT` pro **název**.</span><span class="sxs-lookup"><span data-stu-id="4b5de-165">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="4b5de-166">V poli **hodnota**zadejte prostředí (například `Staging`).</span><span class="sxs-lookup"><span data-stu-id="4b5de-166">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="4b5de-167">Zaškrtněte políčko **nastavení slotu nasazení** , pokud chcete, aby nastavení prostředí zůstalo v aktuální pozici, když jsou sloty nasazení prohozeny.</span><span class="sxs-lookup"><span data-stu-id="4b5de-167">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="4b5de-168">Další informace najdete v tématu [Nastavení přípravného prostředí v Azure App Service](/azure/app-service/web-sites-staged-publishing) v dokumentaci k Azure.</span><span class="sxs-lookup"><span data-stu-id="4b5de-168">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="4b5de-169">Výběrem **OK** zavřete okno **Přidat/upravit nastavení aplikace** .</span><span class="sxs-lookup"><span data-stu-id="4b5de-169">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="4b5de-170">V horní části okna **Konfigurace** vyberte **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="4b5de-170">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="4b5de-171">Azure App Service automaticky restartuje aplikaci po přidání, změně nebo odstranění nastavení aplikace (proměnná prostředí) v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="4b5de-171">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="4b5de-172">Windows</span><span class="sxs-lookup"><span data-stu-id="4b5de-172">Windows</span></span>

<span data-ttu-id="4b5de-173">Chcete-li nastavit `ASPNETCORE_ENVIRONMENT` pro aktuální relaci při zahájení aplikace pomocí [příkazu dotnet](/dotnet/core/tools/dotnet-run), jsou použity následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="4b5de-173">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="4b5de-174">**Příkazový řádek**</span><span class="sxs-lookup"><span data-stu-id="4b5de-174">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="4b5de-175">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="4b5de-175">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="4b5de-176">Tyto příkazy se projeví pouze pro aktuální okno.</span><span class="sxs-lookup"><span data-stu-id="4b5de-176">These commands only take effect for the current window.</span></span> <span data-ttu-id="4b5de-177">Po zavření okna se nastavení `ASPNETCORE_ENVIRONMENT` vrátí k výchozímu nastavení nebo hodnotě počítače.</span><span class="sxs-lookup"><span data-stu-id="4b5de-177">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="4b5de-178">K nastavení hodnoty globálně ve Windows použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="4b5de-178">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="4b5de-179">Otevřete **Ovládací panely** > **systémové** > **Pokročilé nastavení systému** a přidejte nebo upravte `ASPNETCORE_ENVIRONMENT`ovou hodnotu:</span><span class="sxs-lookup"><span data-stu-id="4b5de-179">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Rozšířené vlastnosti systému](environments/_static/systemsetting_environment.png)

  ![Proměnná prostředí ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="4b5de-182">Otevřete příkazový řádek pro správu a použijte příkaz `setx` nebo otevřete příkazový řádek prostředí PowerShell pro správu a použijte `[Environment]::SetEnvironmentVariable`:</span><span class="sxs-lookup"><span data-stu-id="4b5de-182">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="4b5de-183">**Příkazový řádek**</span><span class="sxs-lookup"><span data-stu-id="4b5de-183">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="4b5de-184">Přepínač `/M` označuje nastavení proměnné prostředí na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="4b5de-184">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="4b5de-185">Pokud není použit přepínač `/M`, je pro uživatelský účet nastavena proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="4b5de-185">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="4b5de-186">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="4b5de-186">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="4b5de-187">Hodnota možnosti `Machine` označuje, že se má nastavit proměnná prostředí na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="4b5de-187">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="4b5de-188">Pokud je hodnota možnosti změněna na `User`, je pro uživatelský účet nastavena proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="4b5de-188">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="4b5de-189">Pokud je proměnná prostředí `ASPNETCORE_ENVIRONMENT` nastavena globálně, projeví se `dotnet run` v jakémkoli příkazovém okně otevřeném po nastavení hodnoty.</span><span class="sxs-lookup"><span data-stu-id="4b5de-189">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="4b5de-190">**web.config**</span><span class="sxs-lookup"><span data-stu-id="4b5de-190">**web.config**</span></span>

<span data-ttu-id="4b5de-191">Chcete-li nastavit proměnnou prostředí `ASPNETCORE_ENVIRONMENT` pomocí *souboru Web. config*, přečtěte si část *nastavení proměnných prostředí* v <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="4b5de-191">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="4b5de-192">**Projektový soubor nebo profil publikování**</span><span class="sxs-lookup"><span data-stu-id="4b5de-192">**Project file or publish profile**</span></span>

<span data-ttu-id="4b5de-193">**Pro nasazení služby Windows IIS:** Do [profilu publikování (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu zahrňte vlastnost `<EnvironmentName>`.</span><span class="sxs-lookup"><span data-stu-id="4b5de-193">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="4b5de-194">Tento přístup nastaví prostředí v *souboru Web. config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="4b5de-194">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="4b5de-195">**Pro fond aplikací služby IIS**</span><span class="sxs-lookup"><span data-stu-id="4b5de-195">**Per IIS Application Pool**</span></span>

<span data-ttu-id="4b5de-196">Pokud chcete nastavit proměnnou prostředí `ASPNETCORE_ENVIRONMENT` pro aplikaci spuštěnou v izolovaném fondu aplikací (podporovaném ve službě IIS 10,0 nebo novější), přečtěte si část *příkazového řádku Appcmd. exe* v tématu [proměnné prostředí &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) .</span><span class="sxs-lookup"><span data-stu-id="4b5de-196">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="4b5de-197">Pokud je pro fond aplikací nastavená proměnná prostředí `ASPNETCORE_ENVIRONMENT`, přepíše její hodnota nastavení na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="4b5de-197">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4b5de-198">Při hostování aplikace ve službě IIS a přidání nebo změny proměnné prostředí `ASPNETCORE_ENVIRONMENT` použijte libovolný z následujících přístupů, aby byla nová hodnota vyzvednuta aplikacemi:</span><span class="sxs-lookup"><span data-stu-id="4b5de-198">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="4b5de-199">Spustí `net stop was /y` následovaný `net start w3svc` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="4b5de-199">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="4b5de-200">Restartujte server.</span><span class="sxs-lookup"><span data-stu-id="4b5de-200">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="4b5de-201">macOS</span><span class="sxs-lookup"><span data-stu-id="4b5de-201">macOS</span></span>

<span data-ttu-id="4b5de-202">Nastavení aktuálního prostředí pro macOS se dá při spuštění aplikace provést na řádku:</span><span class="sxs-lookup"><span data-stu-id="4b5de-202">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="4b5de-203">Případně můžete nastavit prostředí pomocí `export` před spuštěním aplikace:</span><span class="sxs-lookup"><span data-stu-id="4b5de-203">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="4b5de-204">Proměnné prostředí na úrovni počítače jsou nastaveny v souboru *. bashrc* nebo *. bash_profile* .</span><span class="sxs-lookup"><span data-stu-id="4b5de-204">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="4b5de-205">Upravte soubor pomocí libovolného textového editoru.</span><span class="sxs-lookup"><span data-stu-id="4b5de-205">Edit the file using any text editor.</span></span> <span data-ttu-id="4b5de-206">Přidejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4b5de-206">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="4b5de-207">Linux</span><span class="sxs-lookup"><span data-stu-id="4b5de-207">Linux</span></span>

<span data-ttu-id="4b5de-208">Pro Linux distribuce použijte na příkazovém řádku příkaz `export` pro nastavení proměnné založené na relaci a soubor *bash_profile* pro nastavení prostředí na úrovni počítače.</span><span class="sxs-lookup"><span data-stu-id="4b5de-208">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="4b5de-209">Nastavení prostředí v kódu</span><span class="sxs-lookup"><span data-stu-id="4b5de-209">Set the environment in code</span></span>

<span data-ttu-id="4b5de-210">Při sestavování hostitele volejte <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*>.</span><span class="sxs-lookup"><span data-stu-id="4b5de-210">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="4b5de-211">Podívejte se na téma <xref:fundamentals/host/generic-host#environmentname>.</span><span class="sxs-lookup"><span data-stu-id="4b5de-211">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>


### <a name="configuration-by-environment"></a><span data-ttu-id="4b5de-212">Konfigurace podle prostředí</span><span class="sxs-lookup"><span data-stu-id="4b5de-212">Configuration by environment</span></span>

<span data-ttu-id="4b5de-213">K načtení konfigurace podle prostředí doporučujeme:</span><span class="sxs-lookup"><span data-stu-id="4b5de-213">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="4b5de-214">soubory *appSettings* (*appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="4b5de-214">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="4b5de-215">Podívejte se na téma <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="4b5de-215">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="4b5de-216">Proměnné prostředí (nastavené v každém systému, ve kterém je aplikace hostovaná).</span><span class="sxs-lookup"><span data-stu-id="4b5de-216">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="4b5de-217">Viz témata <xref:fundamentals/host/generic-host#environmentname> a <xref:security/app-secrets#environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="4b5de-217">See <xref:fundamentals/host/generic-host#environmentname> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="4b5de-218">Správce tajných klíčů (jenom ve vývojovém prostředí).</span><span class="sxs-lookup"><span data-stu-id="4b5de-218">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="4b5de-219">Podívejte se na téma <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="4b5de-219">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="4b5de-220">Třída a metody spouštění založené na prostředí</span><span class="sxs-lookup"><span data-stu-id="4b5de-220">Environment-based Startup class and methods</span></span>

### <a name="inject-iwebhostenvironment-into-startupconfigure"></a><span data-ttu-id="4b5de-221">Vloží IWebHostEnvironment do Startup. Configure</span><span class="sxs-lookup"><span data-stu-id="4b5de-221">Inject IWebHostEnvironment into Startup.Configure</span></span>

<span data-ttu-id="4b5de-222">Vloží <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="4b5de-222">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="4b5de-223">Tento přístup je užitečný, když aplikace vyžaduje úpravu `Startup.Configure` jenom pro několik prostředí s minimálními rozdíly v kódu na prostředí.</span><span class="sxs-lookup"><span data-stu-id="4b5de-223">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>

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

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="4b5de-224">Vložení IWebHostEnvironment do spouštěcí třídy</span><span class="sxs-lookup"><span data-stu-id="4b5de-224">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="4b5de-225">Vloží <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do konstruktoru `Startup`.</span><span class="sxs-lookup"><span data-stu-id="4b5de-225">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="4b5de-226">Tento přístup je užitečný, když aplikace vyžaduje konfiguraci `Startup` jenom pro několik prostředí s minimálními rozdíly v kódu pro každé prostředí.</span><span class="sxs-lookup"><span data-stu-id="4b5de-226">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="4b5de-227">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="4b5de-227">In the following example:</span></span>

* <span data-ttu-id="4b5de-228">Prostředí se uchovává v poli `_env`.</span><span class="sxs-lookup"><span data-stu-id="4b5de-228">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="4b5de-229">`_env` se používá v `ConfigureServices` a `Configure` pro použití konfigurace spuštění na základě prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b5de-229">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

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
### <a name="startup-class-conventions"></a><span data-ttu-id="4b5de-230">Konvence třídy spouštění</span><span class="sxs-lookup"><span data-stu-id="4b5de-230">Startup class conventions</span></span>

<span data-ttu-id="4b5de-231">Při spuštění aplikace ASP.NET Core spustí [spouštěcí třída](xref:fundamentals/startup) aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4b5de-231">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="4b5de-232">Aplikace může definovat samostatné třídy `Startup` pro různá prostředí (například `StartupDevelopment`).</span><span class="sxs-lookup"><span data-stu-id="4b5de-232">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="4b5de-233">Příslušná třída `Startup` je vybrána za běhu.</span><span class="sxs-lookup"><span data-stu-id="4b5de-233">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="4b5de-234">Třída, jejíž název má příponu odpovídající aktuálnímu prostředí, je upřednostněna.</span><span class="sxs-lookup"><span data-stu-id="4b5de-234">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="4b5de-235">Pokud není nalezena shodná `Startup{EnvironmentName}` třída, je použita třída `Startup`.</span><span class="sxs-lookup"><span data-stu-id="4b5de-235">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="4b5de-236">Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly v kódu pro každé prostředí.</span><span class="sxs-lookup"><span data-stu-id="4b5de-236">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="4b5de-237">Chcete-li implementovat třídy `Startup` založené na prostředí, vytvořte třídu `Startup{EnvironmentName}` pro každé používané prostředí a záložní třídu `Startup`:</span><span class="sxs-lookup"><span data-stu-id="4b5de-237">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

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

<span data-ttu-id="4b5de-238">Použijte přetížení [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) , které přijímá název sestavení:</span><span class="sxs-lookup"><span data-stu-id="4b5de-238">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

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

### <a name="startup-method-conventions"></a><span data-ttu-id="4b5de-239">Konvence metody spuštění</span><span class="sxs-lookup"><span data-stu-id="4b5de-239">Startup method conventions</span></span>

<span data-ttu-id="4b5de-240">[Nakonfigurujte](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) a [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) , které podporují verze formuláře `Configure<EnvironmentName>` a `Configure<EnvironmentName>Services`na konkrétní prostředí.</span><span class="sxs-lookup"><span data-stu-id="4b5de-240">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="4b5de-241">Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly v kódu pro každé prostředí.</span><span class="sxs-lookup"><span data-stu-id="4b5de-241">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="4b5de-242">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="4b5de-242">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4b5de-243">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4b5de-243">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4b5de-244">ASP.NET Core konfiguruje chování aplikace na základě běhového prostředí pomocí proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="4b5de-244">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="4b5de-245">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4b5de-245">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="4b5de-246">Prostředí</span><span class="sxs-lookup"><span data-stu-id="4b5de-246">Environments</span></span>

<span data-ttu-id="4b5de-247">ASP.NET Core přečte proměnnou prostředí `ASPNETCORE_ENVIRONMENT` při spuštění aplikace a uloží hodnotu v [IHostingEnvironment. Environment](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="4b5de-247">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="4b5de-248">`ASPNETCORE_ENVIRONMENT` lze nastavit na libovolnou hodnotu, ale rozhraní poskytuje tři hodnoty:</span><span class="sxs-lookup"><span data-stu-id="4b5de-248">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="4b5de-249"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (výchozí)</span><span class="sxs-lookup"><span data-stu-id="4b5de-249"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="4b5de-250">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="4b5de-250">The preceding code:</span></span>

* <span data-ttu-id="4b5de-251">Volá [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) , když je `ASPNETCORE_ENVIRONMENT` nastaveno na `Development`.</span><span class="sxs-lookup"><span data-stu-id="4b5de-251">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="4b5de-252">Volá [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) , pokud je hodnota `ASPNETCORE_ENVIRONMENT` nastavena na jednu z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="4b5de-252">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="4b5de-253">[Pomocná značka prostředí](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) používá hodnotu `IHostingEnvironment.EnvironmentName` k zahrnutí nebo vyloučení značek v elementu:</span><span class="sxs-lookup"><span data-stu-id="4b5de-253">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="4b5de-254">V systému Windows a macOS hodnoty proměnných prostředí a hodnot nerozlišují velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="4b5de-254">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="4b5de-255">Ve výchozím nastavení se v proměnných a hodnotách prostředí Linux **rozlišují velká a malá písmena** .</span><span class="sxs-lookup"><span data-stu-id="4b5de-255">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="4b5de-256">Vývoj</span><span class="sxs-lookup"><span data-stu-id="4b5de-256">Development</span></span>

<span data-ttu-id="4b5de-257">Vývojové prostředí může povolit funkce, které by se neměly zveřejnit v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="4b5de-257">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="4b5de-258">Například šablony ASP.NET Core umožňují [stránku s výjimkou vývojáře](xref:fundamentals/error-handling#developer-exception-page) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="4b5de-258">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="4b5de-259">Prostředí pro vývoj místních počítačů lze nastavit v souboru *Properties\launchSettings.JSON* projektu.</span><span class="sxs-lookup"><span data-stu-id="4b5de-259">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="4b5de-260">Hodnoty prostředí nastavené v hodnotách přepisu *launchSettings. JSON* nastavené v prostředí systému</span><span class="sxs-lookup"><span data-stu-id="4b5de-260">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="4b5de-261">Následující JSON zobrazuje tři profily ze souboru *launchSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="4b5de-261">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

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
> <span data-ttu-id="4b5de-262">Vlastnost `applicationUrl` v *launchSettings. JSON* může určovat seznam adres URL serveru.</span><span class="sxs-lookup"><span data-stu-id="4b5de-262">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="4b5de-263">Použijte středník mezi adresami URL v seznamu:</span><span class="sxs-lookup"><span data-stu-id="4b5de-263">Use a semicolon between the URLs in the list:</span></span>
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

<span data-ttu-id="4b5de-264">Když se aplikace spustí pomocí [příkazu dotnet](/dotnet/core/tools/dotnet-run), použije se první profil s `"commandName": "Project"`.</span><span class="sxs-lookup"><span data-stu-id="4b5de-264">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="4b5de-265">Hodnota `commandName` určuje webový server, který se má spustit.</span><span class="sxs-lookup"><span data-stu-id="4b5de-265">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="4b5de-266">`commandName` může být jedna z následujících:</span><span class="sxs-lookup"><span data-stu-id="4b5de-266">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="4b5de-267">`Project` (spouští Kestrel)</span><span class="sxs-lookup"><span data-stu-id="4b5de-267">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="4b5de-268">Když se aplikace spustí s použitím [příkazu dotnet](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="4b5de-268">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="4b5de-269">*launchSettings. JSON* je přečtený, pokud je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="4b5de-269">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="4b5de-270">nastavení `environmentVariables` v proměnných prostředí přepsat *launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="4b5de-270">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="4b5de-271">Zobrazí se hostující prostředí.</span><span class="sxs-lookup"><span data-stu-id="4b5de-271">The hosting environment is displayed.</span></span>

<span data-ttu-id="4b5de-272">Následující výstup ukazuje aplikaci spuštěnou pomocí [příkazu dotnet](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="4b5de-272">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="4b5de-273">Karta **ladění** vlastností projektu sady Visual Studio poskytuje grafické uživatelské rozhraní pro úpravy souboru *launchSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="4b5de-273">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Vlastnosti projektu nastavení proměnných prostředí](environments/_static/project-properties-debug.png)

<span data-ttu-id="4b5de-275">Změny provedené v profilech projektu se neprojeví, dokud se webový server nerestartuje.</span><span class="sxs-lookup"><span data-stu-id="4b5de-275">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="4b5de-276">Kestrel se musí restartovat, aby bylo možné zjistit změny provedené ve svém prostředí.</span><span class="sxs-lookup"><span data-stu-id="4b5de-276">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="4b5de-277">*launchSettings. JSON* by neměl ukládat tajné klíče.</span><span class="sxs-lookup"><span data-stu-id="4b5de-277">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="4b5de-278">[Nástroj Správce tajných](xref:security/app-secrets) klíčů je možné použít k ukládání tajných kódů pro místní vývoj.</span><span class="sxs-lookup"><span data-stu-id="4b5de-278">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="4b5de-279">Při použití [Visual Studio Code](https://code.visualstudio.com/)lze proměnné prostředí nastavit v souboru *. VSCode/Launch. JSON* .</span><span class="sxs-lookup"><span data-stu-id="4b5de-279">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="4b5de-280">Následující příklad nastaví prostředí na `Development`:</span><span class="sxs-lookup"><span data-stu-id="4b5de-280">The following example sets the environment to `Development`:</span></span>

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

<span data-ttu-id="4b5de-281">Soubor *. VSCode/Launch. JSON* v projektu není čten při spuštění aplikace s `dotnet run` stejným způsobem jako *vlastnosti/launchSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="4b5de-281">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="4b5de-282">Při spouštění aplikace ve vývoji, která nemá soubor *launchSettings. JSON* , buď nastavte prostředí s proměnnou prostředí nebo argumentem příkazového řádku na příkaz `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="4b5de-282">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="4b5de-283">Provoz</span><span class="sxs-lookup"><span data-stu-id="4b5de-283">Production</span></span>

<span data-ttu-id="4b5de-284">Provozní prostředí by mělo být nakonfigurované tak, aby maximalizovalo zabezpečení, výkon a odolnost aplikací.</span><span class="sxs-lookup"><span data-stu-id="4b5de-284">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="4b5de-285">Mezi běžná nastavení, která se liší od vývoje, patří:</span><span class="sxs-lookup"><span data-stu-id="4b5de-285">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="4b5de-286">Vyrovnávací.</span><span class="sxs-lookup"><span data-stu-id="4b5de-286">Caching.</span></span>
* <span data-ttu-id="4b5de-287">Prostředky na straně klienta jsou seskupené, minifikovaného a potenciálně poskytované z CDN.</span><span class="sxs-lookup"><span data-stu-id="4b5de-287">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="4b5de-288">Chybové stránky diagnostiky jsou zakázané.</span><span class="sxs-lookup"><span data-stu-id="4b5de-288">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="4b5de-289">Jsou povoleny popisné chybové stránky.</span><span class="sxs-lookup"><span data-stu-id="4b5de-289">Friendly error pages enabled.</span></span>
* <span data-ttu-id="4b5de-290">Protokolování a sledování výroby je povolené.</span><span class="sxs-lookup"><span data-stu-id="4b5de-290">Production logging and monitoring enabled.</span></span> <span data-ttu-id="4b5de-291">Například [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="4b5de-291">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="4b5de-292">Nastavení prostředí</span><span class="sxs-lookup"><span data-stu-id="4b5de-292">Set the environment</span></span>

<span data-ttu-id="4b5de-293">Je často užitečné nastavit konkrétní prostředí pro testování pomocí proměnné prostředí nebo nastavení platformy.</span><span class="sxs-lookup"><span data-stu-id="4b5de-293">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="4b5de-294">Pokud prostředí není nastavené, nastaví se jako výchozí `Production`, což zakáže většinu funkcí ladění.</span><span class="sxs-lookup"><span data-stu-id="4b5de-294">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="4b5de-295">Metoda nastavení prostředí závisí na operačním systému.</span><span class="sxs-lookup"><span data-stu-id="4b5de-295">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="4b5de-296">Po sestavení hostitele určuje poslední nastavení prostředí načtené aplikací prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b5de-296">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="4b5de-297">Prostředí aplikace se nedá změnit, když je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="4b5de-297">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="4b5de-298">Nastavení proměnné prostředí nebo platformy</span><span class="sxs-lookup"><span data-stu-id="4b5de-298">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="4b5de-299">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="4b5de-299">Azure App Service</span></span>

<span data-ttu-id="4b5de-300">Pokud chcete nastavit prostředí v [Azure App Service](https://azure.microsoft.com/services/app-service/), proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="4b5de-300">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="4b5de-301">Vyberte aplikaci z okna **App Services** .</span><span class="sxs-lookup"><span data-stu-id="4b5de-301">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="4b5de-302">Ve skupině **Nastavení** vyberte okno **Konfigurace** .</span><span class="sxs-lookup"><span data-stu-id="4b5de-302">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="4b5de-303">Na kartě **nastavení aplikace** vyberte možnost **nové nastavení aplikace**.</span><span class="sxs-lookup"><span data-stu-id="4b5de-303">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="4b5de-304">V okně **Přidat/upravit nastavení aplikace** zadejte `ASPNETCORE_ENVIRONMENT` pro **název**.</span><span class="sxs-lookup"><span data-stu-id="4b5de-304">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="4b5de-305">V poli **hodnota**zadejte prostředí (například `Staging`).</span><span class="sxs-lookup"><span data-stu-id="4b5de-305">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="4b5de-306">Zaškrtněte políčko **nastavení slotu nasazení** , pokud chcete, aby nastavení prostředí zůstalo v aktuální pozici, když jsou sloty nasazení prohozeny.</span><span class="sxs-lookup"><span data-stu-id="4b5de-306">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="4b5de-307">Další informace najdete v tématu [Nastavení přípravného prostředí v Azure App Service](/azure/app-service/web-sites-staged-publishing) v dokumentaci k Azure.</span><span class="sxs-lookup"><span data-stu-id="4b5de-307">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="4b5de-308">Výběrem **OK** zavřete okno **Přidat/upravit nastavení aplikace** .</span><span class="sxs-lookup"><span data-stu-id="4b5de-308">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="4b5de-309">V horní části okna **Konfigurace** vyberte **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="4b5de-309">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="4b5de-310">Azure App Service automaticky restartuje aplikaci po přidání, změně nebo odstranění nastavení aplikace (proměnná prostředí) v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="4b5de-310">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="4b5de-311">Windows</span><span class="sxs-lookup"><span data-stu-id="4b5de-311">Windows</span></span>

<span data-ttu-id="4b5de-312">Chcete-li nastavit `ASPNETCORE_ENVIRONMENT` pro aktuální relaci při zahájení aplikace pomocí [příkazu dotnet](/dotnet/core/tools/dotnet-run), jsou použity následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="4b5de-312">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="4b5de-313">**Příkazový řádek**</span><span class="sxs-lookup"><span data-stu-id="4b5de-313">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="4b5de-314">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="4b5de-314">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="4b5de-315">Tyto příkazy se projeví pouze pro aktuální okno.</span><span class="sxs-lookup"><span data-stu-id="4b5de-315">These commands only take effect for the current window.</span></span> <span data-ttu-id="4b5de-316">Po zavření okna se nastavení `ASPNETCORE_ENVIRONMENT` vrátí k výchozímu nastavení nebo hodnotě počítače.</span><span class="sxs-lookup"><span data-stu-id="4b5de-316">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="4b5de-317">K nastavení hodnoty globálně ve Windows použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="4b5de-317">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="4b5de-318">Otevřete **Ovládací panely** > **systémové** > **Pokročilé nastavení systému** a přidejte nebo upravte `ASPNETCORE_ENVIRONMENT`ovou hodnotu:</span><span class="sxs-lookup"><span data-stu-id="4b5de-318">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Rozšířené vlastnosti systému](environments/_static/systemsetting_environment.png)

  ![Proměnná prostředí ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="4b5de-321">Otevřete příkazový řádek pro správu a použijte příkaz `setx` nebo otevřete příkazový řádek prostředí PowerShell pro správu a použijte `[Environment]::SetEnvironmentVariable`:</span><span class="sxs-lookup"><span data-stu-id="4b5de-321">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="4b5de-322">**Příkazový řádek**</span><span class="sxs-lookup"><span data-stu-id="4b5de-322">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="4b5de-323">Přepínač `/M` označuje nastavení proměnné prostředí na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="4b5de-323">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="4b5de-324">Pokud není použit přepínač `/M`, je pro uživatelský účet nastavena proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="4b5de-324">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="4b5de-325">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="4b5de-325">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="4b5de-326">Hodnota možnosti `Machine` označuje, že se má nastavit proměnná prostředí na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="4b5de-326">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="4b5de-327">Pokud je hodnota možnosti změněna na `User`, je pro uživatelský účet nastavena proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="4b5de-327">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="4b5de-328">Pokud je proměnná prostředí `ASPNETCORE_ENVIRONMENT` nastavena globálně, projeví se `dotnet run` v jakémkoli příkazovém okně otevřeném po nastavení hodnoty.</span><span class="sxs-lookup"><span data-stu-id="4b5de-328">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="4b5de-329">**web.config**</span><span class="sxs-lookup"><span data-stu-id="4b5de-329">**web.config**</span></span>

<span data-ttu-id="4b5de-330">Chcete-li nastavit proměnnou prostředí `ASPNETCORE_ENVIRONMENT` pomocí *souboru Web. config*, přečtěte si část *nastavení proměnných prostředí* v <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="4b5de-330">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="4b5de-331">**Projektový soubor nebo profil publikování**</span><span class="sxs-lookup"><span data-stu-id="4b5de-331">**Project file or publish profile**</span></span>

<span data-ttu-id="4b5de-332">**Pro nasazení služby Windows IIS:** Do [profilu publikování (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu zahrňte vlastnost `<EnvironmentName>`.</span><span class="sxs-lookup"><span data-stu-id="4b5de-332">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="4b5de-333">Tento přístup nastaví prostředí v *souboru Web. config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="4b5de-333">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="4b5de-334">**Pro fond aplikací služby IIS**</span><span class="sxs-lookup"><span data-stu-id="4b5de-334">**Per IIS Application Pool**</span></span>

<span data-ttu-id="4b5de-335">Pokud chcete nastavit proměnnou prostředí `ASPNETCORE_ENVIRONMENT` pro aplikaci spuštěnou v izolovaném fondu aplikací (podporovaném ve službě IIS 10,0 nebo novější), přečtěte si část *příkazového řádku Appcmd. exe* v tématu [proměnné prostředí &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) .</span><span class="sxs-lookup"><span data-stu-id="4b5de-335">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="4b5de-336">Pokud je pro fond aplikací nastavená proměnná prostředí `ASPNETCORE_ENVIRONMENT`, přepíše její hodnota nastavení na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="4b5de-336">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4b5de-337">Při hostování aplikace ve službě IIS a přidání nebo změny proměnné prostředí `ASPNETCORE_ENVIRONMENT` použijte libovolný z následujících přístupů, aby byla nová hodnota vyzvednuta aplikacemi:</span><span class="sxs-lookup"><span data-stu-id="4b5de-337">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="4b5de-338">Spustí `net stop was /y` následovaný `net start w3svc` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="4b5de-338">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="4b5de-339">Restartujte server.</span><span class="sxs-lookup"><span data-stu-id="4b5de-339">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="4b5de-340">macOS</span><span class="sxs-lookup"><span data-stu-id="4b5de-340">macOS</span></span>

<span data-ttu-id="4b5de-341">Nastavení aktuálního prostředí pro macOS se dá při spuštění aplikace provést na řádku:</span><span class="sxs-lookup"><span data-stu-id="4b5de-341">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="4b5de-342">Případně můžete nastavit prostředí pomocí `export` před spuštěním aplikace:</span><span class="sxs-lookup"><span data-stu-id="4b5de-342">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="4b5de-343">Proměnné prostředí na úrovni počítače jsou nastaveny v souboru *. bashrc* nebo *. bash_profile* .</span><span class="sxs-lookup"><span data-stu-id="4b5de-343">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="4b5de-344">Upravte soubor pomocí libovolného textového editoru.</span><span class="sxs-lookup"><span data-stu-id="4b5de-344">Edit the file using any text editor.</span></span> <span data-ttu-id="4b5de-345">Přidejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4b5de-345">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="4b5de-346">Linux</span><span class="sxs-lookup"><span data-stu-id="4b5de-346">Linux</span></span>

<span data-ttu-id="4b5de-347">Pro Linux distribuce použijte na příkazovém řádku příkaz `export` pro nastavení proměnné založené na relaci a soubor *bash_profile* pro nastavení prostředí na úrovni počítače.</span><span class="sxs-lookup"><span data-stu-id="4b5de-347">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="4b5de-348">Nastavení prostředí v kódu</span><span class="sxs-lookup"><span data-stu-id="4b5de-348">Set the environment in code</span></span>

<span data-ttu-id="4b5de-349">Při sestavování hostitele volejte <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*>.</span><span class="sxs-lookup"><span data-stu-id="4b5de-349">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="4b5de-350">Podívejte se na téma <xref:fundamentals/host/web-host#environment>.</span><span class="sxs-lookup"><span data-stu-id="4b5de-350">See <xref:fundamentals/host/web-host#environment>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="4b5de-351">Konfigurace podle prostředí</span><span class="sxs-lookup"><span data-stu-id="4b5de-351">Configuration by environment</span></span>

<span data-ttu-id="4b5de-352">K načtení konfigurace podle prostředí doporučujeme:</span><span class="sxs-lookup"><span data-stu-id="4b5de-352">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="4b5de-353">soubory *appSettings* (*appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="4b5de-353">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="4b5de-354">Podívejte se na téma <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="4b5de-354">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="4b5de-355">Proměnné prostředí (nastavené v každém systému, ve kterém je aplikace hostovaná).</span><span class="sxs-lookup"><span data-stu-id="4b5de-355">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="4b5de-356">Viz témata <xref:fundamentals/host/web-host#environment> a <xref:security/app-secrets#environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="4b5de-356">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="4b5de-357">Správce tajných klíčů (jenom ve vývojovém prostředí).</span><span class="sxs-lookup"><span data-stu-id="4b5de-357">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="4b5de-358">Podívejte se na téma <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="4b5de-358">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="4b5de-359">Třída a metody spouštění založené na prostředí</span><span class="sxs-lookup"><span data-stu-id="4b5de-359">Environment-based Startup class and methods</span></span>

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="4b5de-360">Vloží IHostingEnvironment do Startup. Configure</span><span class="sxs-lookup"><span data-stu-id="4b5de-360">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="4b5de-361">Vloží <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> do `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="4b5de-361">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="4b5de-362">Tento přístup je užitečný, když aplikace vyžaduje jenom konfiguraci `Startup.Configure` jenom pro několik prostředí s minimálními rozdíly v kódu pro každé prostředí.</span><span class="sxs-lookup"><span data-stu-id="4b5de-362">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

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

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="4b5de-363">Vložení IHostingEnvironment do spouštěcí třídy</span><span class="sxs-lookup"><span data-stu-id="4b5de-363">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="4b5de-364">Vložení <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> do konstruktoru `Startup` a přiřazení služby k poli pro použití v rámci třídy `Startup`.</span><span class="sxs-lookup"><span data-stu-id="4b5de-364">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="4b5de-365">Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spouštění jenom pro několik prostředí s minimálními rozdíly v kódu pro každé prostředí.</span><span class="sxs-lookup"><span data-stu-id="4b5de-365">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="4b5de-366">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="4b5de-366">In the following example:</span></span>

* <span data-ttu-id="4b5de-367">Prostředí se uchovává v poli `_env`.</span><span class="sxs-lookup"><span data-stu-id="4b5de-367">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="4b5de-368">`_env` se používá v `ConfigureServices` a `Configure` pro použití konfigurace spuštění na základě prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b5de-368">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

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

### <a name="startup-class-conventions"></a><span data-ttu-id="4b5de-369">Konvence třídy spouštění</span><span class="sxs-lookup"><span data-stu-id="4b5de-369">Startup class conventions</span></span>

<span data-ttu-id="4b5de-370">Při spuštění aplikace ASP.NET Core spustí [spouštěcí třída](xref:fundamentals/startup) aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4b5de-370">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="4b5de-371">Aplikace může definovat samostatné třídy `Startup` pro různá prostředí (například `StartupDevelopment`).</span><span class="sxs-lookup"><span data-stu-id="4b5de-371">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="4b5de-372">Příslušná třída `Startup` je vybrána za běhu.</span><span class="sxs-lookup"><span data-stu-id="4b5de-372">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="4b5de-373">Třída, jejíž název má příponu odpovídající aktuálnímu prostředí, je upřednostněna.</span><span class="sxs-lookup"><span data-stu-id="4b5de-373">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="4b5de-374">Pokud není nalezena shodná `Startup{EnvironmentName}` třída, je použita třída `Startup`.</span><span class="sxs-lookup"><span data-stu-id="4b5de-374">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="4b5de-375">Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly v kódu pro každé prostředí.</span><span class="sxs-lookup"><span data-stu-id="4b5de-375">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="4b5de-376">Chcete-li implementovat třídy `Startup` založené na prostředí, vytvořte třídu `Startup{EnvironmentName}` pro každé používané prostředí a záložní třídu `Startup`:</span><span class="sxs-lookup"><span data-stu-id="4b5de-376">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

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

<span data-ttu-id="4b5de-377">Použijte přetížení [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) , které přijímá název sestavení:</span><span class="sxs-lookup"><span data-stu-id="4b5de-377">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

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

### <a name="startup-method-conventions"></a><span data-ttu-id="4b5de-378">Konvence metody spuštění</span><span class="sxs-lookup"><span data-stu-id="4b5de-378">Startup method conventions</span></span>

<span data-ttu-id="4b5de-379">[Nakonfigurujte](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) a [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) , které podporují verze formuláře `Configure<EnvironmentName>` a `Configure<EnvironmentName>Services`na konkrétní prostředí.</span><span class="sxs-lookup"><span data-stu-id="4b5de-379">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="4b5de-380">Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly v kódu pro každé prostředí.</span><span class="sxs-lookup"><span data-stu-id="4b5de-380">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="4b5de-381">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="4b5de-381">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
