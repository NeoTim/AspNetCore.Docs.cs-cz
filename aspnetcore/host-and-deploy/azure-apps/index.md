---
title: Nasazení aplikací ASP.NET Core pro Azure App Service
author: bradygaster
description: Tento článek obsahuje odkazy na hostitele Azure a nasazení prostředků.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: 9ffeebbf8125ddac5d6e621e411c4e86c5bd34b1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399306"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a><span data-ttu-id="29fc1-103">Nasazení aplikací ASP.NET Core pro Azure App Service</span><span class="sxs-lookup"><span data-stu-id="29fc1-103">Deploy ASP.NET Core apps to Azure App Service</span></span>

<span data-ttu-id="29fc1-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) je [platforma cloud computingu od Microsoftu](https://azure.microsoft.com/) pro hostování webových aplikací, včetně ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="29fc1-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span>

## <a name="useful-resources"></a><span data-ttu-id="29fc1-105">Užitečné materiály</span><span class="sxs-lookup"><span data-stu-id="29fc1-105">Useful resources</span></span>

<span data-ttu-id="29fc1-106">[Dokumentace k App Service](/azure/app-service/) je Domovská stránka pro Azure Apps, kurzy, ukázky, návody a další materiály.</span><span class="sxs-lookup"><span data-stu-id="29fc1-106">[App Service Documentation](/azure/app-service/) is the home for Azure Apps documentation, tutorials, samples, how-to guides, and other resources.</span></span> <span data-ttu-id="29fc1-107">Existují dva významné kurzy, které se týkají hostování ASP.NET Corech aplikací:</span><span class="sxs-lookup"><span data-stu-id="29fc1-107">Two notable tutorials that pertain to hosting ASP.NET Core apps are:</span></span>

[<span data-ttu-id="29fc1-108">Vytvoření webové aplikace ASP.NET Core v Azure</span><span class="sxs-lookup"><span data-stu-id="29fc1-108">Create an ASP.NET Core web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)  
<span data-ttu-id="29fc1-109">Pomocí sady Visual Studio můžete vytvořit a nasadit webovou aplikaci ASP.NET Core pro Azure App Service ve Windows.</span><span class="sxs-lookup"><span data-stu-id="29fc1-109">Use Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service on Windows.</span></span>

[<span data-ttu-id="29fc1-110">Vytvoření aplikace ASP.NET Core v App Service v systému Linux</span><span class="sxs-lookup"><span data-stu-id="29fc1-110">Create an ASP.NET Core app in App Service on Linux</span></span>](/azure/app-service/containers/quickstart-dotnetcore)  
<span data-ttu-id="29fc1-111">Pomocí příkazového řádku můžete vytvořit a nasadit webovou aplikaci ASP.NET Core pro Azure App Service v systému Linux.</span><span class="sxs-lookup"><span data-stu-id="29fc1-111">Use the command line to create and deploy an ASP.NET Core web app to Azure App Service on Linux.</span></span>

