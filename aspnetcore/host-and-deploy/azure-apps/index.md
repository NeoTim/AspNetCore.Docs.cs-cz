---
title: Deploy ASP.NET Core apps to Azure App Service
author: bradygaster
description: This article contains links to Azure host and deploy resources.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/07/2019
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: f9fc6e706046165c142e19ca38d97ac21914dc9b
ms.sourcegitcommit: a104ba258ae7c0b3ee7c6fa7eaea1ddeb8b6eb73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74478764"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a><span data-ttu-id="ee715-103">Deploy ASP.NET Core apps to Azure App Service</span><span class="sxs-lookup"><span data-stu-id="ee715-103">Deploy ASP.NET Core apps to Azure App Service</span></span>

<span data-ttu-id="ee715-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ee715-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span>

## <a name="useful-resources"></a><span data-ttu-id="ee715-105">Useful resources</span><span class="sxs-lookup"><span data-stu-id="ee715-105">Useful resources</span></span>

<span data-ttu-id="ee715-106">[App Service Documentation](/azure/app-service/) is the home for Azure Apps documentation, tutorials, samples, how-to guides, and other resources.</span><span class="sxs-lookup"><span data-stu-id="ee715-106">[App Service Documentation](/azure/app-service/) is the home for Azure Apps documentation, tutorials, samples, how-to guides, and other resources.</span></span> <span data-ttu-id="ee715-107">Two notable tutorials that pertain to hosting ASP.NET Core apps are:</span><span class="sxs-lookup"><span data-stu-id="ee715-107">Two notable tutorials that pertain to hosting ASP.NET Core apps are:</span></span>

[<span data-ttu-id="ee715-108">Create an ASP.NET Core web app in Azure</span><span class="sxs-lookup"><span data-stu-id="ee715-108">Create an ASP.NET Core web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)  
<span data-ttu-id="ee715-109">Use Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service on Windows.</span><span class="sxs-lookup"><span data-stu-id="ee715-109">Use Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service on Windows.</span></span>

[<span data-ttu-id="ee715-110">Create an ASP.NET Core app in App Service on Linux</span><span class="sxs-lookup"><span data-stu-id="ee715-110">Create an ASP.NET Core app in App Service on Linux</span></span>](/azure/app-service/containers/quickstart-dotnetcore)  
<span data-ttu-id="ee715-111">Use the command line to create and deploy an ASP.NET Core web app to Azure App Service on Linux.</span><span class="sxs-lookup"><span data-stu-id="ee715-111">Use the command line to create and deploy an ASP.NET Core web app to Azure App Service on Linux.</span></span>

<span data-ttu-id="ee715-112">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span><span class="sxs-lookup"><span data-stu-id="ee715-112">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

