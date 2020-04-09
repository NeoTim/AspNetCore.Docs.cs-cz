---
title: Nasazení aplikací ASP.NET Core do služby Azure App Service
author: bradygaster
description: Tento článek obsahuje odkazy na hostitele Azure a nasadit prostředky.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/16/2019
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: ba9671f68a0faf99ff5232a6d5dd132d0a1d5ac5
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665142"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a><span data-ttu-id="e9efd-103">Nasazení aplikací ASP.NET Core do služby Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e9efd-103">Deploy ASP.NET Core apps to Azure App Service</span></span>

<span data-ttu-id="e9efd-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) je [platforma pro cloud computing společnosti Microsoft](https://azure.microsoft.com/) pro hostování webových aplikací, včetně ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e9efd-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span>

## <a name="useful-resources"></a><span data-ttu-id="e9efd-105">Užitečné materiály</span><span class="sxs-lookup"><span data-stu-id="e9efd-105">Useful resources</span></span>

<span data-ttu-id="e9efd-106">[Dokumentace ke službě App Service](/azure/app-service/) je domovpro dokumentaci, kurzy, ukázky, návody a další prostředky pro Azure Apps.</span><span class="sxs-lookup"><span data-stu-id="e9efd-106">[App Service Documentation](/azure/app-service/) is the home for Azure Apps documentation, tutorials, samples, how-to guides, and other resources.</span></span> <span data-ttu-id="e9efd-107">Dva pozoruhodné výukové programy, které se týkající se hostování ASP.NET aplikace Core jsou:</span><span class="sxs-lookup"><span data-stu-id="e9efd-107">Two notable tutorials that pertain to hosting ASP.NET Core apps are:</span></span>

[<span data-ttu-id="e9efd-108">Vytvoření webové aplikace ASP.NET Core v Azure</span><span class="sxs-lookup"><span data-stu-id="e9efd-108">Create an ASP.NET Core web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)  
<span data-ttu-id="e9efd-109">Pomocí Visual Studia můžete vytvořit a nasadit webovou aplikaci ASP.NET Core do služby Azure App Service ve Windows.</span><span class="sxs-lookup"><span data-stu-id="e9efd-109">Use Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service on Windows.</span></span>

[<span data-ttu-id="e9efd-110">Vytvoření aplikace ASP.NET Core ve službě App Service na Linuxu</span><span class="sxs-lookup"><span data-stu-id="e9efd-110">Create an ASP.NET Core app in App Service on Linux</span></span>](/azure/app-service/containers/quickstart-dotnetcore)  
<span data-ttu-id="e9efd-111">Pomocí příkazového řádku můžete vytvořit a nasadit ASP.NET webovou aplikaci Core do služby Azure App Service na Linuxu.</span><span class="sxs-lookup"><span data-stu-id="e9efd-111">Use the command line to create and deploy an ASP.NET Core web app to Azure App Service on Linux.</span></span>