<span data-ttu-id="29fc1-112">Verzi ASP.NET Core dostupnou ve službě Azure App Service najdete na [App Service řídicím panelu ASP.NET Core](https://aspnetcoreon.azurewebsites.net/) .</span><span class="sxs-lookup"><span data-stu-id="29fc1-112">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

<span data-ttu-id="29fc1-113">Přihlaste se k odběru úložiště [oznámení App Service](https://github.com/Azure/app-service-announcements/) a sledujte problémy.</span><span class="sxs-lookup"><span data-stu-id="29fc1-113">Subscribe to the [App Service Announcements](https://github.com/Azure/app-service-announcements/) repository and monitor the issues.</span></span> <span data-ttu-id="29fc1-114">Tým App Service pravidelně publikuje oznámení a scénáře, které dorazí v App Service.</span><span class="sxs-lookup"><span data-stu-id="29fc1-114">The App Service team regularly posts announcements and scenarios arriving in App Service.</span></span>

<span data-ttu-id="29fc1-115">V dokumentaci k ASP.NET Core jsou k dispozici následující články:</span><span class="sxs-lookup"><span data-stu-id="29fc1-115">The following articles are available in ASP.NET Core documentation:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="29fc1-116">Naučte se publikovat aplikaci ASP.NET Core pro Azure App Service pomocí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="29fc1-116">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
<span data-ttu-id="29fc1-117">Naučte se, jak vytvořit webovou aplikaci ASP.NET Core pomocí sady Visual Studio a nasadit ji do Azure App Service pomocí Gitu pro průběžné nasazování.</span><span class="sxs-lookup"><span data-stu-id="29fc1-117">Learn how to create an ASP.NET Core web app using Visual Studio and deploy it to Azure App Service using Git for continuous deployment.</span></span>

[<span data-ttu-id="29fc1-118">Vytvoření prvního kanálu</span><span class="sxs-lookup"><span data-stu-id="29fc1-118">Create your first pipeline</span></span>](/azure/devops/pipelines/get-started-yaml)  
<span data-ttu-id="29fc1-119">Nastavte sestavení CI pro aplikaci ASP.NET Core a pak vytvořte Azure App Service vydanou verzi pro průběžné nasazování.</span><span class="sxs-lookup"><span data-stu-id="29fc1-119">Set up a CI build for an ASP.NET Core app, then create a continuous deployment release to Azure App Service.</span></span>

[<span data-ttu-id="29fc1-120">Sandbox webové aplikace Azure</span><span class="sxs-lookup"><span data-stu-id="29fc1-120">Azure Web App sandbox</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
<span data-ttu-id="29fc1-121">Zjištění omezení spouštění Azure App Service runtime, která vynutila platforma Azure Apps</span><span class="sxs-lookup"><span data-stu-id="29fc1-121">Discover Azure App Service runtime execution limitations enforced by the Azure Apps platform.</span></span>

<xref:test/troubleshoot>  
<span data-ttu-id="29fc1-122">Pochopení a řešení potíží s chybami a problémy s ASP.NET Core projekty.</span><span class="sxs-lookup"><span data-stu-id="29fc1-122">Understand and troubleshoot warnings and errors with ASP.NET Core projects.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="29fc1-123">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="29fc1-123">Application configuration</span></span>

### <a name="platform"></a><span data-ttu-id="29fc1-124">Platforma</span><span class="sxs-lookup"><span data-stu-id="29fc1-124">Platform</span></span>

<span data-ttu-id="29fc1-125">Architektura platformy (x86/x64) aplikace App Services je nastavená v nastavení aplikace na webu Azure Portal pro aplikace, které jsou hostované v úrovni hostování A-Series COMPUTE (Basic) nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="29fc1-125">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="29fc1-126">Ověřte, že nastavení publikování aplikace (například v profilu publikování sady Visual Studio [(. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) odpovídá nastavení v konfiguraci služby aplikace na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="29fc1-126">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure Portal.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="29fc1-127">V Azure App Service jsou k dispozici moduly runtime pro 64 (x64) a 32-bit (x86) aplikací.</span><span class="sxs-lookup"><span data-stu-id="29fc1-127">Runtimes for 64-bit (x64) and 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="29fc1-128">[.NET Core SDK](/dotnet/core/sdk) k dispozici v App Service je 32-bit, ale můžete nasadit 64 aplikace sestavené místně pomocí konzoly [Kudu](https://github.com/projectkudu/kudu/wiki) nebo procesu publikování v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="29fc1-128">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit, but you can deploy 64-bit apps built locally using the [Kudu](https://github.com/projectkudu/kudu/wiki) console or the publish process in Visual Studio.</span></span> <span data-ttu-id="29fc1-129">Další informace najdete v části [publikování a nasazení aplikace](#publish-and-deploy-the-app) .</span><span class="sxs-lookup"><span data-stu-id="29fc1-129">For more information, see the [Publish and deploy the app](#publish-and-deploy-the-app) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="29fc1-130">Pro aplikace s nativními závislostmi jsou moduly runtime pro 32 aplikace (x86) k dispozici na Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="29fc1-130">For apps with native dependencies, runtimes for 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="29fc1-131">[.NET Core SDK](/dotnet/core/sdk) k dispozici v App Service je 32-bit.</span><span class="sxs-lookup"><span data-stu-id="29fc1-131">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit.</span></span>

::: moniker-end

<span data-ttu-id="29fc1-132">Další informace o komponentách rozhraní .NET Core Framework a metodách distribuce, jako jsou informace o modulu runtime .NET Core a .NET Core SDK, naleznete v tématu [About .NET Core: kompozice](/dotnet/core/about#composition).</span><span class="sxs-lookup"><span data-stu-id="29fc1-132">For more information on .NET Core framework components and distribution methods, such as information on the .NET Core runtime and the .NET Core SDK, see [About .NET Core: Composition](/dotnet/core/about#composition).</span></span>

### <a name="packages"></a><span data-ttu-id="29fc1-133">Balíčky</span><span class="sxs-lookup"><span data-stu-id="29fc1-133">Packages</span></span>

<span data-ttu-id="29fc1-134">Zahrňte následující balíčky NuGet pro poskytování automatických funkcí protokolování pro aplikace nasazené do Azure App Service:</span><span class="sxs-lookup"><span data-stu-id="29fc1-134">Include the following NuGet packages to provide automatic logging features for apps deployed to Azure App Service:</span></span>

* <span data-ttu-id="29fc1-135">[Microsoft. AspNetCore. AzureAppServices. HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) využívá [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) k poskytování ASP.NET Coreho urychlené integrace s Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="29fc1-135">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) uses [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) to provide ASP.NET Core light-up integration with Azure App Service.</span></span> <span data-ttu-id="29fc1-136">Přidané funkce protokolování poskytuje `Microsoft.AspNetCore.AzureAppServicesIntegration` balíček.</span><span class="sxs-lookup"><span data-stu-id="29fc1-136">The added logging features are provided by the `Microsoft.AspNetCore.AzureAppServicesIntegration` package.</span></span>
* <span data-ttu-id="29fc1-137">[Microsoft. AspNetCore. AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) spustí [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) pro Azure App Service přidání zprostředkovatelů protokolování diagnostiky do `Microsoft.Extensions.Logging.AzureAppServices` balíčku.</span><span class="sxs-lookup"><span data-stu-id="29fc1-137">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executes [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) to add Azure App Service diagnostics logging providers in the `Microsoft.Extensions.Logging.AzureAppServices` package.</span></span>
* <span data-ttu-id="29fc1-138">[Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) poskytuje implementace protokolovacích nástrojů pro podporu Azure App Servicech diagnostických protokolů a funkcí streamování protokolů.</span><span class="sxs-lookup"><span data-stu-id="29fc1-138">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) provides logger implementations to support Azure App Service diagnostics logs and log streaming features.</span></span>

<span data-ttu-id="29fc1-139">Předchozí balíčky nejsou k dispozici ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="29fc1-139">The preceding packages aren't available from the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="29fc1-140">Aplikace, které .NET Framework cílí na Metapackage nebo odkazují na `Microsoft.AspNetCore.App` , musí explicitně odkazovat na jednotlivé balíčky v souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="29fc1-140">Apps that target .NET Framework or reference the `Microsoft.AspNetCore.App` metapackage must explicitly reference the individual packages in the app's project file.</span></span>

## <a name="override-app-configuration-using-the-azure-portal"></a><span data-ttu-id="29fc1-141">Přepsání konfigurace aplikace pomocí webu Azure Portal</span><span class="sxs-lookup"><span data-stu-id="29fc1-141">Override app configuration using the Azure Portal</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="29fc1-142">Nastavení aplikace na webu Azure Portal umožňuje nastavit proměnné prostředí pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="29fc1-142">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="29fc1-143">Proměnné prostředí mohou být spotřebovány [poskytovatelem konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="29fc1-143">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables).</span></span>

<span data-ttu-id="29fc1-144">Když se na webu Azure Portal vytvoří nebo upraví nastavení aplikace a vybere se tlačítko **Save (Uložit** ), aplikace Azure se restartuje.</span><span class="sxs-lookup"><span data-stu-id="29fc1-144">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="29fc1-145">Proměnná prostředí je k dispozici aplikaci po restartování služby.</span><span class="sxs-lookup"><span data-stu-id="29fc1-145">The environment variable is available to the app after the service restarts.</span></span>

<span data-ttu-id="29fc1-146">Když aplikace používá [obecného hostitele](xref:fundamentals/host/generic-host), proměnné prostředí se načtou do konfigurace aplikace, když <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> se volá k sestavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="29fc1-146">When an app uses the [Generic Host](xref:fundamentals/host/generic-host), environment variables are loaded into the app's configuration when <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="29fc1-147">Další informace naleznete v tématu <xref:fundamentals/host/generic-host> a [zprostředkovatel konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="29fc1-147">For more information, see <xref:fundamentals/host/generic-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables).</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="29fc1-148">Nastavení aplikace na webu Azure Portal umožňuje nastavit proměnné prostředí pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="29fc1-148">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="29fc1-149">Proměnné prostředí mohou být spotřebovány [poskytovatelem konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="29fc1-149">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="29fc1-150">Když se na webu Azure Portal vytvoří nebo upraví nastavení aplikace a vybere se tlačítko **Save (Uložit** ), aplikace Azure se restartuje.</span><span class="sxs-lookup"><span data-stu-id="29fc1-150">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="29fc1-151">Proměnná prostředí je k dispozici aplikaci po restartování služby.</span><span class="sxs-lookup"><span data-stu-id="29fc1-151">The environment variable is available to the app after the service restarts.</span></span>

<span data-ttu-id="29fc1-152">Když aplikace používá [webového hostitele](xref:fundamentals/host/web-host), proměnné prostředí se načtou do konfigurace aplikace, když <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> se volá k sestavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="29fc1-152">When an app uses the [Web Host](xref:fundamentals/host/web-host), environment variables are loaded into the app's configuration when <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="29fc1-153">Další informace naleznete v tématu <xref:fundamentals/host/web-host> a [zprostředkovatel konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="29fc1-153">For more information, see <xref:fundamentals/host/web-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="29fc1-154">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="29fc1-154">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="29fc1-155">[IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), který konfiguruje přesměrované hlavičky middlewaru při hostování [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model)a modul ASP.NET Core je nakonfigurován pro přeposílání schématu (http/https) a vzdálené IP adresy, na které pochází požadavek.</span><span class="sxs-lookup"><span data-stu-id="29fc1-155">The [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), which configures Forwarded Headers Middleware when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="29fc1-156">Pro aplikace hostované za dalšími proxy servery a nástroji pro vyrovnávání zatížení může být vyžadována další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="29fc1-156">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="29fc1-157">Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="29fc1-157">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="monitoring-and-logging"></a><span data-ttu-id="29fc1-158">Monitorování a protokolování</span><span class="sxs-lookup"><span data-stu-id="29fc1-158">Monitoring and logging</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="29fc1-159">Aplikace ASP.NET Core nasazené pro App Service automaticky dostanou rozšíření App Service **ASP.NET Core Integration Logging**.</span><span class="sxs-lookup"><span data-stu-id="29fc1-159">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Integration**.</span></span> <span data-ttu-id="29fc1-160">Rozšíření umožňuje integraci protokolování pro aplikace ASP.NET Core v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="29fc1-160">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="29fc1-161">Aplikace ASP.NET Core nasazené pro App Service automaticky dostanou rozšíření App Service **ASP.NET Core rozšíření protokolování**.</span><span class="sxs-lookup"><span data-stu-id="29fc1-161">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Extensions**.</span></span> <span data-ttu-id="29fc1-162">Rozšíření umožňuje integraci protokolování pro aplikace ASP.NET Core v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="29fc1-162">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

<span data-ttu-id="29fc1-163">Informace o monitorování, protokolování a odstraňování potíží najdete v následujících článcích:</span><span class="sxs-lookup"><span data-stu-id="29fc1-163">For monitoring, logging, and troubleshooting information, see the following articles:</span></span>

[<span data-ttu-id="29fc1-164">Monitorování aplikací v Azure App Service</span><span class="sxs-lookup"><span data-stu-id="29fc1-164">Monitor apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)  
<span data-ttu-id="29fc1-165">Naučte se kontrolovat kvóty a metriky pro aplikace a App Service plány.</span><span class="sxs-lookup"><span data-stu-id="29fc1-165">Learn how to review quotas and metrics for apps and App Service plans.</span></span>

[<span data-ttu-id="29fc1-166">Povolit protokolování diagnostiky pro aplikace v Azure App Service</span><span class="sxs-lookup"><span data-stu-id="29fc1-166">Enable diagnostics logging for apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)  
<span data-ttu-id="29fc1-167">Zjistěte, jak povolit a získat přístup k protokolování diagnostiky pro stavové kódy HTTP, neúspěšné žádosti a aktivitu webového serveru.</span><span class="sxs-lookup"><span data-stu-id="29fc1-167">Discover how to enable and access diagnostic logging for HTTP status codes, failed requests, and web server activity.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="29fc1-168">Seznamte se s běžnými přístupy ke zpracování chyb v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="29fc1-168">Understand common approaches to handling errors in ASP.NET Core apps.</span></span>

<xref:test/troubleshoot-azure-iis>  
<span data-ttu-id="29fc1-169">Naučte se diagnostikovat problémy s Azure App Service nasazeními s ASP.NET Core aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="29fc1-169">Learn how to diagnose issues with Azure App Service deployments with ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-iis-errors-reference>  
<span data-ttu-id="29fc1-170">Projděte si nejčastější chyby konfigurace nasazení pro aplikace hostované službou Azure App Service/IIS s pokyny k řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="29fc1-170">See the common deployment configuration errors for apps hosted by Azure App Service/IIS with troubleshooting advice.</span></span>

## <a name="data-protection-key-ring-and-deployment-slots"></a><span data-ttu-id="29fc1-171">Kroužky klíčů pro ochranu dat a sloty nasazení</span><span class="sxs-lookup"><span data-stu-id="29fc1-171">Data Protection key ring and deployment slots</span></span>

<span data-ttu-id="29fc1-172">[Klíče ochrany dat](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) jsou trvale uložené ve složce *%Home%\ASP.NET\DataProtection-Keys* .</span><span class="sxs-lookup"><span data-stu-id="29fc1-172">[Data Protection keys](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="29fc1-173">Tato složka je zajištěná síťovým úložištěm a je synchronizovaná napříč všemi počítači hostujícími aplikaci.</span><span class="sxs-lookup"><span data-stu-id="29fc1-173">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span> <span data-ttu-id="29fc1-174">Klíče nejsou chráněny v klidovém umístění.</span><span class="sxs-lookup"><span data-stu-id="29fc1-174">Keys aren't protected at rest.</span></span> <span data-ttu-id="29fc1-175">Tato složka poskytuje klíčového prstence pro všechny instance aplikace v jednom slotu nasazení.</span><span class="sxs-lookup"><span data-stu-id="29fc1-175">This folder supplies the key ring to all instances of an app in a single deployment slot.</span></span> <span data-ttu-id="29fc1-176">Samostatné sloty nasazení, jako je například pracovní a produkční, nesdílejte klíč Ring.</span><span class="sxs-lookup"><span data-stu-id="29fc1-176">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span>

<span data-ttu-id="29fc1-177">Při záměně mezi sloty nasazení nebude možné dešifrovat uložená data pomocí nástroje Data Protection, a to pomocí klíčeového kruhu v předchozí pozici.</span><span class="sxs-lookup"><span data-stu-id="29fc1-177">When swapping between deployment slots, any system using data protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="29fc1-178">Middleware souborů cookie ASP.NET používá ochranu dat k ochraně souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="29fc1-178">ASP.NET Cookie Middleware uses data protection to protect its cookies.</span></span> <span data-ttu-id="29fc1-179">To vede k tomu, že se uživatelé odhlásí z aplikace, která používá standardní middleware souborů cookie ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="29fc1-179">This leads to users being signed out of an app that uses the standard ASP.NET Cookie Middleware.</span></span> <span data-ttu-id="29fc1-180">Pro řešení Key Ring nezávislé na slotech použijte externího poskytovatele služby Key Ring, například:</span><span class="sxs-lookup"><span data-stu-id="29fc1-180">For a slot-independent key ring solution, use an external key ring provider, such as:</span></span>

* <span data-ttu-id="29fc1-181">Azure Blob Storage</span><span class="sxs-lookup"><span data-stu-id="29fc1-181">Azure Blob Storage</span></span>
* <span data-ttu-id="29fc1-182">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="29fc1-182">Azure Key Vault</span></span>
* <span data-ttu-id="29fc1-183">Úložiště SQL</span><span class="sxs-lookup"><span data-stu-id="29fc1-183">SQL store</span></span>
* <span data-ttu-id="29fc1-184">Redis Cache</span><span class="sxs-lookup"><span data-stu-id="29fc1-184">Redis cache</span></span>

<span data-ttu-id="29fc1-185">Další informace naleznete v tématu <xref:security/data-protection/implementation/key-storage-providers>.</span><span class="sxs-lookup"><span data-stu-id="29fc1-185">For more information, see <xref:security/data-protection/implementation/key-storage-providers>.</span></span>
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>

## <a name="deploy-an-aspnet-core-app-that-uses-a-net-core-preview"></a><span data-ttu-id="29fc1-186">Nasazení aplikace ASP.NET Core, která používá .NET Core Preview</span><span class="sxs-lookup"><span data-stu-id="29fc1-186">Deploy an ASP.NET Core app that uses a .NET Core preview</span></span>

<span data-ttu-id="29fc1-187">Pokud chcete nasadit aplikaci, která používá verzi Preview rozhraní .NET Core, Projděte si následující zdroje informací.</span><span class="sxs-lookup"><span data-stu-id="29fc1-187">To deploy an app that uses a preview release of .NET Core, see the following resources.</span></span> <span data-ttu-id="29fc1-188">Tyto přístupy se používají také v případě, že je modul runtime k dispozici, ale sada SDK nebyla nainstalována na Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="29fc1-188">These approaches are also used when the runtime is available but the SDK hasn't been installed on Azure App Service.</span></span>

* [<span data-ttu-id="29fc1-189">Určení verze .NET Core SDK pomocí Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="29fc1-189">Specify the .NET Core SDK Version using Azure Pipelines</span></span>](#specify-the-net-core-sdk-version-using-azure-pipelines)
* [<span data-ttu-id="29fc1-190">Nasazení samostatné aplikace ve verzi Preview</span><span class="sxs-lookup"><span data-stu-id="29fc1-190">Deploy a self-contained preview app</span></span>](#deploy-a-self-contained-preview-app)
* [<span data-ttu-id="29fc1-191">Použití Docker s Web Apps pro kontejnery</span><span class="sxs-lookup"><span data-stu-id="29fc1-191">Use Docker with Web Apps for containers</span></span>](#use-docker-with-web-apps-for-containers)
* [<span data-ttu-id="29fc1-192">Nainstalovat rozšíření webu verze Preview</span><span class="sxs-lookup"><span data-stu-id="29fc1-192">Install the preview site extension</span></span>](#install-the-preview-site-extension)

<span data-ttu-id="29fc1-193">Verzi ASP.NET Core dostupnou ve službě Azure App Service najdete na [App Service řídicím panelu ASP.NET Core](https://aspnetcoreon.azurewebsites.net/) .</span><span class="sxs-lookup"><span data-stu-id="29fc1-193">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

### <a name="specify-the-net-core-sdk-version-using-azure-pipelines"></a><span data-ttu-id="29fc1-194">Určení verze .NET Core SDK pomocí Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="29fc1-194">Specify the .NET Core SDK Version using Azure Pipelines</span></span>

<span data-ttu-id="29fc1-195">Pomocí [Azure App Service scénářů CI/CD](/azure/app-service/deploy-continuous-deployment) můžete nastavit průběžnou integraci sestavení s využitím Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="29fc1-195">Use [Azure App Service CI/CD scenarios](/azure/app-service/deploy-continuous-deployment) to set up a continuous integration build with Azure DevOps.</span></span> <span data-ttu-id="29fc1-196">Po vytvoření sestavení Azure DevOps můžete nakonfigurovat sestavení tak, aby používalo konkrétní verzi sady SDK.</span><span class="sxs-lookup"><span data-stu-id="29fc1-196">After the Azure DevOps build is created, optionally configure the build to use a specific SDK version.</span></span> 

#### <a name="specify-the-net-core-sdk-version"></a><span data-ttu-id="29fc1-197">Zadejte verzi .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="29fc1-197">Specify the .NET Core SDK version</span></span>

<span data-ttu-id="29fc1-198">Při použití centra nasazení App Service k vytvoření buildu služby Azure DevOps obsahuje výchozí kanál sestavení kroky pro `Restore` ,, a `Build` `Test` `Publish` .</span><span class="sxs-lookup"><span data-stu-id="29fc1-198">When using the App Service deployment center to create an Azure DevOps build, the default build pipeline includes steps for `Restore`, `Build`, `Test`, and `Publish`.</span></span> <span data-ttu-id="29fc1-199">Chcete-li zadat verzi sady SDK, přidejte nový krok kliknutím na tlačítko **Přidat (+)** v seznamu úloh agenta.</span><span class="sxs-lookup"><span data-stu-id="29fc1-199">To specify the SDK version, select the **Add (+)** button in the Agent job list to add a new step.</span></span> <span data-ttu-id="29fc1-200">Na panelu hledání vyhledejte **.NET Core SDK** .</span><span class="sxs-lookup"><span data-stu-id="29fc1-200">Search for **.NET Core SDK** in the search bar.</span></span> 

![Přidat .NET Core SDK krok](index/add-sdk-step.png)

<span data-ttu-id="29fc1-202">Přesuňte krok do první pozice v sestavení tak, aby následující kroky používaly určenou verzi .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="29fc1-202">Move the step into the first position in the build so that the steps following it use the specified version of the .NET Core SDK.</span></span> <span data-ttu-id="29fc1-203">Zadejte verzi .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="29fc1-203">Specify the version of the .NET Core SDK.</span></span> <span data-ttu-id="29fc1-204">V tomto příkladu je sada SDK nastavena na `3.0.100` .</span><span class="sxs-lookup"><span data-stu-id="29fc1-204">In this example, the SDK is set to `3.0.100`.</span></span>

![Krok dokončené sady SDK](index/sdk-step-first-place.png)

<span data-ttu-id="29fc1-206">Pro publikování [samostatně zahrnutého nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)nakonfigurujte SCD v `Publish` kroku a zadejte [identifikátor modulu runtime (RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="29fc1-206">To publish a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), configure SCD in the `Publish` step and provide the [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

![Publikování samostatného kontejneru](index/self-contained.png)

### <a name="deploy-a-self-contained-preview-app"></a><span data-ttu-id="29fc1-208">Nasazení samostatné aplikace ve verzi Preview</span><span class="sxs-lookup"><span data-stu-id="29fc1-208">Deploy a self-contained preview app</span></span>

<span data-ttu-id="29fc1-209">[Samostatné nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) , které cílí na modul runtime verze Preview, přenáší modul runtime verze Preview do nasazení.</span><span class="sxs-lookup"><span data-stu-id="29fc1-209">A [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) that targets a preview runtime carries the preview runtime in the deployment.</span></span>

<span data-ttu-id="29fc1-210">Při nasazení samostatně obsažené aplikace:</span><span class="sxs-lookup"><span data-stu-id="29fc1-210">When deploying a self-contained app:</span></span>

* <span data-ttu-id="29fc1-211">Lokalita v Azure App Service nevyžaduje [rozšíření webu verze Preview](#install-the-preview-site-extension).</span><span class="sxs-lookup"><span data-stu-id="29fc1-211">The site in Azure App Service doesn't require the [preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="29fc1-212">Aplikace musí být publikována při jiném přístupu než při publikování pro [nasazení závislé na rozhraní (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="29fc1-212">The app must be published following a different approach than when publishing for a [framework-dependent deployment (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="29fc1-213">Postupujte podle pokynů v části [nasazení samostatně obsaženého oddílu aplikace](#deploy-the-app-self-contained) .</span><span class="sxs-lookup"><span data-stu-id="29fc1-213">Follow the guidance in the [Deploy the app self-contained](#deploy-the-app-self-contained) section.</span></span>

### <a name="use-docker-with-web-apps-for-containers"></a><span data-ttu-id="29fc1-214">Použití Docker s Web Apps pro kontejnery</span><span class="sxs-lookup"><span data-stu-id="29fc1-214">Use Docker with Web Apps for containers</span></span>

<span data-ttu-id="29fc1-215">[Dokovací centrum](https://hub.docker.com/r/microsoft/aspnetcore/) obsahuje nejnovější image Docker Preview.</span><span class="sxs-lookup"><span data-stu-id="29fc1-215">The [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contains the latest preview Docker images.</span></span> <span data-ttu-id="29fc1-216">Obrázky lze použít jako základní bitovou kopii.</span><span class="sxs-lookup"><span data-stu-id="29fc1-216">The images can be used as a base image.</span></span> <span data-ttu-id="29fc1-217">Použijte image a nasaďte ji normálně pro Web Apps kontejnerů.</span><span class="sxs-lookup"><span data-stu-id="29fc1-217">Use the image and deploy to Web Apps for Containers normally.</span></span>

### <a name="install-the-preview-site-extension"></a><span data-ttu-id="29fc1-218">Nainstalovat rozšíření webu verze Preview</span><span class="sxs-lookup"><span data-stu-id="29fc1-218">Install the preview site extension</span></span>

<span data-ttu-id="29fc1-219">Pokud dojde k potížím pomocí rozšíření webu verze Preview, otevřete [problém dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/issues).</span><span class="sxs-lookup"><span data-stu-id="29fc1-219">If a problem occurs using the preview site extension, open an [dotnet/AspNetCore issue](https://github.com/dotnet/AspNetCore/issues).</span></span>

1. <span data-ttu-id="29fc1-220">Na webu Azure Portal přejděte na App Service.</span><span class="sxs-lookup"><span data-stu-id="29fc1-220">From the Azure Portal, navigate to the App Service.</span></span>
1. <span data-ttu-id="29fc1-221">Vyberte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="29fc1-221">Select the web app.</span></span>
1. <span data-ttu-id="29fc1-222">Do vyhledávacího pole zadejte "ex", chcete-li filtrovat "rozšíření", nebo se posuňte dolů v seznamu nástrojů pro správu.</span><span class="sxs-lookup"><span data-stu-id="29fc1-222">Type "ex" in the search box to filter for "Extensions" or scroll down the list of management tools.</span></span>
1. <span data-ttu-id="29fc1-223">Vyberte **Extensions** (Rozšíření).</span><span class="sxs-lookup"><span data-stu-id="29fc1-223">Select **Extensions**.</span></span>
1. <span data-ttu-id="29fc1-224">Vyberte možnost **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="29fc1-224">Select **Add**.</span></span>
1. <span data-ttu-id="29fc1-225">V seznamu vyberte rozšíření **Runtime ASP.NET Core {X. Y} ({x64 | x86})** , kde `{X.Y}` je verze Preview ASP.NET Core a `{x64|x86}` specifikuje platformu.</span><span class="sxs-lookup"><span data-stu-id="29fc1-225">Select the **ASP.NET Core {X.Y} ({x64|x86}) Runtime** extension from the list, where `{X.Y}` is the ASP.NET Core preview version and `{x64|x86}` specifies the platform.</span></span>
1. <span data-ttu-id="29fc1-226">Kliknutím na **OK** přijměte právní podmínky.</span><span class="sxs-lookup"><span data-stu-id="29fc1-226">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="29fc1-227">Pro instalaci rozšíření vyberte **OK** .</span><span class="sxs-lookup"><span data-stu-id="29fc1-227">Select **OK** to install the extension.</span></span>

<span data-ttu-id="29fc1-228">Po dokončení operace je nainstalovaná nejnovější verze .NET Core Preview.</span><span class="sxs-lookup"><span data-stu-id="29fc1-228">When the operation completes, the latest .NET Core preview is installed.</span></span> <span data-ttu-id="29fc1-229">Ověřte instalaci:</span><span class="sxs-lookup"><span data-stu-id="29fc1-229">Verify the installation:</span></span>

1. <span data-ttu-id="29fc1-230">Vyberte **Rozšířené nástroje**.</span><span class="sxs-lookup"><span data-stu-id="29fc1-230">Select **Advanced Tools**.</span></span>
1. <span data-ttu-id="29fc1-231">Vyberte **Přejít** v **pokročilých nástrojích**.</span><span class="sxs-lookup"><span data-stu-id="29fc1-231">Select **Go** in **Advanced Tools**.</span></span>
1. <span data-ttu-id="29fc1-232">Vyberte **Debug console**  >  položku nabídky**prostředí PowerShell** konzoly ladění.</span><span class="sxs-lookup"><span data-stu-id="29fc1-232">Select the **Debug console** > **PowerShell** menu item.</span></span>
1. <span data-ttu-id="29fc1-233">Na příkazovém řádku PowerShellu spusťte následující příkaz.</span><span class="sxs-lookup"><span data-stu-id="29fc1-233">At the PowerShell prompt, execute the following command.</span></span> <span data-ttu-id="29fc1-234">Nahraďte verzi ASP.NET Core Runtime pro `{X.Y}` a platformu pro `{PLATFORM}` v příkazu:</span><span class="sxs-lookup"><span data-stu-id="29fc1-234">Substitute the ASP.NET Core runtime version for `{X.Y}` and the platform for `{PLATFORM}` in the command:</span></span>

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   <span data-ttu-id="29fc1-235">Příkaz vrátí, `True` když je nainstalován modul runtime x64 Preview.</span><span class="sxs-lookup"><span data-stu-id="29fc1-235">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="29fc1-236">Architektura platformy (x86/x64) aplikace App Services je nastavená v nastavení aplikace na webu Azure Portal pro aplikace, které jsou hostované v úrovni hostování A-Series COMPUTE (Basic) nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="29fc1-236">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="29fc1-237">Ověřte, že nastavení publikování aplikace (například v profilu publikování sady Visual Studio [(. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) odpovídá nastavení v konfiguraci služby aplikace v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="29fc1-237">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure portal.</span></span>
>
> <span data-ttu-id="29fc1-238">Pokud je aplikace spuštěná v režimu v procesu a architektura platformy je nakonfigurovaná pro 64-bit (x64), modul ASP.NET Core používá 64 runtime Preview, pokud je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="29fc1-238">If the app is run in in-process mode and the platform architecture is configured for 64-bit (x64), the ASP.NET Core Module uses the 64-bit preview runtime, if present.</span></span> <span data-ttu-id="29fc1-239">Nainstalujte rozšíření **Runtime ASP.NET Core {X. Y} (x64)** pomocí webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="29fc1-239">Install the **ASP.NET Core {X.Y} (x64) Runtime** extension using the Azure Portal.</span></span>
>
> <span data-ttu-id="29fc1-240">Po instalaci prostředí runtime x64 Preview spusťte následující příkaz v příkazovém okně Azure Kudu PowerShell a ověřte instalaci.</span><span class="sxs-lookup"><span data-stu-id="29fc1-240">After installing the x64 preview runtime, run the following command in the Azure Kudu PowerShell command window to verify the installation.</span></span> <span data-ttu-id="29fc1-241">V následujícím příkazu nahraďte verzi ASP.NET Core Runtime pro `{X.Y}` :</span><span class="sxs-lookup"><span data-stu-id="29fc1-241">Substitute the ASP.NET Core runtime version for `{X.Y}` in the following command:</span></span>
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> <span data-ttu-id="29fc1-242">Příkaz vrátí, `True` když je nainstalován modul runtime x64 Preview.</span><span class="sxs-lookup"><span data-stu-id="29fc1-242">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="29fc1-243">**Rozšíření ASP.NET Core** umožňují pro ASP.NET Core v Azure App Services další funkce, jako je například povolení protokolování Azure.</span><span class="sxs-lookup"><span data-stu-id="29fc1-243">**ASP.NET Core Extensions** enables additional functionality for ASP.NET Core on Azure App Services, such as enabling Azure logging.</span></span> <span data-ttu-id="29fc1-244">Rozšíření se nainstaluje automaticky při nasazení ze sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="29fc1-244">The extension is installed automatically when deploying from Visual Studio.</span></span> <span data-ttu-id="29fc1-245">Pokud rozšíření není nainstalované, nainstalujte ho pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="29fc1-245">If the extension isn't installed, install it for the app.</span></span>

<span data-ttu-id="29fc1-246">**Použití rozšíření webu ve verzi Preview se šablonou ARM**</span><span class="sxs-lookup"><span data-stu-id="29fc1-246">**Use the preview site extension with an ARM template**</span></span>

<span data-ttu-id="29fc1-247">Pokud se k vytváření a nasazování aplikací používá šablona ARM, `siteextensions` typ prostředku se dá použít k přidání rozšíření webu do webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="29fc1-247">If an ARM template is used to create and deploy apps, the `siteextensions` resource type can be used to add the site extension to a web app.</span></span> <span data-ttu-id="29fc1-248">Například:</span><span class="sxs-lookup"><span data-stu-id="29fc1-248">For example:</span></span>

[!code-json[](index/sample/arm.json?highlight=2)]

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="29fc1-249">Publikování a nasazení aplikace</span><span class="sxs-lookup"><span data-stu-id="29fc1-249">Publish and deploy the app</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="29fc1-250">Pro 64 nasazení:</span><span class="sxs-lookup"><span data-stu-id="29fc1-250">For a 64-bit deployment:</span></span>

* <span data-ttu-id="29fc1-251">K vytvoření 64 aplikace použijte 64 .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="29fc1-251">Use a 64-bit .NET Core SDK to build a 64-bit app.</span></span>
* <span data-ttu-id="29fc1-252">V **Configuration**obecných nastaveních konfigurace App Service nastavte **platformou** na **64 bit**  >  **General settings**.</span><span class="sxs-lookup"><span data-stu-id="29fc1-252">Set the **Platform** to **64 Bit** in the App Service's **Configuration** > **General settings**.</span></span> <span data-ttu-id="29fc1-253">Aby bylo možné vybrat bitová verze platformy, musí aplikace používat základní nebo vyšší plán služby.</span><span class="sxs-lookup"><span data-stu-id="29fc1-253">The app must use a Basic or higher service plan to enable the choice of platform bitness.</span></span>

::: moniker-end

### <a name="deploy-the-app-framework-dependent"></a><span data-ttu-id="29fc1-254">Nasazení závislého rozhraní App Framework</span><span class="sxs-lookup"><span data-stu-id="29fc1-254">Deploy the app framework-dependent</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29fc1-255">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29fc1-255">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="29fc1-256">Vyberte **sestavení**  >  **Publikovat {název aplikace}** z panelu nástrojů sady Visual Studio nebo klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="29fc1-256">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="29fc1-257">V dialogovém okně **vybrat cíl publikování** potvrďte, že je vybrána možnost **App Service** .</span><span class="sxs-lookup"><span data-stu-id="29fc1-257">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="29fc1-258">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="29fc1-258">Select **Advanced**.</span></span> <span data-ttu-id="29fc1-259">Otevře se dialogové okno **publikovat** .</span><span class="sxs-lookup"><span data-stu-id="29fc1-259">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="29fc1-260">V dialogovém okně **publikovat** :</span><span class="sxs-lookup"><span data-stu-id="29fc1-260">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="29fc1-261">Potvrďte, že je vybraná konfigurace **vydané verze** .</span><span class="sxs-lookup"><span data-stu-id="29fc1-261">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="29fc1-262">Otevřete rozevírací seznam **režim nasazení** a vyberte možnost závislé na **rozhraní**.</span><span class="sxs-lookup"><span data-stu-id="29fc1-262">Open the **Deployment Mode** drop-down list and select **Framework-Dependent**.</span></span>
   * <span data-ttu-id="29fc1-263">Jako **cílový modul runtime**vyberte **přenosný** .</span><span class="sxs-lookup"><span data-stu-id="29fc1-263">Select **Portable** as the **Target Runtime**.</span></span>
   * <span data-ttu-id="29fc1-264">Pokud potřebujete po nasazení odebrat další soubory, otevřete **Možnosti publikování souborů** a zaškrtnutím políčka odeberte další soubory v cílovém umístění.</span><span class="sxs-lookup"><span data-stu-id="29fc1-264">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="29fc1-265">Vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="29fc1-265">Select **Save**.</span></span>
1. <span data-ttu-id="29fc1-266">Pomocí zbývajících výzev Průvodce publikováním vytvořte novou lokalitu nebo aktualizujte existující web.</span><span class="sxs-lookup"><span data-stu-id="29fc1-266">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="29fc1-267">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="29fc1-267">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="29fc1-268">V souboru projektu nezadávejte [identifikátor modulu runtime (RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="29fc1-268">In the project file, don't specify a [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

1. <span data-ttu-id="29fc1-269">V příkazovém prostředí publikujte aplikaci v konfiguraci vydaných verzí pomocí příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="29fc1-269">From a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="29fc1-270">V následujícím příkladu je aplikace publikována jako aplikace závislá na rozhraní:</span><span class="sxs-lookup"><span data-stu-id="29fc1-270">In the following example, the app is published as a framework-dependent app:</span></span>

   ```console
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="29fc1-271">Přesuňte obsah adresáře *bin/Release/{Target Framework}/Publish* do lokality v App Service.</span><span class="sxs-lookup"><span data-stu-id="29fc1-271">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="29fc1-272">Pokud přetáhnete obsah složky pro *publikování* z místního pevného disku nebo sdílené síťové složky přímo do App Service v konzole [Kudu](https://github.com/projectkudu/kudu/wiki) , přetáhněte soubory do `D:\home\site\wwwroot` složky v konzole Kudu.</span><span class="sxs-lookup"><span data-stu-id="29fc1-272">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the [Kudu](https://github.com/projectkudu/kudu/wiki) console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

### <a name="deploy-the-app-self-contained"></a><span data-ttu-id="29fc1-273">Nasazení samostatně obsažené aplikace</span><span class="sxs-lookup"><span data-stu-id="29fc1-273">Deploy the app self-contained</span></span>

<span data-ttu-id="29fc1-274">Použijte aplikaci Visual Studio nebo .NET Core CLI pro [samostatně obsažené nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="29fc1-274">Use Visual Studio or the .NET Core CLI for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29fc1-275">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29fc1-275">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="29fc1-276">Vyberte **sestavení**  >  **Publikovat {název aplikace}** z panelu nástrojů sady Visual Studio nebo klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="29fc1-276">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="29fc1-277">V dialogovém okně **vybrat cíl publikování** potvrďte, že je vybrána možnost **App Service** .</span><span class="sxs-lookup"><span data-stu-id="29fc1-277">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="29fc1-278">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="29fc1-278">Select **Advanced**.</span></span> <span data-ttu-id="29fc1-279">Otevře se dialogové okno **publikovat** .</span><span class="sxs-lookup"><span data-stu-id="29fc1-279">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="29fc1-280">V dialogovém okně **publikovat** :</span><span class="sxs-lookup"><span data-stu-id="29fc1-280">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="29fc1-281">Potvrďte, že je vybraná konfigurace **vydané verze** .</span><span class="sxs-lookup"><span data-stu-id="29fc1-281">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="29fc1-282">Otevřete rozevírací seznam **režim nasazení** a vyberte možnost **samostatně obsaženo**.</span><span class="sxs-lookup"><span data-stu-id="29fc1-282">Open the **Deployment Mode** drop-down list and select **Self-Contained**.</span></span>
   * <span data-ttu-id="29fc1-283">Z rozevíracího seznamu **cílový modul runtime** vyberte cílový modul runtime.</span><span class="sxs-lookup"><span data-stu-id="29fc1-283">Select the target runtime from the **Target Runtime** drop-down list.</span></span> <span data-ttu-id="29fc1-284">Výchozí formát je `win-x86`.</span><span class="sxs-lookup"><span data-stu-id="29fc1-284">The default is `win-x86`.</span></span>
   * <span data-ttu-id="29fc1-285">Pokud potřebujete po nasazení odebrat další soubory, otevřete **Možnosti publikování souborů** a zaškrtnutím políčka odeberte další soubory v cílovém umístění.</span><span class="sxs-lookup"><span data-stu-id="29fc1-285">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="29fc1-286">Vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="29fc1-286">Select **Save**.</span></span>
1. <span data-ttu-id="29fc1-287">Pomocí zbývajících výzev Průvodce publikováním vytvořte novou lokalitu nebo aktualizujte existující web.</span><span class="sxs-lookup"><span data-stu-id="29fc1-287">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="29fc1-288">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="29fc1-288">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="29fc1-289">V souboru projektu zadejte jeden nebo více [identifikátorů modulu runtime (identifikátorů RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="29fc1-289">In the project file, specify one or more [Runtime Identifiers (RIDs)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="29fc1-290">`<RuntimeIdentifier>`Pro jeden identifikátor RID použijte (jednotné číslo), nebo použijte `<RuntimeIdentifiers>` (plural) k poskytnutí seznamu identifikátorů ridů oddělených středníkem.</span><span class="sxs-lookup"><span data-stu-id="29fc1-290">Use `<RuntimeIdentifier>` (singular) for a single RID, or use `<RuntimeIdentifiers>` (plural) to provide a semicolon-delimited list of RIDs.</span></span> <span data-ttu-id="29fc1-291">V následujícím příkladu `win-x86` je zadáno RID:</span><span class="sxs-lookup"><span data-stu-id="29fc1-291">In the following example, the `win-x86` RID is specified:</span></span>

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. <span data-ttu-id="29fc1-292">Z příkazového prostředí publikujte aplikaci v konfiguraci vydání pro modul runtime hostitele pomocí příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="29fc1-292">From a command shell, publish the app in Release configuration for the host's runtime with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="29fc1-293">V následujícím příkladu je aplikace publikována pro `win-x86` identifikátor RID.</span><span class="sxs-lookup"><span data-stu-id="29fc1-293">In the following example, the app is published for the `win-x86` RID.</span></span> <span data-ttu-id="29fc1-294">Identifikátor RID zadaný do `--runtime` Možnosti musí být uveden ve `<RuntimeIdentifier>` vlastnosti (nebo `<RuntimeIdentifiers>` ) v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="29fc1-294">The RID supplied to the `--runtime` option must be provided in the `<RuntimeIdentifier>` (or `<RuntimeIdentifiers>`) property in the project file.</span></span>

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. <span data-ttu-id="29fc1-295">Přesuňte obsah složky *bin/Release/{Target Framework}/{runtime identifikátor}/Publish* do lokality v App Service.</span><span class="sxs-lookup"><span data-stu-id="29fc1-295">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="29fc1-296">Pokud přetáhnete obsah složky pro *publikování* z místního pevného disku nebo sdílené síťové složky přímo do App Service v konzole Kudu, přetáhněte soubory do `D:\home\site\wwwroot` složky v konzole Kudu.</span><span class="sxs-lookup"><span data-stu-id="29fc1-296">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the Kudu console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

## <a name="protocol-settings-https"></a><span data-ttu-id="29fc1-297">Nastavení protokolu (HTTPS)</span><span class="sxs-lookup"><span data-stu-id="29fc1-297">Protocol settings (HTTPS)</span></span>

<span data-ttu-id="29fc1-298">Vazby zabezpečeného protokolu umožňují zadat certifikát, který se má použít při reagování na požadavky přes protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="29fc1-298">Secure protocol bindings allow you specify a certificate to use when responding to requests over HTTPS.</span></span> <span data-ttu-id="29fc1-299">Vazba vyžaduje platný privátní certifikát (*. pfx*), který byl vydán pro konkrétní název hostitele.</span><span class="sxs-lookup"><span data-stu-id="29fc1-299">Binding requires a valid private certificate (*.pfx*) issued for the specific hostname.</span></span> <span data-ttu-id="29fc1-300">Další informace najdete v tématu [kurz: vytvoření vazby existujícího vlastního certifikátu SSL k Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="29fc1-300">For more information, see [Tutorial: Bind an existing custom SSL certificate to Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="29fc1-301">Transformace souboru web.config</span><span class="sxs-lookup"><span data-stu-id="29fc1-301">Transform web.config</span></span>

<span data-ttu-id="29fc1-302">Pokud potřebujete transformovat *web.config* při publikování (například nastavit proměnné prostředí na základě konfigurace, profilu nebo prostředí), přečtěte si téma <xref:host-and-deploy/iis/transform-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="29fc1-302">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="29fc1-303">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="29fc1-303">Additional resources</span></span>

* [<span data-ttu-id="29fc1-304">Přehled služby App Service</span><span class="sxs-lookup"><span data-stu-id="29fc1-304">App Service overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="29fc1-305">Azure App Service: nejlepší místo pro hostování aplikací .NET (video s přehledem 55-minut)</span><span class="sxs-lookup"><span data-stu-id="29fc1-305">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span></span>](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [<span data-ttu-id="29fc1-306">Azure pátek: Azure App Service diagnostické prostředí a řešení potíží (12 minut video)</span><span class="sxs-lookup"><span data-stu-id="29fc1-306">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [<span data-ttu-id="29fc1-307">Přehled diagnostiky Azure App Service</span><span class="sxs-lookup"><span data-stu-id="29fc1-307">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

<span data-ttu-id="29fc1-308">Azure App Service na Windows serveru používá [Internetová informační služba (IIS)](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="29fc1-308">Azure App Service on Windows Server uses [Internet Information Services (IIS)](https://www.iis.net/).</span></span> <span data-ttu-id="29fc1-309">Následující témata se týkají základní technologie IIS:</span><span class="sxs-lookup"><span data-stu-id="29fc1-309">The following topics pertain to the underlying IIS technology:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [<span data-ttu-id="29fc1-310">Windows Server – obsah správce IT pro aktuální a předchozí verze</span><span class="sxs-lookup"><span data-stu-id="29fc1-310">Windows Server - IT administrator content for current and previous releases</span></span>](/windows-server/windows-server-versions)
