---
title: Nasazení aplikací ASP.NET Core pro Azure App Service
author: bradygaster
description: Tento článek obsahuje odkazy na hostitele Azure a nasazení prostředků.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/16/2019
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: 51d82d1deadb3d2adbdccd39c8d949e3f9f812fd
ms.sourcegitcommit: 79850db9e79b1705b89f466c6f2c961ff15485de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75693840"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a><span data-ttu-id="ca6df-103">Nasazení aplikací ASP.NET Core pro Azure App Service</span><span class="sxs-lookup"><span data-stu-id="ca6df-103">Deploy ASP.NET Core apps to Azure App Service</span></span>

<span data-ttu-id="ca6df-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) je [platforma cloud computingu od Microsoftu](https://azure.microsoft.com/) pro hostování webových aplikací, včetně ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca6df-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span>

## <a name="useful-resources"></a><span data-ttu-id="ca6df-105">Užitečné materiály</span><span class="sxs-lookup"><span data-stu-id="ca6df-105">Useful resources</span></span>

<span data-ttu-id="ca6df-106">[Dokumentace k App Service](/azure/app-service/) je Domovská stránka pro Azure Apps, kurzy, ukázky, návody a další materiály.</span><span class="sxs-lookup"><span data-stu-id="ca6df-106">[App Service Documentation](/azure/app-service/) is the home for Azure Apps documentation, tutorials, samples, how-to guides, and other resources.</span></span> <span data-ttu-id="ca6df-107">Existují dva významné kurzy, které se týkají hostování ASP.NET Corech aplikací:</span><span class="sxs-lookup"><span data-stu-id="ca6df-107">Two notable tutorials that pertain to hosting ASP.NET Core apps are:</span></span>

[<span data-ttu-id="ca6df-108">Vytvoření webové aplikace v ASP.NET Core v Azure</span><span class="sxs-lookup"><span data-stu-id="ca6df-108">Create an ASP.NET Core web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)  
<span data-ttu-id="ca6df-109">Pomocí sady Visual Studio můžete vytvořit a nasadit webovou aplikaci ASP.NET Core pro Azure App Service ve Windows.</span><span class="sxs-lookup"><span data-stu-id="ca6df-109">Use Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service on Windows.</span></span>

[<span data-ttu-id="ca6df-110">Vytvoření aplikace ASP.NET Core v App Service v systému Linux</span><span class="sxs-lookup"><span data-stu-id="ca6df-110">Create an ASP.NET Core app in App Service on Linux</span></span>](/azure/app-service/containers/quickstart-dotnetcore)  
<span data-ttu-id="ca6df-111">Pomocí příkazového řádku můžete vytvořit a nasadit webovou aplikaci ASP.NET Core pro Azure App Service v systému Linux.</span><span class="sxs-lookup"><span data-stu-id="ca6df-111">Use the command line to create and deploy an ASP.NET Core web app to Azure App Service on Linux.</span></span>

