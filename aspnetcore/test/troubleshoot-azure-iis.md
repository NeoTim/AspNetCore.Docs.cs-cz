---
title: Troubleshoot ASP.NET Core on Azure App Service and IIS
author: guardrex
description: Learn how to diagnose problems with Azure App Service and Internet Information Services (IIS) deployments of ASP.NET Core apps.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/20/2019
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: 49a0f59fb6930235de10c726f3695f2a5352efb2
ms.sourcegitcommit: 8157e5a351f49aeef3769f7d38b787b4386aad5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74251968"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="98c23-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span><span class="sxs-lookup"><span data-stu-id="98c23-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="98c23-104">By [Luke Latham](https://github.com/guardrex) and [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="98c23-104">By [Luke Latham](https://github.com/guardrex) and [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="98c23-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span><span class="sxs-lookup"><span data-stu-id="98c23-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="98c23-106">App startup errors</span><span class="sxs-lookup"><span data-stu-id="98c23-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="98c23-107">Explains common startup HTTP status code scenarios.</span><span class="sxs-lookup"><span data-stu-id="98c23-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="98c23-108">Troubleshoot on Azure App Service</span><span class="sxs-lookup"><span data-stu-id="98c23-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="98c23-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="98c23-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="98c23-110">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="98c23-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="98c23-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span><span class="sxs-lookup"><span data-stu-id="98c23-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="98c23-112">The guidance applies to both Windows Server and Windows desktop deployments.</span><span class="sxs-lookup"><span data-stu-id="98c23-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="98c23-113">Clear package caches</span><span class="sxs-lookup"><span data-stu-id="98c23-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="98c23-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span><span class="sxs-lookup"><span data-stu-id="98c23-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="98c23-115">Additional resources</span><span class="sxs-lookup"><span data-stu-id="98c23-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="98c23-116">Lists additional troubleshooting topics.</span><span class="sxs-lookup"><span data-stu-id="98c23-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="98c23-117">App startup errors</span><span class="sxs-lookup"><span data-stu-id="98c23-117">App startup errors</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="98c23-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span><span class="sxs-lookup"><span data-stu-id="98c23-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="98c23-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span><span class="sxs-lookup"><span data-stu-id="98c23-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="98c23-120">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span><span class="sxs-lookup"><span data-stu-id="98c23-120">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="98c23-121">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span><span class="sxs-lookup"><span data-stu-id="98c23-121">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

::: moniker-end

### <a name="40314-forbidden"></a><span data-ttu-id="98c23-122">403.14 Forbidden</span><span class="sxs-lookup"><span data-stu-id="98c23-122">403.14 Forbidden</span></span>

<span data-ttu-id="98c23-123">The app fails to start.</span><span class="sxs-lookup"><span data-stu-id="98c23-123">The app fails to start.</span></span> <span data-ttu-id="98c23-124">The following error is logged:</span><span class="sxs-lookup"><span data-stu-id="98c23-124">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="98c23-125">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span><span class="sxs-lookup"><span data-stu-id="98c23-125">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="98c23-126">The app is deployed to the wrong folder on the hosting system.</span><span class="sxs-lookup"><span data-stu-id="98c23-126">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="98c23-127">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span><span class="sxs-lookup"><span data-stu-id="98c23-127">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="98c23-128">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span><span class="sxs-lookup"><span data-stu-id="98c23-128">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="98c23-129">Perform the following steps:</span><span class="sxs-lookup"><span data-stu-id="98c23-129">Perform the following steps:</span></span>

1. <span data-ttu-id="98c23-130">Delete all of the files and folders from the deployment folder on the hosting system.</span><span class="sxs-lookup"><span data-stu-id="98c23-130">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="98c23-131">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span><span class="sxs-lookup"><span data-stu-id="98c23-131">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="98c23-132">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span><span class="sxs-lookup"><span data-stu-id="98c23-132">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="98c23-133">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span><span class="sxs-lookup"><span data-stu-id="98c23-133">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="98c23-134">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span><span class="sxs-lookup"><span data-stu-id="98c23-134">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="98c23-135">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span><span class="sxs-lookup"><span data-stu-id="98c23-135">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="98c23-136">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="98c23-136">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="98c23-137">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="98c23-137">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="98c23-138">500 Internal Server Error</span><span class="sxs-lookup"><span data-stu-id="98c23-138">500 Internal Server Error</span></span>

<span data-ttu-id="98c23-139">The app starts, but an error prevents the server from fulfilling the request.</span><span class="sxs-lookup"><span data-stu-id="98c23-139">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="98c23-140">This error occurs within the app's code during startup or while creating a response.</span><span class="sxs-lookup"><span data-stu-id="98c23-140">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="98c23-141">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span><span class="sxs-lookup"><span data-stu-id="98c23-141">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="98c23-142">The Application Event Log usually states that the app started normally.</span><span class="sxs-lookup"><span data-stu-id="98c23-142">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="98c23-143">From the server's perspective, that's correct.</span><span class="sxs-lookup"><span data-stu-id="98c23-143">From the server's perspective, that's correct.</span></span> <span data-ttu-id="98c23-144">The app did start, but it can't generate a valid response.</span><span class="sxs-lookup"><span data-stu-id="98c23-144">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="98c23-145">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span><span class="sxs-lookup"><span data-stu-id="98c23-145">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

::: moniker range="= aspnetcore-2.2"

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="98c23-146">500.0 In-Process Handler Load Failure</span><span class="sxs-lookup"><span data-stu-id="98c23-146">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="98c23-147">The worker process fails.</span><span class="sxs-lookup"><span data-stu-id="98c23-147">The worker process fails.</span></span> <span data-ttu-id="98c23-148">The app doesn't start.</span><span class="sxs-lookup"><span data-stu-id="98c23-148">The app doesn't start.</span></span>

<span data-ttu-id="98c23-149">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span><span class="sxs-lookup"><span data-stu-id="98c23-149">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="98c23-150">Check that:</span><span class="sxs-lookup"><span data-stu-id="98c23-150">Check that:</span></span>