<span data-ttu-id="ee715-113">Subscribe to the [App Service Announcements](https://github.com/Azure/app-service-announcements/) repository and monitor the issues.</span><span class="sxs-lookup"><span data-stu-id="ee715-113">Subscribe to the [App Service Announcements](https://github.com/Azure/app-service-announcements/) repository and monitor the issues.</span></span> <span data-ttu-id="ee715-114">The App Service team regularly posts announcements and scenarios arriving in App Service.</span><span class="sxs-lookup"><span data-stu-id="ee715-114">The App Service team regularly posts announcements and scenarios arriving in App Service.</span></span>

<span data-ttu-id="ee715-115">The following articles are available in ASP.NET Core documentation:</span><span class="sxs-lookup"><span data-stu-id="ee715-115">The following articles are available in ASP.NET Core documentation:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="ee715-116">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ee715-116">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
<span data-ttu-id="ee715-117">Learn how to create an ASP.NET Core web app using Visual Studio and deploy it to Azure App Service using Git for continuous deployment.</span><span class="sxs-lookup"><span data-stu-id="ee715-117">Learn how to create an ASP.NET Core web app using Visual Studio and deploy it to Azure App Service using Git for continuous deployment.</span></span>

[<span data-ttu-id="ee715-118">Create your first pipeline</span><span class="sxs-lookup"><span data-stu-id="ee715-118">Create your first pipeline</span></span>](/azure/devops/pipelines/get-started-yaml)  
<span data-ttu-id="ee715-119">Set up a CI build for an ASP.NET Core app, then create a continuous deployment release to Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="ee715-119">Set up a CI build for an ASP.NET Core app, then create a continuous deployment release to Azure App Service.</span></span>

[<span data-ttu-id="ee715-120">Azure Web App sandbox</span><span class="sxs-lookup"><span data-stu-id="ee715-120">Azure Web App sandbox</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
<span data-ttu-id="ee715-121">Discover Azure App Service runtime execution limitations enforced by the Azure Apps platform.</span><span class="sxs-lookup"><span data-stu-id="ee715-121">Discover Azure App Service runtime execution limitations enforced by the Azure Apps platform.</span></span>

<xref:test/troubleshoot>  
<span data-ttu-id="ee715-122">Understand and troubleshoot warnings and errors with ASP.NET Core projects.</span><span class="sxs-lookup"><span data-stu-id="ee715-122">Understand and troubleshoot warnings and errors with ASP.NET Core projects.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="ee715-123">Application configuration</span><span class="sxs-lookup"><span data-stu-id="ee715-123">Application configuration</span></span>

### <a name="platform"></a><span data-ttu-id="ee715-124">Platforma</span><span class="sxs-lookup"><span data-stu-id="ee715-124">Platform</span></span>

<span data-ttu-id="ee715-125">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span><span class="sxs-lookup"><span data-stu-id="ee715-125">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="ee715-126">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="ee715-126">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure Portal.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="ee715-127">Runtimes for 64-bit (x64) and 32-bit (x86) apps are present on Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="ee715-127">Runtimes for 64-bit (x64) and 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="ee715-128">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit, but you can deploy 64-bit apps built locally using the [Kudu](https://github.com/projectkudu/kudu/wiki) console or the publish process in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ee715-128">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit, but you can deploy 64-bit apps built locally using the [Kudu](https://github.com/projectkudu/kudu/wiki) console or the publish process in Visual Studio.</span></span> <span data-ttu-id="ee715-129">For more information, see the [Publish and deploy the app](#publish-and-deploy-the-app) section.</span><span class="sxs-lookup"><span data-stu-id="ee715-129">For more information, see the [Publish and deploy the app](#publish-and-deploy-the-app) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="ee715-130">For apps with native dependencies, runtimes for 32-bit (x86) apps are present on Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="ee715-130">For apps with native dependencies, runtimes for 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="ee715-131">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit.</span><span class="sxs-lookup"><span data-stu-id="ee715-131">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit.</span></span>

::: moniker-end

<span data-ttu-id="ee715-132">For more information on .NET Core framework components and distribution methods, such as information on the .NET Core runtime and the .NET Core SDK, see [About .NET Core: Composition](/dotnet/core/about#composition).</span><span class="sxs-lookup"><span data-stu-id="ee715-132">For more information on .NET Core framework components and distribution methods, such as information on the .NET Core runtime and the .NET Core SDK, see [About .NET Core: Composition](/dotnet/core/about#composition).</span></span>

### <a name="packages"></a><span data-ttu-id="ee715-133">Balíčky</span><span class="sxs-lookup"><span data-stu-id="ee715-133">Packages</span></span>

<span data-ttu-id="ee715-134">Include the following NuGet packages to provide automatic logging features for apps deployed to Azure App Service:</span><span class="sxs-lookup"><span data-stu-id="ee715-134">Include the following NuGet packages to provide automatic logging features for apps deployed to Azure App Service:</span></span>

* <span data-ttu-id="ee715-135">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) uses [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) to provide ASP.NET Core light-up integration with Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="ee715-135">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) uses [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) to provide ASP.NET Core light-up integration with Azure App Service.</span></span> <span data-ttu-id="ee715-136">The added logging features are provided by the `Microsoft.AspNetCore.AzureAppServicesIntegration` package.</span><span class="sxs-lookup"><span data-stu-id="ee715-136">The added logging features are provided by the `Microsoft.AspNetCore.AzureAppServicesIntegration` package.</span></span>
* <span data-ttu-id="ee715-137">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executes [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) to add Azure App Service diagnostics logging providers in the `Microsoft.Extensions.Logging.AzureAppServices` package.</span><span class="sxs-lookup"><span data-stu-id="ee715-137">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executes [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) to add Azure App Service diagnostics logging providers in the `Microsoft.Extensions.Logging.AzureAppServices` package.</span></span>
* <span data-ttu-id="ee715-138">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) provides logger implementations to support Azure App Service diagnostics logs and log streaming features.</span><span class="sxs-lookup"><span data-stu-id="ee715-138">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) provides logger implementations to support Azure App Service diagnostics logs and log streaming features.</span></span>

<span data-ttu-id="ee715-139">The preceding packages aren't available from the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="ee715-139">The preceding packages aren't available from the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="ee715-140">Apps that target .NET Framework or reference the `Microsoft.AspNetCore.App` metapackage must explicitly reference the individual packages in the app's project file.</span><span class="sxs-lookup"><span data-stu-id="ee715-140">Apps that target .NET Framework or reference the `Microsoft.AspNetCore.App` metapackage must explicitly reference the individual packages in the app's project file.</span></span>

## <a name="override-app-configuration-using-the-azure-portal"></a><span data-ttu-id="ee715-141">Override app configuration using the Azure Portal</span><span class="sxs-lookup"><span data-stu-id="ee715-141">Override app configuration using the Azure Portal</span></span>

