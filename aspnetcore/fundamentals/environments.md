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
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="73481-103">Používání více prostředí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="73481-103">Use multiple environments in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="73481-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Kirka Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="73481-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="73481-105">ASP.NET Core konfiguruje chování aplikace na základě běhového prostředí pomocí proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="73481-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="73481-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="73481-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="73481-107">Prostředí</span><span class="sxs-lookup"><span data-stu-id="73481-107">Environments</span></span>

<span data-ttu-id="73481-108">Pro určení běhového prostředí ASP.NET Core čtení z následujících proměnných prostředí:</span><span class="sxs-lookup"><span data-stu-id="73481-108">To determine the runtime environment, ASP.NET Core reads from the following environment variables:</span></span>

1. [<span data-ttu-id="73481-109">DOTNET_ENVIRONMENT</span><span class="sxs-lookup"><span data-stu-id="73481-109">DOTNET_ENVIRONMENT</span></span>](xref:fundamentals/configuration/index#default-host-configuration)
1. <span data-ttu-id="73481-110">`ASPNETCORE_ENVIRONMENT`Kdy <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> je volána metoda.</span><span class="sxs-lookup"><span data-stu-id="73481-110">`ASPNETCORE_ENVIRONMENT` when <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> is called.</span></span> <span data-ttu-id="73481-111">Výchozí ASP.NET Core volání šablon webové aplikace `ConfigureWebHostDefaults` .</span><span class="sxs-lookup"><span data-stu-id="73481-111">The default ASP.NET Core web app templates call `ConfigureWebHostDefaults`.</span></span> <span data-ttu-id="73481-112">`ASPNETCORE_ENVIRONMENT`Hodnota Přepisuje `DOTNET_ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="73481-112">The `ASPNETCORE_ENVIRONMENT` value overrides `DOTNET_ENVIRONMENT`.</span></span>

<span data-ttu-id="73481-113">`IHostEnvironment.EnvironmentName`lze nastavit na libovolnou hodnotu, ale následující hodnoty jsou poskytovány rozhraním:</span><span class="sxs-lookup"><span data-stu-id="73481-113">`IHostEnvironment.EnvironmentName` can be set to any value, but the following values are provided by the framework:</span></span>

* <span data-ttu-id="73481-114"><xref:Microsoft.Extensions.Hosting.Environments.Development>: [launchSettings.jsv](#lsj) sadě souborů na `ASPNETCORE_ENVIRONMENT` `Development` místním počítači.</span><span class="sxs-lookup"><span data-stu-id="73481-114"><xref:Microsoft.Extensions.Hosting.Environments.Development> : The [launchSettings.json](#lsj) file sets `ASPNETCORE_ENVIRONMENT` to `Development` on the local machine.</span></span>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="73481-115"><xref:Microsoft.Extensions.Hosting.Environments.Production>: Výchozí nastavení `DOTNET_ENVIRONMENT` , pokud a nebylo `ASPNETCORE_ENVIRONMENT` nastaveno.</span><span class="sxs-lookup"><span data-stu-id="73481-115"><xref:Microsoft.Extensions.Hosting.Environments.Production> : The default if `DOTNET_ENVIRONMENT` and `ASPNETCORE_ENVIRONMENT` have not been set.</span></span>

<span data-ttu-id="73481-116">Následující kód:</span><span class="sxs-lookup"><span data-stu-id="73481-116">The following code:</span></span>

* <span data-ttu-id="73481-117">Volá [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) , pokud `ASPNETCORE_ENVIRONMENT` je nastaven na `Development` .</span><span class="sxs-lookup"><span data-stu-id="73481-117">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="73481-118">Volá [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) , pokud je hodnota `ASPNETCORE_ENVIRONMENT` nastavena na `Staging` , `Production` nebo `Staging_2` .</span><span class="sxs-lookup"><span data-stu-id="73481-118">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set to `Staging`, `Production`, or  `Staging_2`.</span></span>
* <span data-ttu-id="73481-119">Vloží <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="73481-119">Injects <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="73481-120">Tento přístup je užitečný, když aplikace vyžaduje úpravu jenom `Startup.Configure` pro několik prostředí s minimálními rozdíly v kódu na prostředí.</span><span class="sxs-lookup"><span data-stu-id="73481-120">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>
* <span data-ttu-id="73481-121">Se podobá kódu generovanému šablonami ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="73481-121">Is similar to the code generated by the ASP.NET Core templates.</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="73481-122">[Pomocná značka prostředí](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) používá hodnotu [IHostEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) pro zahrnutí nebo vyloučení značek v elementu:</span><span class="sxs-lookup"><span data-stu-id="73481-122">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="73481-123">[Stránka about](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) z [ukázkového kódu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) obsahuje předchozí kód a zobrazuje hodnotu `IWebHostEnvironment.EnvironmentName` .</span><span class="sxs-lookup"><span data-stu-id="73481-123">The [About page](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) from the [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) includes the preceding markup and displays the value of `IWebHostEnvironment.EnvironmentName`.</span></span>

<span data-ttu-id="73481-124">V systému Windows a macOS se v proměnných a hodnotách prostředí nerozlišují velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="73481-124">On Windows and macOS, environment variables and values aren't case-sensitive.</span></span> <span data-ttu-id="73481-125">Ve výchozím nastavení se v proměnných a hodnotách prostředí Linux rozlišují **velká a malá písmena** .</span><span class="sxs-lookup"><span data-stu-id="73481-125">Linux environment variables and values are **case-sensitive** by default.</span></span>

### <a name="create-environmentssample"></a><span data-ttu-id="73481-126">Vytvořit EnvironmentsSample</span><span class="sxs-lookup"><span data-stu-id="73481-126">Create EnvironmentsSample</span></span>

<span data-ttu-id="73481-127">[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) použitý v tomto dokumentu je založen na Razor projektu stránky s názvem *EnvironmentsSample*.</span><span class="sxs-lookup"><span data-stu-id="73481-127">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) used in this document is based on a Razor Pages project named *EnvironmentsSample*.</span></span>

<span data-ttu-id="73481-128">Následující kód vytvoří a spustí webovou aplikaci s názvem *EnvironmentsSample*:</span><span class="sxs-lookup"><span data-stu-id="73481-128">The following code creates and runs a web app named *EnvironmentsSample*:</span></span>

```bash
dotnet new webapp -o EnvironmentsSample
cd EnvironmentsSample
dotnet run --verbosity normal
```

<span data-ttu-id="73481-129">Po spuštění aplikace se zobrazí některé z následujících výstupů:</span><span class="sxs-lookup"><span data-stu-id="73481-129">When the app runs, it displays some of the following output:</span></span>

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

### <a name="development-and-launchsettingsjson"></a><span data-ttu-id="73481-130">Vývoj a launchSettings.jsna</span><span class="sxs-lookup"><span data-stu-id="73481-130">Development and launchSettings.json</span></span>

<span data-ttu-id="73481-131">Vývojové prostředí může povolit funkce, které by se neměly zveřejnit v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="73481-131">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="73481-132">Například šablony ASP.NET Core umožňují [stránku s výjimkou vývojáře](xref:fundamentals/error-handling#developer-exception-page) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="73481-132">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="73481-133">Prostředí pro vývoj místních počítačů lze nastavit v *Properties\launchSettings.jsv* souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="73481-133">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="73481-134">Hodnoty prostředí nastavené v *launchSettings.jspři* přepisování hodnot nastavených v prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="73481-134">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="73481-135">*launchSettings.jsv* souboru:</span><span class="sxs-lookup"><span data-stu-id="73481-135">The *launchSettings.json* file:</span></span>

* <span data-ttu-id="73481-136">Se používá pouze v místním vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="73481-136">Is only used on the local development machine.</span></span>
* <span data-ttu-id="73481-137">Není nasazen.</span><span class="sxs-lookup"><span data-stu-id="73481-137">Is not deployed.</span></span>
* <span data-ttu-id="73481-138">obsahuje nastavení profilu.</span><span class="sxs-lookup"><span data-stu-id="73481-138">contains profile settings.</span></span>

<span data-ttu-id="73481-139">Následující JSON zobrazuje *launchSettings.jsv* souboru pro ASP.NET Core webový projekt s názvem *EnvironmentsSample* vytvořený v aplikaci Visual Studio nebo `dotnet new` :</span><span class="sxs-lookup"><span data-stu-id="73481-139">The following JSON shows the *launchSettings.json* file for an ASP.NET Core web projected named *EnvironmentsSample* created with Visual Studio or `dotnet new`:</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettingsCopy.json)]

<span data-ttu-id="73481-140">Předchozí kód obsahuje dva profily:</span><span class="sxs-lookup"><span data-stu-id="73481-140">The preceding markup contains two profiles:</span></span>

* <span data-ttu-id="73481-141">`IIS Express`: Výchozí profil, který se používá při spuštění aplikace ze sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="73481-141">`IIS Express`: The default profile used when launching the app from Visual Studio.</span></span> <span data-ttu-id="73481-142">`"commandName"`Klíč má hodnotu `"IISExpress"` , proto je [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) webovým serverem.</span><span class="sxs-lookup"><span data-stu-id="73481-142">The `"commandName"` key has the value `"IISExpress"`, therefore, [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) is the web server.</span></span> <span data-ttu-id="73481-143">Můžete nastavit profil spuštění na projekt nebo jakýkoli jiný profil, který je součástí.</span><span class="sxs-lookup"><span data-stu-id="73481-143">You can set the launch profile to the project or any other profile included.</span></span> <span data-ttu-id="73481-144">Například na následujícím obrázku vyberte název projektu, který spustí [webový server Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="73481-144">For example, in the image below, selecting the project name launches the [Kestrel web server](xref:fundamentals/servers/kestrel).</span></span>

  ![IIS Express spustit v nabídce](environments/_static/iisx2.png)
* <span data-ttu-id="73481-146">`EnvironmentsSample`: Název profilu je název projektu.</span><span class="sxs-lookup"><span data-stu-id="73481-146">`EnvironmentsSample`: The profile name is the project name.</span></span> <span data-ttu-id="73481-147">Tento profil se ve výchozím nastavení používá při spouštění aplikace pomocí `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="73481-147">This profile is used by default when launching the app with `dotnet run`.</span></span>  <span data-ttu-id="73481-148">`"commandName"`Klíč má hodnotu `"Project"` , proto je spuštěn [webový server Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="73481-148">The `"commandName"` key has the value `"Project"`, therefore, the [Kestrel web server](xref:fundamentals/servers/kestrel) is launched.</span></span>

<span data-ttu-id="73481-149">Hodnota `commandName` může určovat webový server, který se má spustit.</span><span class="sxs-lookup"><span data-stu-id="73481-149">The value of `commandName` can specify the web server to launch.</span></span> <span data-ttu-id="73481-150">`commandName`může to být jedna z následujících:</span><span class="sxs-lookup"><span data-stu-id="73481-150">`commandName` can be any one of the following:</span></span>

* <span data-ttu-id="73481-151">`IISExpress`: Spustí IIS Express.</span><span class="sxs-lookup"><span data-stu-id="73481-151">`IISExpress` : Launches IIS Express.</span></span>
* <span data-ttu-id="73481-152">`IIS`: Nebyl spuštěn žádný webový server.</span><span class="sxs-lookup"><span data-stu-id="73481-152">`IIS` : No web server launched.</span></span> <span data-ttu-id="73481-153">Očekává se, že služba IIS bude k dispozici.</span><span class="sxs-lookup"><span data-stu-id="73481-153">IIS is expected to be available.</span></span>
* <span data-ttu-id="73481-154">`Project`: Spustí Kestrel.</span><span class="sxs-lookup"><span data-stu-id="73481-154">`Project` : Launches Kestrel.</span></span>

<span data-ttu-id="73481-155">Karta **ladění** vlastností projektu sady Visual Studio poskytuje grafické uživatelské rozhraní pro úpravu *launchSettings.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="73481-155">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file.</span></span> <span data-ttu-id="73481-156">Změny provedené v profilech projektu se neprojeví, dokud se webový server nerestartuje.</span><span class="sxs-lookup"><span data-stu-id="73481-156">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="73481-157">Kestrel se musí restartovat, aby bylo možné zjistit změny provedené ve svém prostředí.</span><span class="sxs-lookup"><span data-stu-id="73481-157">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

![Vlastnosti projektu nastavení proměnných prostředí](environments/_static/project-properties-debug.png)

<span data-ttu-id="73481-159">Následující *launchSettings.js* souboru obsahuje několik profilů:</span><span class="sxs-lookup"><span data-stu-id="73481-159">The following *launchSettings.json* file contains multiple profiles:</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettings.json)]

