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
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="2636e-103">Používání více prostředí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2636e-103">Use multiple environments in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2636e-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2636e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2636e-105">ASP.NET Core konfiguruje chování aplikací na základě runtime prostředí pomocí proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="2636e-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="2636e-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="2636e-107">Prostředí</span><span class="sxs-lookup"><span data-stu-id="2636e-107">Environments</span></span>

<span data-ttu-id="2636e-108">ASP.NET Core přečte `ASPNETCORE_ENVIRONMENT` proměnnou prostředí při spuštění aplikace a uloží hodnotu do [iWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="2636e-108">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span></span> <span data-ttu-id="2636e-109">`ASPNETCORE_ENVIRONMENT`lze nastavit na libovolnou hodnotu, ale rámci jsou uvedeny tři hodnoty:</span><span class="sxs-lookup"><span data-stu-id="2636e-109">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.Extensions.Hosting.Environments.Development>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="2636e-110"><xref:Microsoft.Extensions.Hosting.Environments.Production>(výchozí)</span><span class="sxs-lookup"><span data-stu-id="2636e-110"><xref:Microsoft.Extensions.Hosting.Environments.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="2636e-111">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="2636e-111">The preceding code:</span></span>

* <span data-ttu-id="2636e-112">Volání [UseDeveloperExceptionPage,](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) `ASPNETCORE_ENVIRONMENT` pokud `Development`je nastavena na .</span><span class="sxs-lookup"><span data-stu-id="2636e-112">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="2636e-113">Volá [UseExceptionHandler,](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) pokud `ASPNETCORE_ENVIRONMENT` je nastavena hodnota jedné z následujících možností:</span><span class="sxs-lookup"><span data-stu-id="2636e-113">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="2636e-114">Pomocník [značky prostředí](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) používá `IHostingEnvironment.EnvironmentName` hodnotu zahrnout nebo vyloučit značky v prvku:</span><span class="sxs-lookup"><span data-stu-id="2636e-114">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="2636e-115">Ve Windows a macOS nerozlišují proměnné a hodnoty prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-115">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="2636e-116">Proměnné a hodnoty prostředí Linuxu jsou ve výchozím nastavení **rozlišovány malá a velká písmena.**</span><span class="sxs-lookup"><span data-stu-id="2636e-116">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="2636e-117">Vývoj</span><span class="sxs-lookup"><span data-stu-id="2636e-117">Development</span></span>

<span data-ttu-id="2636e-118">Vývojové prostředí může povolit funkce, které by neměly být vystaveny v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-118">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="2636e-119">Například ASP.NET základní šablony umožňují [stránku výjimky pro vývojáře](xref:fundamentals/error-handling#developer-exception-page) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-119">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="2636e-120">Prostředí pro vývoj místního počítače lze nastavit v souboru *Properties\launchSettings.json* projektu.</span><span class="sxs-lookup"><span data-stu-id="2636e-120">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="2636e-121">Hodnoty prostředí nastavené v *launchSettings.json* přepíší hodnoty nastavené v systémovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-121">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="2636e-122">Následující JSON zobrazuje tři profily ze souboru *launchSettings.json:*</span><span class="sxs-lookup"><span data-stu-id="2636e-122">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

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
> <span data-ttu-id="2636e-123">Vlastnost `applicationUrl` v *launchSettings.json* můžete zadat seznam adres URL serveru.</span><span class="sxs-lookup"><span data-stu-id="2636e-123">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="2636e-124">Použijte středník mezi adresami URL v seznamu:</span><span class="sxs-lookup"><span data-stu-id="2636e-124">Use a semicolon between the URLs in the list:</span></span>
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

<span data-ttu-id="2636e-125">Při spuštění aplikace s [dotnet spustit](/dotnet/core/tools/dotnet-run), `"commandName": "Project"` první profil s se používá.</span><span class="sxs-lookup"><span data-stu-id="2636e-125">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="2636e-126">Hodnota `commandName` určuje webový server, který má být spuštěn.</span><span class="sxs-lookup"><span data-stu-id="2636e-126">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="2636e-127">`commandName`může být některý z následujících:</span><span class="sxs-lookup"><span data-stu-id="2636e-127">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="2636e-128">`Project`(který spouští Kestrel)</span><span class="sxs-lookup"><span data-stu-id="2636e-128">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="2636e-129">Při spuštění aplikace s [dotnet spustit](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="2636e-129">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="2636e-130">*launchSettings.json* je přečten, pokud je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="2636e-130">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="2636e-131">`environmentVariables`nastavení v *launchSettings.json* přepsat proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-131">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="2636e-132">Zobrazí se hostitelské prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-132">The hosting environment is displayed.</span></span>

<span data-ttu-id="2636e-133">Následující výstup ukazuje aplikaci spuštěnou [s dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="2636e-133">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="2636e-134">Karta **Ladění** vlastností projektu sady Visual Studio poskytuje grafické uživatelské rozhraní pro úpravu souboru *launchSettings.json:*</span><span class="sxs-lookup"><span data-stu-id="2636e-134">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Vlastnosti projektu Nastavení proměnných prostředí](environments/_static/project-properties-debug.png)

<span data-ttu-id="2636e-136">Změny provedené v profilech projektu se nemusí projevit, dokud nebude webový server restartován.</span><span class="sxs-lookup"><span data-stu-id="2636e-136">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="2636e-137">Kestrel musí být restartován předtím, než může rozpoznat změny provedené v jeho prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-137">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="2636e-138">*launchSettings.json* by neměl ukládat tajné klíče.</span><span class="sxs-lookup"><span data-stu-id="2636e-138">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="2636e-139">[Nástroj Správce tajných barev](xref:security/app-secrets) lze použít k ukládání tajných kódů pro místní vývoj.</span><span class="sxs-lookup"><span data-stu-id="2636e-139">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="2636e-140">Při použití [kódu sady Visual Studio](https://code.visualstudio.com/)lze proměnné prostředí nastavit v souboru *.vscode/launch.json.*</span><span class="sxs-lookup"><span data-stu-id="2636e-140">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="2636e-141">Následující příklad nastaví `Development`prostředí na :</span><span class="sxs-lookup"><span data-stu-id="2636e-141">The following example sets the environment to `Development`:</span></span>

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

<span data-ttu-id="2636e-142">Soubor *.vscode/launch.json* v projektu se nečte při `dotnet run` spuštění aplikace stejným způsobem jako *Vlastnosti/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="2636e-142">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="2636e-143">Při spouštění aplikace ve vývoji, která nemá soubor *launchSettings.json,* nastavte prostředí s proměnnou `dotnet run` prostředí nebo argument příkazového řádku na příkaz.</span><span class="sxs-lookup"><span data-stu-id="2636e-143">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="2636e-144">Výroba</span><span class="sxs-lookup"><span data-stu-id="2636e-144">Production</span></span>

<span data-ttu-id="2636e-145">Produkční prostředí by mělo být nakonfigurováno tak, aby maximalizovalo zabezpečení, výkon a robustnost aplikací.</span><span class="sxs-lookup"><span data-stu-id="2636e-145">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="2636e-146">Některá běžná nastavení, která se liší od vývoje, zahrnují:</span><span class="sxs-lookup"><span data-stu-id="2636e-146">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="2636e-147">Mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="2636e-147">Caching.</span></span>
* <span data-ttu-id="2636e-148">Prostředky na straně klienta jsou sdružené, minifikovány a potenciálně obsluhovány z cdn.</span><span class="sxs-lookup"><span data-stu-id="2636e-148">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="2636e-149">Diagnostické chybové stránky jsou zakázány.</span><span class="sxs-lookup"><span data-stu-id="2636e-149">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="2636e-150">Jsou povoleny popisné chybové stránky.</span><span class="sxs-lookup"><span data-stu-id="2636e-150">Friendly error pages enabled.</span></span>
* <span data-ttu-id="2636e-151">Povoleno protokolování a monitorování výroby.</span><span class="sxs-lookup"><span data-stu-id="2636e-151">Production logging and monitoring enabled.</span></span> <span data-ttu-id="2636e-152">Například [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="2636e-152">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="2636e-153">Nastavení prostředí</span><span class="sxs-lookup"><span data-stu-id="2636e-153">Set the environment</span></span>

<span data-ttu-id="2636e-154">Často je užitečné nastavit konkrétní prostředí pro testování s proměnnou prostředí nebo nastavení platformy.</span><span class="sxs-lookup"><span data-stu-id="2636e-154">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="2636e-155">Pokud prostředí není nastaveno, je `Production`výchozí na , který zakáže většinu funkcí ladění.</span><span class="sxs-lookup"><span data-stu-id="2636e-155">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="2636e-156">Způsob nastavení prostředí závisí na operačním systému.</span><span class="sxs-lookup"><span data-stu-id="2636e-156">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="2636e-157">Při vytváření hostitele určuje prostředí aplikace poslední nastavení prostředí přečtené aplikací.</span><span class="sxs-lookup"><span data-stu-id="2636e-157">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="2636e-158">Prostředí aplikace nelze změnit, když je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="2636e-158">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="2636e-159">Nastavení proměnné prostředí nebo platformy</span><span class="sxs-lookup"><span data-stu-id="2636e-159">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="2636e-160">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="2636e-160">Azure App Service</span></span>

<span data-ttu-id="2636e-161">Chcete-li nastavit prostředí ve [službě Azure App Service](https://azure.microsoft.com/services/app-service/), proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="2636e-161">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="2636e-162">Vyberte aplikaci z okna **Služby aplikací.**</span><span class="sxs-lookup"><span data-stu-id="2636e-162">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="2636e-163">Ve skupině **Nastavení** vyberte okno **Konfigurace.**</span><span class="sxs-lookup"><span data-stu-id="2636e-163">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="2636e-164">Na kartě **Nastavení aplikace** vyberte Nastavení **nové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="2636e-164">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="2636e-165">V okně Nastavení aplikace Přidat `ASPNETCORE_ENVIRONMENT` nebo **upravit** zadejte **název**.</span><span class="sxs-lookup"><span data-stu-id="2636e-165">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="2636e-166">Pro **hodnotu**zadejte prostředí `Staging`(například).</span><span class="sxs-lookup"><span data-stu-id="2636e-166">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="2636e-167">Pokud chcete, aby nastavení prostředí zůstalo s aktuální mašleí při výměně slotů nasazení, zaškrtněte políčko **Nastavení patice nasazení.**</span><span class="sxs-lookup"><span data-stu-id="2636e-167">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="2636e-168">Další informace najdete v tématu [Nastavení pracovních prostředí ve službě Azure App Service](/azure/app-service/web-sites-staged-publishing) v dokumentaci k Azure.</span><span class="sxs-lookup"><span data-stu-id="2636e-168">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="2636e-169">Výběrem **možnosti OK** zavřete okno **Nastavení aplikace Přidat nebo upravit.**</span><span class="sxs-lookup"><span data-stu-id="2636e-169">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="2636e-170">V horní části okna **Konfigurace** vyberte **Uložit.**</span><span class="sxs-lookup"><span data-stu-id="2636e-170">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="2636e-171">Služba Azure App Service aplikaci automaticky restartuje po přidání, změně nebo odstranění na webu Azure Portal (proměnná prostředí).</span><span class="sxs-lookup"><span data-stu-id="2636e-171">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="2636e-172">Windows</span><span class="sxs-lookup"><span data-stu-id="2636e-172">Windows</span></span>

<span data-ttu-id="2636e-173">Chcete-li `ASPNETCORE_ENVIRONMENT` nastavit aktuální relaci při spuštění aplikace pomocí [dotnet run](/dotnet/core/tools/dotnet-run), použijí se následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="2636e-173">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="2636e-174">**Příkazového řádku**</span><span class="sxs-lookup"><span data-stu-id="2636e-174">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="2636e-175">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="2636e-175">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="2636e-176">Tyto příkazy se projeví pouze pro aktuální okno.</span><span class="sxs-lookup"><span data-stu-id="2636e-176">These commands only take effect for the current window.</span></span> <span data-ttu-id="2636e-177">Když je okno zavřené, `ASPNETCORE_ENVIRONMENT` nastavení se vrátí na výchozí nastavení nebo hodnotu počítače.</span><span class="sxs-lookup"><span data-stu-id="2636e-177">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="2636e-178">Chcete-li nastavit hodnotu globálně v systému Windows, použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="2636e-178">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="2636e-179">Otevřete `ASPNETCORE_ENVIRONMENT` nastavení > **systému Ovládací** **panely:** > **System**</span><span class="sxs-lookup"><span data-stu-id="2636e-179">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Rozšířené vlastnosti systému](environments/_static/systemsetting_environment.png)

  ![Proměnná jádrového prostředí ASPNET](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="2636e-182">Otevřete příkazový řádek `setx` pro správu a použijte příkaz `[Environment]::SetEnvironmentVariable`nebo příkaz PowerShell pro správu a použijte :</span><span class="sxs-lookup"><span data-stu-id="2636e-182">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="2636e-183">**Příkazového řádku**</span><span class="sxs-lookup"><span data-stu-id="2636e-183">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="2636e-184">Přepínač `/M` označuje nastavení proměnné prostředí na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="2636e-184">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="2636e-185">Pokud `/M` se přepínač nepoužívá, je pro uživatelský účet nastavena proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-185">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="2636e-186">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="2636e-186">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="2636e-187">Hodnota `Machine` možnosti označuje nastavení proměnné prostředí na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="2636e-187">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="2636e-188">Pokud se hodnota možnosti změní na `User`, je pro uživatelský účet nastavena proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-188">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="2636e-189">Když `ASPNETCORE_ENVIRONMENT` je proměnná prostředí nastavena globálně, projeví se v `dotnet run` libovolném příkazovém okně otevřeném po nastavení hodnoty.</span><span class="sxs-lookup"><span data-stu-id="2636e-189">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="2636e-190">**Souboru web.config**</span><span class="sxs-lookup"><span data-stu-id="2636e-190">**web.config**</span></span>

<span data-ttu-id="2636e-191">Chcete-li `ASPNETCORE_ENVIRONMENT` nastavit proměnnou prostředí pomocí *souboru web.config*, přečtěte si část *Nastavení proměnných prostředí* v aplikaci <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="2636e-191">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="2636e-192">**Soubor projektu nebo profil publikování**</span><span class="sxs-lookup"><span data-stu-id="2636e-192">**Project file or publish profile**</span></span>

<span data-ttu-id="2636e-193">**Pro nasazení služby Windows IIS:** Zahrňte `<EnvironmentName>` vlastnost do [profilu publikování (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo do souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="2636e-193">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="2636e-194">Tento přístup nastaví prostředí v *web.config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="2636e-194">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="2636e-195">**Na fond aplikací služby IIS**</span><span class="sxs-lookup"><span data-stu-id="2636e-195">**Per IIS Application Pool**</span></span>

<span data-ttu-id="2636e-196">Pokud chcete `ASPNETCORE_ENVIRONMENT` nastavit proměnnou prostředí pro aplikaci spuštěnou v izolovaném fondu aplikací (podporovaná ve službě IIS 10.0 nebo novější), přečtěte si část *příkazu AppCmd.exe* v tématu Proměnné [ &lt;&gt; prostředí proměnných](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-196">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="2636e-197">Když `ASPNETCORE_ENVIRONMENT` je proměnná prostředí nastavená pro fond aplikací, její hodnota přepíše nastavení na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="2636e-197">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2636e-198">Při hostování aplikace ve službě IIS `ASPNETCORE_ENVIRONMENT` a přidávání nebo změně proměnné prostředí použijte některý z následujících přístupů, aby se nová hodnota zvedla aplikacemi:</span><span class="sxs-lookup"><span data-stu-id="2636e-198">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="2636e-199">Provedení `net stop was /y` následované `net start w3svc` příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="2636e-199">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="2636e-200">Restartujte server.</span><span class="sxs-lookup"><span data-stu-id="2636e-200">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="2636e-201">macOS</span><span class="sxs-lookup"><span data-stu-id="2636e-201">macOS</span></span>

<span data-ttu-id="2636e-202">Nastavení aktuálního prostředí pro macOS lze provést in-line při spuštění aplikace:</span><span class="sxs-lookup"><span data-stu-id="2636e-202">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="2636e-203">Případně nastavte prostředí s `export` před spuštěním aplikace:</span><span class="sxs-lookup"><span data-stu-id="2636e-203">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="2636e-204">Proměnné prostředí na úrovni počítače jsou nastaveny v souboru *%.bashrc* nebo *.bash_profile.*</span><span class="sxs-lookup"><span data-stu-id="2636e-204">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="2636e-205">Upravte soubor pomocí libovolného textového editoru.</span><span class="sxs-lookup"><span data-stu-id="2636e-205">Edit the file using any text editor.</span></span> <span data-ttu-id="2636e-206">Přidejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="2636e-206">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="2636e-207">Linux</span><span class="sxs-lookup"><span data-stu-id="2636e-207">Linux</span></span>

<span data-ttu-id="2636e-208">Pro distribuce Linuxu `export` použijte příkaz na příkazovém řádku pro nastavení proměnných na základě relace a *bash_profile* soubor pro nastavení prostředí na úrovni počítače.</span><span class="sxs-lookup"><span data-stu-id="2636e-208">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="2636e-209">Nastavení prostředí v kódu</span><span class="sxs-lookup"><span data-stu-id="2636e-209">Set the environment in code</span></span>

<span data-ttu-id="2636e-210">Zavolejte <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> při budování hostitele.</span><span class="sxs-lookup"><span data-stu-id="2636e-210">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="2636e-211">Viz třída <xref:fundamentals/host/generic-host#environmentname>.</span><span class="sxs-lookup"><span data-stu-id="2636e-211">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>


### <a name="configuration-by-environment"></a><span data-ttu-id="2636e-212">Konfigurace podle prostředí</span><span class="sxs-lookup"><span data-stu-id="2636e-212">Configuration by environment</span></span>

<span data-ttu-id="2636e-213">Chcete-li načíst konfiguraci podle prostředí, doporučujeme:</span><span class="sxs-lookup"><span data-stu-id="2636e-213">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="2636e-214">*soubory nastavení aplikace* (*appsettings.{ prostředí}.json*).</span><span class="sxs-lookup"><span data-stu-id="2636e-214">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="2636e-215">Viz třída <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="2636e-215">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="2636e-216">Proměnné prostředí (nastavené v každém systému, kde je aplikace hostována).</span><span class="sxs-lookup"><span data-stu-id="2636e-216">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="2636e-217">Zobrazit <xref:fundamentals/host/generic-host#environmentname> <xref:security/app-secrets#environment-variables>a .</span><span class="sxs-lookup"><span data-stu-id="2636e-217">See <xref:fundamentals/host/generic-host#environmentname> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="2636e-218">Správce tajných barev (pouze ve vývojovém prostředí).</span><span class="sxs-lookup"><span data-stu-id="2636e-218">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="2636e-219">Viz třída <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="2636e-219">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="2636e-220">Třída a metody startupu založené na prostředí</span><span class="sxs-lookup"><span data-stu-id="2636e-220">Environment-based Startup class and methods</span></span>

### <a name="inject-iwebhostenvironment-into-startupconfigure"></a><span data-ttu-id="2636e-221">Vložte prostředí IWebHostEnvironment do startupu.Konfigurace</span><span class="sxs-lookup"><span data-stu-id="2636e-221">Inject IWebHostEnvironment into Startup.Configure</span></span>

<span data-ttu-id="2636e-222"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> Aplikujte `Startup.Configure`do něj .</span><span class="sxs-lookup"><span data-stu-id="2636e-222">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="2636e-223">Tento přístup je užitečný, když `Startup.Configure` aplikace vyžaduje pouze úpravu pro několik prostředí s minimálními rozdíly kódu na prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-223">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>

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

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="2636e-224">Vstříkněte prostředí IWebHostEnvironment do třídy Startup</span><span class="sxs-lookup"><span data-stu-id="2636e-224">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="2636e-225">Vstříkněte <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do konstruktoru. `Startup`</span><span class="sxs-lookup"><span data-stu-id="2636e-225">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="2636e-226">Tento přístup je užitečný, když `Startup` aplikace vyžaduje konfiguraci pouze pro několik prostředí s minimálními rozdíly kódu na prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-226">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="2636e-227">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="2636e-227">In the following example:</span></span>

* <span data-ttu-id="2636e-228">Prostředí je drženo `_env` v terénu.</span><span class="sxs-lookup"><span data-stu-id="2636e-228">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="2636e-229">`_env`se používá `ConfigureServices` `Configure` v a použít konfiguraci při spuštění na základě prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="2636e-229">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

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
### <a name="startup-class-conventions"></a><span data-ttu-id="2636e-230">Konvence spouštěcí třídy</span><span class="sxs-lookup"><span data-stu-id="2636e-230">Startup class conventions</span></span>

<span data-ttu-id="2636e-231">Když se spustí ASP.NET základní aplikace, [spustí se třída Startup.](xref:fundamentals/startup)</span><span class="sxs-lookup"><span data-stu-id="2636e-231">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="2636e-232">Aplikace může definovat `Startup` samostatné třídy pro různá prostředí `StartupDevelopment`(například).</span><span class="sxs-lookup"><span data-stu-id="2636e-232">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="2636e-233">Příslušná `Startup` třída je vybrána za běhu.</span><span class="sxs-lookup"><span data-stu-id="2636e-233">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="2636e-234">Třída, jejíž přípona názvu odpovídá aktuálnímu prostředí, je upřednostněna.</span><span class="sxs-lookup"><span data-stu-id="2636e-234">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="2636e-235">Pokud odpovídající `Startup{EnvironmentName}` třída nebyla nalezena, třída se `Startup` používá.</span><span class="sxs-lookup"><span data-stu-id="2636e-235">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="2636e-236">Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly kódu na prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-236">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="2636e-237">Chcete-li implementovat třídy založené na `Startup` prostředí, vytvořte třídu `Startup{EnvironmentName}` pro každé prostředí, které se používá, a záložní `Startup` třídu:</span><span class="sxs-lookup"><span data-stu-id="2636e-237">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

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

<span data-ttu-id="2636e-238">Použijte [přetížení UseStartup(IWebHostBuilder, String),](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) které přijímá název sestavení:</span><span class="sxs-lookup"><span data-stu-id="2636e-238">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

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

### <a name="startup-method-conventions"></a><span data-ttu-id="2636e-239">Konvence metod spouštění</span><span class="sxs-lookup"><span data-stu-id="2636e-239">Startup method conventions</span></span>

<span data-ttu-id="2636e-240">[Konfigurace](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) a [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) podporují verze formuláře `Configure<EnvironmentName>` a `Configure<EnvironmentName>Services`.</span><span class="sxs-lookup"><span data-stu-id="2636e-240">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="2636e-241">Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly kódu na prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-241">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="2636e-242">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2636e-242">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2636e-243">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2636e-243">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2636e-244">ASP.NET Core konfiguruje chování aplikací na základě runtime prostředí pomocí proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-244">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="2636e-245">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="2636e-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="2636e-246">Prostředí</span><span class="sxs-lookup"><span data-stu-id="2636e-246">Environments</span></span>

<span data-ttu-id="2636e-247">ASP.NET Core přečte `ASPNETCORE_ENVIRONMENT` proměnnou prostředí při spuštění aplikace a uloží hodnotu do [iHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="2636e-247">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="2636e-248">`ASPNETCORE_ENVIRONMENT`lze nastavit na libovolnou hodnotu, ale rámci jsou uvedeny tři hodnoty:</span><span class="sxs-lookup"><span data-stu-id="2636e-248">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="2636e-249"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production>(výchozí)</span><span class="sxs-lookup"><span data-stu-id="2636e-249"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="2636e-250">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="2636e-250">The preceding code:</span></span>

* <span data-ttu-id="2636e-251">Volání [UseDeveloperExceptionPage,](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) `ASPNETCORE_ENVIRONMENT` pokud `Development`je nastavena na .</span><span class="sxs-lookup"><span data-stu-id="2636e-251">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="2636e-252">Volá [UseExceptionHandler,](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) pokud `ASPNETCORE_ENVIRONMENT` je nastavena hodnota jedné z následujících možností:</span><span class="sxs-lookup"><span data-stu-id="2636e-252">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="2636e-253">Pomocník [značky prostředí](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) používá `IHostingEnvironment.EnvironmentName` hodnotu zahrnout nebo vyloučit značky v prvku:</span><span class="sxs-lookup"><span data-stu-id="2636e-253">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="2636e-254">Ve Windows a macOS nerozlišují proměnné a hodnoty prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-254">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="2636e-255">Proměnné a hodnoty prostředí Linuxu jsou ve výchozím nastavení **rozlišovány malá a velká písmena.**</span><span class="sxs-lookup"><span data-stu-id="2636e-255">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="2636e-256">Vývoj</span><span class="sxs-lookup"><span data-stu-id="2636e-256">Development</span></span>

<span data-ttu-id="2636e-257">Vývojové prostředí může povolit funkce, které by neměly být vystaveny v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-257">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="2636e-258">Například ASP.NET základní šablony umožňují [stránku výjimky pro vývojáře](xref:fundamentals/error-handling#developer-exception-page) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-258">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="2636e-259">Prostředí pro vývoj místního počítače lze nastavit v souboru *Properties\launchSettings.json* projektu.</span><span class="sxs-lookup"><span data-stu-id="2636e-259">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="2636e-260">Hodnoty prostředí nastavené v *launchSettings.json* přepíší hodnoty nastavené v systémovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-260">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="2636e-261">Následující JSON zobrazuje tři profily ze souboru *launchSettings.json:*</span><span class="sxs-lookup"><span data-stu-id="2636e-261">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

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
> <span data-ttu-id="2636e-262">Vlastnost `applicationUrl` v *launchSettings.json* můžete zadat seznam adres URL serveru.</span><span class="sxs-lookup"><span data-stu-id="2636e-262">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="2636e-263">Použijte středník mezi adresami URL v seznamu:</span><span class="sxs-lookup"><span data-stu-id="2636e-263">Use a semicolon between the URLs in the list:</span></span>
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

<span data-ttu-id="2636e-264">Při spuštění aplikace s [dotnet spustit](/dotnet/core/tools/dotnet-run), `"commandName": "Project"` první profil s se používá.</span><span class="sxs-lookup"><span data-stu-id="2636e-264">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="2636e-265">Hodnota `commandName` určuje webový server, který má být spuštěn.</span><span class="sxs-lookup"><span data-stu-id="2636e-265">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="2636e-266">`commandName`může být některý z následujících:</span><span class="sxs-lookup"><span data-stu-id="2636e-266">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="2636e-267">`Project`(který spouští Kestrel)</span><span class="sxs-lookup"><span data-stu-id="2636e-267">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="2636e-268">Při spuštění aplikace s [dotnet spustit](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="2636e-268">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="2636e-269">*launchSettings.json* je přečten, pokud je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="2636e-269">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="2636e-270">`environmentVariables`nastavení v *launchSettings.json* přepsat proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-270">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="2636e-271">Zobrazí se hostitelské prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-271">The hosting environment is displayed.</span></span>

<span data-ttu-id="2636e-272">Následující výstup ukazuje aplikaci spuštěnou [s dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="2636e-272">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="2636e-273">Karta **Ladění** vlastností projektu sady Visual Studio poskytuje grafické uživatelské rozhraní pro úpravu souboru *launchSettings.json:*</span><span class="sxs-lookup"><span data-stu-id="2636e-273">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Vlastnosti projektu Nastavení proměnných prostředí](environments/_static/project-properties-debug.png)

<span data-ttu-id="2636e-275">Změny provedené v profilech projektu se nemusí projevit, dokud nebude webový server restartován.</span><span class="sxs-lookup"><span data-stu-id="2636e-275">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="2636e-276">Kestrel musí být restartován předtím, než může rozpoznat změny provedené v jeho prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-276">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="2636e-277">*launchSettings.json* by neměl ukládat tajné klíče.</span><span class="sxs-lookup"><span data-stu-id="2636e-277">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="2636e-278">[Nástroj Správce tajných barev](xref:security/app-secrets) lze použít k ukládání tajných kódů pro místní vývoj.</span><span class="sxs-lookup"><span data-stu-id="2636e-278">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="2636e-279">Při použití [kódu sady Visual Studio](https://code.visualstudio.com/)lze proměnné prostředí nastavit v souboru *.vscode/launch.json.*</span><span class="sxs-lookup"><span data-stu-id="2636e-279">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="2636e-280">Následující příklad nastaví `Development`prostředí na :</span><span class="sxs-lookup"><span data-stu-id="2636e-280">The following example sets the environment to `Development`:</span></span>

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

<span data-ttu-id="2636e-281">Soubor *.vscode/launch.json* v projektu se nečte při `dotnet run` spuštění aplikace stejným způsobem jako *Vlastnosti/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="2636e-281">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="2636e-282">Při spouštění aplikace ve vývoji, která nemá soubor *launchSettings.json,* nastavte prostředí s proměnnou `dotnet run` prostředí nebo argument příkazového řádku na příkaz.</span><span class="sxs-lookup"><span data-stu-id="2636e-282">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="2636e-283">Výroba</span><span class="sxs-lookup"><span data-stu-id="2636e-283">Production</span></span>

<span data-ttu-id="2636e-284">Produkční prostředí by mělo být nakonfigurováno tak, aby maximalizovalo zabezpečení, výkon a robustnost aplikací.</span><span class="sxs-lookup"><span data-stu-id="2636e-284">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="2636e-285">Některá běžná nastavení, která se liší od vývoje, zahrnují:</span><span class="sxs-lookup"><span data-stu-id="2636e-285">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="2636e-286">Mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="2636e-286">Caching.</span></span>
* <span data-ttu-id="2636e-287">Prostředky na straně klienta jsou sdružené, minifikovány a potenciálně obsluhovány z cdn.</span><span class="sxs-lookup"><span data-stu-id="2636e-287">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="2636e-288">Diagnostické chybové stránky jsou zakázány.</span><span class="sxs-lookup"><span data-stu-id="2636e-288">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="2636e-289">Jsou povoleny popisné chybové stránky.</span><span class="sxs-lookup"><span data-stu-id="2636e-289">Friendly error pages enabled.</span></span>
* <span data-ttu-id="2636e-290">Povoleno protokolování a monitorování výroby.</span><span class="sxs-lookup"><span data-stu-id="2636e-290">Production logging and monitoring enabled.</span></span> <span data-ttu-id="2636e-291">Například [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="2636e-291">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="2636e-292">Nastavení prostředí</span><span class="sxs-lookup"><span data-stu-id="2636e-292">Set the environment</span></span>

<span data-ttu-id="2636e-293">Často je užitečné nastavit konkrétní prostředí pro testování s proměnnou prostředí nebo nastavení platformy.</span><span class="sxs-lookup"><span data-stu-id="2636e-293">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="2636e-294">Pokud prostředí není nastaveno, je `Production`výchozí na , který zakáže většinu funkcí ladění.</span><span class="sxs-lookup"><span data-stu-id="2636e-294">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="2636e-295">Způsob nastavení prostředí závisí na operačním systému.</span><span class="sxs-lookup"><span data-stu-id="2636e-295">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="2636e-296">Při vytváření hostitele určuje prostředí aplikace poslední nastavení prostředí přečtené aplikací.</span><span class="sxs-lookup"><span data-stu-id="2636e-296">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="2636e-297">Prostředí aplikace nelze změnit, když je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="2636e-297">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="2636e-298">Nastavení proměnné prostředí nebo platformy</span><span class="sxs-lookup"><span data-stu-id="2636e-298">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="2636e-299">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="2636e-299">Azure App Service</span></span>

<span data-ttu-id="2636e-300">Chcete-li nastavit prostředí ve [službě Azure App Service](https://azure.microsoft.com/services/app-service/), proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="2636e-300">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="2636e-301">Vyberte aplikaci z okna **Služby aplikací.**</span><span class="sxs-lookup"><span data-stu-id="2636e-301">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="2636e-302">Ve skupině **Nastavení** vyberte okno **Konfigurace.**</span><span class="sxs-lookup"><span data-stu-id="2636e-302">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="2636e-303">Na kartě **Nastavení aplikace** vyberte Nastavení **nové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="2636e-303">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="2636e-304">V okně Nastavení aplikace Přidat `ASPNETCORE_ENVIRONMENT` nebo **upravit** zadejte **název**.</span><span class="sxs-lookup"><span data-stu-id="2636e-304">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="2636e-305">Pro **hodnotu**zadejte prostředí `Staging`(například).</span><span class="sxs-lookup"><span data-stu-id="2636e-305">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="2636e-306">Pokud chcete, aby nastavení prostředí zůstalo s aktuální mašleí při výměně slotů nasazení, zaškrtněte políčko **Nastavení patice nasazení.**</span><span class="sxs-lookup"><span data-stu-id="2636e-306">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="2636e-307">Další informace najdete v tématu [Nastavení pracovních prostředí ve službě Azure App Service](/azure/app-service/web-sites-staged-publishing) v dokumentaci k Azure.</span><span class="sxs-lookup"><span data-stu-id="2636e-307">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="2636e-308">Výběrem **možnosti OK** zavřete okno **Nastavení aplikace Přidat nebo upravit.**</span><span class="sxs-lookup"><span data-stu-id="2636e-308">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="2636e-309">V horní části okna **Konfigurace** vyberte **Uložit.**</span><span class="sxs-lookup"><span data-stu-id="2636e-309">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="2636e-310">Služba Azure App Service aplikaci automaticky restartuje po přidání, změně nebo odstranění na webu Azure Portal (proměnná prostředí).</span><span class="sxs-lookup"><span data-stu-id="2636e-310">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="2636e-311">Windows</span><span class="sxs-lookup"><span data-stu-id="2636e-311">Windows</span></span>

<span data-ttu-id="2636e-312">Chcete-li `ASPNETCORE_ENVIRONMENT` nastavit aktuální relaci při spuštění aplikace pomocí [dotnet run](/dotnet/core/tools/dotnet-run), použijí se následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="2636e-312">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="2636e-313">**Příkazového řádku**</span><span class="sxs-lookup"><span data-stu-id="2636e-313">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="2636e-314">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="2636e-314">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="2636e-315">Tyto příkazy se projeví pouze pro aktuální okno.</span><span class="sxs-lookup"><span data-stu-id="2636e-315">These commands only take effect for the current window.</span></span> <span data-ttu-id="2636e-316">Když je okno zavřené, `ASPNETCORE_ENVIRONMENT` nastavení se vrátí na výchozí nastavení nebo hodnotu počítače.</span><span class="sxs-lookup"><span data-stu-id="2636e-316">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="2636e-317">Chcete-li nastavit hodnotu globálně v systému Windows, použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="2636e-317">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="2636e-318">Otevřete `ASPNETCORE_ENVIRONMENT` nastavení > **systému Ovládací** **panely:** > **System**</span><span class="sxs-lookup"><span data-stu-id="2636e-318">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Rozšířené vlastnosti systému](environments/_static/systemsetting_environment.png)

  ![Proměnná jádrového prostředí ASPNET](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="2636e-321">Otevřete příkazový řádek `setx` pro správu a použijte příkaz `[Environment]::SetEnvironmentVariable`nebo příkaz PowerShell pro správu a použijte :</span><span class="sxs-lookup"><span data-stu-id="2636e-321">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="2636e-322">**Příkazového řádku**</span><span class="sxs-lookup"><span data-stu-id="2636e-322">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="2636e-323">Přepínač `/M` označuje nastavení proměnné prostředí na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="2636e-323">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="2636e-324">Pokud `/M` se přepínač nepoužívá, je pro uživatelský účet nastavena proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-324">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="2636e-325">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="2636e-325">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="2636e-326">Hodnota `Machine` možnosti označuje nastavení proměnné prostředí na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="2636e-326">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="2636e-327">Pokud se hodnota možnosti změní na `User`, je pro uživatelský účet nastavena proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-327">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="2636e-328">Když `ASPNETCORE_ENVIRONMENT` je proměnná prostředí nastavena globálně, projeví se v `dotnet run` libovolném příkazovém okně otevřeném po nastavení hodnoty.</span><span class="sxs-lookup"><span data-stu-id="2636e-328">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="2636e-329">**Souboru web.config**</span><span class="sxs-lookup"><span data-stu-id="2636e-329">**web.config**</span></span>

<span data-ttu-id="2636e-330">Chcete-li `ASPNETCORE_ENVIRONMENT` nastavit proměnnou prostředí pomocí *souboru web.config*, přečtěte si část *Nastavení proměnných prostředí* v aplikaci <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="2636e-330">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="2636e-331">**Soubor projektu nebo profil publikování**</span><span class="sxs-lookup"><span data-stu-id="2636e-331">**Project file or publish profile**</span></span>

<span data-ttu-id="2636e-332">**Pro nasazení služby Windows IIS:** Zahrňte `<EnvironmentName>` vlastnost do [profilu publikování (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo do souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="2636e-332">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="2636e-333">Tento přístup nastaví prostředí v *web.config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="2636e-333">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="2636e-334">**Na fond aplikací služby IIS**</span><span class="sxs-lookup"><span data-stu-id="2636e-334">**Per IIS Application Pool**</span></span>

<span data-ttu-id="2636e-335">Pokud chcete `ASPNETCORE_ENVIRONMENT` nastavit proměnnou prostředí pro aplikaci spuštěnou v izolovaném fondu aplikací (podporovaná ve službě IIS 10.0 nebo novější), přečtěte si část *příkazu AppCmd.exe* v tématu Proměnné [ &lt;&gt; prostředí proměnných](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-335">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="2636e-336">Když `ASPNETCORE_ENVIRONMENT` je proměnná prostředí nastavená pro fond aplikací, její hodnota přepíše nastavení na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="2636e-336">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2636e-337">Při hostování aplikace ve službě IIS `ASPNETCORE_ENVIRONMENT` a přidávání nebo změně proměnné prostředí použijte některý z následujících přístupů, aby se nová hodnota zvedla aplikacemi:</span><span class="sxs-lookup"><span data-stu-id="2636e-337">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="2636e-338">Provedení `net stop was /y` následované `net start w3svc` příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="2636e-338">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="2636e-339">Restartujte server.</span><span class="sxs-lookup"><span data-stu-id="2636e-339">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="2636e-340">macOS</span><span class="sxs-lookup"><span data-stu-id="2636e-340">macOS</span></span>

<span data-ttu-id="2636e-341">Nastavení aktuálního prostředí pro macOS lze provést in-line při spuštění aplikace:</span><span class="sxs-lookup"><span data-stu-id="2636e-341">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="2636e-342">Případně nastavte prostředí s `export` před spuštěním aplikace:</span><span class="sxs-lookup"><span data-stu-id="2636e-342">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="2636e-343">Proměnné prostředí na úrovni počítače jsou nastaveny v souboru *%.bashrc* nebo *.bash_profile.*</span><span class="sxs-lookup"><span data-stu-id="2636e-343">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="2636e-344">Upravte soubor pomocí libovolného textového editoru.</span><span class="sxs-lookup"><span data-stu-id="2636e-344">Edit the file using any text editor.</span></span> <span data-ttu-id="2636e-345">Přidejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="2636e-345">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="2636e-346">Linux</span><span class="sxs-lookup"><span data-stu-id="2636e-346">Linux</span></span>

<span data-ttu-id="2636e-347">Pro distribuce Linuxu `export` použijte příkaz na příkazovém řádku pro nastavení proměnných na základě relace a *bash_profile* soubor pro nastavení prostředí na úrovni počítače.</span><span class="sxs-lookup"><span data-stu-id="2636e-347">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="2636e-348">Nastavení prostředí v kódu</span><span class="sxs-lookup"><span data-stu-id="2636e-348">Set the environment in code</span></span>

<span data-ttu-id="2636e-349">Zavolejte <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> při budování hostitele.</span><span class="sxs-lookup"><span data-stu-id="2636e-349">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="2636e-350">Viz třída <xref:fundamentals/host/web-host#environment>.</span><span class="sxs-lookup"><span data-stu-id="2636e-350">See <xref:fundamentals/host/web-host#environment>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="2636e-351">Konfigurace podle prostředí</span><span class="sxs-lookup"><span data-stu-id="2636e-351">Configuration by environment</span></span>

<span data-ttu-id="2636e-352">Chcete-li načíst konfiguraci podle prostředí, doporučujeme:</span><span class="sxs-lookup"><span data-stu-id="2636e-352">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="2636e-353">*soubory nastavení aplikace* (*appsettings.{ prostředí}.json*).</span><span class="sxs-lookup"><span data-stu-id="2636e-353">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="2636e-354">Viz třída <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="2636e-354">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="2636e-355">Proměnné prostředí (nastavené v každém systému, kde je aplikace hostována).</span><span class="sxs-lookup"><span data-stu-id="2636e-355">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="2636e-356">Zobrazit <xref:fundamentals/host/web-host#environment> <xref:security/app-secrets#environment-variables>a .</span><span class="sxs-lookup"><span data-stu-id="2636e-356">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="2636e-357">Správce tajných barev (pouze ve vývojovém prostředí).</span><span class="sxs-lookup"><span data-stu-id="2636e-357">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="2636e-358">Viz třída <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="2636e-358">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="2636e-359">Třída a metody startupu založené na prostředí</span><span class="sxs-lookup"><span data-stu-id="2636e-359">Environment-based Startup class and methods</span></span>

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="2636e-360">Vložte iHostingEnvironment do startup.Configure</span><span class="sxs-lookup"><span data-stu-id="2636e-360">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="2636e-361"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> Aplikujte `Startup.Configure`do něj .</span><span class="sxs-lookup"><span data-stu-id="2636e-361">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="2636e-362">Tento přístup je užitečný, když `Startup.Configure` aplikace vyžaduje konfiguraci pouze pro několik prostředí s minimálními rozdíly kódu na prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-362">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

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

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="2636e-363">Vstříkněte iHostingEnvironment do třídy Startup</span><span class="sxs-lookup"><span data-stu-id="2636e-363">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="2636e-364">Vstříkněte <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> do konstruktoru `Startup` a přiřaďte službu k poli pro použití v `Startup` celé třídě.</span><span class="sxs-lookup"><span data-stu-id="2636e-364">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="2636e-365">Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pouze pro několik prostředí s minimálními rozdíly kódu na prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-365">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="2636e-366">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="2636e-366">In the following example:</span></span>

* <span data-ttu-id="2636e-367">Prostředí je drženo `_env` v terénu.</span><span class="sxs-lookup"><span data-stu-id="2636e-367">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="2636e-368">`_env`se používá `ConfigureServices` `Configure` v a použít konfiguraci při spuštění na základě prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="2636e-368">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

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

### <a name="startup-class-conventions"></a><span data-ttu-id="2636e-369">Konvence spouštěcí třídy</span><span class="sxs-lookup"><span data-stu-id="2636e-369">Startup class conventions</span></span>

<span data-ttu-id="2636e-370">Když se spustí ASP.NET základní aplikace, [spustí se třída Startup.](xref:fundamentals/startup)</span><span class="sxs-lookup"><span data-stu-id="2636e-370">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="2636e-371">Aplikace může definovat `Startup` samostatné třídy pro různá prostředí `StartupDevelopment`(například).</span><span class="sxs-lookup"><span data-stu-id="2636e-371">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="2636e-372">Příslušná `Startup` třída je vybrána za běhu.</span><span class="sxs-lookup"><span data-stu-id="2636e-372">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="2636e-373">Třída, jejíž přípona názvu odpovídá aktuálnímu prostředí, je upřednostněna.</span><span class="sxs-lookup"><span data-stu-id="2636e-373">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="2636e-374">Pokud odpovídající `Startup{EnvironmentName}` třída nebyla nalezena, třída se `Startup` používá.</span><span class="sxs-lookup"><span data-stu-id="2636e-374">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="2636e-375">Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly kódu na prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-375">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="2636e-376">Chcete-li implementovat třídy založené na `Startup` prostředí, vytvořte třídu `Startup{EnvironmentName}` pro každé prostředí, které se používá, a záložní `Startup` třídu:</span><span class="sxs-lookup"><span data-stu-id="2636e-376">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

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

<span data-ttu-id="2636e-377">Použijte [přetížení UseStartup(IWebHostBuilder, String),](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) které přijímá název sestavení:</span><span class="sxs-lookup"><span data-stu-id="2636e-377">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

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

### <a name="startup-method-conventions"></a><span data-ttu-id="2636e-378">Konvence metod spouštění</span><span class="sxs-lookup"><span data-stu-id="2636e-378">Startup method conventions</span></span>

<span data-ttu-id="2636e-379">[Konfigurace](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) a [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) podporují verze formuláře `Configure<EnvironmentName>` a `Configure<EnvironmentName>Services`.</span><span class="sxs-lookup"><span data-stu-id="2636e-379">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="2636e-380">Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly kódu na prostředí.</span><span class="sxs-lookup"><span data-stu-id="2636e-380">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="2636e-381">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2636e-381">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