* <span data-ttu-id="98c23-151">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="98c23-151">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="98c23-152">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span><span class="sxs-lookup"><span data-stu-id="98c23-152">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="98c23-153">500.0 Out-Of-Process Handler Load Failure</span><span class="sxs-lookup"><span data-stu-id="98c23-153">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="98c23-154">The worker process fails.</span><span class="sxs-lookup"><span data-stu-id="98c23-154">The worker process fails.</span></span> <span data-ttu-id="98c23-155">The app doesn't start.</span><span class="sxs-lookup"><span data-stu-id="98c23-155">The app doesn't start.</span></span>

<span data-ttu-id="98c23-156">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span><span class="sxs-lookup"><span data-stu-id="98c23-156">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="98c23-157">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span><span class="sxs-lookup"><span data-stu-id="98c23-157">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="98c23-158">500.0 In-Process Handler Load Failure</span><span class="sxs-lookup"><span data-stu-id="98c23-158">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="98c23-159">The worker process fails.</span><span class="sxs-lookup"><span data-stu-id="98c23-159">The worker process fails.</span></span> <span data-ttu-id="98c23-160">The app doesn't start.</span><span class="sxs-lookup"><span data-stu-id="98c23-160">The app doesn't start.</span></span>

<span data-ttu-id="98c23-161">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span><span class="sxs-lookup"><span data-stu-id="98c23-161">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="98c23-162">Take one of the following actions:</span><span class="sxs-lookup"><span data-stu-id="98c23-162">Take one of the following actions:</span></span>