<span data-ttu-id="73481-160">Můžete vybrat profily:</span><span class="sxs-lookup"><span data-stu-id="73481-160">Profiles can be selected:</span></span>

* <span data-ttu-id="73481-161">Z uživatelského rozhraní sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="73481-161">From the Visual Studio UI.</span></span>
* <span data-ttu-id="73481-162">Pomocí [`dotnet run`](/dotnet/core/tools/dotnet-run) příkazu v příkazovém prostředí s `--launch-profile` možností nastavenou na název profilu.</span><span class="sxs-lookup"><span data-stu-id="73481-162">Using the [`dotnet run`](/dotnet/core/tools/dotnet-run) command in a command shell with the `--launch-profile` option set to the profile's name.</span></span> <span data-ttu-id="73481-163">*Tento přístup podporuje jenom Kestrel profily.*</span><span class="sxs-lookup"><span data-stu-id="73481-163">*This approach only supports Kestrel profiles.*</span></span>

  ```dotnetcli
  dotnet run --launch-profile "SampleApp"
  ```

> [!WARNING]
> <span data-ttu-id="73481-164">*launchSettings.js* by neměl ukládat tajné klíče.</span><span class="sxs-lookup"><span data-stu-id="73481-164">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="73481-165">[Nástroj Správce tajných](xref:security/app-secrets) klíčů je možné použít k ukládání tajných kódů pro místní vývoj.</span><span class="sxs-lookup"><span data-stu-id="73481-165">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="73481-166">Při použití [Visual Studio Code](https://code.visualstudio.com/)lze proměnné prostředí nastavit v souboru *. VSCode/launch.js* .</span><span class="sxs-lookup"><span data-stu-id="73481-166">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="73481-167">Následující příklad nastaví několik [hodnot konfigurace hostitele proměnné prostředí](xref:fundamentals/host/web-host#host-configuration-values):</span><span class="sxs-lookup"><span data-stu-id="73481-167">The following example sets several [Host configuration values environment variables](xref:fundamentals/host/web-host#host-configuration-values):</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/.vscode/launch.json?range=4-10,32-38)]

<span data-ttu-id="73481-168">Soubor *. VSCode/launch.js* je používán pouze Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="73481-168">The *.vscode/launch.json* file is only used by Visual Studio Code.</span></span>

### <a name="production"></a><span data-ttu-id="73481-169">Produkce</span><span class="sxs-lookup"><span data-stu-id="73481-169">Production</span></span>

<span data-ttu-id="73481-170">Provozní prostředí by mělo být nakonfigurované tak, aby maximalizovalo zabezpečení, [výkon](xref:performance/performance-best-practices)a odolnost aplikací.</span><span class="sxs-lookup"><span data-stu-id="73481-170">The production environment should be configured to maximize security, [performance](xref:performance/performance-best-practices), and application robustness.</span></span> <span data-ttu-id="73481-171">Mezi běžná nastavení, která se liší od vývoje, patří:</span><span class="sxs-lookup"><span data-stu-id="73481-171">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="73481-172">[Ukládání do mezipaměti](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="73481-172">[Caching](xref:performance/caching/memory).</span></span>
* <span data-ttu-id="73481-173">Prostředky na straně klienta jsou seskupené, minifikovaného a potenciálně poskytované z CDN.</span><span class="sxs-lookup"><span data-stu-id="73481-173">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="73481-174">Chybové stránky diagnostiky jsou zakázané.</span><span class="sxs-lookup"><span data-stu-id="73481-174">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="73481-175">Jsou povoleny popisné chybové stránky.</span><span class="sxs-lookup"><span data-stu-id="73481-175">Friendly error pages enabled.</span></span>
* <span data-ttu-id="73481-176">[Protokolování](xref:fundamentals/logging/index) a sledování výroby je povolené.</span><span class="sxs-lookup"><span data-stu-id="73481-176">Production [logging](xref:fundamentals/logging/index) and monitoring enabled.</span></span> <span data-ttu-id="73481-177">Například pomocí [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="73481-177">For example, using [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="73481-178">Nastavení prostředí</span><span class="sxs-lookup"><span data-stu-id="73481-178">Set the environment</span></span>

<span data-ttu-id="73481-179">Je často užitečné nastavit konkrétní prostředí pro testování pomocí proměnné prostředí nebo nastavení platformy.</span><span class="sxs-lookup"><span data-stu-id="73481-179">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="73481-180">Pokud prostředí není nastavené, nastaví se jako výchozí `Production` , což zakáže většinu funkcí ladění.</span><span class="sxs-lookup"><span data-stu-id="73481-180">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="73481-181">Metoda nastavení prostředí závisí na operačním systému.</span><span class="sxs-lookup"><span data-stu-id="73481-181">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="73481-182">Po sestavení hostitele určuje poslední nastavení prostředí načtené aplikací prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="73481-182">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="73481-183">Prostředí aplikace se nedá změnit, když je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="73481-183">The app's environment can't be changed while the app is running.</span></span>

<span data-ttu-id="73481-184">[Stránka about](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) z [ukázkového kódu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) zobrazí hodnotu `IWebHostEnvironment.EnvironmentName` .</span><span class="sxs-lookup"><span data-stu-id="73481-184">The [About page](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) from the [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) displays the value of `IWebHostEnvironment.EnvironmentName`.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="73481-185">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="73481-185">Azure App Service</span></span>

<span data-ttu-id="73481-186">Pokud chcete nastavit prostředí v [Azure App Service](https://azure.microsoft.com/services/app-service/), proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="73481-186">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="73481-187">Vyberte aplikaci z okna **App Services** .</span><span class="sxs-lookup"><span data-stu-id="73481-187">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="73481-188">Ve skupině **Nastavení** vyberte okno **Konfigurace** .</span><span class="sxs-lookup"><span data-stu-id="73481-188">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="73481-189">Na kartě **nastavení aplikace** vyberte možnost **nové nastavení aplikace**.</span><span class="sxs-lookup"><span data-stu-id="73481-189">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="73481-190">V okně **Přidat nebo upravit nastavení aplikace** zadejte `ASPNETCORE_ENVIRONMENT` **název**.</span><span class="sxs-lookup"><span data-stu-id="73481-190">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="73481-191">V poli **hodnota**zadejte prostředí (například `Staging` ).</span><span class="sxs-lookup"><span data-stu-id="73481-191">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="73481-192">Zaškrtněte políčko **nastavení slotu nasazení** , pokud chcete, aby nastavení prostředí zůstalo v aktuální pozici, když jsou sloty nasazení prohozeny.</span><span class="sxs-lookup"><span data-stu-id="73481-192">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="73481-193">Další informace najdete v tématu [Nastavení přípravného prostředí v Azure App Service](/azure/app-service/web-sites-staged-publishing) v dokumentaci k Azure.</span><span class="sxs-lookup"><span data-stu-id="73481-193">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="73481-194">Výběrem **OK** zavřete okno **Přidat/upravit nastavení aplikace** .</span><span class="sxs-lookup"><span data-stu-id="73481-194">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="73481-195">V horní části okna **Konfigurace** vyberte **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="73481-195">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="73481-196">Azure App Service automaticky restartuje aplikaci po přidání, změně nebo odstranění nastavení aplikace v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="73481-196">Azure App Service automatically restarts the app after an app setting is added, changed, or deleted in the Azure portal.</span></span>

### <a name="windows"></a><span data-ttu-id="73481-197">Windows</span><span class="sxs-lookup"><span data-stu-id="73481-197">Windows</span></span>

<span data-ttu-id="73481-198">Hodnoty prostředí v *launchSettings.jspři* přepisování hodnot nastavených v prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="73481-198">Environment values in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="73481-199">K nastavení `ASPNETCORE_ENVIRONMENT` pro aktuální relaci při zahájení aplikace pomocí [spuštění dotnet](/dotnet/core/tools/dotnet-run)se použijí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="73481-199">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="73481-200">**Příkazový řádek**</span><span class="sxs-lookup"><span data-stu-id="73481-200">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Staging
dotnet run --no-launch-profile
```

<span data-ttu-id="73481-201">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="73481-201">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Staging"
dotnet run --no-launch-profile
```

<span data-ttu-id="73481-202">Předchozí příkaz nastaví `ASPNETCORE_ENVIRONMENT` pouze procesy spouštěné z tohoto příkazového okna.</span><span class="sxs-lookup"><span data-stu-id="73481-202">The preceding command sets `ASPNETCORE_ENVIRONMENT` only for processes launched from that command window.</span></span>

<span data-ttu-id="73481-203">K nastavení hodnoty globálně ve Windows použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="73481-203">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="73481-204">Otevřete **ovládací panel** > **System** > **Upřesnit systémová nastavení** a přidejte nebo upravte `ASPNETCORE_ENVIRONMENT` hodnotu:</span><span class="sxs-lookup"><span data-stu-id="73481-204">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Rozšířené vlastnosti systému](environments/_static/systemsetting_environment.png)

  ![Proměnná prostředí ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="73481-207">Otevřete příkazový řádek pro správu a použijte `setx` příkaz nebo otevřete příkazový řádek prostředí PowerShell pro správu a použijte příkaz `[Environment]::SetEnvironmentVariable` :</span><span class="sxs-lookup"><span data-stu-id="73481-207">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="73481-208">**Příkazový řádek**</span><span class="sxs-lookup"><span data-stu-id="73481-208">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Staging /M
  ```

  <span data-ttu-id="73481-209">`/M`Přepínač označuje nastavení proměnné prostředí na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="73481-209">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="73481-210">Pokud `/M` přepínač není použit, je pro uživatelský účet nastavena proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="73481-210">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="73481-211">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="73481-211">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Staging", "Machine")
  ```

  <span data-ttu-id="73481-212">`Machine`Hodnota možnosti určuje nastavení proměnné prostředí na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="73481-212">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="73481-213">Pokud je hodnota možnosti změněna na `User` , je pro uživatelský účet nastavena proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="73481-213">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="73481-214">Pokud `ASPNETCORE_ENVIRONMENT` je proměnná prostředí nastavena globálně, projeví se `dotnet run` v jakémkoli příkazovém okně otevřeném po nastavení hodnoty.</span><span class="sxs-lookup"><span data-stu-id="73481-214">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span> <span data-ttu-id="73481-215">Hodnoty prostředí v *launchSettings.jspři* přepisování hodnot nastavených v prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="73481-215">Environment values in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="73481-216">**web.config**</span><span class="sxs-lookup"><span data-stu-id="73481-216">**web.config**</span></span>

<span data-ttu-id="73481-217">Chcete-li nastavit `ASPNETCORE_ENVIRONMENT` proměnnou prostředí pomocí *web.config*, přečtěte si část *nastavení proměnných prostředí* v tématu <xref:host-and-deploy/aspnet-core-module#setting-environment-variables> .</span><span class="sxs-lookup"><span data-stu-id="73481-217">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="73481-218">**Projektový soubor nebo profil publikování**</span><span class="sxs-lookup"><span data-stu-id="73481-218">**Project file or publish profile**</span></span>

<span data-ttu-id="73481-219">**Pro nasazení služby Windows IIS:** Zahrňte `<EnvironmentName>` vlastnost do [profilu publikování (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="73481-219">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="73481-220">Tento přístup nastaví prostředí v *web.config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="73481-220">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="73481-221">**Pro fond aplikací služby IIS**</span><span class="sxs-lookup"><span data-stu-id="73481-221">**Per IIS Application Pool**</span></span>

<span data-ttu-id="73481-222">Pokud chcete nastavit `ASPNETCORE_ENVIRONMENT` proměnnou prostředí pro aplikaci spuštěnou v izolovaném fondu aplikací (podporovaném ve službě IIS 10,0 nebo novější), přečtěte si část *AppCmd.exe příkazu* v tématu [ &lt; environmentVariables &gt; Variables (prostředí](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) ).</span><span class="sxs-lookup"><span data-stu-id="73481-222">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="73481-223">Když `ASPNETCORE_ENVIRONMENT` je pro fond aplikací nastavená proměnná prostředí, přepíše její hodnota nastavení na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="73481-223">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

<span data-ttu-id="73481-224">Při hostování aplikace ve službě IIS a přidání nebo změny `ASPNETCORE_ENVIRONMENT` proměnné prostředí použijte libovolný z následujících přístupů, aby byla nová hodnota vyzvednuta aplikacemi:</span><span class="sxs-lookup"><span data-stu-id="73481-224">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>

* <span data-ttu-id="73481-225">Spustí `net stop was /y` a pak `net start w3svc` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="73481-225">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
* <span data-ttu-id="73481-226">Restartujte server.</span><span class="sxs-lookup"><span data-stu-id="73481-226">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="73481-227">macOS</span><span class="sxs-lookup"><span data-stu-id="73481-227">macOS</span></span>

<span data-ttu-id="73481-228">Nastavení aktuálního prostředí pro macOS se dá při spuštění aplikace provést na řádku:</span><span class="sxs-lookup"><span data-stu-id="73481-228">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Staging dotnet run
```

<span data-ttu-id="73481-229">Případně můžete nastavit prostředí pomocí `export` před spuštěním aplikace:</span><span class="sxs-lookup"><span data-stu-id="73481-229">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

<span data-ttu-id="73481-230">Proměnné prostředí na úrovni počítače jsou nastaveny v souboru *. bashrc* nebo *. bash_profile* .</span><span class="sxs-lookup"><span data-stu-id="73481-230">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="73481-231">Upravte soubor pomocí libovolného textového editoru.</span><span class="sxs-lookup"><span data-stu-id="73481-231">Edit the file using any text editor.</span></span> <span data-ttu-id="73481-232">Přidejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="73481-232">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

#### <a name="linux"></a><span data-ttu-id="73481-233">Linux</span><span class="sxs-lookup"><span data-stu-id="73481-233">Linux</span></span>

<span data-ttu-id="73481-234">U distribucí systému Linux použijte `export` příkaz na příkazovém řádku pro nastavení proměnné založené na relaci a soubor *bash_profile* pro nastavení prostředí na úrovni počítače.</span><span class="sxs-lookup"><span data-stu-id="73481-234">For Linux distributions, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="73481-235">Nastavení prostředí v kódu</span><span class="sxs-lookup"><span data-stu-id="73481-235">Set the environment in code</span></span>

<span data-ttu-id="73481-236">Zavolejte <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="73481-236">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="73481-237">Viz třída <xref:fundamentals/host/generic-host#environmentname>.</span><span class="sxs-lookup"><span data-stu-id="73481-237">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="73481-238">Konfigurace podle prostředí</span><span class="sxs-lookup"><span data-stu-id="73481-238">Configuration by environment</span></span>

<span data-ttu-id="73481-239">Pokud chcete načíst konfiguraci podle prostředí, přečtěte si téma <xref:fundamentals/configuration/index#json-configuration-provider> .</span><span class="sxs-lookup"><span data-stu-id="73481-239">To load configuration by environment, see <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="73481-240">Třída a metody spouštění založené na prostředí</span><span class="sxs-lookup"><span data-stu-id="73481-240">Environment-based Startup class and methods</span></span>

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="73481-241">Vložení IWebHostEnvironment do spouštěcí třídy</span><span class="sxs-lookup"><span data-stu-id="73481-241">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="73481-242">Vložit <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do `Startup` konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="73481-242">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="73481-243">Tento přístup je užitečný, když aplikace vyžaduje konfiguraci `Startup` jenom pro několik prostředí s minimálními rozdíly v kódu pro každé prostředí.</span><span class="sxs-lookup"><span data-stu-id="73481-243">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="73481-244">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="73481-244">In the following example:</span></span>

* <span data-ttu-id="73481-245">Prostředí se uchovává v `_env` poli.</span><span class="sxs-lookup"><span data-stu-id="73481-245">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="73481-246">`_env`se používá v `ConfigureServices` `Configure` systémech a k aplikování konfigurace spuštění na základě prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="73481-246">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupInject.cs?name=snippet&highlight=3-11)]

### <a name="startup-class-conventions"></a><span data-ttu-id="73481-247">Konvence třídy spouštění</span><span class="sxs-lookup"><span data-stu-id="73481-247">Startup class conventions</span></span>

<span data-ttu-id="73481-248">Při spuštění aplikace ASP.NET Core spustí [spouštěcí třída](xref:fundamentals/startup) aplikaci.</span><span class="sxs-lookup"><span data-stu-id="73481-248">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="73481-249">Aplikace může definovat více `Startup` tříd pro různá prostředí.</span><span class="sxs-lookup"><span data-stu-id="73481-249">The app can define multiple `Startup` classes for different environments.</span></span> <span data-ttu-id="73481-250">Příslušná `Startup` Třída je vybrána za běhu.</span><span class="sxs-lookup"><span data-stu-id="73481-250">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="73481-251">Určuje prioritu třídy, jejíž přípona názvu odpovídá aktuálnímu prostředí.</span><span class="sxs-lookup"><span data-stu-id="73481-251">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="73481-252">Pokud `Startup{EnvironmentName}` není nalezena shodná třída, `Startup` je použita třída.</span><span class="sxs-lookup"><span data-stu-id="73481-252">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="73481-253">Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly v kódu pro každé prostředí.</span><span class="sxs-lookup"><span data-stu-id="73481-253">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span> <span data-ttu-id="73481-254">Typické aplikace nebudou tento přístup potřebovat.</span><span class="sxs-lookup"><span data-stu-id="73481-254">Typical apps will not need this approach.</span></span>

<span data-ttu-id="73481-255">Chcete-li implementovat třídy založené na prostředí `Startup` , vytvořte `Startup{EnvironmentName}` třídy a záložní `Startup` třídu:</span><span class="sxs-lookup"><span data-stu-id="73481-255">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` classes and a fallback `Startup` class:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupClassConventions.cs?name=snippet)]

<span data-ttu-id="73481-256">Použijte přetížení [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) , které přijímá název sestavení:</span><span class="sxs-lookup"><span data-stu-id="73481-256">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Program.cs?name=snippet)]

### <a name="startup-method-conventions"></a><span data-ttu-id="73481-257">Konvence metody spuštění</span><span class="sxs-lookup"><span data-stu-id="73481-257">Startup method conventions</span></span>

<span data-ttu-id="73481-258">[Nakonfigurujte](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) a [ConfigureServiceste](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) podporu pro konkrétní verze formuláře a prostředí `Configure<EnvironmentName>` `Configure<EnvironmentName>Services` .</span><span class="sxs-lookup"><span data-stu-id="73481-258">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="73481-259">Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly v kódu na prostředí:</span><span class="sxs-lookup"><span data-stu-id="73481-259">This approach is useful when the app requires configuring startup for several environments with many code differences per environment:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupMethodConventions.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="73481-260">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="73481-260">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* <xref:blazor/fundamentals/environments>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="73481-261">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="73481-261">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="73481-262">ASP.NET Core konfiguruje chování aplikace na základě běhového prostředí pomocí proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="73481-262">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="73481-263">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="73481-263">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="73481-264">Prostředí</span><span class="sxs-lookup"><span data-stu-id="73481-264">Environments</span></span>

<span data-ttu-id="73481-265">ASP.NET Core přečte proměnnou prostředí `ASPNETCORE_ENVIRONMENT` při spuštění aplikace a uloží hodnotu do [IHostingEnvironment. Environment](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="73481-265">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="73481-266">`ASPNETCORE_ENVIRONMENT`lze nastavit na libovolnou hodnotu, ale rozhraní poskytuje tři hodnoty:</span><span class="sxs-lookup"><span data-stu-id="73481-266">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="73481-267"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production>výchozí</span><span class="sxs-lookup"><span data-stu-id="73481-267"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="73481-268">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="73481-268">The preceding code:</span></span>

* <span data-ttu-id="73481-269">Volá [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) , pokud `ASPNETCORE_ENVIRONMENT` je nastaven na `Development` .</span><span class="sxs-lookup"><span data-stu-id="73481-269">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="73481-270">Volá [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) , pokud je hodnota `ASPNETCORE_ENVIRONMENT` nastavená na jednu z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="73481-270">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="73481-271">[Pomocná značka prostředí](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) používá hodnotu `IHostingEnvironment.EnvironmentName` pro zahrnutí nebo vyloučení značek v elementu:</span><span class="sxs-lookup"><span data-stu-id="73481-271">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="73481-272">V systému Windows a macOS se v proměnných a hodnotách prostředí nerozlišují velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="73481-272">On Windows and macOS, environment variables and values aren't case-sensitive.</span></span> <span data-ttu-id="73481-273">Ve výchozím nastavení se v proměnných a hodnotách prostředí Linux rozlišují velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="73481-273">Linux environment variables and values are case-sensitive by default.</span></span>

### <a name="development"></a><span data-ttu-id="73481-274">Vývoj</span><span class="sxs-lookup"><span data-stu-id="73481-274">Development</span></span>

<span data-ttu-id="73481-275">Vývojové prostředí může povolit funkce, které by se neměly zveřejnit v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="73481-275">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="73481-276">Například šablony ASP.NET Core umožňují [stránku s výjimkou vývojáře](xref:fundamentals/error-handling#developer-exception-page) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="73481-276">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="73481-277">Prostředí pro vývoj místních počítačů lze nastavit v *Properties\launchSettings.jsv* souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="73481-277">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="73481-278">Hodnoty prostředí nastavené v *launchSettings.jspři* přepisování hodnot nastavených v prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="73481-278">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="73481-279">Následující JSON zobrazuje tři profily z *launchSettings.jsv* souboru:</span><span class="sxs-lookup"><span data-stu-id="73481-279">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

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
> <span data-ttu-id="73481-280">`applicationUrl`Vlastnost v *launchSettings.jsv* může určovat seznam adres URL serveru.</span><span class="sxs-lookup"><span data-stu-id="73481-280">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="73481-281">Použijte středník mezi adresami URL v seznamu:</span><span class="sxs-lookup"><span data-stu-id="73481-281">Use a semicolon between the URLs in the list:</span></span>
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

<span data-ttu-id="73481-282">Když se aplikace spustí pomocí [příkazu dotnet](/dotnet/core/tools/dotnet-run), použije se první profil `"commandName": "Project"` .</span><span class="sxs-lookup"><span data-stu-id="73481-282">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="73481-283">Hodnota `commandName` Určuje webový server, který se má spustit.</span><span class="sxs-lookup"><span data-stu-id="73481-283">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="73481-284">`commandName`může to být jedna z následujících:</span><span class="sxs-lookup"><span data-stu-id="73481-284">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="73481-285">`Project`(která spouští Kestrel)</span><span class="sxs-lookup"><span data-stu-id="73481-285">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="73481-286">Když se aplikace spustí s použitím [příkazu dotnet](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="73481-286">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="73481-287">*launchSettings.jszapnuto* je přečteno, pokud je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="73481-287">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="73481-288">`environmentVariables`nastavení v *launchSettings.jspři* přepisu proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="73481-288">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="73481-289">Zobrazí se hostující prostředí.</span><span class="sxs-lookup"><span data-stu-id="73481-289">The hosting environment is displayed.</span></span>

<span data-ttu-id="73481-290">Následující výstup ukazuje aplikaci spuštěnou pomocí [příkazu dotnet](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="73481-290">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="73481-291">Karta **ladění** vlastností projektu sady Visual Studio poskytuje grafické uživatelské rozhraní pro úpravu *launchSettings.jsv* souboru:</span><span class="sxs-lookup"><span data-stu-id="73481-291">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Vlastnosti projektu nastavení proměnných prostředí](environments/_static/project-properties-debug.png)

<span data-ttu-id="73481-293">Změny provedené v profilech projektu se neprojeví, dokud se webový server nerestartuje.</span><span class="sxs-lookup"><span data-stu-id="73481-293">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="73481-294">Kestrel se musí restartovat, aby bylo možné zjistit změny provedené ve svém prostředí.</span><span class="sxs-lookup"><span data-stu-id="73481-294">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="73481-295">*launchSettings.js* by neměl ukládat tajné klíče.</span><span class="sxs-lookup"><span data-stu-id="73481-295">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="73481-296">[Nástroj Správce tajných](xref:security/app-secrets) klíčů je možné použít k ukládání tajných kódů pro místní vývoj.</span><span class="sxs-lookup"><span data-stu-id="73481-296">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="73481-297">Při použití [Visual Studio Code](https://code.visualstudio.com/)lze proměnné prostředí nastavit v souboru *. VSCode/launch.js* .</span><span class="sxs-lookup"><span data-stu-id="73481-297">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="73481-298">Následující příklad nastaví prostředí na `Development` :</span><span class="sxs-lookup"><span data-stu-id="73481-298">The following example sets the environment to `Development`:</span></span>

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

<span data-ttu-id="73481-299">A *. VSCode/launch.js* v souboru v projektu se při spouštění aplikace `dotnet run` stejným způsobem jako *vlastnosti/launchSettings.js*nenačte.</span><span class="sxs-lookup"><span data-stu-id="73481-299">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="73481-300">Při spouštění aplikace ve vývoji, která nemá *launchSettings.jsv* souboru, buď nastavte prostředí s proměnnou prostředí nebo argumentem příkazového řádku pro `dotnet run` příkaz.</span><span class="sxs-lookup"><span data-stu-id="73481-300">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="73481-301">Produkce</span><span class="sxs-lookup"><span data-stu-id="73481-301">Production</span></span>

<span data-ttu-id="73481-302">Provozní prostředí by mělo být nakonfigurované tak, aby maximalizovalo zabezpečení, výkon a odolnost aplikací.</span><span class="sxs-lookup"><span data-stu-id="73481-302">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="73481-303">Mezi běžná nastavení, která se liší od vývoje, patří:</span><span class="sxs-lookup"><span data-stu-id="73481-303">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="73481-304">Vyrovnávací.</span><span class="sxs-lookup"><span data-stu-id="73481-304">Caching.</span></span>
* <span data-ttu-id="73481-305">Prostředky na straně klienta jsou seskupené, minifikovaného a potenciálně poskytované z CDN.</span><span class="sxs-lookup"><span data-stu-id="73481-305">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="73481-306">Chybové stránky diagnostiky jsou zakázané.</span><span class="sxs-lookup"><span data-stu-id="73481-306">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="73481-307">Jsou povoleny popisné chybové stránky.</span><span class="sxs-lookup"><span data-stu-id="73481-307">Friendly error pages enabled.</span></span>
* <span data-ttu-id="73481-308">Protokolování a sledování výroby je povolené.</span><span class="sxs-lookup"><span data-stu-id="73481-308">Production logging and monitoring enabled.</span></span> <span data-ttu-id="73481-309">Například [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="73481-309">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="73481-310">Nastavení prostředí</span><span class="sxs-lookup"><span data-stu-id="73481-310">Set the environment</span></span>

<span data-ttu-id="73481-311">Je často užitečné nastavit konkrétní prostředí pro testování pomocí proměnné prostředí nebo nastavení platformy.</span><span class="sxs-lookup"><span data-stu-id="73481-311">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="73481-312">Pokud prostředí není nastavené, nastaví se jako výchozí `Production` , což zakáže většinu funkcí ladění.</span><span class="sxs-lookup"><span data-stu-id="73481-312">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="73481-313">Metoda nastavení prostředí závisí na operačním systému.</span><span class="sxs-lookup"><span data-stu-id="73481-313">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="73481-314">Po sestavení hostitele určuje poslední nastavení prostředí načtené aplikací prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="73481-314">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="73481-315">Prostředí aplikace se nedá změnit, když je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="73481-315">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="73481-316">Nastavení proměnné prostředí nebo platformy</span><span class="sxs-lookup"><span data-stu-id="73481-316">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="73481-317">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="73481-317">Azure App Service</span></span>

<span data-ttu-id="73481-318">Pokud chcete nastavit prostředí v [Azure App Service](https://azure.microsoft.com/services/app-service/), proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="73481-318">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="73481-319">Vyberte aplikaci z okna **App Services** .</span><span class="sxs-lookup"><span data-stu-id="73481-319">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="73481-320">Ve skupině **Nastavení** vyberte okno **Konfigurace** .</span><span class="sxs-lookup"><span data-stu-id="73481-320">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="73481-321">Na kartě **nastavení aplikace** vyberte možnost **nové nastavení aplikace**.</span><span class="sxs-lookup"><span data-stu-id="73481-321">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="73481-322">V okně **Přidat nebo upravit nastavení aplikace** zadejte `ASPNETCORE_ENVIRONMENT` **název**.</span><span class="sxs-lookup"><span data-stu-id="73481-322">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="73481-323">V poli **hodnota**zadejte prostředí (například `Staging` ).</span><span class="sxs-lookup"><span data-stu-id="73481-323">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="73481-324">Zaškrtněte políčko **nastavení slotu nasazení** , pokud chcete, aby nastavení prostředí zůstalo v aktuální pozici, když jsou sloty nasazení prohozeny.</span><span class="sxs-lookup"><span data-stu-id="73481-324">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="73481-325">Další informace najdete v tématu [Nastavení přípravného prostředí v Azure App Service](/azure/app-service/web-sites-staged-publishing) v dokumentaci k Azure.</span><span class="sxs-lookup"><span data-stu-id="73481-325">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="73481-326">Výběrem **OK** zavřete okno **Přidat/upravit nastavení aplikace** .</span><span class="sxs-lookup"><span data-stu-id="73481-326">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="73481-327">V horní části okna **Konfigurace** vyberte **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="73481-327">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="73481-328">Azure App Service automaticky restartuje aplikaci po přidání, změně nebo odstranění nastavení aplikace (proměnná prostředí) v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="73481-328">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="73481-329">Windows</span><span class="sxs-lookup"><span data-stu-id="73481-329">Windows</span></span>

<span data-ttu-id="73481-330">K nastavení `ASPNETCORE_ENVIRONMENT` pro aktuální relaci při zahájení aplikace pomocí [spuštění dotnet](/dotnet/core/tools/dotnet-run)se použijí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="73481-330">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="73481-331">**Příkazový řádek**</span><span class="sxs-lookup"><span data-stu-id="73481-331">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="73481-332">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="73481-332">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="73481-333">Tyto příkazy se projeví pouze pro aktuální okno.</span><span class="sxs-lookup"><span data-stu-id="73481-333">These commands only take effect for the current window.</span></span> <span data-ttu-id="73481-334">Po zavření okna se `ASPNETCORE_ENVIRONMENT` nastavení vrátí k výchozímu nastavení nebo hodnotě počítače.</span><span class="sxs-lookup"><span data-stu-id="73481-334">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="73481-335">K nastavení hodnoty globálně ve Windows použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="73481-335">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="73481-336">Otevřete **ovládací panel** > **System** > **Upřesnit systémová nastavení** a přidejte nebo upravte `ASPNETCORE_ENVIRONMENT` hodnotu:</span><span class="sxs-lookup"><span data-stu-id="73481-336">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Rozšířené vlastnosti systému](environments/_static/systemsetting_environment.png)

  ![Proměnná prostředí ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="73481-339">Otevřete příkazový řádek pro správu a použijte `setx` příkaz nebo otevřete příkazový řádek prostředí PowerShell pro správu a použijte příkaz `[Environment]::SetEnvironmentVariable` :</span><span class="sxs-lookup"><span data-stu-id="73481-339">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="73481-340">**Příkazový řádek**</span><span class="sxs-lookup"><span data-stu-id="73481-340">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="73481-341">`/M`Přepínač označuje nastavení proměnné prostředí na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="73481-341">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="73481-342">Pokud `/M` přepínač není použit, je pro uživatelský účet nastavena proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="73481-342">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="73481-343">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="73481-343">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="73481-344">`Machine`Hodnota možnosti určuje nastavení proměnné prostředí na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="73481-344">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="73481-345">Pokud je hodnota možnosti změněna na `User` , je pro uživatelský účet nastavena proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="73481-345">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="73481-346">Pokud `ASPNETCORE_ENVIRONMENT` je proměnná prostředí nastavena globálně, projeví se `dotnet run` v jakémkoli příkazovém okně otevřeném po nastavení hodnoty.</span><span class="sxs-lookup"><span data-stu-id="73481-346">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="73481-347">**web.config**</span><span class="sxs-lookup"><span data-stu-id="73481-347">**web.config**</span></span>

<span data-ttu-id="73481-348">Chcete-li nastavit `ASPNETCORE_ENVIRONMENT` proměnnou prostředí pomocí *web.config*, přečtěte si část *nastavení proměnných prostředí* v tématu <xref:host-and-deploy/aspnet-core-module#setting-environment-variables> .</span><span class="sxs-lookup"><span data-stu-id="73481-348">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="73481-349">**Projektový soubor nebo profil publikování**</span><span class="sxs-lookup"><span data-stu-id="73481-349">**Project file or publish profile**</span></span>

<span data-ttu-id="73481-350">**Pro nasazení služby Windows IIS:** Zahrňte `<EnvironmentName>` vlastnost do [profilu publikování (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="73481-350">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="73481-351">Tento přístup nastaví prostředí v *web.config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="73481-351">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="73481-352">**Pro fond aplikací služby IIS**</span><span class="sxs-lookup"><span data-stu-id="73481-352">**Per IIS Application Pool**</span></span>

<span data-ttu-id="73481-353">Pokud chcete nastavit `ASPNETCORE_ENVIRONMENT` proměnnou prostředí pro aplikaci spuštěnou v izolovaném fondu aplikací (podporovaném ve službě IIS 10,0 nebo novější), přečtěte si část *AppCmd.exe příkazu* v tématu [ &lt; environmentVariables &gt; Variables (prostředí](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) ).</span><span class="sxs-lookup"><span data-stu-id="73481-353">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="73481-354">Když `ASPNETCORE_ENVIRONMENT` je pro fond aplikací nastavená proměnná prostředí, přepíše její hodnota nastavení na úrovni systému.</span><span class="sxs-lookup"><span data-stu-id="73481-354">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="73481-355">Při hostování aplikace ve službě IIS a přidání nebo změny `ASPNETCORE_ENVIRONMENT` proměnné prostředí použijte libovolný z následujících přístupů, aby byla nová hodnota vyzvednuta aplikacemi:</span><span class="sxs-lookup"><span data-stu-id="73481-355">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="73481-356">Spustí `net stop was /y` a pak `net start w3svc` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="73481-356">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="73481-357">Restartujte server.</span><span class="sxs-lookup"><span data-stu-id="73481-357">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="73481-358">macOS</span><span class="sxs-lookup"><span data-stu-id="73481-358">macOS</span></span>

<span data-ttu-id="73481-359">Nastavení aktuálního prostředí pro macOS se dá při spuštění aplikace provést na řádku:</span><span class="sxs-lookup"><span data-stu-id="73481-359">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="73481-360">Případně můžete nastavit prostředí pomocí `export` před spuštěním aplikace:</span><span class="sxs-lookup"><span data-stu-id="73481-360">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="73481-361">Proměnné prostředí na úrovni počítače jsou nastaveny v souboru *. bashrc* nebo *. bash_profile* .</span><span class="sxs-lookup"><span data-stu-id="73481-361">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="73481-362">Upravte soubor pomocí libovolného textového editoru.</span><span class="sxs-lookup"><span data-stu-id="73481-362">Edit the file using any text editor.</span></span> <span data-ttu-id="73481-363">Přidejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="73481-363">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="73481-364">Linux</span><span class="sxs-lookup"><span data-stu-id="73481-364">Linux</span></span>

<span data-ttu-id="73481-365">U distribucí systému Linux použijte `export` příkaz na příkazovém řádku pro nastavení proměnné založené na relaci a soubor *bash_profile* pro nastavení prostředí na úrovni počítače.</span><span class="sxs-lookup"><span data-stu-id="73481-365">For Linux distributions, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="73481-366">Nastavení prostředí v kódu</span><span class="sxs-lookup"><span data-stu-id="73481-366">Set the environment in code</span></span>

<span data-ttu-id="73481-367">Zavolejte <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="73481-367">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="73481-368">Viz třída <xref:fundamentals/host/web-host#environment>.</span><span class="sxs-lookup"><span data-stu-id="73481-368">See <xref:fundamentals/host/web-host#environment>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="73481-369">Konfigurace podle prostředí</span><span class="sxs-lookup"><span data-stu-id="73481-369">Configuration by environment</span></span>

<span data-ttu-id="73481-370">K načtení konfigurace podle prostředí doporučujeme:</span><span class="sxs-lookup"><span data-stu-id="73481-370">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="73481-371">soubory *appSettings* (*appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="73481-371">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="73481-372">Viz třída <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="73481-372">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="73481-373">Proměnné prostředí (nastavené v každém systému, ve kterém je aplikace hostovaná).</span><span class="sxs-lookup"><span data-stu-id="73481-373">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="73481-374">Viz <xref:fundamentals/host/web-host#environment> a <xref:security/app-secrets#environment-variables> .</span><span class="sxs-lookup"><span data-stu-id="73481-374">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="73481-375">Správce tajných klíčů (jenom ve vývojovém prostředí).</span><span class="sxs-lookup"><span data-stu-id="73481-375">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="73481-376">Viz třída <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="73481-376">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="73481-377">Třída a metody spouštění založené na prostředí</span><span class="sxs-lookup"><span data-stu-id="73481-377">Environment-based Startup class and methods</span></span>

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="73481-378">Vložení IHostingEnvironment do Startup.Configurovat</span><span class="sxs-lookup"><span data-stu-id="73481-378">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="73481-379">Vložit <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> do `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="73481-379">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="73481-380">Tento přístup je užitečný, když aplikace vyžaduje jenom konfiguraci `Startup.Configure` jenom pro několik prostředí s minimálními rozdíly v kódu pro každé prostředí.</span><span class="sxs-lookup"><span data-stu-id="73481-380">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

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

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="73481-381">Vložení IHostingEnvironment do spouštěcí třídy</span><span class="sxs-lookup"><span data-stu-id="73481-381">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="73481-382">Vložení <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> do `Startup` konstruktoru a přiřazení služby k poli pro použití v rámci `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="73481-382">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="73481-383">Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spouštění jenom pro několik prostředí s minimálními rozdíly v kódu pro každé prostředí.</span><span class="sxs-lookup"><span data-stu-id="73481-383">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="73481-384">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="73481-384">In the following example:</span></span>

* <span data-ttu-id="73481-385">Prostředí se uchovává v `_env` poli.</span><span class="sxs-lookup"><span data-stu-id="73481-385">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="73481-386">`_env`se používá v `ConfigureServices` `Configure` systémech a k aplikování konfigurace spuštění na základě prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="73481-386">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

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

### <a name="startup-class-conventions"></a><span data-ttu-id="73481-387">Konvence třídy spouštění</span><span class="sxs-lookup"><span data-stu-id="73481-387">Startup class conventions</span></span>

<span data-ttu-id="73481-388">Při spuštění aplikace ASP.NET Core spustí [spouštěcí třída](xref:fundamentals/startup) aplikaci.</span><span class="sxs-lookup"><span data-stu-id="73481-388">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="73481-389">Aplikace může definovat samostatné `Startup` třídy pro různá prostředí (například `StartupDevelopment` ).</span><span class="sxs-lookup"><span data-stu-id="73481-389">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="73481-390">Příslušná `Startup` Třída je vybrána za běhu.</span><span class="sxs-lookup"><span data-stu-id="73481-390">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="73481-391">Určuje prioritu třídy, jejíž přípona názvu odpovídá aktuálnímu prostředí.</span><span class="sxs-lookup"><span data-stu-id="73481-391">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="73481-392">Pokud `Startup{EnvironmentName}` není nalezena shodná třída, `Startup` je použita třída.</span><span class="sxs-lookup"><span data-stu-id="73481-392">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="73481-393">Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly v kódu pro každé prostředí.</span><span class="sxs-lookup"><span data-stu-id="73481-393">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="73481-394">Chcete-li implementovat třídy založené na prostředí `Startup` , vytvořte `Startup{EnvironmentName}` třídu pro každé používané prostředí a záložní `Startup` třídu:</span><span class="sxs-lookup"><span data-stu-id="73481-394">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

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

<span data-ttu-id="73481-395">Použijte přetížení [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) , které přijímá název sestavení:</span><span class="sxs-lookup"><span data-stu-id="73481-395">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

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

### <a name="startup-method-conventions"></a><span data-ttu-id="73481-396">Konvence metody spuštění</span><span class="sxs-lookup"><span data-stu-id="73481-396">Startup method conventions</span></span>

<span data-ttu-id="73481-397">[Nakonfigurujte](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) a [ConfigureServiceste](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) podporu pro konkrétní verze formuláře a prostředí `Configure<EnvironmentName>` `Configure<EnvironmentName>Services` .</span><span class="sxs-lookup"><span data-stu-id="73481-397">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="73481-398">Tento přístup je užitečný, když aplikace vyžaduje konfiguraci spuštění pro několik prostředí s mnoha rozdíly v kódu pro každé prostředí.</span><span class="sxs-lookup"><span data-stu-id="73481-398">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="73481-399">Další materiály</span><span class="sxs-lookup"><span data-stu-id="73481-399">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