<span data-ttu-id="ee715-142">App settings in the Azure Portal permit you to set environment variables for the app.</span><span class="sxs-lookup"><span data-stu-id="ee715-142">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="ee715-143">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ee715-143">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="ee715-144">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span><span class="sxs-lookup"><span data-stu-id="ee715-144">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="ee715-145">The environment variable is available to the app after the service restarts.</span><span class="sxs-lookup"><span data-stu-id="ee715-145">The environment variable is available to the app after the service restarts.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ee715-146">When an app uses the [Generic Host](xref:fundamentals/host/generic-host), environment variables aren't loaded into an app's configuration by default and the configuration provider must be added by the developer.</span><span class="sxs-lookup"><span data-stu-id="ee715-146">When an app uses the [Generic Host](xref:fundamentals/host/generic-host), environment variables aren't loaded into an app's configuration by default and the configuration provider must be added by the developer.</span></span> <span data-ttu-id="ee715-147">The developer determines the environment variable prefix when the configuration provider is added.</span><span class="sxs-lookup"><span data-stu-id="ee715-147">The developer determines the environment variable prefix when the configuration provider is added.</span></span> <span data-ttu-id="ee715-148">For more information, see <xref:fundamentals/host/generic-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ee715-148">For more information, see <xref:fundamentals/host/generic-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ee715-149">When an app builds the host using [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), environment variables that configure the host use the `ASPNETCORE_` prefix.</span><span class="sxs-lookup"><span data-stu-id="ee715-149">When an app builds the host using [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), environment variables that configure the host use the `ASPNETCORE_` prefix.</span></span> <span data-ttu-id="ee715-150">For more information, see <xref:fundamentals/host/web-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ee715-150">For more information, see <xref:fundamentals/host/web-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="ee715-151">Proxy server and load balancer scenarios</span><span class="sxs-lookup"><span data-stu-id="ee715-151">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="ee715-152">The [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), which configures Forwarded Headers Middleware when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span><span class="sxs-lookup"><span data-stu-id="ee715-152">The [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), which configures Forwarded Headers Middleware when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="ee715-153">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span><span class="sxs-lookup"><span data-stu-id="ee715-153">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="ee715-154">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="ee715-154">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="monitoring-and-logging"></a><span data-ttu-id="ee715-155">Monitoring and logging</span><span class="sxs-lookup"><span data-stu-id="ee715-155">Monitoring and logging</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ee715-156">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Integration**.</span><span class="sxs-lookup"><span data-stu-id="ee715-156">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Integration**.</span></span> <span data-ttu-id="ee715-157">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="ee715-157">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ee715-158">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Extensions**.</span><span class="sxs-lookup"><span data-stu-id="ee715-158">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Extensions**.</span></span> <span data-ttu-id="ee715-159">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="ee715-159">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

<span data-ttu-id="ee715-160">For monitoring, logging, and troubleshooting information, see the following articles:</span><span class="sxs-lookup"><span data-stu-id="ee715-160">For monitoring, logging, and troubleshooting information, see the following articles:</span></span>

[<span data-ttu-id="ee715-161">Monitor apps in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="ee715-161">Monitor apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)  
<span data-ttu-id="ee715-162">Learn how to review quotas and metrics for apps and App Service plans.</span><span class="sxs-lookup"><span data-stu-id="ee715-162">Learn how to review quotas and metrics for apps and App Service plans.</span></span>

[<span data-ttu-id="ee715-163">Enable diagnostics logging for apps in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="ee715-163">Enable diagnostics logging for apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)  
<span data-ttu-id="ee715-164">Discover how to enable and access diagnostic logging for HTTP status codes, failed requests, and web server activity.</span><span class="sxs-lookup"><span data-stu-id="ee715-164">Discover how to enable and access diagnostic logging for HTTP status codes, failed requests, and web server activity.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="ee715-165">Understand common approaches to handling errors in ASP.NET Core apps.</span><span class="sxs-lookup"><span data-stu-id="ee715-165">Understand common approaches to handling errors in ASP.NET Core apps.</span></span>

<xref:test/troubleshoot-azure-iis>  
<span data-ttu-id="ee715-166">Learn how to diagnose issues with Azure App Service deployments with ASP.NET Core apps.</span><span class="sxs-lookup"><span data-stu-id="ee715-166">Learn how to diagnose issues with Azure App Service deployments with ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-iis-errors-reference>  
<span data-ttu-id="ee715-167">See the common deployment configuration errors for apps hosted by Azure App Service/IIS with troubleshooting advice.</span><span class="sxs-lookup"><span data-stu-id="ee715-167">See the common deployment configuration errors for apps hosted by Azure App Service/IIS with troubleshooting advice.</span></span>

## <a name="data-protection-key-ring-and-deployment-slots"></a><span data-ttu-id="ee715-168">Data Protection key ring and deployment slots</span><span class="sxs-lookup"><span data-stu-id="ee715-168">Data Protection key ring and deployment slots</span></span>