* <span data-ttu-id="98c23-163">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span><span class="sxs-lookup"><span data-stu-id="98c23-163">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="98c23-164">Ask a question on Stack Overflow.</span><span class="sxs-lookup"><span data-stu-id="98c23-164">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="98c23-165">File an issue on our [GitHub repository](https://github.com/aspnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="98c23-165">File an issue on our [GitHub repository](https://github.com/aspnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="98c23-166">500.30 In-Process Startup Failure</span><span class="sxs-lookup"><span data-stu-id="98c23-166">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="98c23-167">The worker process fails.</span><span class="sxs-lookup"><span data-stu-id="98c23-167">The worker process fails.</span></span> <span data-ttu-id="98c23-168">The app doesn't start.</span><span class="sxs-lookup"><span data-stu-id="98c23-168">The app doesn't start.</span></span>

<span data-ttu-id="98c23-169">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span><span class="sxs-lookup"><span data-stu-id="98c23-169">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="98c23-170">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span><span class="sxs-lookup"><span data-stu-id="98c23-170">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="98c23-171">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span><span class="sxs-lookup"><span data-stu-id="98c23-171">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="98c23-172">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span><span class="sxs-lookup"><span data-stu-id="98c23-172">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="98c23-173">500.31 ANCM Failed to Find Native Dependencies</span><span class="sxs-lookup"><span data-stu-id="98c23-173">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="98c23-174">The worker process fails.</span><span class="sxs-lookup"><span data-stu-id="98c23-174">The worker process fails.</span></span> <span data-ttu-id="98c23-175">The app doesn't start.</span><span class="sxs-lookup"><span data-stu-id="98c23-175">The app doesn't start.</span></span>

<span data-ttu-id="98c23-176">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span><span class="sxs-lookup"><span data-stu-id="98c23-176">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="98c23-177">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span><span class="sxs-lookup"><span data-stu-id="98c23-177">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="98c23-178">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span><span class="sxs-lookup"><span data-stu-id="98c23-178">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="98c23-179">An example error message follows:</span><span class="sxs-lookup"><span data-stu-id="98c23-179">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="98c23-180">The error message lists all the installed .NET Core versions and the version requested by the app.</span><span class="sxs-lookup"><span data-stu-id="98c23-180">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="98c23-181">To fix this error, either:</span><span class="sxs-lookup"><span data-stu-id="98c23-181">To fix this error, either:</span></span>

* <span data-ttu-id="98c23-182">Install the appropriate version of .NET Core on the machine.</span><span class="sxs-lookup"><span data-stu-id="98c23-182">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="98c23-183">Change the app to target a version of .NET Core that's present on the machine.</span><span class="sxs-lookup"><span data-stu-id="98c23-183">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="98c23-184">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="98c23-184">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="98c23-185">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span><span class="sxs-lookup"><span data-stu-id="98c23-185">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="98c23-186">The cause of a process startup failure is also found in the Application Event Log.</span><span class="sxs-lookup"><span data-stu-id="98c23-186">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="98c23-187">500.32 ANCM Failed to Load dll</span><span class="sxs-lookup"><span data-stu-id="98c23-187">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="98c23-188">The worker process fails.</span><span class="sxs-lookup"><span data-stu-id="98c23-188">The worker process fails.</span></span> <span data-ttu-id="98c23-189">The app doesn't start.</span><span class="sxs-lookup"><span data-stu-id="98c23-189">The app doesn't start.</span></span>

<span data-ttu-id="98c23-190">The most common cause for this error is that the app is published for an incompatible processor architecture.</span><span class="sxs-lookup"><span data-stu-id="98c23-190">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="98c23-191">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span><span class="sxs-lookup"><span data-stu-id="98c23-191">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="98c23-192">To fix this error, either:</span><span class="sxs-lookup"><span data-stu-id="98c23-192">To fix this error, either:</span></span>

* <span data-ttu-id="98c23-193">Republish the app for the same processor architecture as the worker process.</span><span class="sxs-lookup"><span data-stu-id="98c23-193">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="98c23-194">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span><span class="sxs-lookup"><span data-stu-id="98c23-194">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="98c23-195">500.33 ANCM Request Handler Load Failure</span><span class="sxs-lookup"><span data-stu-id="98c23-195">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="98c23-196">The worker process fails.</span><span class="sxs-lookup"><span data-stu-id="98c23-196">The worker process fails.</span></span> <span data-ttu-id="98c23-197">The app doesn't start.</span><span class="sxs-lookup"><span data-stu-id="98c23-197">The app doesn't start.</span></span>

<span data-ttu-id="98c23-198">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span><span class="sxs-lookup"><span data-stu-id="98c23-198">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="98c23-199">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="98c23-199">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="98c23-200">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span><span class="sxs-lookup"><span data-stu-id="98c23-200">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="98c23-201">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span><span class="sxs-lookup"><span data-stu-id="98c23-201">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="98c23-202">500.34 ANCM Mixed Hosting Models Not Supported</span><span class="sxs-lookup"><span data-stu-id="98c23-202">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="98c23-203">The worker process can't run both an in-process app and an out-of-process app in the same process.</span><span class="sxs-lookup"><span data-stu-id="98c23-203">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="98c23-204">To fix this error, run apps in separate IIS application pools.</span><span class="sxs-lookup"><span data-stu-id="98c23-204">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="98c23-205">500.35 ANCM Multiple In-Process Applications in same Process</span><span class="sxs-lookup"><span data-stu-id="98c23-205">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="98c23-206">The worker process can't run multiple in-process apps in the same process.</span><span class="sxs-lookup"><span data-stu-id="98c23-206">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="98c23-207">To fix this error, run apps in separate IIS application pools.</span><span class="sxs-lookup"><span data-stu-id="98c23-207">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="98c23-208">500.36 ANCM Out-Of-Process Handler Load Failure</span><span class="sxs-lookup"><span data-stu-id="98c23-208">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="98c23-209">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span><span class="sxs-lookup"><span data-stu-id="98c23-209">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="98c23-210">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="98c23-210">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="98c23-211">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span><span class="sxs-lookup"><span data-stu-id="98c23-211">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="98c23-212">500.37 ANCM Failed to Start Within Startup Time Limit</span><span class="sxs-lookup"><span data-stu-id="98c23-212">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="98c23-213">ANCM failed to start within the provied startup time limit.</span><span class="sxs-lookup"><span data-stu-id="98c23-213">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="98c23-214">By default, the timeout is 120 seconds.</span><span class="sxs-lookup"><span data-stu-id="98c23-214">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="98c23-215">This error can occur when starting a large number of apps on the same machine.</span><span class="sxs-lookup"><span data-stu-id="98c23-215">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="98c23-216">Check for CPU/Memory usage spikes on the server during startup.</span><span class="sxs-lookup"><span data-stu-id="98c23-216">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="98c23-217">You may need to stagger the startup process of multiple apps.</span><span class="sxs-lookup"><span data-stu-id="98c23-217">You may need to stagger the startup process of multiple apps.</span></span>

::: moniker-end

### <a name="5025-process-failure"></a><span data-ttu-id="98c23-218">502.5 Process Failure</span><span class="sxs-lookup"><span data-stu-id="98c23-218">502.5 Process Failure</span></span>

<span data-ttu-id="98c23-219">The worker process fails.</span><span class="sxs-lookup"><span data-stu-id="98c23-219">The worker process fails.</span></span> <span data-ttu-id="98c23-220">The app doesn't start.</span><span class="sxs-lookup"><span data-stu-id="98c23-220">The app doesn't start.</span></span>

<span data-ttu-id="98c23-221">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span><span class="sxs-lookup"><span data-stu-id="98c23-221">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="98c23-222">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span><span class="sxs-lookup"><span data-stu-id="98c23-222">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="98c23-223">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span><span class="sxs-lookup"><span data-stu-id="98c23-223">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="98c23-224">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span><span class="sxs-lookup"><span data-stu-id="98c23-224">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="98c23-225">The *shared framework* is the set of assemblies ( *.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="98c23-225">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="98c23-226">The metapackage reference can specify a minimum required version.</span><span class="sxs-lookup"><span data-stu-id="98c23-226">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="98c23-227">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="98c23-227">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="98c23-228">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span><span class="sxs-lookup"><span data-stu-id="98c23-228">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="98c23-229">Failed to start application (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="98c23-229">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="98c23-230">The app failed to start because the app's assembly ( *.dll*) couldn't be loaded.</span><span class="sxs-lookup"><span data-stu-id="98c23-230">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="98c23-231">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span><span class="sxs-lookup"><span data-stu-id="98c23-231">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="98c23-232">Confirm that the app pool's 32-bit setting is correct:</span><span class="sxs-lookup"><span data-stu-id="98c23-232">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="98c23-233">Select the app pool in IIS Manager's **Application Pools**.</span><span class="sxs-lookup"><span data-stu-id="98c23-233">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="98c23-234">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span><span class="sxs-lookup"><span data-stu-id="98c23-234">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="98c23-235">Set **Enable 32-Bit Applications**:</span><span class="sxs-lookup"><span data-stu-id="98c23-235">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="98c23-236">If deploying a 32-bit (x86) app, set the value to `True`.</span><span class="sxs-lookup"><span data-stu-id="98c23-236">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="98c23-237">If deploying a 64-bit (x64) app, set the value to `False`.</span><span class="sxs-lookup"><span data-stu-id="98c23-237">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="98c23-238">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span><span class="sxs-lookup"><span data-stu-id="98c23-238">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="98c23-239">Connection reset</span><span class="sxs-lookup"><span data-stu-id="98c23-239">Connection reset</span></span>

<span data-ttu-id="98c23-240">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span><span class="sxs-lookup"><span data-stu-id="98c23-240">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="98c23-241">This often happens when an error occurs during the serialization of complex objects for a response.</span><span class="sxs-lookup"><span data-stu-id="98c23-241">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="98c23-242">This type of error appears as a *connection reset* error on the client.</span><span class="sxs-lookup"><span data-stu-id="98c23-242">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="98c23-243">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span><span class="sxs-lookup"><span data-stu-id="98c23-243">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="98c23-244">Default startup limits</span><span class="sxs-lookup"><span data-stu-id="98c23-244">Default startup limits</span></span>

<span data-ttu-id="98c23-245">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span><span class="sxs-lookup"><span data-stu-id="98c23-245">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="98c23-246">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span><span class="sxs-lookup"><span data-stu-id="98c23-246">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="98c23-247">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="98c23-247">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="98c23-248">Troubleshoot on Azure App Service</span><span class="sxs-lookup"><span data-stu-id="98c23-248">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="98c23-249">Application Event Log (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="98c23-249">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="98c23-250">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span><span class="sxs-lookup"><span data-stu-id="98c23-250">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="98c23-251">In the Azure portal, open the app in **App Services**.</span><span class="sxs-lookup"><span data-stu-id="98c23-251">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="98c23-252">Select **Diagnose and solve problems**.</span><span class="sxs-lookup"><span data-stu-id="98c23-252">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="98c23-253">Select the **Diagnostic Tools** heading.</span><span class="sxs-lookup"><span data-stu-id="98c23-253">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="98c23-254">Under **Support Tools**, select the **Application Events** button.</span><span class="sxs-lookup"><span data-stu-id="98c23-254">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="98c23-255">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span><span class="sxs-lookup"><span data-stu-id="98c23-255">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="98c23-256">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span><span class="sxs-lookup"><span data-stu-id="98c23-256">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="98c23-257">Open **Advanced Tools** in the **Development Tools** area.</span><span class="sxs-lookup"><span data-stu-id="98c23-257">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="98c23-258">Select the **Go&rarr;** button.</span><span class="sxs-lookup"><span data-stu-id="98c23-258">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="98c23-259">The Kudu console opens in a new browser tab or window.</span><span class="sxs-lookup"><span data-stu-id="98c23-259">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="98c23-260">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span><span class="sxs-lookup"><span data-stu-id="98c23-260">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="98c23-261">Open the **LogFiles** folder.</span><span class="sxs-lookup"><span data-stu-id="98c23-261">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="98c23-262">Select the pencil icon next to the *eventlog.xml* file.</span><span class="sxs-lookup"><span data-stu-id="98c23-262">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="98c23-263">Examine the log.</span><span class="sxs-lookup"><span data-stu-id="98c23-263">Examine the log.</span></span> <span data-ttu-id="98c23-264">Scroll to the bottom of the log to see the most recent events.</span><span class="sxs-lookup"><span data-stu-id="98c23-264">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="98c23-265">Run the app in the Kudu console</span><span class="sxs-lookup"><span data-stu-id="98c23-265">Run the app in the Kudu console</span></span>

<span data-ttu-id="98c23-266">Many startup errors don't produce useful information in the Application Event Log.</span><span class="sxs-lookup"><span data-stu-id="98c23-266">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="98c23-267">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span><span class="sxs-lookup"><span data-stu-id="98c23-267">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="98c23-268">Open **Advanced Tools** in the **Development Tools** area.</span><span class="sxs-lookup"><span data-stu-id="98c23-268">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="98c23-269">Select the **Go&rarr;** button.</span><span class="sxs-lookup"><span data-stu-id="98c23-269">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="98c23-270">The Kudu console opens in a new browser tab or window.</span><span class="sxs-lookup"><span data-stu-id="98c23-270">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="98c23-271">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span><span class="sxs-lookup"><span data-stu-id="98c23-271">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="98c23-272">Test a 32-bit (x86) app</span><span class="sxs-lookup"><span data-stu-id="98c23-272">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="98c23-273">**Current release**</span><span class="sxs-lookup"><span data-stu-id="98c23-273">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="98c23-274">Run the app:</span><span class="sxs-lookup"><span data-stu-id="98c23-274">Run the app:</span></span>
   * <span data-ttu-id="98c23-275">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="98c23-275">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="98c23-276">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="98c23-276">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="98c23-277">The console output from the app, showing any errors, is piped to the Kudu console.</span><span class="sxs-lookup"><span data-stu-id="98c23-277">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="98c23-278">**Framework-dependent deployment running on a preview release**</span><span class="sxs-lookup"><span data-stu-id="98c23-278">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="98c23-279">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span><span class="sxs-lookup"><span data-stu-id="98c23-279">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="98c23-280">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span><span class="sxs-lookup"><span data-stu-id="98c23-280">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="98c23-281">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="98c23-281">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="98c23-282">The console output from the app, showing any errors, is piped to the Kudu console.</span><span class="sxs-lookup"><span data-stu-id="98c23-282">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="98c23-283">Test a 64-bit (x64) app</span><span class="sxs-lookup"><span data-stu-id="98c23-283">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="98c23-284">**Current release**</span><span class="sxs-lookup"><span data-stu-id="98c23-284">**Current release**</span></span>

* <span data-ttu-id="98c23-285">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="98c23-285">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="98c23-286">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="98c23-286">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="98c23-287">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="98c23-287">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="98c23-288">Run the app: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="98c23-288">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="98c23-289">The console output from the app, showing any errors, is piped to the Kudu console.</span><span class="sxs-lookup"><span data-stu-id="98c23-289">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="98c23-290">**Framework-dependent deployment running on a preview release**</span><span class="sxs-lookup"><span data-stu-id="98c23-290">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="98c23-291">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span><span class="sxs-lookup"><span data-stu-id="98c23-291">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="98c23-292">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span><span class="sxs-lookup"><span data-stu-id="98c23-292">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="98c23-293">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="98c23-293">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="98c23-294">The console output from the app, showing any errors, is piped to the Kudu console.</span><span class="sxs-lookup"><span data-stu-id="98c23-294">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="98c23-295">ASP.NET Core Module stdout log (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="98c23-295">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="98c23-296">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span><span class="sxs-lookup"><span data-stu-id="98c23-296">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="98c23-297">To enable and view stdout logs:</span><span class="sxs-lookup"><span data-stu-id="98c23-297">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="98c23-298">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span><span class="sxs-lookup"><span data-stu-id="98c23-298">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="98c23-299">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span><span class="sxs-lookup"><span data-stu-id="98c23-299">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="98c23-300">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span><span class="sxs-lookup"><span data-stu-id="98c23-300">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="98c23-301">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="98c23-301">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="98c23-302">Scroll down to reveal the *web.config* file at the bottom of the list.</span><span class="sxs-lookup"><span data-stu-id="98c23-302">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="98c23-303">Click the pencil icon next to the *web.config* file.</span><span class="sxs-lookup"><span data-stu-id="98c23-303">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="98c23-304">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="98c23-304">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="98c23-305">Select **Save** to save the updated *web.config* file.</span><span class="sxs-lookup"><span data-stu-id="98c23-305">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="98c23-306">Make a request to the app.</span><span class="sxs-lookup"><span data-stu-id="98c23-306">Make a request to the app.</span></span>
1. <span data-ttu-id="98c23-307">Return to the Azure portal.</span><span class="sxs-lookup"><span data-stu-id="98c23-307">Return to the Azure portal.</span></span> <span data-ttu-id="98c23-308">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span><span class="sxs-lookup"><span data-stu-id="98c23-308">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="98c23-309">Select the **Go&rarr;** button.</span><span class="sxs-lookup"><span data-stu-id="98c23-309">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="98c23-310">The Kudu console opens in a new browser tab or window.</span><span class="sxs-lookup"><span data-stu-id="98c23-310">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="98c23-311">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span><span class="sxs-lookup"><span data-stu-id="98c23-311">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="98c23-312">Select the **LogFiles** folder.</span><span class="sxs-lookup"><span data-stu-id="98c23-312">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="98c23-313">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span><span class="sxs-lookup"><span data-stu-id="98c23-313">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="98c23-314">When the log file opens, the error is displayed.</span><span class="sxs-lookup"><span data-stu-id="98c23-314">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="98c23-315">Disable stdout logging when troubleshooting is complete:</span><span class="sxs-lookup"><span data-stu-id="98c23-315">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="98c23-316">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span><span class="sxs-lookup"><span data-stu-id="98c23-316">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="98c23-317">Open the **web.config** file again by selecting the pencil icon.</span><span class="sxs-lookup"><span data-stu-id="98c23-317">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="98c23-318">Set **stdoutLogEnabled** to `false`.</span><span class="sxs-lookup"><span data-stu-id="98c23-318">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="98c23-319">Select **Save** to save the file.</span><span class="sxs-lookup"><span data-stu-id="98c23-319">Select **Save** to save the file.</span></span>

<span data-ttu-id="98c23-320">Další informace najdete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="98c23-320">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="98c23-321">Failure to disable the stdout log can lead to app or server failure.</span><span class="sxs-lookup"><span data-stu-id="98c23-321">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="98c23-322">There's no limit on log file size or the number of log files created.</span><span class="sxs-lookup"><span data-stu-id="98c23-322">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="98c23-323">Only use stdout logging to troubleshoot app startup problems.</span><span class="sxs-lookup"><span data-stu-id="98c23-323">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="98c23-324">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span><span class="sxs-lookup"><span data-stu-id="98c23-324">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="98c23-325">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="98c23-325">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="98c23-326">ASP.NET Core Module debug log (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="98c23-326">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="98c23-327">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span><span class="sxs-lookup"><span data-stu-id="98c23-327">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="98c23-328">To enable and view stdout logs:</span><span class="sxs-lookup"><span data-stu-id="98c23-328">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="98c23-329">To enable the enhanced diagnostic log, perform either of the following:</span><span class="sxs-lookup"><span data-stu-id="98c23-329">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="98c23-330">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span><span class="sxs-lookup"><span data-stu-id="98c23-330">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="98c23-331">Redeploy the app.</span><span class="sxs-lookup"><span data-stu-id="98c23-331">Redeploy the app.</span></span>
   * <span data-ttu-id="98c23-332">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span><span class="sxs-lookup"><span data-stu-id="98c23-332">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="98c23-333">Open **Advanced Tools** in the **Development Tools** area.</span><span class="sxs-lookup"><span data-stu-id="98c23-333">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="98c23-334">Select the **Go&rarr;** button.</span><span class="sxs-lookup"><span data-stu-id="98c23-334">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="98c23-335">The Kudu console opens in a new browser tab or window.</span><span class="sxs-lookup"><span data-stu-id="98c23-335">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="98c23-336">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span><span class="sxs-lookup"><span data-stu-id="98c23-336">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="98c23-337">Open the folders to the path **site** > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="98c23-337">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="98c23-338">Edit the *web.config* file by selecting the pencil button.</span><span class="sxs-lookup"><span data-stu-id="98c23-338">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="98c23-339">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span><span class="sxs-lookup"><span data-stu-id="98c23-339">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="98c23-340">Select the **Save** button.</span><span class="sxs-lookup"><span data-stu-id="98c23-340">Select the **Save** button.</span></span>
1. <span data-ttu-id="98c23-341">Open **Advanced Tools** in the **Development Tools** area.</span><span class="sxs-lookup"><span data-stu-id="98c23-341">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="98c23-342">Select the **Go&rarr;** button.</span><span class="sxs-lookup"><span data-stu-id="98c23-342">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="98c23-343">The Kudu console opens in a new browser tab or window.</span><span class="sxs-lookup"><span data-stu-id="98c23-343">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="98c23-344">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span><span class="sxs-lookup"><span data-stu-id="98c23-344">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="98c23-345">Open the folders to the path **site** > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="98c23-345">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="98c23-346">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span><span class="sxs-lookup"><span data-stu-id="98c23-346">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="98c23-347">If you supplied a path, navigate to the location of the log file.</span><span class="sxs-lookup"><span data-stu-id="98c23-347">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="98c23-348">Open the log file with the pencil button next to the file name.</span><span class="sxs-lookup"><span data-stu-id="98c23-348">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="98c23-349">Disable debug logging when troubleshooting is complete:</span><span class="sxs-lookup"><span data-stu-id="98c23-349">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="98c23-350">To disable the enhanced debug log, perform either of the following:</span><span class="sxs-lookup"><span data-stu-id="98c23-350">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="98c23-351">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span><span class="sxs-lookup"><span data-stu-id="98c23-351">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="98c23-352">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span><span class="sxs-lookup"><span data-stu-id="98c23-352">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="98c23-353">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="98c23-353">Save the file.</span></span>

<span data-ttu-id="98c23-354">Další informace najdete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="98c23-354">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="98c23-355">Failure to disable the debug log can lead to app or server failure.</span><span class="sxs-lookup"><span data-stu-id="98c23-355">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="98c23-356">There's no limit on log file size.</span><span class="sxs-lookup"><span data-stu-id="98c23-356">There's no limit on log file size.</span></span> <span data-ttu-id="98c23-357">Only use debug logging to troubleshoot app startup problems.</span><span class="sxs-lookup"><span data-stu-id="98c23-357">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="98c23-358">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span><span class="sxs-lookup"><span data-stu-id="98c23-358">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="98c23-359">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="98c23-359">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker-end

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="98c23-360">Slow or hanging app (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="98c23-360">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="98c23-361">When an app responds slowly or hangs on a request, see the following articles:</span><span class="sxs-lookup"><span data-stu-id="98c23-361">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="98c23-362">Troubleshoot slow web app performance issues in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="98c23-362">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="98c23-363">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span><span class="sxs-lookup"><span data-stu-id="98c23-363">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="98c23-364">Monitoring blades</span><span class="sxs-lookup"><span data-stu-id="98c23-364">Monitoring blades</span></span>

<span data-ttu-id="98c23-365">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span><span class="sxs-lookup"><span data-stu-id="98c23-365">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="98c23-366">These blades can be used to diagnose 500-series errors.</span><span class="sxs-lookup"><span data-stu-id="98c23-366">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="98c23-367">Confirm that the ASP.NET Core Extensions are installed.</span><span class="sxs-lookup"><span data-stu-id="98c23-367">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="98c23-368">If the extensions aren't installed, install them manually:</span><span class="sxs-lookup"><span data-stu-id="98c23-368">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="98c23-369">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span><span class="sxs-lookup"><span data-stu-id="98c23-369">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="98c23-370">The **ASP.NET Core Extensions** should appear in the list.</span><span class="sxs-lookup"><span data-stu-id="98c23-370">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="98c23-371">If the extensions aren't installed, select the **Add** button.</span><span class="sxs-lookup"><span data-stu-id="98c23-371">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="98c23-372">Choose the **ASP.NET Core Extensions** from the list.</span><span class="sxs-lookup"><span data-stu-id="98c23-372">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="98c23-373">Select **OK** to accept the legal terms.</span><span class="sxs-lookup"><span data-stu-id="98c23-373">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="98c23-374">Select **OK** on the **Add extension** blade.</span><span class="sxs-lookup"><span data-stu-id="98c23-374">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="98c23-375">An informational pop-up message indicates when the extensions are successfully installed.</span><span class="sxs-lookup"><span data-stu-id="98c23-375">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="98c23-376">If stdout logging isn't enabled, follow these steps:</span><span class="sxs-lookup"><span data-stu-id="98c23-376">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="98c23-377">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span><span class="sxs-lookup"><span data-stu-id="98c23-377">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="98c23-378">Select the **Go&rarr;** button.</span><span class="sxs-lookup"><span data-stu-id="98c23-378">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="98c23-379">The Kudu console opens in a new browser tab or window.</span><span class="sxs-lookup"><span data-stu-id="98c23-379">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="98c23-380">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span><span class="sxs-lookup"><span data-stu-id="98c23-380">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="98c23-381">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span><span class="sxs-lookup"><span data-stu-id="98c23-381">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="98c23-382">Click the pencil icon next to the *web.config* file.</span><span class="sxs-lookup"><span data-stu-id="98c23-382">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="98c23-383">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="98c23-383">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="98c23-384">Select **Save** to save the updated *web.config* file.</span><span class="sxs-lookup"><span data-stu-id="98c23-384">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="98c23-385">Proceed to activate diagnostic logging:</span><span class="sxs-lookup"><span data-stu-id="98c23-385">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="98c23-386">In the Azure portal, select the **Diagnostics logs** blade.</span><span class="sxs-lookup"><span data-stu-id="98c23-386">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="98c23-387">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span><span class="sxs-lookup"><span data-stu-id="98c23-387">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="98c23-388">Select the **Save** button at the top of the blade.</span><span class="sxs-lookup"><span data-stu-id="98c23-388">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="98c23-389">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span><span class="sxs-lookup"><span data-stu-id="98c23-389">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="98c23-390">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span><span class="sxs-lookup"><span data-stu-id="98c23-390">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="98c23-391">Make a request to the app.</span><span class="sxs-lookup"><span data-stu-id="98c23-391">Make a request to the app.</span></span>
1. <span data-ttu-id="98c23-392">Within the log stream data, the cause of the error is indicated.</span><span class="sxs-lookup"><span data-stu-id="98c23-392">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="98c23-393">Be sure to disable stdout logging when troubleshooting is complete.</span><span class="sxs-lookup"><span data-stu-id="98c23-393">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="98c23-394">To view the failed request tracing logs (FREB logs):</span><span class="sxs-lookup"><span data-stu-id="98c23-394">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="98c23-395">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span><span class="sxs-lookup"><span data-stu-id="98c23-395">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="98c23-396">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span><span class="sxs-lookup"><span data-stu-id="98c23-396">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="98c23-397">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span><span class="sxs-lookup"><span data-stu-id="98c23-397">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="98c23-398">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="98c23-398">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="98c23-399">Failure to disable the stdout log can lead to app or server failure.</span><span class="sxs-lookup"><span data-stu-id="98c23-399">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="98c23-400">There's no limit on log file size or the number of log files created.</span><span class="sxs-lookup"><span data-stu-id="98c23-400">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="98c23-401">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span><span class="sxs-lookup"><span data-stu-id="98c23-401">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="98c23-402">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="98c23-402">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="98c23-403">Troubleshoot on IIS</span><span class="sxs-lookup"><span data-stu-id="98c23-403">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="98c23-404">Application Event Log (IIS)</span><span class="sxs-lookup"><span data-stu-id="98c23-404">Application Event Log (IIS)</span></span>

<span data-ttu-id="98c23-405">Access the Application Event Log:</span><span class="sxs-lookup"><span data-stu-id="98c23-405">Access the Application Event Log:</span></span>

1. <span data-ttu-id="98c23-406">Open the Start menu, search for **Event Viewer**, and then select the **Event Viewer** app.</span><span class="sxs-lookup"><span data-stu-id="98c23-406">Open the Start menu, search for **Event Viewer**, and then select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="98c23-407">In **Event Viewer**, open the **Windows Logs** node.</span><span class="sxs-lookup"><span data-stu-id="98c23-407">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="98c23-408">Select **Application** to open the Application Event Log.</span><span class="sxs-lookup"><span data-stu-id="98c23-408">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="98c23-409">Search for errors associated with the failing app.</span><span class="sxs-lookup"><span data-stu-id="98c23-409">Search for errors associated with the failing app.</span></span> <span data-ttu-id="98c23-410">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span><span class="sxs-lookup"><span data-stu-id="98c23-410">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="98c23-411">Run the app at a command prompt</span><span class="sxs-lookup"><span data-stu-id="98c23-411">Run the app at a command prompt</span></span>

<span data-ttu-id="98c23-412">Many startup errors don't produce useful information in the Application Event Log.</span><span class="sxs-lookup"><span data-stu-id="98c23-412">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="98c23-413">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span><span class="sxs-lookup"><span data-stu-id="98c23-413">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="98c23-414">Framework-dependent deployment</span><span class="sxs-lookup"><span data-stu-id="98c23-414">Framework-dependent deployment</span></span>

<span data-ttu-id="98c23-415">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="98c23-415">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="98c23-416">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span><span class="sxs-lookup"><span data-stu-id="98c23-416">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="98c23-417">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span><span class="sxs-lookup"><span data-stu-id="98c23-417">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="98c23-418">The console output from the app, showing any errors, is written to the console window.</span><span class="sxs-lookup"><span data-stu-id="98c23-418">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="98c23-419">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span><span class="sxs-lookup"><span data-stu-id="98c23-419">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="98c23-420">Using the default host and post, make a request to `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="98c23-420">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="98c23-421">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span><span class="sxs-lookup"><span data-stu-id="98c23-421">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="98c23-422">Self-contained deployment</span><span class="sxs-lookup"><span data-stu-id="98c23-422">Self-contained deployment</span></span>

<span data-ttu-id="98c23-423">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="98c23-423">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="98c23-424">At a command prompt, navigate to the deployment folder and run the app's executable.</span><span class="sxs-lookup"><span data-stu-id="98c23-424">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="98c23-425">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span><span class="sxs-lookup"><span data-stu-id="98c23-425">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="98c23-426">The console output from the app, showing any errors, is written to the console window.</span><span class="sxs-lookup"><span data-stu-id="98c23-426">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="98c23-427">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span><span class="sxs-lookup"><span data-stu-id="98c23-427">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="98c23-428">Using the default host and post, make a request to `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="98c23-428">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="98c23-429">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span><span class="sxs-lookup"><span data-stu-id="98c23-429">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="98c23-430">ASP.NET Core Module stdout log (IIS)</span><span class="sxs-lookup"><span data-stu-id="98c23-430">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="98c23-431">To enable and view stdout logs:</span><span class="sxs-lookup"><span data-stu-id="98c23-431">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="98c23-432">Navigate to the site's deployment folder on the hosting system.</span><span class="sxs-lookup"><span data-stu-id="98c23-432">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="98c23-433">If the *logs* folder isn't present, create the folder.</span><span class="sxs-lookup"><span data-stu-id="98c23-433">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="98c23-434">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span><span class="sxs-lookup"><span data-stu-id="98c23-434">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="98c23-435">Edit the *web.config* file.</span><span class="sxs-lookup"><span data-stu-id="98c23-435">Edit the *web.config* file.</span></span> <span data-ttu-id="98c23-436">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="98c23-436">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="98c23-437">`stdout` in the path is the log file name prefix.</span><span class="sxs-lookup"><span data-stu-id="98c23-437">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="98c23-438">A timestamp, process id, and file extension are added automatically when the log is created.</span><span class="sxs-lookup"><span data-stu-id="98c23-438">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="98c23-439">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span><span class="sxs-lookup"><span data-stu-id="98c23-439">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="98c23-440">Ensure your application pool's identity has write permissions to the *logs* folder.</span><span class="sxs-lookup"><span data-stu-id="98c23-440">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="98c23-441">Save the updated *web.config* file.</span><span class="sxs-lookup"><span data-stu-id="98c23-441">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="98c23-442">Make a request to the app.</span><span class="sxs-lookup"><span data-stu-id="98c23-442">Make a request to the app.</span></span>
1. <span data-ttu-id="98c23-443">Navigate to the *logs* folder.</span><span class="sxs-lookup"><span data-stu-id="98c23-443">Navigate to the *logs* folder.</span></span> <span data-ttu-id="98c23-444">Find and open the most recent stdout log.</span><span class="sxs-lookup"><span data-stu-id="98c23-444">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="98c23-445">Study the log for errors.</span><span class="sxs-lookup"><span data-stu-id="98c23-445">Study the log for errors.</span></span>

<span data-ttu-id="98c23-446">Disable stdout logging when troubleshooting is complete:</span><span class="sxs-lookup"><span data-stu-id="98c23-446">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="98c23-447">Edit the *web.config* file.</span><span class="sxs-lookup"><span data-stu-id="98c23-447">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="98c23-448">Set **stdoutLogEnabled** to `false`.</span><span class="sxs-lookup"><span data-stu-id="98c23-448">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="98c23-449">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="98c23-449">Save the file.</span></span>

<span data-ttu-id="98c23-450">Další informace najdete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="98c23-450">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="98c23-451">Failure to disable the stdout log can lead to app or server failure.</span><span class="sxs-lookup"><span data-stu-id="98c23-451">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="98c23-452">There's no limit on log file size or the number of log files created.</span><span class="sxs-lookup"><span data-stu-id="98c23-452">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="98c23-453">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span><span class="sxs-lookup"><span data-stu-id="98c23-453">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="98c23-454">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="98c23-454">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="98c23-455">ASP.NET Core Module debug log (IIS)</span><span class="sxs-lookup"><span data-stu-id="98c23-455">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="98c23-456">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span><span class="sxs-lookup"><span data-stu-id="98c23-456">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="98c23-457">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span><span class="sxs-lookup"><span data-stu-id="98c23-457">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="98c23-458">Další informace najdete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="98c23-458">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

::: moniker-end

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="98c23-459">Enable the Developer Exception Page</span><span class="sxs-lookup"><span data-stu-id="98c23-459">Enable the Developer Exception Page</span></span>

<span data-ttu-id="98c23-460">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span><span class="sxs-lookup"><span data-stu-id="98c23-460">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="98c23-461">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span><span class="sxs-lookup"><span data-stu-id="98c23-461">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

<span data-ttu-id="98c23-462">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span><span class="sxs-lookup"><span data-stu-id="98c23-462">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="98c23-463">Remove the environment variable from the *web.config* file after troubleshooting.</span><span class="sxs-lookup"><span data-stu-id="98c23-463">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="98c23-464">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="98c23-464">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="98c23-465">Obtain data from an app</span><span class="sxs-lookup"><span data-stu-id="98c23-465">Obtain data from an app</span></span>

<span data-ttu-id="98c23-466">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span><span class="sxs-lookup"><span data-stu-id="98c23-466">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="98c23-467">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span><span class="sxs-lookup"><span data-stu-id="98c23-467">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="98c23-468">Slow or hanging app (IIS)</span><span class="sxs-lookup"><span data-stu-id="98c23-468">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="98c23-469">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span><span class="sxs-lookup"><span data-stu-id="98c23-469">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="98c23-470">App crashes or encounters an exception</span><span class="sxs-lookup"><span data-stu-id="98c23-470">App crashes or encounters an exception</span></span>

<span data-ttu-id="98c23-471">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="98c23-471">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="98c23-472">Create a folder to hold crash dump files at `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="98c23-472">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="98c23-473">The app pool must have write access to the folder.</span><span class="sxs-lookup"><span data-stu-id="98c23-473">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="98c23-474">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="98c23-474">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="98c23-475">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="98c23-475">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="98c23-476">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="98c23-476">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="98c23-477">Run the app under the conditions that cause the crash to occur.</span><span class="sxs-lookup"><span data-stu-id="98c23-477">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="98c23-478">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="98c23-478">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="98c23-479">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="98c23-479">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="98c23-480">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="98c23-480">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="98c23-481">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span><span class="sxs-lookup"><span data-stu-id="98c23-481">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="98c23-482">The PowerShell script configures WER to collect up to five dumps per app.</span><span class="sxs-lookup"><span data-stu-id="98c23-482">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="98c23-483">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span><span class="sxs-lookup"><span data-stu-id="98c23-483">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="98c23-484">App hangs, fails during startup, or runs normally</span><span class="sxs-lookup"><span data-stu-id="98c23-484">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="98c23-485">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span><span class="sxs-lookup"><span data-stu-id="98c23-485">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="98c23-486">Analyze the dump</span><span class="sxs-lookup"><span data-stu-id="98c23-486">Analyze the dump</span></span>

<span data-ttu-id="98c23-487">A dump can be analyzed using several approaches.</span><span class="sxs-lookup"><span data-stu-id="98c23-487">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="98c23-488">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="98c23-488">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="98c23-489">Clear package caches</span><span class="sxs-lookup"><span data-stu-id="98c23-489">Clear package caches</span></span>

<span data-ttu-id="98c23-490">Sometimes a functioning app fails immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span><span class="sxs-lookup"><span data-stu-id="98c23-490">Sometimes a functioning app fails immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="98c23-491">In some cases, incoherent packages may break an app when performing major upgrades.</span><span class="sxs-lookup"><span data-stu-id="98c23-491">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="98c23-492">Most of these issues can be fixed by following these instructions:</span><span class="sxs-lookup"><span data-stu-id="98c23-492">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="98c23-493">Delete the *bin* and *obj* folders.</span><span class="sxs-lookup"><span data-stu-id="98c23-493">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="98c23-494">Clear the package caches by executing `dotnet nuget locals all --clear` from a command shell.</span><span class="sxs-lookup"><span data-stu-id="98c23-494">Clear the package caches by executing `dotnet nuget locals all --clear` from a command shell.</span></span>

   <span data-ttu-id="98c23-495">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="98c23-495">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="98c23-496">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="98c23-496">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="98c23-497">Restore and rebuild the project.</span><span class="sxs-lookup"><span data-stu-id="98c23-497">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="98c23-498">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span><span class="sxs-lookup"><span data-stu-id="98c23-498">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="98c23-499">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="98c23-499">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="98c23-500">Azure documentation</span><span class="sxs-lookup"><span data-stu-id="98c23-500">Azure documentation</span></span>

* [<span data-ttu-id="98c23-501">Application Insights for ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="98c23-501">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="98c23-502">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98c23-502">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="98c23-503">Azure App Service diagnostics overview</span><span class="sxs-lookup"><span data-stu-id="98c23-503">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="98c23-504">How to: Monitor Apps in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="98c23-504">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="98c23-505">Troubleshoot a web app in Azure App Service using Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98c23-505">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="98c23-506">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span><span class="sxs-lookup"><span data-stu-id="98c23-506">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="98c23-507">Troubleshoot slow web app performance issues in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="98c23-507">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="98c23-508">Application performance FAQs for Web Apps in Azure</span><span class="sxs-lookup"><span data-stu-id="98c23-508">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="98c23-509">Azure Web App sandbox (App Service runtime execution limitations)</span><span class="sxs-lookup"><span data-stu-id="98c23-509">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="98c23-510">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span><span class="sxs-lookup"><span data-stu-id="98c23-510">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="98c23-511">Dokumentace sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98c23-511">Visual Studio documentation</span></span>

* [<span data-ttu-id="98c23-512">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="98c23-512">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="98c23-513">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="98c23-513">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="98c23-514">Další informace k ladění pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98c23-514">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="98c23-515">Visual Studio Code documentation</span><span class="sxs-lookup"><span data-stu-id="98c23-515">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="98c23-516">Debugging with Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="98c23-516">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)