<span data-ttu-id="e9efd-112">Podívejte se [na ASP.NET Core na panelu služby App Service](https://aspnetcoreon.azurewebsites.net/) pro verzi ASP.NET Core, která je dostupná ve službě Azure App.</span><span class="sxs-lookup"><span data-stu-id="e9efd-112">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

<span data-ttu-id="e9efd-113">Přihlaste se k odběru úložiště [oznámení služby App Service](https://github.com/Azure/app-service-announcements/) a sledujte problémy.</span><span class="sxs-lookup"><span data-stu-id="e9efd-113">Subscribe to the [App Service Announcements](https://github.com/Azure/app-service-announcements/) repository and monitor the issues.</span></span> <span data-ttu-id="e9efd-114">Tým služby App Service pravidelně zveřejňuje oznámení a scénáře přicházející do služby App Service.</span><span class="sxs-lookup"><span data-stu-id="e9efd-114">The App Service team regularly posts announcements and scenarios arriving in App Service.</span></span>

<span data-ttu-id="e9efd-115">Následující články jsou k dispozici v dokumentaci ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="e9efd-115">The following articles are available in ASP.NET Core documentation:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="e9efd-116">Zjistěte, jak publikovat aplikaci ASP.NET Core do služby Azure App Service pomocí Visual Studia.</span><span class="sxs-lookup"><span data-stu-id="e9efd-116">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
<span data-ttu-id="e9efd-117">Zjistěte, jak pomocí Sady Visual Studio vytvořit ASP.NET webovou aplikaci Core a nasadit ji do služby Azure App Service pomocí Gitu pro průběžné nasazení.</span><span class="sxs-lookup"><span data-stu-id="e9efd-117">Learn how to create an ASP.NET Core web app using Visual Studio and deploy it to Azure App Service using Git for continuous deployment.</span></span>

[<span data-ttu-id="e9efd-118">Vytvoření prvního kanálu</span><span class="sxs-lookup"><span data-stu-id="e9efd-118">Create your first pipeline</span></span>](/azure/devops/pipelines/get-started-yaml)  
<span data-ttu-id="e9efd-119">Nastavte sestavení CI pro aplikaci ASP.NET Core a pak vytvořte verzi průběžného nasazení do služby Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="e9efd-119">Set up a CI build for an ASP.NET Core app, then create a continuous deployment release to Azure App Service.</span></span>

[<span data-ttu-id="e9efd-120">Izolované ho poli Azure Web Appu</span><span class="sxs-lookup"><span data-stu-id="e9efd-120">Azure Web App sandbox</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
<span data-ttu-id="e9efd-121">Seznamte se s omezeními spuštění služby Azure App Service vynucenými platformou Azure Apps.</span><span class="sxs-lookup"><span data-stu-id="e9efd-121">Discover Azure App Service runtime execution limitations enforced by the Azure Apps platform.</span></span>

<xref:test/troubleshoot>  
<span data-ttu-id="e9efd-122">Seznamte se s ASP.NET základními projekty a vyřešujte je.</span><span class="sxs-lookup"><span data-stu-id="e9efd-122">Understand and troubleshoot warnings and errors with ASP.NET Core projects.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="e9efd-123">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="e9efd-123">Application configuration</span></span>

### <a name="platform"></a><span data-ttu-id="e9efd-124">Platforma</span><span class="sxs-lookup"><span data-stu-id="e9efd-124">Platform</span></span>

<span data-ttu-id="e9efd-125">Architektura platformy (x86/x64) aplikace App Services se nastaví v nastavení aplikace na Webu Azure Portal pro aplikace, které jsou hostované na výpočetní (základní) nebo vyšší úrovni hostování řady A.</span><span class="sxs-lookup"><span data-stu-id="e9efd-125">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="e9efd-126">Zkontrolujte, zda nastavení publikování aplikace (například v profilu publikování ve Visual Studiu [(.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) odpovídá nastavení v konfiguraci služby aplikace na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="e9efd-126">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure Portal.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="e9efd-127">Runtimes pro 64bitové (x64) a 32bitové (x86) aplikace jsou k dispozici ve službě Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="e9efd-127">Runtimes for 64-bit (x64) and 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="e9efd-128">Sada [.NET Core SDK](/dotnet/core/sdk) dostupná ve službě App Service je 32bitová, ale 64bitové aplikace vytvořené místně pomocí konzoly Kudu nebo procespublikování v sadě Visual Studio můžete nasadit místně pomocí [konzoly Kudu](https://github.com/projectkudu/kudu/wiki) nebo procespublikování.</span><span class="sxs-lookup"><span data-stu-id="e9efd-128">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit, but you can deploy 64-bit apps built locally using the [Kudu](https://github.com/projectkudu/kudu/wiki) console or the publish process in Visual Studio.</span></span> <span data-ttu-id="e9efd-129">Další informace najdete v části [Publikování a nasazení aplikace.](#publish-and-deploy-the-app)</span><span class="sxs-lookup"><span data-stu-id="e9efd-129">For more information, see the [Publish and deploy the app](#publish-and-deploy-the-app) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="e9efd-130">Pro aplikace s nativnízávislosti, runtimes pro 32bitové (x86) aplikace jsou k dispozici na Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="e9efd-130">For apps with native dependencies, runtimes for 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="e9efd-131">Sada [.NET Core SDK](/dotnet/core/sdk) dostupná ve službě App Service je 32bitová.</span><span class="sxs-lookup"><span data-stu-id="e9efd-131">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit.</span></span>

::: moniker-end

<span data-ttu-id="e9efd-132">Další informace o součástech rozhraní .NET Core framework a metodách distribuce, jako jsou informace o běhu .NET Core a .NET Core SDK, naleznete [v tématu About .NET Core: Composition](/dotnet/core/about#composition).</span><span class="sxs-lookup"><span data-stu-id="e9efd-132">For more information on .NET Core framework components and distribution methods, such as information on the .NET Core runtime and the .NET Core SDK, see [About .NET Core: Composition](/dotnet/core/about#composition).</span></span>

### <a name="packages"></a><span data-ttu-id="e9efd-133">Balíčky</span><span class="sxs-lookup"><span data-stu-id="e9efd-133">Packages</span></span>

<span data-ttu-id="e9efd-134">Zahrňte následující balíčky NuGet, které poskytují funkce automatického protokolování pro aplikace nasazené do služby Azure App Service:</span><span class="sxs-lookup"><span data-stu-id="e9efd-134">Include the following NuGet packages to provide automatic logging features for apps deployed to Azure App Service:</span></span>

* <span data-ttu-id="e9efd-135">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) používá [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) k zajištění ASP.NET základní integrace se službou Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="e9efd-135">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) uses [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) to provide ASP.NET Core light-up integration with Azure App Service.</span></span> <span data-ttu-id="e9efd-136">Přidané funkce protokolování jsou `Microsoft.AspNetCore.AzureAppServicesIntegration` poskytovány balíčkem.</span><span class="sxs-lookup"><span data-stu-id="e9efd-136">The added logging features are provided by the `Microsoft.AspNetCore.AzureAppServicesIntegration` package.</span></span>
* <span data-ttu-id="e9efd-137">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) provede [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) pro přidání zprostředkovatelů protokolování `Microsoft.Extensions.Logging.AzureAppServices` diagnostiky služby Azure App Service v balíčku.</span><span class="sxs-lookup"><span data-stu-id="e9efd-137">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executes [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) to add Azure App Service diagnostics logging providers in the `Microsoft.Extensions.Logging.AzureAppServices` package.</span></span>
* <span data-ttu-id="e9efd-138">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) poskytuje implementace protokolů pro podporu protokolů diagnostiky služby Azure App Service a funkcí streamování protokolů.</span><span class="sxs-lookup"><span data-stu-id="e9efd-138">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) provides logger implementations to support Azure App Service diagnostics logs and log streaming features.</span></span>

<span data-ttu-id="e9efd-139">Předchozí balíčky nejsou k dispozici z [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="e9efd-139">The preceding packages aren't available from the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="e9efd-140">Aplikace, které cílí na `Microsoft.AspNetCore.App` rozhraní .NET Framework nebo odkazují na metabalíček, musí explicitně odkazovat na jednotlivé balíčky v souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="e9efd-140">Apps that target .NET Framework or reference the `Microsoft.AspNetCore.App` metapackage must explicitly reference the individual packages in the app's project file.</span></span>

## <a name="override-app-configuration-using-the-azure-portal"></a><span data-ttu-id="e9efd-141">Přepsání konfigurace aplikace pomocí portálu Azure Portal</span><span class="sxs-lookup"><span data-stu-id="e9efd-141">Override app configuration using the Azure Portal</span></span>

<span data-ttu-id="e9efd-142">Nastavení aplikací na webu Azure Portal umožňuje nastavit proměnné prostředí pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e9efd-142">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="e9efd-143">Proměnné prostředí mohou být spotřebovány [zprostředkovatelem konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e9efd-143">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="e9efd-144">Když se na webu Azure Portal vytvoří nebo upraví nastavení aplikace a vybere se tlačítko **Uložit,** aplikace Azure se restartuje.</span><span class="sxs-lookup"><span data-stu-id="e9efd-144">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="e9efd-145">Proměnná prostředí je k dispozici aplikaci po restartování služby.</span><span class="sxs-lookup"><span data-stu-id="e9efd-145">The environment variable is available to the app after the service restarts.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e9efd-146">Když aplikace používá [obecný hostitel](xref:fundamentals/host/generic-host), proměnné prostředí se načtou do konfigurace aplikace, když <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> se volá k sestavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="e9efd-146">When an app uses the [Generic Host](xref:fundamentals/host/generic-host), environment variables are loaded into the app's configuration when <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="e9efd-147">Další informace naleznete <xref:fundamentals/host/generic-host> v tématu a [zprostředkovatelkonfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e9efd-147">For more information, see <xref:fundamentals/host/generic-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e9efd-148">Když aplikace používá [webhostingu](xref:fundamentals/host/web-host), proměnné prostředí se načtou do konfigurace aplikace, když <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> se volá k sestavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="e9efd-148">When an app uses the [Web Host](xref:fundamentals/host/web-host), environment variables are loaded into the app's configuration when <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="e9efd-149">Další informace naleznete <xref:fundamentals/host/web-host> v tématu a [zprostředkovatelkonfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e9efd-149">For more information, see <xref:fundamentals/host/web-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="e9efd-150">Scénáře proxy serveru a vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="e9efd-150">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="e9efd-151">[Middleware integrace služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), který konfiguruje middleware předávaných záhlaví při hostování [mimo proces,](xref:host-and-deploy/iis/index#out-of-process-hosting-model)a ASP.NET core modul jsou konfigurovány tak, aby předávaly schéma (HTTP/HTTPS) a vzdálenou adresu IP, ze které požadavek pochází.</span><span class="sxs-lookup"><span data-stu-id="e9efd-151">The [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), which configures Forwarded Headers Middleware when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="e9efd-152">Další konfigurace může být vyžadována pro aplikace hostované za dalšími proxy servery a nástroji pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="e9efd-152">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="e9efd-153">Další informace naleznete v [tématu Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="e9efd-153">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="monitoring-and-logging"></a><span data-ttu-id="e9efd-154">Monitorování a protokolování</span><span class="sxs-lookup"><span data-stu-id="e9efd-154">Monitoring and logging</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e9efd-155">ASP.NET aplikace Core nasazené do služby App Service automaticky obdrží rozšíření služby App Service **ASP.NET integrace core protokolování**.</span><span class="sxs-lookup"><span data-stu-id="e9efd-155">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Integration**.</span></span> <span data-ttu-id="e9efd-156">Toto rozšíření umožňuje integraci protokolování pro aplikace ASP.NET Core ve službě Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="e9efd-156">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e9efd-157">ASP.NET aplikace Core nasazené do služby App Service automaticky obdrží rozšíření služby App Service **ASP.NET rozšíření core protokolování**.</span><span class="sxs-lookup"><span data-stu-id="e9efd-157">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Extensions**.</span></span> <span data-ttu-id="e9efd-158">Toto rozšíření umožňuje integraci protokolování pro aplikace ASP.NET Core ve službě Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="e9efd-158">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

<span data-ttu-id="e9efd-159">Informace o monitorování, protokolování a odstraňování potíží naleznete v následujících článcích:</span><span class="sxs-lookup"><span data-stu-id="e9efd-159">For monitoring, logging, and troubleshooting information, see the following articles:</span></span>

[<span data-ttu-id="e9efd-160">Monitorování aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e9efd-160">Monitor apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)  
<span data-ttu-id="e9efd-161">Přečtěte si, jak zkontrolovat kvóty a metriky pro aplikace a plány služby App Service.</span><span class="sxs-lookup"><span data-stu-id="e9efd-161">Learn how to review quotas and metrics for apps and App Service plans.</span></span>

[<span data-ttu-id="e9efd-162">Povolení protokolování diagnostiky aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e9efd-162">Enable diagnostics logging for apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)  
<span data-ttu-id="e9efd-163">Zjistěte, jak povolit a získat přístup k diagnostickému protokolování pro stavové kódy HTTP, neúspěšné požadavky a aktivitu webového serveru.</span><span class="sxs-lookup"><span data-stu-id="e9efd-163">Discover how to enable and access diagnostic logging for HTTP status codes, failed requests, and web server activity.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="e9efd-164">Seznamte se s běžnými přístupy ke zpracování chyb v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e9efd-164">Understand common approaches to handling errors in ASP.NET Core apps.</span></span>

<xref:test/troubleshoot-azure-iis>  
<span data-ttu-id="e9efd-165">Zjistěte, jak diagnostikovat problémy s nasazením služby Azure App Service pomocí aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e9efd-165">Learn how to diagnose issues with Azure App Service deployments with ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-iis-errors-reference>  
<span data-ttu-id="e9efd-166">Podívejte se na běžné chyby konfigurace nasazení pro aplikace hostované službou Azure App Service/IIS s radami pro řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="e9efd-166">See the common deployment configuration errors for apps hosted by Azure App Service/IIS with troubleshooting advice.</span></span>

## <a name="data-protection-key-ring-and-deployment-slots"></a><span data-ttu-id="e9efd-167">Kroužek klíčů pro ochranu dat a sloty pro nasazení</span><span class="sxs-lookup"><span data-stu-id="e9efd-167">Data Protection key ring and deployment slots</span></span>

<span data-ttu-id="e9efd-168">[Klíče ochrany dat](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) jsou uloženy ve složce *%HOME%\ASP.NET\DataProtection-Keys.*</span><span class="sxs-lookup"><span data-stu-id="e9efd-168">[Data Protection keys](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="e9efd-169">Tato složka je zálohována síťovým úložištěm a je synchronizována ve všech počítačích hostujících aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e9efd-169">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span> <span data-ttu-id="e9efd-170">Klíče nejsou v klidu chráněny.</span><span class="sxs-lookup"><span data-stu-id="e9efd-170">Keys aren't protected at rest.</span></span> <span data-ttu-id="e9efd-171">Tato složka poskytuje vyzváněcí klávesy pro všechny instance aplikace v jednom slotu nasazení.</span><span class="sxs-lookup"><span data-stu-id="e9efd-171">This folder supplies the key ring to all instances of an app in a single deployment slot.</span></span> <span data-ttu-id="e9efd-172">Samostatné sloty nasazení, jako je pracovní a produkční, nesdílejí kroužek klíčů.</span><span class="sxs-lookup"><span data-stu-id="e9efd-172">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span>

<span data-ttu-id="e9efd-173">Při přepnutí mezi sloty nasazení nebude žádný systém využívající ochranu dat schopen dešifrovat uložená data pomocí kroužku klíče uvnitř předchozího slotu.</span><span class="sxs-lookup"><span data-stu-id="e9efd-173">When swapping between deployment slots, any system using data protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="e9efd-174">ASP.NET Cookie Middleware používá ochranu dat k ochraně svých souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="e9efd-174">ASP.NET Cookie Middleware uses data protection to protect its cookies.</span></span> <span data-ttu-id="e9efd-175">To vede k tomu, že uživatelé jsou odhlášeni z aplikace, která používá standardní ASP.NET cookie Middleware.</span><span class="sxs-lookup"><span data-stu-id="e9efd-175">This leads to users being signed out of an app that uses the standard ASP.NET Cookie Middleware.</span></span> <span data-ttu-id="e9efd-176">Pro řešení kroužku na klíče nezávislé na slotu použijte externího poskytovatele kroužku na klíče, například:</span><span class="sxs-lookup"><span data-stu-id="e9efd-176">For a slot-independent key ring solution, use an external key ring provider, such as:</span></span>

* <span data-ttu-id="e9efd-177">Azure Blob Storage</span><span class="sxs-lookup"><span data-stu-id="e9efd-177">Azure Blob Storage</span></span>
* <span data-ttu-id="e9efd-178">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="e9efd-178">Azure Key Vault</span></span>
* <span data-ttu-id="e9efd-179">Úložiště SQL</span><span class="sxs-lookup"><span data-stu-id="e9efd-179">SQL store</span></span>
* <span data-ttu-id="e9efd-180">Redis Cache</span><span class="sxs-lookup"><span data-stu-id="e9efd-180">Redis cache</span></span>

<span data-ttu-id="e9efd-181">Další informace naleznete v tématu <xref:security/data-protection/implementation/key-storage-providers>.</span><span class="sxs-lookup"><span data-stu-id="e9efd-181">For more information, see <xref:security/data-protection/implementation/key-storage-providers>.</span></span>
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>

## <a name="deploy-an-aspnet-core-app-that-uses-a-net-core-preview"></a><span data-ttu-id="e9efd-182">Nasazení aplikace ASP.NET Core, která používá náhled jádra .NET</span><span class="sxs-lookup"><span data-stu-id="e9efd-182">Deploy an ASP.NET Core app that uses a .NET Core preview</span></span>

<span data-ttu-id="e9efd-183">Pokud chcete nasadit aplikaci, která používá předběžnou verzi .NET Core, podívejte se na následující prostředky.</span><span class="sxs-lookup"><span data-stu-id="e9efd-183">To deploy an app that uses a preview release of .NET Core, see the following resources.</span></span> <span data-ttu-id="e9efd-184">Tyto přístupy se také používají, když je k dispozici runtime, ale sada SDK nebyla nainstalována ve službě Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="e9efd-184">These approaches are also used when the runtime is available but the SDK hasn't been installed on Azure App Service.</span></span>

* [<span data-ttu-id="e9efd-185">Určení verze sady .NET Core SDK pomocí azure pipelines</span><span class="sxs-lookup"><span data-stu-id="e9efd-185">Specify the .NET Core SDK Version using Azure Pipelines</span></span>](#specify-the-net-core-sdk-version-using-azure-pipelines)
* [<span data-ttu-id="e9efd-186">Nasazení samostatné aplikace preview</span><span class="sxs-lookup"><span data-stu-id="e9efd-186">Deploy a self-contained preview app</span></span>](#deploy-a-self-contained-preview-app)
* [<span data-ttu-id="e9efd-187">Použití Dockeru s webovými aplikacemi pro kontejnery</span><span class="sxs-lookup"><span data-stu-id="e9efd-187">Use Docker with Web Apps for containers</span></span>](#use-docker-with-web-apps-for-containers)
* [<span data-ttu-id="e9efd-188">Instalace rozšíření webu ve verzi Preview</span><span class="sxs-lookup"><span data-stu-id="e9efd-188">Install the preview site extension</span></span>](#install-the-preview-site-extension)

<span data-ttu-id="e9efd-189">Podívejte se [na ASP.NET Core na panelu služby App Service](https://aspnetcoreon.azurewebsites.net/) pro verzi ASP.NET Core, která je dostupná ve službě Azure App.</span><span class="sxs-lookup"><span data-stu-id="e9efd-189">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

### <a name="specify-the-net-core-sdk-version-using-azure-pipelines"></a><span data-ttu-id="e9efd-190">Určení verze sady .NET Core SDK pomocí azure pipelines</span><span class="sxs-lookup"><span data-stu-id="e9efd-190">Specify the .NET Core SDK Version using Azure Pipelines</span></span>

<span data-ttu-id="e9efd-191">Pomocí [scénářů CI/CD služby Azure App Service](/azure/app-service/deploy-continuous-deployment) můžete nastavit sestavení průběžné integrace s Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="e9efd-191">Use [Azure App Service CI/CD scenarios](/azure/app-service/deploy-continuous-deployment) to set up a continuous integration build with Azure DevOps.</span></span> <span data-ttu-id="e9efd-192">Po vytvoření sestavení Azure DevOps volitelně nakonfigurujte sestavení tak, aby používalo konkrétní verzi sady SDK.</span><span class="sxs-lookup"><span data-stu-id="e9efd-192">After the Azure DevOps build is created, optionally configure the build to use a specific SDK version.</span></span> 

#### <a name="specify-the-net-core-sdk-version"></a><span data-ttu-id="e9efd-193">Určení verze sady .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="e9efd-193">Specify the .NET Core SDK version</span></span>

<span data-ttu-id="e9efd-194">Při použití Centra nasazení služby App Service k vytvoření sestavení Azure DevOps `Restore`obsahuje výchozí kanál sestavení kroky pro `Build`, , `Test`a `Publish`.</span><span class="sxs-lookup"><span data-stu-id="e9efd-194">When using the App Service deployment center to create an Azure DevOps build, the default build pipeline includes steps for `Restore`, `Build`, `Test`, and `Publish`.</span></span> <span data-ttu-id="e9efd-195">Chcete-li určit verzi sady SDK, vyberte tlačítko **Přidat (+)** v seznamu úloh agenta a přidejte nový krok.</span><span class="sxs-lookup"><span data-stu-id="e9efd-195">To specify the SDK version, select the **Add (+)** button in the Agent job list to add a new step.</span></span> <span data-ttu-id="e9efd-196">Vyhledejte na panelu hledání **souboru .NET Core SDK.**</span><span class="sxs-lookup"><span data-stu-id="e9efd-196">Search for **.NET Core SDK** in the search bar.</span></span> 

![Přidání kroku sady .NET Core SDK](index/add-sdk-step.png)

<span data-ttu-id="e9efd-198">Přesuňte krok do první pozice v sestavení tak, aby následující kroky používaly zadanou verzi sady .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="e9efd-198">Move the step into the first position in the build so that the steps following it use the specified version of the .NET Core SDK.</span></span> <span data-ttu-id="e9efd-199">Zadejte verzi sady .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="e9efd-199">Specify the version of the .NET Core SDK.</span></span> <span data-ttu-id="e9efd-200">V tomto příkladu je sada `3.0.100`SDK nastavena na .</span><span class="sxs-lookup"><span data-stu-id="e9efd-200">In this example, the SDK is set to `3.0.100`.</span></span>

![Dokončení kroku sady SDK](index/sdk-step-first-place.png)

<span data-ttu-id="e9efd-202">Chcete-li publikovat [samostatné nasazení (SCD),](/dotnet/core/deploying/#self-contained-deployments-scd)nakonfigurujte scd v `Publish` kroku a zadejte identifikátor [runtime (RID).](/dotnet/core/rid-catalog)</span><span class="sxs-lookup"><span data-stu-id="e9efd-202">To publish a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), configure SCD in the `Publish` step and provide the [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

![Samostatné publikování](index/self-contained.png)

### <a name="deploy-a-self-contained-preview-app"></a><span data-ttu-id="e9efd-204">Nasazení samostatné aplikace preview</span><span class="sxs-lookup"><span data-stu-id="e9efd-204">Deploy a self-contained preview app</span></span>

<span data-ttu-id="e9efd-205">[Samostatné nasazení (SCD),](/dotnet/core/deploying/#self-contained-deployments-scd) které se zaměřuje na běhový čas náhledu nese za běhu náhledu v nasazení.</span><span class="sxs-lookup"><span data-stu-id="e9efd-205">A [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) that targets a preview runtime carries the preview runtime in the deployment.</span></span>

<span data-ttu-id="e9efd-206">Při nasazování samostatné aplikace:</span><span class="sxs-lookup"><span data-stu-id="e9efd-206">When deploying a self-contained app:</span></span>

* <span data-ttu-id="e9efd-207">Web ve službě Azure App Service nevyžaduje [rozšíření webu ve verzi Preview](#install-the-preview-site-extension).</span><span class="sxs-lookup"><span data-stu-id="e9efd-207">The site in Azure App Service doesn't require the [preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="e9efd-208">Aplikace musí být publikována s jiným přístupem než při publikování pro [nasazení závislé na rámci (FDD).](/dotnet/core/deploying#framework-dependent-deployments-fdd)</span><span class="sxs-lookup"><span data-stu-id="e9efd-208">The app must be published following a different approach than when publishing for a [framework-dependent deployment (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="e9efd-209">Postupujte podle pokynů v části [Nasazení samostatné aplikace.](#deploy-the-app-self-contained)</span><span class="sxs-lookup"><span data-stu-id="e9efd-209">Follow the guidance in the [Deploy the app self-contained](#deploy-the-app-self-contained) section.</span></span>

### <a name="use-docker-with-web-apps-for-containers"></a><span data-ttu-id="e9efd-210">Použití Dockeru s webovými aplikacemi pro kontejnery</span><span class="sxs-lookup"><span data-stu-id="e9efd-210">Use Docker with Web Apps for containers</span></span>

<span data-ttu-id="e9efd-211">Docker [Hub](https://hub.docker.com/r/microsoft/aspnetcore/) obsahuje nejnovější náhled imitace Dockeru.</span><span class="sxs-lookup"><span data-stu-id="e9efd-211">The [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contains the latest preview Docker images.</span></span> <span data-ttu-id="e9efd-212">Obrázky lze použít jako základní obrázek.</span><span class="sxs-lookup"><span data-stu-id="e9efd-212">The images can be used as a base image.</span></span> <span data-ttu-id="e9efd-213">Použijte bitovou kopii a nasaďte do webových aplikací pro kontejnery normálně.</span><span class="sxs-lookup"><span data-stu-id="e9efd-213">Use the image and deploy to Web Apps for Containers normally.</span></span>

### <a name="install-the-preview-site-extension"></a><span data-ttu-id="e9efd-214">Instalace rozšíření webu ve verzi Preview</span><span class="sxs-lookup"><span data-stu-id="e9efd-214">Install the preview site extension</span></span>

<span data-ttu-id="e9efd-215">Pokud dojde k potížím pomocí rozšíření webu preview, otevřete [problém dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/issues).</span><span class="sxs-lookup"><span data-stu-id="e9efd-215">If a problem occurs using the preview site extension, open an [dotnet/AspNetCore issue](https://github.com/dotnet/AspNetCore/issues).</span></span>

1. <span data-ttu-id="e9efd-216">Z portálu Azure přejděte na službu App Service.</span><span class="sxs-lookup"><span data-stu-id="e9efd-216">From the Azure Portal, navigate to the App Service.</span></span>
1. <span data-ttu-id="e9efd-217">Vyberte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e9efd-217">Select the web app.</span></span>
1. <span data-ttu-id="e9efd-218">Do vyhledávacího pole zadejte "ex", chcete-li filtrovat "Rozšíření" nebo posuňte seznam nástrojů pro správu dolů.</span><span class="sxs-lookup"><span data-stu-id="e9efd-218">Type "ex" in the search box to filter for "Extensions" or scroll down the list of management tools.</span></span>
1. <span data-ttu-id="e9efd-219">Vyberte **Extensions** (Rozšíření).</span><span class="sxs-lookup"><span data-stu-id="e9efd-219">Select **Extensions**.</span></span>
1. <span data-ttu-id="e9efd-220">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e9efd-220">Select **Add**.</span></span>
1. <span data-ttu-id="e9efd-221">Vyberte rozšíření **prostředí ASP.NET jádra {X.Y} ({x64|x86})** ze seznamu, kde `{X.Y}` je verze náhledu ASP.NET Core a `{x64|x86}` určuje platformu.</span><span class="sxs-lookup"><span data-stu-id="e9efd-221">Select the **ASP.NET Core {X.Y} ({x64|x86}) Runtime** extension from the list, where `{X.Y}` is the ASP.NET Core preview version and `{x64|x86}` specifies the platform.</span></span>
1. <span data-ttu-id="e9efd-222">Chcete-li přijmout zákonné podmínky, vyberte **možnost OK.**</span><span class="sxs-lookup"><span data-stu-id="e9efd-222">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="e9efd-223">Chcete-li nainstalovat rozšíření, vyberte **možnost OK.**</span><span class="sxs-lookup"><span data-stu-id="e9efd-223">Select **OK** to install the extension.</span></span>

<span data-ttu-id="e9efd-224">Po dokončení operace je nainstalován nejnovější náhled jádra .NET.</span><span class="sxs-lookup"><span data-stu-id="e9efd-224">When the operation completes, the latest .NET Core preview is installed.</span></span> <span data-ttu-id="e9efd-225">Ověřte instalaci:</span><span class="sxs-lookup"><span data-stu-id="e9efd-225">Verify the installation:</span></span>

1. <span data-ttu-id="e9efd-226">Vyberte **rozšířené nástroje**.</span><span class="sxs-lookup"><span data-stu-id="e9efd-226">Select **Advanced Tools**.</span></span>
1. <span data-ttu-id="e9efd-227">V **rozšířeném nástroji**vyberte možnost **Přejít** .</span><span class="sxs-lookup"><span data-stu-id="e9efd-227">Select **Go** in **Advanced Tools**.</span></span>
1. <span data-ttu-id="e9efd-228">Vyberte položku nabídky nabídky **ladění konzoly** > **PowerShell.**</span><span class="sxs-lookup"><span data-stu-id="e9efd-228">Select the **Debug console** > **PowerShell** menu item.</span></span>
1. <span data-ttu-id="e9efd-229">Na příkazové masko powershellové matné spusťte následující příkaz.</span><span class="sxs-lookup"><span data-stu-id="e9efd-229">At the PowerShell prompt, execute the following command.</span></span> <span data-ttu-id="e9efd-230">V příkazu nahraďte `{X.Y}` ASP.NET verzi `{PLATFORM}` za běhu Core a platformu:</span><span class="sxs-lookup"><span data-stu-id="e9efd-230">Substitute the ASP.NET Core runtime version for `{X.Y}` and the platform for `{PLATFORM}` in the command:</span></span>

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   <span data-ttu-id="e9efd-231">Příkaz se `True` vrátí při instalaci runtime náhledu x64.</span><span class="sxs-lookup"><span data-stu-id="e9efd-231">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="e9efd-232">Architektura platformy (x86/x64) aplikace App Services se nastaví v nastavení aplikace na Webu Azure Portal pro aplikace, které jsou hostované na výpočetní (základní) nebo vyšší úrovni hostování řady A.</span><span class="sxs-lookup"><span data-stu-id="e9efd-232">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="e9efd-233">Zkontrolujte, zda nastavení publikování aplikace (například v profilu publikování ve Visual Studiu [(.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)odpovídá nastavení v konfiguraci služby aplikace na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="e9efd-233">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure portal.</span></span>
>
> <span data-ttu-id="e9efd-234">Pokud je aplikace spuštěna v režimu v procesu a architektura platformy je nakonfigurovaná pro 64bitovou (x64), ASP.NET Core Module používá 64bitový modul runtime náhledu, pokud je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="e9efd-234">If the app is run in in-process mode and the platform architecture is configured for 64-bit (x64), the ASP.NET Core Module uses the 64-bit preview runtime, if present.</span></span> <span data-ttu-id="e9efd-235">Nainstalujte rozšíření **ASP.NET core {X.Y} (x64) Runtime** pomocí portálu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="e9efd-235">Install the **ASP.NET Core {X.Y} (x64) Runtime** extension using the Azure Portal.</span></span>
>
> <span data-ttu-id="e9efd-236">Po instalaci runtime náhledu x64 spusťte následující příkaz v příkazovém okně Azure Kudu PowerShell a ověřte instalaci.</span><span class="sxs-lookup"><span data-stu-id="e9efd-236">After installing the x64 preview runtime, run the following command in the Azure Kudu PowerShell command window to verify the installation.</span></span> <span data-ttu-id="e9efd-237">V následujícím příkazu nahraďte verzi `{X.Y}` ASP.NET core runtime:</span><span class="sxs-lookup"><span data-stu-id="e9efd-237">Substitute the ASP.NET Core runtime version for `{X.Y}` in the following command:</span></span>
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> <span data-ttu-id="e9efd-238">Příkaz se `True` vrátí při instalaci runtime náhledu x64.</span><span class="sxs-lookup"><span data-stu-id="e9efd-238">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="e9efd-239">**ASP.NET rozšíření** Core umožňuje další funkce pro ASP.NET Core ve službách Azure App Services, jako je povolení protokolování Azure.</span><span class="sxs-lookup"><span data-stu-id="e9efd-239">**ASP.NET Core Extensions** enables additional functionality for ASP.NET Core on Azure App Services, such as enabling Azure logging.</span></span> <span data-ttu-id="e9efd-240">Rozšíření se nainstaluje automaticky při nasazování z Visual Studia.</span><span class="sxs-lookup"><span data-stu-id="e9efd-240">The extension is installed automatically when deploying from Visual Studio.</span></span> <span data-ttu-id="e9efd-241">Pokud rozšíření není nainstalované, nainstalujte ho do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e9efd-241">If the extension isn't installed, install it for the app.</span></span>

<span data-ttu-id="e9efd-242">**Použití rozšíření webu náhledu se šablonou ARM**</span><span class="sxs-lookup"><span data-stu-id="e9efd-242">**Use the preview site extension with an ARM template**</span></span>

<span data-ttu-id="e9efd-243">Pokud se k vytváření a nasazování `siteextensions` aplikací používá šablona ARM, typ prostředku se dá použít k přidání rozšíření webu do webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="e9efd-243">If an ARM template is used to create and deploy apps, the `siteextensions` resource type can be used to add the site extension to a web app.</span></span> <span data-ttu-id="e9efd-244">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e9efd-244">For example:</span></span>

[!code-json[](index/sample/arm.json?highlight=2)]

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="e9efd-245">Publikování a nasazení aplikace</span><span class="sxs-lookup"><span data-stu-id="e9efd-245">Publish and deploy the app</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="e9efd-246">Pro 64bitové nasazení:</span><span class="sxs-lookup"><span data-stu-id="e9efd-246">For a 64-bit deployment:</span></span>

* <span data-ttu-id="e9efd-247">K vytvoření 64bitové aplikace použijte 64bitovou sadu .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="e9efd-247">Use a 64-bit .NET Core SDK to build a 64-bit app.</span></span>
* <span data-ttu-id="e9efd-248">Nastavte **platformu** na **64 bitů** v**nastavení Obecné** **konfigurace** > služby App Service .</span><span class="sxs-lookup"><span data-stu-id="e9efd-248">Set the **Platform** to **64 Bit** in the App Service's **Configuration** > **General settings**.</span></span> <span data-ttu-id="e9efd-249">Aplikace musí používat základní nebo vyšší plán služeb, aby umožnila výběr bitovosti platformy.</span><span class="sxs-lookup"><span data-stu-id="e9efd-249">The app must use a Basic or higher service plan to enable the choice of platform bitness.</span></span>

::: moniker-end

### <a name="deploy-the-app-framework-dependent"></a><span data-ttu-id="e9efd-250">Nasazení architektury aplikace závislé</span><span class="sxs-lookup"><span data-stu-id="e9efd-250">Deploy the app framework-dependent</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9efd-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9efd-251">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e9efd-252">Vyberte **Publikovat** > **{Název aplikace}** na panelu nástrojů Sady Visual Studio nebo klikněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte **Publikovat**.</span><span class="sxs-lookup"><span data-stu-id="e9efd-252">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="e9efd-253">V **dialogovém** okně Vybrat cíl publikování zkontrolujte, že je vybraná **služba App Service.**</span><span class="sxs-lookup"><span data-stu-id="e9efd-253">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="e9efd-254">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="e9efd-254">Select **Advanced**.</span></span> <span data-ttu-id="e9efd-255">Otevře se dialogové okno **Publikovat.**</span><span class="sxs-lookup"><span data-stu-id="e9efd-255">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="e9efd-256">V dialogovém okně **Publikovat:**</span><span class="sxs-lookup"><span data-stu-id="e9efd-256">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="e9efd-257">Zkontrolujte, zda je vybrána konfigurace **vydání.**</span><span class="sxs-lookup"><span data-stu-id="e9efd-257">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="e9efd-258">Otevřete rozevírací seznam **Režim nasazení** a vyberte možnost Závislá **na rámci**.</span><span class="sxs-lookup"><span data-stu-id="e9efd-258">Open the **Deployment Mode** drop-down list and select **Framework-Dependent**.</span></span>
   * <span data-ttu-id="e9efd-259">Jako cílový **běh**vyberte možnost **Přenosné** .</span><span class="sxs-lookup"><span data-stu-id="e9efd-259">Select **Portable** as the **Target Runtime**.</span></span>
   * <span data-ttu-id="e9efd-260">Pokud potřebujete při nasazení odebrat další soubory, otevřete **možnosti publikování souborů** a zaškrtněte políčko pro odebrání dalších souborů v cílovém umístění.</span><span class="sxs-lookup"><span data-stu-id="e9efd-260">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="e9efd-261">Vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="e9efd-261">Select **Save**.</span></span>
1. <span data-ttu-id="e9efd-262">Vytvořte nový web nebo aktualizujte existující web podle zbývajících pokynů průvodce publikováním.</span><span class="sxs-lookup"><span data-stu-id="e9efd-262">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e9efd-263">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="e9efd-263">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="e9efd-264">V souboru projektu nezadávejte [identifikátor runtime (RID).](/dotnet/core/rid-catalog)</span><span class="sxs-lookup"><span data-stu-id="e9efd-264">In the project file, don't specify a [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

1. <span data-ttu-id="e9efd-265">Z příkazového prostředí publikujte aplikaci v konfiguraci release pomocí příkazu [dotnet publish.](/dotnet/core/tools/dotnet-publish)</span><span class="sxs-lookup"><span data-stu-id="e9efd-265">From a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="e9efd-266">V následujícím příkladu se aplikace publikuje jako aplikace závislá na rámci:</span><span class="sxs-lookup"><span data-stu-id="e9efd-266">In the following example, the app is published as a framework-dependent app:</span></span>

   ```console
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="e9efd-267">Přesuňte obsah adresáře *bin/Release/{TARGET FRAMEWORK}/publish* na web v aplikaci App Service.</span><span class="sxs-lookup"><span data-stu-id="e9efd-267">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="e9efd-268">Pokud přetaženíobsahu složky *publikování* z místního pevného disku nebo síťové sdílené složky přímo `D:\home\site\wwwroot` do služby App Service v konzole [Kudu](https://github.com/projectkudu/kudu/wiki) přetáhněte soubory do složky v konzole Kudu.</span><span class="sxs-lookup"><span data-stu-id="e9efd-268">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the [Kudu](https://github.com/projectkudu/kudu/wiki) console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

### <a name="deploy-the-app-self-contained"></a><span data-ttu-id="e9efd-269">Nasazení aplikace soběstačné</span><span class="sxs-lookup"><span data-stu-id="e9efd-269">Deploy the app self-contained</span></span>

<span data-ttu-id="e9efd-270">Použití sady Visual Studio nebo rozhraní CLI jádra .NET pro [samostatné nasazení (SCD).](/dotnet/core/deploying/#self-contained-deployments-scd)</span><span class="sxs-lookup"><span data-stu-id="e9efd-270">Use Visual Studio or the .NET Core CLI for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9efd-271">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9efd-271">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e9efd-272">Vyberte **Publikovat** > **{Název aplikace}** na panelu nástrojů Sady Visual Studio nebo klikněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte **Publikovat**.</span><span class="sxs-lookup"><span data-stu-id="e9efd-272">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="e9efd-273">V **dialogovém** okně Vybrat cíl publikování zkontrolujte, že je vybraná **služba App Service.**</span><span class="sxs-lookup"><span data-stu-id="e9efd-273">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="e9efd-274">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="e9efd-274">Select **Advanced**.</span></span> <span data-ttu-id="e9efd-275">Otevře se dialogové okno **Publikovat.**</span><span class="sxs-lookup"><span data-stu-id="e9efd-275">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="e9efd-276">V dialogovém okně **Publikovat:**</span><span class="sxs-lookup"><span data-stu-id="e9efd-276">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="e9efd-277">Zkontrolujte, zda je vybrána konfigurace **vydání.**</span><span class="sxs-lookup"><span data-stu-id="e9efd-277">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="e9efd-278">Otevřete rozevírací seznam **Režim nasazení** a vyberte **Položku Samostatně obsažené**.</span><span class="sxs-lookup"><span data-stu-id="e9efd-278">Open the **Deployment Mode** drop-down list and select **Self-Contained**.</span></span>
   * <span data-ttu-id="e9efd-279">Vyberte cílový runtime z rozevíracího seznamu **Cílový běh.**</span><span class="sxs-lookup"><span data-stu-id="e9efd-279">Select the target runtime from the **Target Runtime** drop-down list.</span></span> <span data-ttu-id="e9efd-280">Výchozí formát je `win-x86`.</span><span class="sxs-lookup"><span data-stu-id="e9efd-280">The default is `win-x86`.</span></span>
   * <span data-ttu-id="e9efd-281">Pokud potřebujete při nasazení odebrat další soubory, otevřete **možnosti publikování souborů** a zaškrtněte políčko pro odebrání dalších souborů v cílovém umístění.</span><span class="sxs-lookup"><span data-stu-id="e9efd-281">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="e9efd-282">Vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="e9efd-282">Select **Save**.</span></span>
1. <span data-ttu-id="e9efd-283">Vytvořte nový web nebo aktualizujte existující web podle zbývajících pokynů průvodce publikováním.</span><span class="sxs-lookup"><span data-stu-id="e9efd-283">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e9efd-284">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="e9efd-284">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="e9efd-285">V souboru projektu zadejte jeden nebo více [identifikátorů runtime (ID).](/dotnet/core/rid-catalog)</span><span class="sxs-lookup"><span data-stu-id="e9efd-285">In the project file, specify one or more [Runtime Identifiers (RIDs)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="e9efd-286">Použijte `<RuntimeIdentifier>` (singulární) pro `<RuntimeIdentifiers>` jeden RID nebo použijte (množné číslo) k poskytnutí seznamu ridů oddělených středníkem.</span><span class="sxs-lookup"><span data-stu-id="e9efd-286">Use `<RuntimeIdentifier>` (singular) for a single RID, or use `<RuntimeIdentifiers>` (plural) to provide a semicolon-delimited list of RIDs.</span></span> <span data-ttu-id="e9efd-287">V následujícím příkladu `win-x86` je určen rid:</span><span class="sxs-lookup"><span data-stu-id="e9efd-287">In the following example, the `win-x86` RID is specified:</span></span>

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. <span data-ttu-id="e9efd-288">Z příkazového prostředí publikujte aplikaci v konfiguraci vydání pro běh hostitele pomocí příkazu [dotnet publish.](/dotnet/core/tools/dotnet-publish)</span><span class="sxs-lookup"><span data-stu-id="e9efd-288">From a command shell, publish the app in Release configuration for the host's runtime with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="e9efd-289">V následujícím příkladu je aplikace publikována pro `win-x86` RID.</span><span class="sxs-lookup"><span data-stu-id="e9efd-289">In the following example, the app is published for the `win-x86` RID.</span></span> <span data-ttu-id="e9efd-290">Rid dodaný `--runtime` k možnosti musí `<RuntimeIdentifier>` být `<RuntimeIdentifiers>`uveden ve vlastnosti (nebo ) v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="e9efd-290">The RID supplied to the `--runtime` option must be provided in the `<RuntimeIdentifier>` (or `<RuntimeIdentifiers>`) property in the project file.</span></span>

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. <span data-ttu-id="e9efd-291">Přesuňte obsah adresáře *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* na web v aplikaci App Service.</span><span class="sxs-lookup"><span data-stu-id="e9efd-291">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="e9efd-292">Pokud přetaženíobsahu složky *publikování* z místního pevného disku nebo síťové sdílené složky přímo `D:\home\site\wwwroot` do služby App Service v konzole Kudu přetáhněte soubory do složky v konzole Kudu.</span><span class="sxs-lookup"><span data-stu-id="e9efd-292">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the Kudu console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

## <a name="protocol-settings-https"></a><span data-ttu-id="e9efd-293">Nastavení protokolu (HTTPS)</span><span class="sxs-lookup"><span data-stu-id="e9efd-293">Protocol settings (HTTPS)</span></span>

<span data-ttu-id="e9efd-294">Vazby zabezpečeného protokolu umožňují zadat certifikát, který se má použít při odpovídání na požadavky přes protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e9efd-294">Secure protocol bindings allow you specify a certificate to use when responding to requests over HTTPS.</span></span> <span data-ttu-id="e9efd-295">Vazba vyžaduje platný soukromý certifikát (*.pfx*) vydaný pro konkrétní název hostitele.</span><span class="sxs-lookup"><span data-stu-id="e9efd-295">Binding requires a valid private certificate (*.pfx*) issued for the specific hostname.</span></span> <span data-ttu-id="e9efd-296">Další informace najdete [v tématu Kurz: Vazba existující vlastní SSL certifikát na Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="e9efd-296">For more information, see [Tutorial: Bind an existing custom SSL certificate to Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="e9efd-297">Transformace souboru web.config</span><span class="sxs-lookup"><span data-stu-id="e9efd-297">Transform web.config</span></span>

<span data-ttu-id="e9efd-298">Pokud potřebujete transformovat *web.config* při publikování (například nastavit proměnné prostředí na základě konfigurace, profilu nebo prostředí), přečtěte si viz <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="e9efd-298">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e9efd-299">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e9efd-299">Additional resources</span></span>

* [<span data-ttu-id="e9efd-300">Přehled služby App Service</span><span class="sxs-lookup"><span data-stu-id="e9efd-300">App Service overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="e9efd-301">Azure App Service: Nejlepší místo pro hostování aplikací .NET (55minutové video s přehledem)</span><span class="sxs-lookup"><span data-stu-id="e9efd-301">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span></span>](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [<span data-ttu-id="e9efd-302">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12minutové video)</span><span class="sxs-lookup"><span data-stu-id="e9efd-302">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [<span data-ttu-id="e9efd-303">Přehled diagnostiky služby Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e9efd-303">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

<span data-ttu-id="e9efd-304">Služba Azure App Service na serveru Windows Server používá [Internetovou informační službu (IIS).](https://www.iis.net/)</span><span class="sxs-lookup"><span data-stu-id="e9efd-304">Azure App Service on Windows Server uses [Internet Information Services (IIS)](https://www.iis.net/).</span></span> <span data-ttu-id="e9efd-305">Následující témata se týkající se základní technologie iIS:</span><span class="sxs-lookup"><span data-stu-id="e9efd-305">The following topics pertain to the underlying IIS technology:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [<span data-ttu-id="e9efd-306">Windows Server – obsah správce IT pro aktuální a předchozí verze</span><span class="sxs-lookup"><span data-stu-id="e9efd-306">Windows Server - IT administrator content for current and previous releases</span></span>](/windows-server/windows-server-versions)