<span data-ttu-id="ee715-169">[Data Protection keys](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span><span class="sxs-lookup"><span data-stu-id="ee715-169">[Data Protection keys](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="ee715-170">This folder is backed by network storage and is synchronized across all machines hosting the app.</span><span class="sxs-lookup"><span data-stu-id="ee715-170">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span> <span data-ttu-id="ee715-171">Keys aren't protected at rest.</span><span class="sxs-lookup"><span data-stu-id="ee715-171">Keys aren't protected at rest.</span></span> <span data-ttu-id="ee715-172">This folder supplies the key ring to all instances of an app in a single deployment slot.</span><span class="sxs-lookup"><span data-stu-id="ee715-172">This folder supplies the key ring to all instances of an app in a single deployment slot.</span></span> <span data-ttu-id="ee715-173">Separate deployment slots, such as Staging and Production, don't share a key ring.</span><span class="sxs-lookup"><span data-stu-id="ee715-173">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span>

<span data-ttu-id="ee715-174">When swapping between deployment slots, any system using data protection won't be able to decrypt stored data using the key ring inside the previous slot.</span><span class="sxs-lookup"><span data-stu-id="ee715-174">When swapping between deployment slots, any system using data protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="ee715-175">ASP.NET Cookie Middleware uses data protection to protect its cookies.</span><span class="sxs-lookup"><span data-stu-id="ee715-175">ASP.NET Cookie Middleware uses data protection to protect its cookies.</span></span> <span data-ttu-id="ee715-176">This leads to users being signed out of an app that uses the standard ASP.NET Cookie Middleware.</span><span class="sxs-lookup"><span data-stu-id="ee715-176">This leads to users being signed out of an app that uses the standard ASP.NET Cookie Middleware.</span></span> <span data-ttu-id="ee715-177">For a slot-independent key ring solution, use an external key ring provider, such as:</span><span class="sxs-lookup"><span data-stu-id="ee715-177">For a slot-independent key ring solution, use an external key ring provider, such as:</span></span>

* <span data-ttu-id="ee715-178">Azure Blob Storage</span><span class="sxs-lookup"><span data-stu-id="ee715-178">Azure Blob Storage</span></span>
* <span data-ttu-id="ee715-179">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="ee715-179">Azure Key Vault</span></span>
* <span data-ttu-id="ee715-180">SQL store</span><span class="sxs-lookup"><span data-stu-id="ee715-180">SQL store</span></span>
* <span data-ttu-id="ee715-181">Redis cache</span><span class="sxs-lookup"><span data-stu-id="ee715-181">Redis cache</span></span>

<span data-ttu-id="ee715-182">Další informace najdete v tématu <xref:security/data-protection/implementation/key-storage-providers>.</span><span class="sxs-lookup"><span data-stu-id="ee715-182">For more information, see <xref:security/data-protection/implementation/key-storage-providers>.</span></span>
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>

## <a name="deploy-aspnet-core-30-to-azure-app-service"></a><span data-ttu-id="ee715-183">Deploy ASP.NET Core 3.0 to Azure App Service</span><span class="sxs-lookup"><span data-stu-id="ee715-183">Deploy ASP.NET Core 3.0 to Azure App Service</span></span>

<span data-ttu-id="ee715-184">ASP.NET Core 3.0 is supported on Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="ee715-184">ASP.NET Core 3.0 is supported on Azure App Service.</span></span> <span data-ttu-id="ee715-185">To deploy a preview release of a .NET Core version later than .NET Core 3.0, use one of the following techniques.</span><span class="sxs-lookup"><span data-stu-id="ee715-185">To deploy a preview release of a .NET Core version later than .NET Core 3.0, use one of the following techniques.</span></span> <span data-ttu-id="ee715-186">These approaches are also used when the runtime is available but the SDK hasn't been installed on Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="ee715-186">These approaches are also used when the runtime is available but the SDK hasn't been installed on Azure App Service.</span></span>

* [<span data-ttu-id="ee715-187">Specify the .NET Core SDK Version using Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="ee715-187">Specify the .NET Core SDK Version using Azure Pipelines</span></span>](#specify-the-net-core-sdk-version-using-azure-pipelines)
* <span data-ttu-id="ee715-188">[Deploy a self-contained preview app](#deploy-a-self-contained-preview-app).</span><span class="sxs-lookup"><span data-stu-id="ee715-188">[Deploy a self-contained preview app](#deploy-a-self-contained-preview-app).</span></span>
* <span data-ttu-id="ee715-189">[Use Docker with Web Apps for containers](#use-docker-with-web-apps-for-containers).</span><span class="sxs-lookup"><span data-stu-id="ee715-189">[Use Docker with Web Apps for containers](#use-docker-with-web-apps-for-containers).</span></span>
* <span data-ttu-id="ee715-190">[Install the preview site extension](#install-the-preview-site-extension).</span><span class="sxs-lookup"><span data-stu-id="ee715-190">[Install the preview site extension](#install-the-preview-site-extension).</span></span>

### <a name="specify-the-net-core-sdk-version-using-azure-pipelines"></a><span data-ttu-id="ee715-191">Specify the .NET Core SDK Version using Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="ee715-191">Specify the .NET Core SDK Version using Azure Pipelines</span></span>

<span data-ttu-id="ee715-192">Use [Azure App Service CI/CD scenarios](/azure/app-service/deploy-continuous-deployment) to set up a continuous integration build with Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="ee715-192">Use [Azure App Service CI/CD scenarios](/azure/app-service/deploy-continuous-deployment) to set up a continuous integration build with Azure DevOps.</span></span> <span data-ttu-id="ee715-193">After the Azure DevOps build is created, optionally configure the build to use a specific SDK version.</span><span class="sxs-lookup"><span data-stu-id="ee715-193">After the Azure DevOps build is created, optionally configure the build to use a specific SDK version.</span></span> 

#### <a name="specify-the-net-core-sdk-version"></a><span data-ttu-id="ee715-194">Specify the .NET Core SDK version</span><span class="sxs-lookup"><span data-stu-id="ee715-194">Specify the .NET Core SDK version</span></span>

<span data-ttu-id="ee715-195">When using the App Service deployment center to create an Azure DevOps build, the default build pipeline includes steps for `Restore`, `Build`, `Test`, and `Publish`.</span><span class="sxs-lookup"><span data-stu-id="ee715-195">When using the App Service deployment center to create an Azure DevOps build, the default build pipeline includes steps for `Restore`, `Build`, `Test`, and `Publish`.</span></span> <span data-ttu-id="ee715-196">To specify the SDK version, select the **Add (+)** button in the Agent job list to add a new step.</span><span class="sxs-lookup"><span data-stu-id="ee715-196">To specify the SDK version, select the **Add (+)** button in the Agent job list to add a new step.</span></span> <span data-ttu-id="ee715-197">Search for **.NET Core SDK** in the search bar.</span><span class="sxs-lookup"><span data-stu-id="ee715-197">Search for **.NET Core SDK** in the search bar.</span></span> 

![Add the .NET Core SDK step](index/add-sdk-step.png)

<span data-ttu-id="ee715-199">Move the step into the first position in the build so that the steps following it use the specified version of the .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="ee715-199">Move the step into the first position in the build so that the steps following it use the specified version of the .NET Core SDK.</span></span> <span data-ttu-id="ee715-200">Specify the version of the .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="ee715-200">Specify the version of the .NET Core SDK.</span></span> <span data-ttu-id="ee715-201">In this example, the SDK is set to `3.0.100`.</span><span class="sxs-lookup"><span data-stu-id="ee715-201">In this example, the SDK is set to `3.0.100`.</span></span>

![Completed SDK step](index/sdk-step-first-place.png)

<span data-ttu-id="ee715-203">To publish a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), configure SCD in the `Publish` step and provide the [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="ee715-203">To publish a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), configure SCD in the `Publish` step and provide the [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

![Self-contained publish](index/self-contained.png)

### <a name="deploy-a-self-contained-preview-app"></a><span data-ttu-id="ee715-205">Deploy a self-contained preview app</span><span class="sxs-lookup"><span data-stu-id="ee715-205">Deploy a self-contained preview app</span></span>

<span data-ttu-id="ee715-206">A [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) that targets a preview runtime carries the preview runtime in the deployment.</span><span class="sxs-lookup"><span data-stu-id="ee715-206">A [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) that targets a preview runtime carries the preview runtime in the deployment.</span></span>

<span data-ttu-id="ee715-207">When deploying a self-contained app:</span><span class="sxs-lookup"><span data-stu-id="ee715-207">When deploying a self-contained app:</span></span>

* <span data-ttu-id="ee715-208">The site in Azure App Service doesn't require the [preview site extension](#install-the-preview-site-extension).</span><span class="sxs-lookup"><span data-stu-id="ee715-208">The site in Azure App Service doesn't require the [preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="ee715-209">The app must be published following a different approach than when publishing for a [framework-dependent deployment (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="ee715-209">The app must be published following a different approach than when publishing for a [framework-dependent deployment (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="ee715-210">Follow the guidance in the [Deploy the app self-contained](#deploy-the-app-self-contained) section.</span><span class="sxs-lookup"><span data-stu-id="ee715-210">Follow the guidance in the [Deploy the app self-contained](#deploy-the-app-self-contained) section.</span></span>

### <a name="use-docker-with-web-apps-for-containers"></a><span data-ttu-id="ee715-211">Use Docker with Web Apps for containers</span><span class="sxs-lookup"><span data-stu-id="ee715-211">Use Docker with Web Apps for containers</span></span>

<span data-ttu-id="ee715-212">The [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contains the latest preview Docker images.</span><span class="sxs-lookup"><span data-stu-id="ee715-212">The [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contains the latest preview Docker images.</span></span> <span data-ttu-id="ee715-213">The images can be used as a base image.</span><span class="sxs-lookup"><span data-stu-id="ee715-213">The images can be used as a base image.</span></span> <span data-ttu-id="ee715-214">Use the image and deploy to Web Apps for Containers normally.</span><span class="sxs-lookup"><span data-stu-id="ee715-214">Use the image and deploy to Web Apps for Containers normally.</span></span>

### <a name="install-the-preview-site-extension"></a><span data-ttu-id="ee715-215">Install the preview site extension</span><span class="sxs-lookup"><span data-stu-id="ee715-215">Install the preview site extension</span></span>

<span data-ttu-id="ee715-216">If a problem occurs using the preview site extension, open an [aspnet/AspNetCore issue](https://github.com/aspnet/AspNetCore/issues).</span><span class="sxs-lookup"><span data-stu-id="ee715-216">If a problem occurs using the preview site extension, open an [aspnet/AspNetCore issue](https://github.com/aspnet/AspNetCore/issues).</span></span>

1. <span data-ttu-id="ee715-217">From the Azure Portal, navigate to the App Service.</span><span class="sxs-lookup"><span data-stu-id="ee715-217">From the Azure Portal, navigate to the App Service.</span></span>
1. <span data-ttu-id="ee715-218">Select the web app.</span><span class="sxs-lookup"><span data-stu-id="ee715-218">Select the web app.</span></span>
1. <span data-ttu-id="ee715-219">Type "ex" in the search box to filter for "Extensions" or scroll down the list of management tools.</span><span class="sxs-lookup"><span data-stu-id="ee715-219">Type "ex" in the search box to filter for "Extensions" or scroll down the list of management tools.</span></span>
1. <span data-ttu-id="ee715-220">Select **Extensions**.</span><span class="sxs-lookup"><span data-stu-id="ee715-220">Select **Extensions**.</span></span>
1. <span data-ttu-id="ee715-221">Select **Add**.</span><span class="sxs-lookup"><span data-stu-id="ee715-221">Select **Add**.</span></span>
1. <span data-ttu-id="ee715-222">Select the **ASP.NET Core {X.Y} ({x64|x86}) Runtime** extension from the list, where `{X.Y}` is the ASP.NET Core preview version and `{x64|x86}` specifies the platform.</span><span class="sxs-lookup"><span data-stu-id="ee715-222">Select the **ASP.NET Core {X.Y} ({x64|x86}) Runtime** extension from the list, where `{X.Y}` is the ASP.NET Core preview version and `{x64|x86}` specifies the platform.</span></span>
1. <span data-ttu-id="ee715-223">Select **OK** to accept the legal terms.</span><span class="sxs-lookup"><span data-stu-id="ee715-223">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="ee715-224">Select **OK** to install the extension.</span><span class="sxs-lookup"><span data-stu-id="ee715-224">Select **OK** to install the extension.</span></span>

<span data-ttu-id="ee715-225">When the operation completes, the latest .NET Core preview is installed.</span><span class="sxs-lookup"><span data-stu-id="ee715-225">When the operation completes, the latest .NET Core preview is installed.</span></span> <span data-ttu-id="ee715-226">Verify the installation:</span><span class="sxs-lookup"><span data-stu-id="ee715-226">Verify the installation:</span></span>

1. <span data-ttu-id="ee715-227">Select **Advanced Tools**.</span><span class="sxs-lookup"><span data-stu-id="ee715-227">Select **Advanced Tools**.</span></span>
1. <span data-ttu-id="ee715-228">Select **Go** in **Advanced Tools**.</span><span class="sxs-lookup"><span data-stu-id="ee715-228">Select **Go** in **Advanced Tools**.</span></span>
1. <span data-ttu-id="ee715-229">Select the **Debug console** > **PowerShell** menu item.</span><span class="sxs-lookup"><span data-stu-id="ee715-229">Select the **Debug console** > **PowerShell** menu item.</span></span>
1. <span data-ttu-id="ee715-230">At the PowerShell prompt, execute the following command.</span><span class="sxs-lookup"><span data-stu-id="ee715-230">At the PowerShell prompt, execute the following command.</span></span> <span data-ttu-id="ee715-231">Substitute the ASP.NET Core runtime version for `{X.Y}` and the platform for `{PLATFORM}` in the command:</span><span class="sxs-lookup"><span data-stu-id="ee715-231">Substitute the ASP.NET Core runtime version for `{X.Y}` and the platform for `{PLATFORM}` in the command:</span></span>

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   <span data-ttu-id="ee715-232">The command returns `True` when the x64 preview runtime is installed.</span><span class="sxs-lookup"><span data-stu-id="ee715-232">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="ee715-233">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span><span class="sxs-lookup"><span data-stu-id="ee715-233">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="ee715-234">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure portal.</span><span class="sxs-lookup"><span data-stu-id="ee715-234">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure portal.</span></span>
>
> <span data-ttu-id="ee715-235">If the app is run in in-process mode and the platform architecture is configured for 64-bit (x64), the ASP.NET Core Module uses the 64-bit preview runtime, if present.</span><span class="sxs-lookup"><span data-stu-id="ee715-235">If the app is run in in-process mode and the platform architecture is configured for 64-bit (x64), the ASP.NET Core Module uses the 64-bit preview runtime, if present.</span></span> <span data-ttu-id="ee715-236">Install the **ASP.NET Core {X.Y} (x64) Runtime** extension using the Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="ee715-236">Install the **ASP.NET Core {X.Y} (x64) Runtime** extension using the Azure Portal.</span></span>
>
> <span data-ttu-id="ee715-237">After installing the x64 preview runtime, run the following command in the Azure Kudu PowerShell command window to verify the installation.</span><span class="sxs-lookup"><span data-stu-id="ee715-237">After installing the x64 preview runtime, run the following command in the Azure Kudu PowerShell command window to verify the installation.</span></span> <span data-ttu-id="ee715-238">Substitute the ASP.NET Core runtime version for `{X.Y}` in the following command:</span><span class="sxs-lookup"><span data-stu-id="ee715-238">Substitute the ASP.NET Core runtime version for `{X.Y}` in the following command:</span></span>
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> <span data-ttu-id="ee715-239">The command returns `True` when the x64 preview runtime is installed.</span><span class="sxs-lookup"><span data-stu-id="ee715-239">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="ee715-240">**ASP.NET Core Extensions** enables additional functionality for ASP.NET Core on Azure App Services, such as enabling Azure logging.</span><span class="sxs-lookup"><span data-stu-id="ee715-240">**ASP.NET Core Extensions** enables additional functionality for ASP.NET Core on Azure App Services, such as enabling Azure logging.</span></span> <span data-ttu-id="ee715-241">The extension is installed automatically when deploying from Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ee715-241">The extension is installed automatically when deploying from Visual Studio.</span></span> <span data-ttu-id="ee715-242">If the extension isn't installed, install it for the app.</span><span class="sxs-lookup"><span data-stu-id="ee715-242">If the extension isn't installed, install it for the app.</span></span>

<span data-ttu-id="ee715-243">**Use the preview site extension with an ARM template**</span><span class="sxs-lookup"><span data-stu-id="ee715-243">**Use the preview site extension with an ARM template**</span></span>

<span data-ttu-id="ee715-244">If an ARM template is used to create and deploy apps, the `siteextensions` resource type can be used to add the site extension to a web app.</span><span class="sxs-lookup"><span data-stu-id="ee715-244">If an ARM template is used to create and deploy apps, the `siteextensions` resource type can be used to add the site extension to a web app.</span></span> <span data-ttu-id="ee715-245">Příklad:</span><span class="sxs-lookup"><span data-stu-id="ee715-245">For example:</span></span>

[!code-json[](index/sample/arm.json?highlight=2)]

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="ee715-246">Publish and deploy the app</span><span class="sxs-lookup"><span data-stu-id="ee715-246">Publish and deploy the app</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="ee715-247">For a 64-bit deployment:</span><span class="sxs-lookup"><span data-stu-id="ee715-247">For a 64-bit deployment:</span></span>

* <span data-ttu-id="ee715-248">Use a 64-bit .NET Core SDK to build a 64-bit app.</span><span class="sxs-lookup"><span data-stu-id="ee715-248">Use a 64-bit .NET Core SDK to build a 64-bit app.</span></span>
* <span data-ttu-id="ee715-249">Set the **Platform** to **64 Bit** in the App Service's **Configuration** > **General settings**.</span><span class="sxs-lookup"><span data-stu-id="ee715-249">Set the **Platform** to **64 Bit** in the App Service's **Configuration** > **General settings**.</span></span> <span data-ttu-id="ee715-250">The app must use a Basic or higher service plan to enable the choice of platform bitness.</span><span class="sxs-lookup"><span data-stu-id="ee715-250">The app must use a Basic or higher service plan to enable the choice of platform bitness.</span></span>

::: moniker-end

### <a name="deploy-the-app-framework-dependent"></a><span data-ttu-id="ee715-251">Deploy the app framework-dependent</span><span class="sxs-lookup"><span data-stu-id="ee715-251">Deploy the app framework-dependent</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ee715-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ee715-252">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ee715-253">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span><span class="sxs-lookup"><span data-stu-id="ee715-253">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="ee715-254">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span><span class="sxs-lookup"><span data-stu-id="ee715-254">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="ee715-255">Select **Advanced**.</span><span class="sxs-lookup"><span data-stu-id="ee715-255">Select **Advanced**.</span></span> <span data-ttu-id="ee715-256">The **Publish** dialog opens.</span><span class="sxs-lookup"><span data-stu-id="ee715-256">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="ee715-257">In the **Publish** dialog:</span><span class="sxs-lookup"><span data-stu-id="ee715-257">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="ee715-258">Confirm that the **Release** configuration is selected.</span><span class="sxs-lookup"><span data-stu-id="ee715-258">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="ee715-259">Open the **Deployment Mode** drop-down list and select **Framework-Dependent**.</span><span class="sxs-lookup"><span data-stu-id="ee715-259">Open the **Deployment Mode** drop-down list and select **Framework-Dependent**.</span></span>
   * <span data-ttu-id="ee715-260">Select **Portable** as the **Target Runtime**.</span><span class="sxs-lookup"><span data-stu-id="ee715-260">Select **Portable** as the **Target Runtime**.</span></span>
   * <span data-ttu-id="ee715-261">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span><span class="sxs-lookup"><span data-stu-id="ee715-261">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="ee715-262">Select **Save**.</span><span class="sxs-lookup"><span data-stu-id="ee715-262">Select **Save**.</span></span>
1. <span data-ttu-id="ee715-263">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span><span class="sxs-lookup"><span data-stu-id="ee715-263">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="ee715-264">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="ee715-264">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="ee715-265">In the project file, don't specify a [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="ee715-265">In the project file, don't specify a [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

1. <span data-ttu-id="ee715-266">From a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span><span class="sxs-lookup"><span data-stu-id="ee715-266">From a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="ee715-267">In the following example, the app is published as a framework-dependent app:</span><span class="sxs-lookup"><span data-stu-id="ee715-267">In the following example, the app is published as a framework-dependent app:</span></span>

   ```console
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="ee715-268">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* directory to the site in App Service.</span><span class="sxs-lookup"><span data-stu-id="ee715-268">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="ee715-269">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the [Kudu](https://github.com/projectkudu/kudu/wiki) console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span><span class="sxs-lookup"><span data-stu-id="ee715-269">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the [Kudu](https://github.com/projectkudu/kudu/wiki) console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

### <a name="deploy-the-app-self-contained"></a><span data-ttu-id="ee715-270">Deploy the app self-contained</span><span class="sxs-lookup"><span data-stu-id="ee715-270">Deploy the app self-contained</span></span>

<span data-ttu-id="ee715-271">Use Visual Studio or the command-line interface (CLI) tools for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="ee715-271">Use Visual Studio or the command-line interface (CLI) tools for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ee715-272">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ee715-272">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ee715-273">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span><span class="sxs-lookup"><span data-stu-id="ee715-273">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="ee715-274">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span><span class="sxs-lookup"><span data-stu-id="ee715-274">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="ee715-275">Select **Advanced**.</span><span class="sxs-lookup"><span data-stu-id="ee715-275">Select **Advanced**.</span></span> <span data-ttu-id="ee715-276">The **Publish** dialog opens.</span><span class="sxs-lookup"><span data-stu-id="ee715-276">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="ee715-277">In the **Publish** dialog:</span><span class="sxs-lookup"><span data-stu-id="ee715-277">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="ee715-278">Confirm that the **Release** configuration is selected.</span><span class="sxs-lookup"><span data-stu-id="ee715-278">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="ee715-279">Open the **Deployment Mode** drop-down list and select **Self-Contained**.</span><span class="sxs-lookup"><span data-stu-id="ee715-279">Open the **Deployment Mode** drop-down list and select **Self-Contained**.</span></span>
   * <span data-ttu-id="ee715-280">Select the target runtime from the **Target Runtime** drop-down list.</span><span class="sxs-lookup"><span data-stu-id="ee715-280">Select the target runtime from the **Target Runtime** drop-down list.</span></span> <span data-ttu-id="ee715-281">Výchozí hodnota je `win-x86`.</span><span class="sxs-lookup"><span data-stu-id="ee715-281">The default is `win-x86`.</span></span>
   * <span data-ttu-id="ee715-282">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span><span class="sxs-lookup"><span data-stu-id="ee715-282">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="ee715-283">Select **Save**.</span><span class="sxs-lookup"><span data-stu-id="ee715-283">Select **Save**.</span></span>
1. <span data-ttu-id="ee715-284">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span><span class="sxs-lookup"><span data-stu-id="ee715-284">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="ee715-285">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="ee715-285">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="ee715-286">In the project file, specify one or more [Runtime Identifiers (RIDs)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="ee715-286">In the project file, specify one or more [Runtime Identifiers (RIDs)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="ee715-287">Use `<RuntimeIdentifier>` (singular) for a single RID, or use `<RuntimeIdentifiers>` (plural) to provide a semicolon-delimited list of RIDs.</span><span class="sxs-lookup"><span data-stu-id="ee715-287">Use `<RuntimeIdentifier>` (singular) for a single RID, or use `<RuntimeIdentifiers>` (plural) to provide a semicolon-delimited list of RIDs.</span></span> <span data-ttu-id="ee715-288">In the following example, the `win-x86` RID is specified:</span><span class="sxs-lookup"><span data-stu-id="ee715-288">In the following example, the `win-x86` RID is specified:</span></span>

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. <span data-ttu-id="ee715-289">From a command shell, publish the app in Release configuration for the host's runtime with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span><span class="sxs-lookup"><span data-stu-id="ee715-289">From a command shell, publish the app in Release configuration for the host's runtime with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="ee715-290">In the following example, the app is published for the `win-x86` RID.</span><span class="sxs-lookup"><span data-stu-id="ee715-290">In the following example, the app is published for the `win-x86` RID.</span></span> <span data-ttu-id="ee715-291">The RID supplied to the `--runtime` option must be provided in the `<RuntimeIdentifier>` (or `<RuntimeIdentifiers>`) property in the project file.</span><span class="sxs-lookup"><span data-stu-id="ee715-291">The RID supplied to the `--runtime` option must be provided in the `<RuntimeIdentifier>` (or `<RuntimeIdentifiers>`) property in the project file.</span></span>

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. <span data-ttu-id="ee715-292">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory to the site in App Service.</span><span class="sxs-lookup"><span data-stu-id="ee715-292">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="ee715-293">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the Kudu console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span><span class="sxs-lookup"><span data-stu-id="ee715-293">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the Kudu console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

## <a name="protocol-settings-https"></a><span data-ttu-id="ee715-294">Protocol settings (HTTPS)</span><span class="sxs-lookup"><span data-stu-id="ee715-294">Protocol settings (HTTPS)</span></span>

<span data-ttu-id="ee715-295">Secure protocol bindings allow you specify a certificate to use when responding to requests over HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ee715-295">Secure protocol bindings allow you specify a certificate to use when responding to requests over HTTPS.</span></span> <span data-ttu-id="ee715-296">Binding requires a valid private certificate ( *.pfx*) issued for the specific hostname.</span><span class="sxs-lookup"><span data-stu-id="ee715-296">Binding requires a valid private certificate (*.pfx*) issued for the specific hostname.</span></span> <span data-ttu-id="ee715-297">For more information, see [Tutorial: Bind an existing custom SSL certificate to Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="ee715-297">For more information, see [Tutorial: Bind an existing custom SSL certificate to Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="ee715-298">Transformace souboru web.config</span><span class="sxs-lookup"><span data-stu-id="ee715-298">Transform web.config</span></span>

<span data-ttu-id="ee715-299">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="ee715-299">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ee715-300">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ee715-300">Additional resources</span></span>

* [<span data-ttu-id="ee715-301">App Service overview</span><span class="sxs-lookup"><span data-stu-id="ee715-301">App Service overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="ee715-302">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span><span class="sxs-lookup"><span data-stu-id="ee715-302">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span></span>](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [<span data-ttu-id="ee715-303">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span><span class="sxs-lookup"><span data-stu-id="ee715-303">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [<span data-ttu-id="ee715-304">Azure App Service diagnostics overview</span><span class="sxs-lookup"><span data-stu-id="ee715-304">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

<span data-ttu-id="ee715-305">Azure App Service on Windows Server uses [Internet Information Services (IIS)](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="ee715-305">Azure App Service on Windows Server uses [Internet Information Services (IIS)](https://www.iis.net/).</span></span> <span data-ttu-id="ee715-306">The following topics pertain to the underlying IIS technology:</span><span class="sxs-lookup"><span data-stu-id="ee715-306">The following topics pertain to the underlying IIS technology:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [<span data-ttu-id="ee715-307">Windows Server - IT administrator content for current and previous releases</span><span class="sxs-lookup"><span data-stu-id="ee715-307">Windows Server - IT administrator content for current and previous releases</span></span>](/windows-server/windows-server-versions)