<span data-ttu-id="ca6df-112">Verzi ASP.NET Core dostupnou ve službě Azure App Service najdete na [App Service řídicím panelu ASP.NET Core](https://aspnetcoreon.azurewebsites.net/) .</span><span class="sxs-lookup"><span data-stu-id="ca6df-112">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

<span data-ttu-id="ca6df-113">Přihlaste se k odběru úložiště [oznámení App Service](https://github.com/Azure/app-service-announcements/) a sledujte problémy.</span><span class="sxs-lookup"><span data-stu-id="ca6df-113">Subscribe to the [App Service Announcements](https://github.com/Azure/app-service-announcements/) repository and monitor the issues.</span></span> <span data-ttu-id="ca6df-114">Tým App Service pravidelně publikuje oznámení a scénáře, které dorazí v App Service.</span><span class="sxs-lookup"><span data-stu-id="ca6df-114">The App Service team regularly posts announcements and scenarios arriving in App Service.</span></span>

<span data-ttu-id="ca6df-115">V dokumentaci k ASP.NET Core jsou k dispozici následující články:</span><span class="sxs-lookup"><span data-stu-id="ca6df-115">The following articles are available in ASP.NET Core documentation:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="ca6df-116">Zjistěte, jak publikovat aplikace ASP.NET Core do služby Azure App Service pomocí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ca6df-116">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
<span data-ttu-id="ca6df-117">Naučte se, jak vytvořit webovou aplikaci ASP.NET Core pomocí sady Visual Studio a nasadit ji do Azure App Service pomocí Gitu pro průběžné nasazování.</span><span class="sxs-lookup"><span data-stu-id="ca6df-117">Learn how to create an ASP.NET Core web app using Visual Studio and deploy it to Azure App Service using Git for continuous deployment.</span></span>

[<span data-ttu-id="ca6df-118">Vytvoření prvního kanálu</span><span class="sxs-lookup"><span data-stu-id="ca6df-118">Create your first pipeline</span></span>](/azure/devops/pipelines/get-started-yaml)  
<span data-ttu-id="ca6df-119">Nastavte sestavení CI pro aplikaci ASP.NET Core a pak vytvořte Azure App Service vydanou verzi pro průběžné nasazování.</span><span class="sxs-lookup"><span data-stu-id="ca6df-119">Set up a CI build for an ASP.NET Core app, then create a continuous deployment release to Azure App Service.</span></span>

[<span data-ttu-id="ca6df-120">Sandbox webové aplikace Azure</span><span class="sxs-lookup"><span data-stu-id="ca6df-120">Azure Web App sandbox</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
<span data-ttu-id="ca6df-121">Zjištění omezení spouštění Azure App Service runtime, která vynutila platforma Azure Apps</span><span class="sxs-lookup"><span data-stu-id="ca6df-121">Discover Azure App Service runtime execution limitations enforced by the Azure Apps platform.</span></span>

<xref:test/troubleshoot>  
<span data-ttu-id="ca6df-122">Pochopení a řešení potíží s chybami a problémy s ASP.NET Core projekty.</span><span class="sxs-lookup"><span data-stu-id="ca6df-122">Understand and troubleshoot warnings and errors with ASP.NET Core projects.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="ca6df-123">Konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="ca6df-123">Application configuration</span></span>

### <a name="platform"></a><span data-ttu-id="ca6df-124">Platforma</span><span class="sxs-lookup"><span data-stu-id="ca6df-124">Platform</span></span>

<span data-ttu-id="ca6df-125">Architektura platformy (x86/x64) aplikace App Services je nastavená v nastavení aplikace na webu Azure Portal pro aplikace, které jsou hostované v úrovni hostování A-Series COMPUTE (Basic) nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="ca6df-125">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="ca6df-126">Ověřte, že nastavení publikování aplikace (například v profilu publikování sady Visual Studio [(. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) odpovídá nastavení v konfiguraci služby aplikace na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="ca6df-126">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure Portal.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="ca6df-127">V Azure App Service jsou k dispozici moduly runtime pro 64 (x64) a 32-bit (x86) aplikací.</span><span class="sxs-lookup"><span data-stu-id="ca6df-127">Runtimes for 64-bit (x64) and 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="ca6df-128">[.NET Core SDK](/dotnet/core/sdk) k dispozici v App Service je 32-bit, ale můžete nasadit 64 aplikace sestavené místně pomocí konzoly [Kudu](https://github.com/projectkudu/kudu/wiki) nebo procesu publikování v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ca6df-128">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit, but you can deploy 64-bit apps built locally using the [Kudu](https://github.com/projectkudu/kudu/wiki) console or the publish process in Visual Studio.</span></span> <span data-ttu-id="ca6df-129">Další informace najdete v části [publikování a nasazení aplikace](#publish-and-deploy-the-app) .</span><span class="sxs-lookup"><span data-stu-id="ca6df-129">For more information, see the [Publish and deploy the app](#publish-and-deploy-the-app) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="ca6df-130">Pro aplikace s nativními závislostmi jsou moduly runtime pro 32 aplikace (x86) k dispozici na Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="ca6df-130">For apps with native dependencies, runtimes for 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="ca6df-131">[.NET Core SDK](/dotnet/core/sdk) k dispozici v App Service je 32-bit.</span><span class="sxs-lookup"><span data-stu-id="ca6df-131">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit.</span></span>

::: moniker-end

<span data-ttu-id="ca6df-132">Další informace o komponentách rozhraní .NET Core Framework a metodách distribuce, jako jsou informace o modulu runtime .NET Core a .NET Core SDK, naleznete v tématu [About .NET Core: kompozice](/dotnet/core/about#composition).</span><span class="sxs-lookup"><span data-stu-id="ca6df-132">For more information on .NET Core framework components and distribution methods, such as information on the .NET Core runtime and the .NET Core SDK, see [About .NET Core: Composition](/dotnet/core/about#composition).</span></span>

### <a name="packages"></a><span data-ttu-id="ca6df-133">Balíčky</span><span class="sxs-lookup"><span data-stu-id="ca6df-133">Packages</span></span>

<span data-ttu-id="ca6df-134">Zahrňte následující balíčky NuGet pro poskytování automatických funkcí protokolování pro aplikace nasazené do Azure App Service:</span><span class="sxs-lookup"><span data-stu-id="ca6df-134">Include the following NuGet packages to provide automatic logging features for apps deployed to Azure App Service:</span></span>

* <span data-ttu-id="ca6df-135">[Microsoft. AspNetCore. AzureAppServices. HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) využívá [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) k poskytování ASP.NET Coreho urychlené integrace s Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="ca6df-135">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) uses [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) to provide ASP.NET Core light-up integration with Azure App Service.</span></span> <span data-ttu-id="ca6df-136">Přidané funkce protokolování jsou k dispozici v balíčku `Microsoft.AspNetCore.AzureAppServicesIntegration`.</span><span class="sxs-lookup"><span data-stu-id="ca6df-136">The added logging features are provided by the `Microsoft.AspNetCore.AzureAppServicesIntegration` package.</span></span>
* <span data-ttu-id="ca6df-137">[Microsoft. AspNetCore. AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) spustí [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) pro Azure App Service přidání zprostředkovatelů protokolování diagnostiky do balíčku `Microsoft.Extensions.Logging.AzureAppServices`.</span><span class="sxs-lookup"><span data-stu-id="ca6df-137">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executes [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) to add Azure App Service diagnostics logging providers in the `Microsoft.Extensions.Logging.AzureAppServices` package.</span></span>
* <span data-ttu-id="ca6df-138">[Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) poskytuje implementace protokolovacích nástrojů pro podporu Azure App Servicech diagnostických protokolů a funkcí streamování protokolů.</span><span class="sxs-lookup"><span data-stu-id="ca6df-138">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) provides logger implementations to support Azure App Service diagnostics logs and log streaming features.</span></span>

<span data-ttu-id="ca6df-139">Předchozí balíčky nejsou k dispozici ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="ca6df-139">The preceding packages aren't available from the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="ca6df-140">Aplikace, které cílí na .NET Framework nebo odkazují na `Microsoft.AspNetCore.App` Metapackage musí explicitně odkazovat na jednotlivé balíčky v souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="ca6df-140">Apps that target .NET Framework or reference the `Microsoft.AspNetCore.App` metapackage must explicitly reference the individual packages in the app's project file.</span></span>

## <a name="override-app-configuration-using-the-azure-portal"></a><span data-ttu-id="ca6df-141">Přepsání konfigurace aplikace pomocí webu Azure Portal</span><span class="sxs-lookup"><span data-stu-id="ca6df-141">Override app configuration using the Azure Portal</span></span>

<span data-ttu-id="ca6df-142">Nastavení aplikace na webu Azure Portal umožňuje nastavit proměnné prostředí pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ca6df-142">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="ca6df-143">Proměnné prostředí mohou být spotřebovány [poskytovatelem konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ca6df-143">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="ca6df-144">Když se na webu Azure Portal vytvoří nebo upraví nastavení aplikace a vybere se tlačítko **Save (Uložit** ), aplikace Azure se restartuje.</span><span class="sxs-lookup"><span data-stu-id="ca6df-144">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="ca6df-145">Proměnná prostředí je k dispozici aplikaci po restartování služby.</span><span class="sxs-lookup"><span data-stu-id="ca6df-145">The environment variable is available to the app after the service restarts.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ca6df-146">Když aplikace používá [obecného hostitele](xref:fundamentals/host/generic-host), proměnné prostředí se načtou do konfigurace aplikace, když se zavolá <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> k sestavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="ca6df-146">When an app uses the [Generic Host](xref:fundamentals/host/generic-host), environment variables are loaded into the app's configuration when <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="ca6df-147">Další informace najdete v tématu <xref:fundamentals/host/generic-host> a ve [zprostředkovateli konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ca6df-147">For more information, see <xref:fundamentals/host/generic-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ca6df-148">Když aplikace používá [webového hostitele](xref:fundamentals/host/web-host), proměnné prostředí se načtou do konfigurace aplikace, když se zavolá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> k sestavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="ca6df-148">When an app uses the [Web Host](xref:fundamentals/host/web-host), environment variables are loaded into the app's configuration when <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="ca6df-149">Další informace najdete v tématu <xref:fundamentals/host/web-host> a ve [zprostředkovateli konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ca6df-149">For more information, see <xref:fundamentals/host/web-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="ca6df-150">Proxy server a scénáře pro nástroj pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="ca6df-150">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="ca6df-151">[IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), který konfiguruje přesměrované hlavičky middlewaru při hostování [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model)a modul ASP.NET Core je nakonfigurován pro přeposílání schématu (http/https) a vzdálené IP adresy, na které pochází požadavek.</span><span class="sxs-lookup"><span data-stu-id="ca6df-151">The [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), which configures Forwarded Headers Middleware when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="ca6df-152">Další konfigurace může být nezbytný pro aplikací hostovaných za službou další proxy servery a nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="ca6df-152">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="ca6df-153">Další informace najdete v tématu [konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="ca6df-153">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="monitoring-and-logging"></a><span data-ttu-id="ca6df-154">Monitorování a protokolování</span><span class="sxs-lookup"><span data-stu-id="ca6df-154">Monitoring and logging</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ca6df-155">Aplikace ASP.NET Core nasazené pro App Service automaticky dostanou rozšíření App Service **ASP.NET Core Integration Logging**.</span><span class="sxs-lookup"><span data-stu-id="ca6df-155">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Integration**.</span></span> <span data-ttu-id="ca6df-156">Rozšíření umožňuje integraci protokolování pro aplikace ASP.NET Core v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="ca6df-156">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ca6df-157">Aplikace ASP.NET Core nasazené pro App Service automaticky dostanou rozšíření App Service **ASP.NET Core rozšíření protokolování**.</span><span class="sxs-lookup"><span data-stu-id="ca6df-157">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Extensions**.</span></span> <span data-ttu-id="ca6df-158">Rozšíření umožňuje integraci protokolování pro aplikace ASP.NET Core v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="ca6df-158">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

<span data-ttu-id="ca6df-159">Informace o monitorování, protokolování a odstraňování potíží najdete v následujících článcích:</span><span class="sxs-lookup"><span data-stu-id="ca6df-159">For monitoring, logging, and troubleshooting information, see the following articles:</span></span>

[<span data-ttu-id="ca6df-160">Monitorování aplikací v Azure App Service</span><span class="sxs-lookup"><span data-stu-id="ca6df-160">Monitor apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)  
<span data-ttu-id="ca6df-161">Naučte se kontrolovat kvóty a metriky pro aplikace a App Service plány.</span><span class="sxs-lookup"><span data-stu-id="ca6df-161">Learn how to review quotas and metrics for apps and App Service plans.</span></span>

[<span data-ttu-id="ca6df-162">Povolit protokolování diagnostiky pro aplikace v Azure App Service</span><span class="sxs-lookup"><span data-stu-id="ca6df-162">Enable diagnostics logging for apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)  
<span data-ttu-id="ca6df-163">Zjistěte, jak povolit a získat přístup k protokolování diagnostiky pro stavové kódy HTTP, neúspěšné žádosti a aktivitu webového serveru.</span><span class="sxs-lookup"><span data-stu-id="ca6df-163">Discover how to enable and access diagnostic logging for HTTP status codes, failed requests, and web server activity.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="ca6df-164">Seznamte se s běžnými přístupy ke zpracování chyb v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca6df-164">Understand common approaches to handling errors in ASP.NET Core apps.</span></span>

<xref:test/troubleshoot-azure-iis>  
<span data-ttu-id="ca6df-165">Naučte se diagnostikovat problémy s Azure App Service nasazeními s ASP.NET Core aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="ca6df-165">Learn how to diagnose issues with Azure App Service deployments with ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-iis-errors-reference>  
<span data-ttu-id="ca6df-166">Projděte si nejčastější chyby konfigurace nasazení pro aplikace hostované službou Azure App Service/IIS s pokyny k řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="ca6df-166">See the common deployment configuration errors for apps hosted by Azure App Service/IIS with troubleshooting advice.</span></span>

## <a name="data-protection-key-ring-and-deployment-slots"></a><span data-ttu-id="ca6df-167">Kroužky klíčů pro ochranu dat a sloty nasazení</span><span class="sxs-lookup"><span data-stu-id="ca6df-167">Data Protection key ring and deployment slots</span></span>

<span data-ttu-id="ca6df-168">[Klíče ochrany dat](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) jsou trvale uložené ve složce *%Home%\ASP.NET\DataProtection-Keys* .</span><span class="sxs-lookup"><span data-stu-id="ca6df-168">[Data Protection keys](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="ca6df-169">Tato složka je zajištěná síťovým úložištěm a je synchronizovaná napříč všemi počítači hostujícími aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ca6df-169">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span> <span data-ttu-id="ca6df-170">Klíče nejsou chráněny v klidovém umístění.</span><span class="sxs-lookup"><span data-stu-id="ca6df-170">Keys aren't protected at rest.</span></span> <span data-ttu-id="ca6df-171">Tato složka poskytuje klíčového prstence pro všechny instance aplikace v jednom slotu nasazení.</span><span class="sxs-lookup"><span data-stu-id="ca6df-171">This folder supplies the key ring to all instances of an app in a single deployment slot.</span></span> <span data-ttu-id="ca6df-172">Samostatné sloty nasazení, jako je například pracovní a produkční, nesdílejte klíč Ring.</span><span class="sxs-lookup"><span data-stu-id="ca6df-172">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span>

<span data-ttu-id="ca6df-173">Při záměně mezi sloty nasazení nebude možné dešifrovat uložená data pomocí nástroje Data Protection, a to pomocí klíčeového kruhu v předchozí pozici.</span><span class="sxs-lookup"><span data-stu-id="ca6df-173">When swapping between deployment slots, any system using data protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="ca6df-174">Middleware souborů cookie ASP.NET používá ochranu dat k ochraně souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="ca6df-174">ASP.NET Cookie Middleware uses data protection to protect its cookies.</span></span> <span data-ttu-id="ca6df-175">To vede k tomu, že se uživatelé odhlásí z aplikace, která používá standardní middleware souborů cookie ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="ca6df-175">This leads to users being signed out of an app that uses the standard ASP.NET Cookie Middleware.</span></span> <span data-ttu-id="ca6df-176">Pro řešení Key Ring nezávislé na slotech použijte externího poskytovatele služby Key Ring, například:</span><span class="sxs-lookup"><span data-stu-id="ca6df-176">For a slot-independent key ring solution, use an external key ring provider, such as:</span></span>

* <span data-ttu-id="ca6df-177">Azure Blob Storage</span><span class="sxs-lookup"><span data-stu-id="ca6df-177">Azure Blob Storage</span></span>
* <span data-ttu-id="ca6df-178">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="ca6df-178">Azure Key Vault</span></span>
* <span data-ttu-id="ca6df-179">Úložiště SQL</span><span class="sxs-lookup"><span data-stu-id="ca6df-179">SQL store</span></span>
* <span data-ttu-id="ca6df-180">Redis Cache</span><span class="sxs-lookup"><span data-stu-id="ca6df-180">Redis cache</span></span>

<span data-ttu-id="ca6df-181">Další informace najdete v tématu <xref:security/data-protection/implementation/key-storage-providers>.</span><span class="sxs-lookup"><span data-stu-id="ca6df-181">For more information, see <xref:security/data-protection/implementation/key-storage-providers>.</span></span>
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>

## <a name="deploy-an-aspnet-core-app-that-uses-a-net-core-preview"></a><span data-ttu-id="ca6df-182">Nasazení aplikace ASP.NET Core, která používá .NET Core Preview</span><span class="sxs-lookup"><span data-stu-id="ca6df-182">Deploy an ASP.NET Core app that uses a .NET Core preview</span></span>

<span data-ttu-id="ca6df-183">Pokud chcete nasadit aplikaci, která používá verzi Preview rozhraní .NET Core, Projděte si následující zdroje informací.</span><span class="sxs-lookup"><span data-stu-id="ca6df-183">To deploy an app that uses a preview release of .NET Core, see the following resources.</span></span> <span data-ttu-id="ca6df-184">Tyto přístupy se používají také v případě, že je modul runtime k dispozici, ale sada SDK nebyla nainstalována na Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="ca6df-184">These approaches are also used when the runtime is available but the SDK hasn't been installed on Azure App Service.</span></span>

* [<span data-ttu-id="ca6df-185">Určení verze .NET Core SDK pomocí Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="ca6df-185">Specify the .NET Core SDK Version using Azure Pipelines</span></span>](#specify-the-net-core-sdk-version-using-azure-pipelines)
* [<span data-ttu-id="ca6df-186">Nasazení samostatné aplikace ve verzi Preview</span><span class="sxs-lookup"><span data-stu-id="ca6df-186">Deploy a self-contained preview app</span></span>](#deploy-a-self-contained-preview-app)
* [<span data-ttu-id="ca6df-187">Použití Docker s Web Apps pro kontejnery</span><span class="sxs-lookup"><span data-stu-id="ca6df-187">Use Docker with Web Apps for containers</span></span>](#use-docker-with-web-apps-for-containers)
* [<span data-ttu-id="ca6df-188">Nainstalovat rozšíření webu verze Preview</span><span class="sxs-lookup"><span data-stu-id="ca6df-188">Install the preview site extension</span></span>](#install-the-preview-site-extension)

<span data-ttu-id="ca6df-189">Verzi ASP.NET Core dostupnou ve službě Azure App Service najdete na [App Service řídicím panelu ASP.NET Core](https://aspnetcoreon.azurewebsites.net/) .</span><span class="sxs-lookup"><span data-stu-id="ca6df-189">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

### <a name="specify-the-net-core-sdk-version-using-azure-pipelines"></a><span data-ttu-id="ca6df-190">Určení verze .NET Core SDK pomocí Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="ca6df-190">Specify the .NET Core SDK Version using Azure Pipelines</span></span>

<span data-ttu-id="ca6df-191">Pomocí [Azure App Service scénářů CI/CD](/azure/app-service/deploy-continuous-deployment) můžete nastavit průběžnou integraci sestavení s využitím Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="ca6df-191">Use [Azure App Service CI/CD scenarios](/azure/app-service/deploy-continuous-deployment) to set up a continuous integration build with Azure DevOps.</span></span> <span data-ttu-id="ca6df-192">Po vytvoření sestavení Azure DevOps můžete nakonfigurovat sestavení tak, aby používalo konkrétní verzi sady SDK.</span><span class="sxs-lookup"><span data-stu-id="ca6df-192">After the Azure DevOps build is created, optionally configure the build to use a specific SDK version.</span></span> 

#### <a name="specify-the-net-core-sdk-version"></a><span data-ttu-id="ca6df-193">Zadejte verzi .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="ca6df-193">Specify the .NET Core SDK version</span></span>

<span data-ttu-id="ca6df-194">Při použití centra nasazení App Service k vytvoření buildu Azure DevOps obsahuje výchozí kanál sestavení kroky pro `Restore`, `Build`, `Test`a `Publish`.</span><span class="sxs-lookup"><span data-stu-id="ca6df-194">When using the App Service deployment center to create an Azure DevOps build, the default build pipeline includes steps for `Restore`, `Build`, `Test`, and `Publish`.</span></span> <span data-ttu-id="ca6df-195">Chcete-li zadat verzi sady SDK, přidejte nový krok kliknutím na tlačítko **Přidat (+)** v seznamu úloh agenta.</span><span class="sxs-lookup"><span data-stu-id="ca6df-195">To specify the SDK version, select the **Add (+)** button in the Agent job list to add a new step.</span></span> <span data-ttu-id="ca6df-196">Na panelu hledání vyhledejte **.NET Core SDK** .</span><span class="sxs-lookup"><span data-stu-id="ca6df-196">Search for **.NET Core SDK** in the search bar.</span></span> 

![Přidat .NET Core SDK krok](index/add-sdk-step.png)

<span data-ttu-id="ca6df-198">Přesuňte krok do první pozice v sestavení tak, aby následující kroky používaly určenou verzi .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="ca6df-198">Move the step into the first position in the build so that the steps following it use the specified version of the .NET Core SDK.</span></span> <span data-ttu-id="ca6df-199">Zadejte verzi .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="ca6df-199">Specify the version of the .NET Core SDK.</span></span> <span data-ttu-id="ca6df-200">V tomto příkladu je sada SDK nastavena na `3.0.100`.</span><span class="sxs-lookup"><span data-stu-id="ca6df-200">In this example, the SDK is set to `3.0.100`.</span></span>

![Krok dokončené sady SDK](index/sdk-step-first-place.png)

<span data-ttu-id="ca6df-202">Chcete-li publikovat [samostatné nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), nakonfigurujte SCD v kroku `Publish` a zadejte [identifikátor modulu runtime (RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="ca6df-202">To publish a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), configure SCD in the `Publish` step and provide the [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

![Publikování samostatného kontejneru](index/self-contained.png)

### <a name="deploy-a-self-contained-preview-app"></a><span data-ttu-id="ca6df-204">Nasazení samostatné aplikace ve verzi Preview</span><span class="sxs-lookup"><span data-stu-id="ca6df-204">Deploy a self-contained preview app</span></span>

<span data-ttu-id="ca6df-205">[Samostatné nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) , které cílí na modul runtime verze Preview, přenáší modul runtime verze Preview do nasazení.</span><span class="sxs-lookup"><span data-stu-id="ca6df-205">A [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) that targets a preview runtime carries the preview runtime in the deployment.</span></span>

<span data-ttu-id="ca6df-206">Při nasazení samostatně obsažené aplikace:</span><span class="sxs-lookup"><span data-stu-id="ca6df-206">When deploying a self-contained app:</span></span>

* <span data-ttu-id="ca6df-207">Lokalita v Azure App Service nevyžaduje [rozšíření webu verze Preview](#install-the-preview-site-extension).</span><span class="sxs-lookup"><span data-stu-id="ca6df-207">The site in Azure App Service doesn't require the [preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="ca6df-208">Aplikace musí být publikována při jiném přístupu než při publikování pro [nasazení závislé na rozhraní (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="ca6df-208">The app must be published following a different approach than when publishing for a [framework-dependent deployment (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="ca6df-209">Postupujte podle pokynů v části [nasazení samostatně obsaženého oddílu aplikace](#deploy-the-app-self-contained) .</span><span class="sxs-lookup"><span data-stu-id="ca6df-209">Follow the guidance in the [Deploy the app self-contained](#deploy-the-app-self-contained) section.</span></span>

### <a name="use-docker-with-web-apps-for-containers"></a><span data-ttu-id="ca6df-210">Použití Docker s Web Apps pro kontejnery</span><span class="sxs-lookup"><span data-stu-id="ca6df-210">Use Docker with Web Apps for containers</span></span>

<span data-ttu-id="ca6df-211">[Dokovací centrum](https://hub.docker.com/r/microsoft/aspnetcore/) obsahuje nejnovější image Docker Preview.</span><span class="sxs-lookup"><span data-stu-id="ca6df-211">The [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contains the latest preview Docker images.</span></span> <span data-ttu-id="ca6df-212">Obrázky lze použít jako základní bitovou kopii.</span><span class="sxs-lookup"><span data-stu-id="ca6df-212">The images can be used as a base image.</span></span> <span data-ttu-id="ca6df-213">Použijte image a nasaďte ji normálně pro Web Apps kontejnerů.</span><span class="sxs-lookup"><span data-stu-id="ca6df-213">Use the image and deploy to Web Apps for Containers normally.</span></span>

### <a name="install-the-preview-site-extension"></a><span data-ttu-id="ca6df-214">Nainstalovat rozšíření webu verze Preview</span><span class="sxs-lookup"><span data-stu-id="ca6df-214">Install the preview site extension</span></span>

<span data-ttu-id="ca6df-215">Pokud dojde k potížím pomocí rozšíření webu verze Preview, otevřete [problém ASPNET/AspNetCore](https://github.com/aspnet/AspNetCore/issues).</span><span class="sxs-lookup"><span data-stu-id="ca6df-215">If a problem occurs using the preview site extension, open an [aspnet/AspNetCore issue](https://github.com/aspnet/AspNetCore/issues).</span></span>

1. <span data-ttu-id="ca6df-216">Na webu Azure Portal přejděte na App Service.</span><span class="sxs-lookup"><span data-stu-id="ca6df-216">From the Azure Portal, navigate to the App Service.</span></span>
1. <span data-ttu-id="ca6df-217">Vyberte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ca6df-217">Select the web app.</span></span>
1. <span data-ttu-id="ca6df-218">Do vyhledávacího pole zadejte "ex", chcete-li filtrovat "rozšíření", nebo se posuňte dolů v seznamu nástrojů pro správu.</span><span class="sxs-lookup"><span data-stu-id="ca6df-218">Type "ex" in the search box to filter for "Extensions" or scroll down the list of management tools.</span></span>
1. <span data-ttu-id="ca6df-219">Vyberte **Rozšíření**.</span><span class="sxs-lookup"><span data-stu-id="ca6df-219">Select **Extensions**.</span></span>
1. <span data-ttu-id="ca6df-220">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="ca6df-220">Select **Add**.</span></span>
1. <span data-ttu-id="ca6df-221">V seznamu vyberte rozšíření **Runtime ASP.NET Core {X. Y} ({x64 | x86})** , kde `{X.Y}` je ASP.NET Core verze preview a `{x64|x86}` Určuje platformu.</span><span class="sxs-lookup"><span data-stu-id="ca6df-221">Select the **ASP.NET Core {X.Y} ({x64|x86}) Runtime** extension from the list, where `{X.Y}` is the ASP.NET Core preview version and `{x64|x86}` specifies the platform.</span></span>
1. <span data-ttu-id="ca6df-222">Kliknutím na **OK** přijměte právní podmínky.</span><span class="sxs-lookup"><span data-stu-id="ca6df-222">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="ca6df-223">Pro instalaci rozšíření vyberte **OK** .</span><span class="sxs-lookup"><span data-stu-id="ca6df-223">Select **OK** to install the extension.</span></span>

<span data-ttu-id="ca6df-224">Po dokončení operace je nainstalovaná nejnovější verze .NET Core Preview.</span><span class="sxs-lookup"><span data-stu-id="ca6df-224">When the operation completes, the latest .NET Core preview is installed.</span></span> <span data-ttu-id="ca6df-225">Ověřte instalaci:</span><span class="sxs-lookup"><span data-stu-id="ca6df-225">Verify the installation:</span></span>

1. <span data-ttu-id="ca6df-226">Vyberte **Rozšířené nástroje**.</span><span class="sxs-lookup"><span data-stu-id="ca6df-226">Select **Advanced Tools**.</span></span>
1. <span data-ttu-id="ca6df-227">Vyberte **Přejít** v **pokročilých nástrojích**.</span><span class="sxs-lookup"><span data-stu-id="ca6df-227">Select **Go** in **Advanced Tools**.</span></span>
1. <span data-ttu-id="ca6df-228">Vyberte **konzolu ladění** > položku nabídky **PowerShellu** .</span><span class="sxs-lookup"><span data-stu-id="ca6df-228">Select the **Debug console** > **PowerShell** menu item.</span></span>
1. <span data-ttu-id="ca6df-229">Na příkazovém řádku PowerShellu spusťte následující příkaz.</span><span class="sxs-lookup"><span data-stu-id="ca6df-229">At the PowerShell prompt, execute the following command.</span></span> <span data-ttu-id="ca6df-230">Nahraďte ASP.NET Core verze modulu runtime pro `{X.Y}` a platformu pro `{PLATFORM}` v příkazu:</span><span class="sxs-lookup"><span data-stu-id="ca6df-230">Substitute the ASP.NET Core runtime version for `{X.Y}` and the platform for `{PLATFORM}` in the command:</span></span>

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   <span data-ttu-id="ca6df-231">Příkaz vrátí `True`, když je nainstalován modul runtime x64 Preview.</span><span class="sxs-lookup"><span data-stu-id="ca6df-231">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="ca6df-232">Architektura platformy (x86/x64) aplikace App Services je nastavená v nastavení aplikace na webu Azure Portal pro aplikace, které jsou hostované v úrovni hostování A-Series COMPUTE (Basic) nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="ca6df-232">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="ca6df-233">Ověřte, že nastavení publikování aplikace (například v profilu publikování sady Visual Studio [(. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) odpovídá nastavení v konfiguraci služby aplikace v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="ca6df-233">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure portal.</span></span>
>
> <span data-ttu-id="ca6df-234">Pokud je aplikace spuštěná v režimu v procesu a architektura platformy je nakonfigurovaná pro 64-bit (x64), modul ASP.NET Core používá 64 runtime Preview, pokud je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="ca6df-234">If the app is run in in-process mode and the platform architecture is configured for 64-bit (x64), the ASP.NET Core Module uses the 64-bit preview runtime, if present.</span></span> <span data-ttu-id="ca6df-235">Nainstalujte rozšíření **Runtime ASP.NET Core {X. Y} (x64)** pomocí webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="ca6df-235">Install the **ASP.NET Core {X.Y} (x64) Runtime** extension using the Azure Portal.</span></span>
>
> <span data-ttu-id="ca6df-236">Po instalaci prostředí runtime x64 Preview spusťte následující příkaz v příkazovém okně Azure Kudu PowerShell a ověřte instalaci.</span><span class="sxs-lookup"><span data-stu-id="ca6df-236">After installing the x64 preview runtime, run the following command in the Azure Kudu PowerShell command window to verify the installation.</span></span> <span data-ttu-id="ca6df-237">V následujícím příkazu nahraďte verzi ASP.NET Core Runtime pro `{X.Y}`:</span><span class="sxs-lookup"><span data-stu-id="ca6df-237">Substitute the ASP.NET Core runtime version for `{X.Y}` in the following command:</span></span>
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> <span data-ttu-id="ca6df-238">Příkaz vrátí `True`, když je nainstalován modul runtime x64 Preview.</span><span class="sxs-lookup"><span data-stu-id="ca6df-238">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="ca6df-239">**Rozšíření ASP.NET Core** umožňují pro ASP.NET Core v Azure App Services další funkce, jako je například povolení protokolování Azure.</span><span class="sxs-lookup"><span data-stu-id="ca6df-239">**ASP.NET Core Extensions** enables additional functionality for ASP.NET Core on Azure App Services, such as enabling Azure logging.</span></span> <span data-ttu-id="ca6df-240">Rozšíření se nainstaluje automaticky při nasazení ze sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ca6df-240">The extension is installed automatically when deploying from Visual Studio.</span></span> <span data-ttu-id="ca6df-241">Pokud rozšíření není nainstalované, nainstalujte ho pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ca6df-241">If the extension isn't installed, install it for the app.</span></span>

<span data-ttu-id="ca6df-242">**Použití rozšíření webu ve verzi Preview se šablonou ARM**</span><span class="sxs-lookup"><span data-stu-id="ca6df-242">**Use the preview site extension with an ARM template**</span></span>

<span data-ttu-id="ca6df-243">Pokud se k vytváření a nasazování aplikací používá šablona ARM, dá se `siteextensions` typ prostředku použít k přidání rozšíření webu do webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="ca6df-243">If an ARM template is used to create and deploy apps, the `siteextensions` resource type can be used to add the site extension to a web app.</span></span> <span data-ttu-id="ca6df-244">Příklad:</span><span class="sxs-lookup"><span data-stu-id="ca6df-244">For example:</span></span>

[!code-json[](index/sample/arm.json?highlight=2)]

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="ca6df-245">Publikování a nasazení aplikace</span><span class="sxs-lookup"><span data-stu-id="ca6df-245">Publish and deploy the app</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="ca6df-246">Pro 64 nasazení:</span><span class="sxs-lookup"><span data-stu-id="ca6df-246">For a 64-bit deployment:</span></span>

* <span data-ttu-id="ca6df-247">K vytvoření 64 aplikace použijte 64 .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="ca6df-247">Use a 64-bit .NET Core SDK to build a 64-bit app.</span></span>
* <span data-ttu-id="ca6df-248">Nastavte hodnotu **Platform** na **64 Bit** v **konfiguraci** App Service > **Obecné nastavení**.</span><span class="sxs-lookup"><span data-stu-id="ca6df-248">Set the **Platform** to **64 Bit** in the App Service's **Configuration** > **General settings**.</span></span> <span data-ttu-id="ca6df-249">Aby bylo možné vybrat bitová verze platformy, musí aplikace používat základní nebo vyšší plán služby.</span><span class="sxs-lookup"><span data-stu-id="ca6df-249">The app must use a Basic or higher service plan to enable the choice of platform bitness.</span></span>

::: moniker-end

### <a name="deploy-the-app-framework-dependent"></a><span data-ttu-id="ca6df-250">Nasazení závislého rozhraní App Framework</span><span class="sxs-lookup"><span data-stu-id="ca6df-250">Deploy the app framework-dependent</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ca6df-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca6df-251">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ca6df-252">Vyberte **Build** > **Publikovat {název aplikace}** z panelu nástrojů sady Visual Studio nebo klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="ca6df-252">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="ca6df-253">V dialogovém okně **vybrat cíl publikování** potvrďte, že je vybrána možnost **App Service** .</span><span class="sxs-lookup"><span data-stu-id="ca6df-253">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="ca6df-254">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="ca6df-254">Select **Advanced**.</span></span> <span data-ttu-id="ca6df-255">Otevře se dialogové okno **publikovat** .</span><span class="sxs-lookup"><span data-stu-id="ca6df-255">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="ca6df-256">V **publikovat** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="ca6df-256">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="ca6df-257">Potvrďte, že je vybraná konfigurace **vydané verze** .</span><span class="sxs-lookup"><span data-stu-id="ca6df-257">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="ca6df-258">Otevřete rozevírací seznam **režim nasazení** a vyberte možnost závislé na **rozhraní**.</span><span class="sxs-lookup"><span data-stu-id="ca6df-258">Open the **Deployment Mode** drop-down list and select **Framework-Dependent**.</span></span>
   * <span data-ttu-id="ca6df-259">Jako **cílový modul runtime**vyberte **přenosný** .</span><span class="sxs-lookup"><span data-stu-id="ca6df-259">Select **Portable** as the **Target Runtime**.</span></span>
   * <span data-ttu-id="ca6df-260">Pokud potřebujete po nasazení odebrat další soubory, otevřete **Možnosti publikování souborů** a zaškrtnutím políčka odeberte další soubory v cílovém umístění.</span><span class="sxs-lookup"><span data-stu-id="ca6df-260">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="ca6df-261">Vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="ca6df-261">Select **Save**.</span></span>
1. <span data-ttu-id="ca6df-262">Pomocí zbývajících výzev Průvodce publikováním vytvořte novou lokalitu nebo aktualizujte existující web.</span><span class="sxs-lookup"><span data-stu-id="ca6df-262">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="ca6df-263">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="ca6df-263">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="ca6df-264">V souboru projektu nezadávejte [identifikátor modulu runtime (RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="ca6df-264">In the project file, don't specify a [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

1. <span data-ttu-id="ca6df-265">V příkazovém prostředí publikujte aplikaci v konfiguraci vydaných verzí pomocí příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="ca6df-265">From a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="ca6df-266">V následujícím příkladu je aplikace publikována jako aplikace závislá na rozhraní:</span><span class="sxs-lookup"><span data-stu-id="ca6df-266">In the following example, the app is published as a framework-dependent app:</span></span>

   ```console
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="ca6df-267">Přesuňte obsah adresáře *bin/Release/{Target Framework}/Publish* do lokality v App Service.</span><span class="sxs-lookup"><span data-stu-id="ca6df-267">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="ca6df-268">Pokud přetáhnete obsah složky pro *publikování* z místního pevného disku nebo sdílené síťové složky přímo do App Service v konzole [Kudu](https://github.com/projectkudu/kudu/wiki) , přetáhněte soubory do složky `D:\home\site\wwwroot` v konzole Kudu.</span><span class="sxs-lookup"><span data-stu-id="ca6df-268">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the [Kudu](https://github.com/projectkudu/kudu/wiki) console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

### <a name="deploy-the-app-self-contained"></a><span data-ttu-id="ca6df-269">Nasazení samostatně obsažené aplikace</span><span class="sxs-lookup"><span data-stu-id="ca6df-269">Deploy the app self-contained</span></span>

<span data-ttu-id="ca6df-270">Použijte nástroje sady Visual Studio nebo rozhraní příkazového řádku (CLI) pro [samostatně uzavřené nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="ca6df-270">Use Visual Studio or the command-line interface (CLI) tools for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ca6df-271">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca6df-271">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ca6df-272">Vyberte **Build** > **Publikovat {název aplikace}** z panelu nástrojů sady Visual Studio nebo klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="ca6df-272">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="ca6df-273">V dialogovém okně **vybrat cíl publikování** potvrďte, že je vybrána možnost **App Service** .</span><span class="sxs-lookup"><span data-stu-id="ca6df-273">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="ca6df-274">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="ca6df-274">Select **Advanced**.</span></span> <span data-ttu-id="ca6df-275">Otevře se dialogové okno **publikovat** .</span><span class="sxs-lookup"><span data-stu-id="ca6df-275">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="ca6df-276">V **publikovat** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="ca6df-276">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="ca6df-277">Potvrďte, že je vybraná konfigurace **vydané verze** .</span><span class="sxs-lookup"><span data-stu-id="ca6df-277">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="ca6df-278">Otevřete rozevírací seznam **režim nasazení** a vyberte možnost **samostatně obsaženo**.</span><span class="sxs-lookup"><span data-stu-id="ca6df-278">Open the **Deployment Mode** drop-down list and select **Self-Contained**.</span></span>
   * <span data-ttu-id="ca6df-279">Z rozevíracího seznamu **cílový modul runtime** vyberte cílový modul runtime.</span><span class="sxs-lookup"><span data-stu-id="ca6df-279">Select the target runtime from the **Target Runtime** drop-down list.</span></span> <span data-ttu-id="ca6df-280">Výchozí hodnota je `win-x86`.</span><span class="sxs-lookup"><span data-stu-id="ca6df-280">The default is `win-x86`.</span></span>
   * <span data-ttu-id="ca6df-281">Pokud potřebujete po nasazení odebrat další soubory, otevřete **Možnosti publikování souborů** a zaškrtnutím políčka odeberte další soubory v cílovém umístění.</span><span class="sxs-lookup"><span data-stu-id="ca6df-281">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="ca6df-282">Vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="ca6df-282">Select **Save**.</span></span>
1. <span data-ttu-id="ca6df-283">Pomocí zbývajících výzev Průvodce publikováním vytvořte novou lokalitu nebo aktualizujte existující web.</span><span class="sxs-lookup"><span data-stu-id="ca6df-283">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="ca6df-284">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="ca6df-284">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="ca6df-285">V souboru projektu zadejte jeden nebo více [identifikátorů modulu runtime (identifikátorů RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="ca6df-285">In the project file, specify one or more [Runtime Identifiers (RIDs)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="ca6df-286">Pro jeden identifikátor RID použijte `<RuntimeIdentifier>` (jednotné číslo), nebo použijte `<RuntimeIdentifiers>` (plural) k poskytnutí seznamu identifikátorů ridů oddělených středníkem.</span><span class="sxs-lookup"><span data-stu-id="ca6df-286">Use `<RuntimeIdentifier>` (singular) for a single RID, or use `<RuntimeIdentifiers>` (plural) to provide a semicolon-delimited list of RIDs.</span></span> <span data-ttu-id="ca6df-287">V následujícím příkladu je zadán `win-x86` RID:</span><span class="sxs-lookup"><span data-stu-id="ca6df-287">In the following example, the `win-x86` RID is specified:</span></span>

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. <span data-ttu-id="ca6df-288">Z příkazového prostředí publikujte aplikaci v konfiguraci vydání pro modul runtime hostitele pomocí příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="ca6df-288">From a command shell, publish the app in Release configuration for the host's runtime with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="ca6df-289">V následujícím příkladu je aplikace publikovaná pro `win-x86` RID.</span><span class="sxs-lookup"><span data-stu-id="ca6df-289">In the following example, the app is published for the `win-x86` RID.</span></span> <span data-ttu-id="ca6df-290">Ve vlastnosti `<RuntimeIdentifier>` (nebo `<RuntimeIdentifiers>`) v souboru projektu musí být uveden identifikátor RID, který je zadaný pro `--runtime`.</span><span class="sxs-lookup"><span data-stu-id="ca6df-290">The RID supplied to the `--runtime` option must be provided in the `<RuntimeIdentifier>` (or `<RuntimeIdentifiers>`) property in the project file.</span></span>

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. <span data-ttu-id="ca6df-291">Přesuňte obsah složky *bin/Release/{Target Framework}/{runtime identifikátor}/Publish* do lokality v App Service.</span><span class="sxs-lookup"><span data-stu-id="ca6df-291">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="ca6df-292">Pokud přetáhnete obsah složky pro *publikování* z místního pevného disku nebo sdílené síťové složky přímo do App Service v konzole Kudu, přetáhněte soubory do složky `D:\home\site\wwwroot` v konzole Kudu.</span><span class="sxs-lookup"><span data-stu-id="ca6df-292">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the Kudu console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

## <a name="protocol-settings-https"></a><span data-ttu-id="ca6df-293">Nastavení protokolu (HTTPS)</span><span class="sxs-lookup"><span data-stu-id="ca6df-293">Protocol settings (HTTPS)</span></span>

<span data-ttu-id="ca6df-294">Vazby zabezpečeného protokolu umožňují zadat certifikát, který se má použít při reagování na požadavky přes protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ca6df-294">Secure protocol bindings allow you specify a certificate to use when responding to requests over HTTPS.</span></span> <span data-ttu-id="ca6df-295">Vazba vyžaduje platný privátní certifikát ( *. pfx*), který byl vydán pro konkrétní název hostitele.</span><span class="sxs-lookup"><span data-stu-id="ca6df-295">Binding requires a valid private certificate (*.pfx*) issued for the specific hostname.</span></span> <span data-ttu-id="ca6df-296">Další informace najdete v tématu [kurz: vytvoření vazby existujícího vlastního certifikátu SSL k Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="ca6df-296">For more information, see [Tutorial: Bind an existing custom SSL certificate to Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="ca6df-297">Transformace souboru web.config</span><span class="sxs-lookup"><span data-stu-id="ca6df-297">Transform web.config</span></span>

<span data-ttu-id="ca6df-298">Pokud potřebujete transformovat *Web. config* při publikování (například nastavit proměnné prostředí na základě konfigurace, profilu nebo prostředí), přečtěte si téma <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="ca6df-298">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ca6df-299">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="ca6df-299">Additional resources</span></span>

* [<span data-ttu-id="ca6df-300">Přehled služby App Service</span><span class="sxs-lookup"><span data-stu-id="ca6df-300">App Service overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="ca6df-301">Azure App Service: nejlepší místo pro hostování aplikací .NET (video s přehledem 55-minut)</span><span class="sxs-lookup"><span data-stu-id="ca6df-301">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span></span>](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [<span data-ttu-id="ca6df-302">Azure pátek: Azure App Service diagnostické prostředí a řešení potíží (12 minut video)</span><span class="sxs-lookup"><span data-stu-id="ca6df-302">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [<span data-ttu-id="ca6df-303">Přehled diagnostiky Azure App Service</span><span class="sxs-lookup"><span data-stu-id="ca6df-303">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

<span data-ttu-id="ca6df-304">Azure App Service na Windows serveru používá [Internetová informační služba (IIS)](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="ca6df-304">Azure App Service on Windows Server uses [Internet Information Services (IIS)](https://www.iis.net/).</span></span> <span data-ttu-id="ca6df-305">Následující témata se týkají základní technologie IIS:</span><span class="sxs-lookup"><span data-stu-id="ca6df-305">The following topics pertain to the underlying IIS technology:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [<span data-ttu-id="ca6df-306">Windows Server – obsah správce IT pro aktuální a předchozí verze</span><span class="sxs-lookup"><span data-stu-id="ca6df-306">Windows Server - IT administrator content for current and previous releases</span></span>](/windows-server/windows-server-versions)
