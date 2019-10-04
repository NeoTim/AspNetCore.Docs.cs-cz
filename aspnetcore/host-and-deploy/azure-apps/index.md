---
title: Nasazení aplikací ASP.NET Core pro Azure App Service
author: guardrex
description: Tento článek obsahuje odkazy na hostitele Azure a nasazení prostředků.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2019
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: bda4923adb0f9769f883ef64f7902c8650308222
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71924891"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a><span data-ttu-id="7fe10-103">Nasazení aplikací ASP.NET Core pro Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7fe10-103">Deploy ASP.NET Core apps to Azure App Service</span></span>

<span data-ttu-id="7fe10-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) je [platforma cloud computingu od Microsoftu](https://azure.microsoft.com/) pro hostování webových aplikací, včetně ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7fe10-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span>

## <a name="useful-resources"></a><span data-ttu-id="7fe10-105">Užitečné zdroje</span><span class="sxs-lookup"><span data-stu-id="7fe10-105">Useful resources</span></span>

<span data-ttu-id="7fe10-106">[Dokumentace k App Service](/azure/app-service/) je Domovská stránka pro Azure Apps, kurzy, ukázky, návody a další materiály.</span><span class="sxs-lookup"><span data-stu-id="7fe10-106">[App Service Documentation](/azure/app-service/) is the home for Azure Apps documentation, tutorials, samples, how-to guides, and other resources.</span></span> <span data-ttu-id="7fe10-107">Existují dva významné kurzy, které se týkají hostování ASP.NET Corech aplikací:</span><span class="sxs-lookup"><span data-stu-id="7fe10-107">Two notable tutorials that pertain to hosting ASP.NET Core apps are:</span></span>

[<span data-ttu-id="7fe10-108">Vytvoření webové aplikace v ASP.NET Core v Azure</span><span class="sxs-lookup"><span data-stu-id="7fe10-108">Create an ASP.NET Core web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)  
<span data-ttu-id="7fe10-109">Pomocí sady Visual Studio můžete vytvořit a nasadit webovou aplikaci ASP.NET Core pro Azure App Service ve Windows.</span><span class="sxs-lookup"><span data-stu-id="7fe10-109">Use Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service on Windows.</span></span>

[<span data-ttu-id="7fe10-110">Vytvoření aplikace ASP.NET Core v App Service v systému Linux</span><span class="sxs-lookup"><span data-stu-id="7fe10-110">Create an ASP.NET Core app in App Service on Linux</span></span>](/azure/app-service/containers/quickstart-dotnetcore)  
<span data-ttu-id="7fe10-111">Pomocí příkazového řádku můžete vytvořit a nasadit webovou aplikaci ASP.NET Core pro Azure App Service v systému Linux.</span><span class="sxs-lookup"><span data-stu-id="7fe10-111">Use the command line to create and deploy an ASP.NET Core web app to Azure App Service on Linux.</span></span>

<span data-ttu-id="7fe10-112">Verzi ASP.NET Core dostupnou ve službě Azure App Service najdete na [App Service řídicím panelu ASP.NET Core](https://aspnetcoreon.azurewebsites.net/) .</span><span class="sxs-lookup"><span data-stu-id="7fe10-112">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

<span data-ttu-id="7fe10-113">V dokumentaci k ASP.NET Core jsou k dispozici následující články:</span><span class="sxs-lookup"><span data-stu-id="7fe10-113">The following articles are available in ASP.NET Core documentation:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="7fe10-114">Zjistěte, jak publikovat aplikace ASP.NET Core do služby Azure App Service pomocí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7fe10-114">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
<span data-ttu-id="7fe10-115">Naučte se, jak vytvořit webovou aplikaci ASP.NET Core pomocí sady Visual Studio a nasadit ji do Azure App Service pomocí Gitu pro průběžné nasazování.</span><span class="sxs-lookup"><span data-stu-id="7fe10-115">Learn how to create an ASP.NET Core web app using Visual Studio and deploy it to Azure App Service using Git for continuous deployment.</span></span>

[<span data-ttu-id="7fe10-116">Vytvoření prvního kanálu</span><span class="sxs-lookup"><span data-stu-id="7fe10-116">Create your first pipeline</span></span>](/azure/devops/pipelines/get-started-yaml)  
<span data-ttu-id="7fe10-117">Nastavte sestavení CI pro aplikaci ASP.NET Core a pak vytvořte Azure App Service vydanou verzi pro průběžné nasazování.</span><span class="sxs-lookup"><span data-stu-id="7fe10-117">Set up a CI build for an ASP.NET Core app, then create a continuous deployment release to Azure App Service.</span></span>

[<span data-ttu-id="7fe10-118">Sandbox webové aplikace Azure</span><span class="sxs-lookup"><span data-stu-id="7fe10-118">Azure Web App sandbox</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
<span data-ttu-id="7fe10-119">Zjištění omezení spouštění Azure App Service runtime, která vynutila platforma Azure Apps</span><span class="sxs-lookup"><span data-stu-id="7fe10-119">Discover Azure App Service runtime execution limitations enforced by the Azure Apps platform.</span></span>

<xref:test/troubleshoot>  
<span data-ttu-id="7fe10-120">Pochopení a řešení potíží s chybami a problémy s ASP.NET Core projekty.</span><span class="sxs-lookup"><span data-stu-id="7fe10-120">Understand and troubleshoot warnings and errors with ASP.NET Core projects.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="7fe10-121">Konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="7fe10-121">Application configuration</span></span>

### <a name="platform"></a><span data-ttu-id="7fe10-122">Platforma</span><span class="sxs-lookup"><span data-stu-id="7fe10-122">Platform</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="7fe10-123">V Azure App Service jsou k dispozici moduly runtime pro 64 (x64) a 32-bit (x86) aplikací.</span><span class="sxs-lookup"><span data-stu-id="7fe10-123">Runtimes for 64-bit (x64) and 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="7fe10-124">[.NET Core SDK](/dotnet/core/sdk) k dispozici v App Service je 32-bit, ale můžete nasadit 64 aplikace sestavené místně pomocí konzoly [Kudu](https://github.com/projectkudu/kudu/wiki) nebo procesu publikování v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7fe10-124">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit, but you can deploy 64-bit apps built locally using the [Kudu](https://github.com/projectkudu/kudu/wiki) console or the publish process in Visual Studio.</span></span> <span data-ttu-id="7fe10-125">Další informace najdete v části [publikování a nasazení aplikace](#publish-and-deploy-the-app) .</span><span class="sxs-lookup"><span data-stu-id="7fe10-125">For more information, see the [Publish and deploy the app](#publish-and-deploy-the-app) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="7fe10-126">Pro aplikace s nativními závislostmi jsou moduly runtime pro 32 aplikace (x86) k dispozici na Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="7fe10-126">For apps with native dependencies, runtimes for 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="7fe10-127">[.NET Core SDK](/dotnet/core/sdk) k dispozici v App Service je 32-bit.</span><span class="sxs-lookup"><span data-stu-id="7fe10-127">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit.</span></span>

::: moniker-end

<span data-ttu-id="7fe10-128">Další informace o komponentách rozhraní .NET Core Framework a metodách distribuce, jako jsou informace o modulu runtime .NET Core a .NET Core SDK, [najdete v tématu o .NET Core: Složení](/dotnet/core/about#composition).</span><span class="sxs-lookup"><span data-stu-id="7fe10-128">For more information on .NET Core framework components and distribution methods, such as information on the .NET Core runtime and the .NET Core SDK, see [About .NET Core: Composition](/dotnet/core/about#composition).</span></span>

### <a name="packages"></a><span data-ttu-id="7fe10-129">Balíčky</span><span class="sxs-lookup"><span data-stu-id="7fe10-129">Packages</span></span>

<span data-ttu-id="7fe10-130">Zahrňte následující balíčky NuGet pro poskytování automatických funkcí protokolování pro aplikace nasazené do Azure App Service:</span><span class="sxs-lookup"><span data-stu-id="7fe10-130">Include the following NuGet packages to provide automatic logging features for apps deployed to Azure App Service:</span></span>

* <span data-ttu-id="7fe10-131">[Microsoft. AspNetCore. AzureAppServices. HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) využívá [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) k poskytování ASP.NET Coreho urychlené integrace s Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="7fe10-131">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) uses [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) to provide ASP.NET Core light-up integration with Azure App Service.</span></span> <span data-ttu-id="7fe10-132">Přidané funkce protokolování poskytuje `Microsoft.AspNetCore.AzureAppServicesIntegration` balíček.</span><span class="sxs-lookup"><span data-stu-id="7fe10-132">The added logging features are provided by the `Microsoft.AspNetCore.AzureAppServicesIntegration` package.</span></span>
* <span data-ttu-id="7fe10-133">[Microsoft. AspNetCore. AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) spustí [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) pro Azure App Service přidání `Microsoft.Extensions.Logging.AzureAppServices` zprostředkovatelů protokolování diagnostiky do balíčku.</span><span class="sxs-lookup"><span data-stu-id="7fe10-133">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executes [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) to add Azure App Service diagnostics logging providers in the `Microsoft.Extensions.Logging.AzureAppServices` package.</span></span>
* <span data-ttu-id="7fe10-134">[Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) poskytuje implementace protokolovacích nástrojů pro podporu Azure App Servicech diagnostických protokolů a funkcí streamování protokolů.</span><span class="sxs-lookup"><span data-stu-id="7fe10-134">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) provides logger implementations to support Azure App Service diagnostics logs and log streaming features.</span></span>

<span data-ttu-id="7fe10-135">Předchozí balíčky nejsou k dispozici ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="7fe10-135">The preceding packages aren't available from the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="7fe10-136">Aplikace, které .NET Framework cílí na Metapackage `Microsoft.AspNetCore.App` nebo odkazují na, musí explicitně odkazovat na jednotlivé balíčky v souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="7fe10-136">Apps that target .NET Framework or reference the `Microsoft.AspNetCore.App` metapackage must explicitly reference the individual packages in the app's project file.</span></span>

## <a name="override-app-configuration-using-the-azure-portal"></a><span data-ttu-id="7fe10-137">Přepsání konfigurace aplikace pomocí webu Azure Portal</span><span class="sxs-lookup"><span data-stu-id="7fe10-137">Override app configuration using the Azure Portal</span></span>

<span data-ttu-id="7fe10-138">Nastavení aplikace na webu Azure Portal umožňuje nastavit proměnné prostředí pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7fe10-138">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="7fe10-139">Proměnné prostředí mohou být spotřebovány [poskytovatelem konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="7fe10-139">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="7fe10-140">Když se na webu Azure Portal vytvoří nebo upraví nastavení aplikace a vybere se tlačítko **Save (Uložit** ), aplikace Azure se restartuje.</span><span class="sxs-lookup"><span data-stu-id="7fe10-140">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="7fe10-141">Proměnná prostředí je k dispozici aplikaci po restartování služby.</span><span class="sxs-lookup"><span data-stu-id="7fe10-141">The environment variable is available to the app after the service restarts.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7fe10-142">Když aplikace používá [obecného hostitele](xref:fundamentals/host/generic-host), proměnné prostředí se ve výchozím nastavení načtou do konfigurace aplikace a Poskytovatel konfigurace musí být přidaný vývojářem.</span><span class="sxs-lookup"><span data-stu-id="7fe10-142">When an app uses the [Generic Host](xref:fundamentals/host/generic-host), environment variables aren't loaded into an app's configuration by default and the configuration provider must be added by the developer.</span></span> <span data-ttu-id="7fe10-143">Vývojář určí předponu proměnné prostředí při přidání poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="7fe10-143">The developer determines the environment variable prefix when the configuration provider is added.</span></span> <span data-ttu-id="7fe10-144">Další informace naleznete v tématu <xref:fundamentals/host/generic-host> a [zprostředkovatel konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="7fe10-144">For more information, see <xref:fundamentals/host/generic-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7fe10-145">Když aplikace vytvoří hostitele pomocí webhost [. CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), proměnné prostředí, které konfigurují hostitele, používají `ASPNETCORE_` předponu.</span><span class="sxs-lookup"><span data-stu-id="7fe10-145">When an app builds the host using [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), environment variables that configure the host use the `ASPNETCORE_` prefix.</span></span> <span data-ttu-id="7fe10-146">Další informace naleznete v tématu <xref:fundamentals/host/web-host> a [zprostředkovatel konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="7fe10-146">For more information, see <xref:fundamentals/host/web-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="7fe10-147">Proxy server a scénáře pro nástroj pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="7fe10-147">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="7fe10-148">[IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), který konfiguruje předávaný middleware hlaviček při hostování [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model)a modul ASP.NET Core je nakonfigurovaný pro přeposílání schématu (http/https) a vzdálené IP adresy, na které pochází požadavek .</span><span class="sxs-lookup"><span data-stu-id="7fe10-148">The [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), which configures Forwarded Headers Middleware when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="7fe10-149">Další konfigurace může být nezbytný pro aplikací hostovaných za službou další proxy servery a nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="7fe10-149">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="7fe10-150">Další informace najdete v tématu [konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="7fe10-150">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="monitoring-and-logging"></a><span data-ttu-id="7fe10-151">Monitorování a protokolování</span><span class="sxs-lookup"><span data-stu-id="7fe10-151">Monitoring and logging</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7fe10-152">Aplikace ASP.NET Core nasazené pro App Service automaticky dostanou rozšíření App Service **ASP.NET Core Integration Logging**.</span><span class="sxs-lookup"><span data-stu-id="7fe10-152">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Integration**.</span></span> <span data-ttu-id="7fe10-153">Rozšíření umožňuje integraci protokolování pro aplikace ASP.NET Core v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="7fe10-153">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7fe10-154">Aplikace ASP.NET Core nasazené pro App Service automaticky dostanou rozšíření App Service **ASP.NET Core rozšíření protokolování**.</span><span class="sxs-lookup"><span data-stu-id="7fe10-154">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Extensions**.</span></span> <span data-ttu-id="7fe10-155">Rozšíření umožňuje integraci protokolování pro aplikace ASP.NET Core v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="7fe10-155">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

<span data-ttu-id="7fe10-156">Informace o monitorování, protokolování a odstraňování potíží najdete v následujících článcích:</span><span class="sxs-lookup"><span data-stu-id="7fe10-156">For monitoring, logging, and troubleshooting information, see the following articles:</span></span>

[<span data-ttu-id="7fe10-157">Monitorování aplikací v Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7fe10-157">Monitor apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)  
<span data-ttu-id="7fe10-158">Naučte se kontrolovat kvóty a metriky pro aplikace a App Service plány.</span><span class="sxs-lookup"><span data-stu-id="7fe10-158">Learn how to review quotas and metrics for apps and App Service plans.</span></span>

[<span data-ttu-id="7fe10-159">Povolit protokolování diagnostiky pro aplikace v Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7fe10-159">Enable diagnostics logging for apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)  
<span data-ttu-id="7fe10-160">Zjistěte, jak povolit a získat přístup k protokolování diagnostiky pro stavové kódy HTTP, neúspěšné žádosti a aktivitu webového serveru.</span><span class="sxs-lookup"><span data-stu-id="7fe10-160">Discover how to enable and access diagnostic logging for HTTP status codes, failed requests, and web server activity.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="7fe10-161">Seznamte se s běžnými přístupy ke zpracování chyb v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7fe10-161">Understand common approaches to handling errors in ASP.NET Core apps.</span></span>

<xref:test/troubleshoot-azure-iis>  
<span data-ttu-id="7fe10-162">Naučte se diagnostikovat problémy s Azure App Service nasazeními s ASP.NET Core aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="7fe10-162">Learn how to diagnose issues with Azure App Service deployments with ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-iis-errors-reference>  
<span data-ttu-id="7fe10-163">Projděte si nejčastější chyby konfigurace nasazení pro aplikace hostované službou Azure App Service/IIS s pokyny k řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="7fe10-163">See the common deployment configuration errors for apps hosted by Azure App Service/IIS with troubleshooting advice.</span></span>

## <a name="data-protection-key-ring-and-deployment-slots"></a><span data-ttu-id="7fe10-164">Kroužky klíčů pro ochranu dat a sloty nasazení</span><span class="sxs-lookup"><span data-stu-id="7fe10-164">Data Protection key ring and deployment slots</span></span>

<span data-ttu-id="7fe10-165">[Klíče ochrany dat](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) jsou trvale uložené ve složce *%Home%\ASP.NET\DataProtection-Keys* .</span><span class="sxs-lookup"><span data-stu-id="7fe10-165">[Data Protection keys](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="7fe10-166">Tato složka je zajištěná síťovým úložištěm a je synchronizovaná napříč všemi počítači hostujícími aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7fe10-166">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span> <span data-ttu-id="7fe10-167">Klíče nejsou chráněny v klidovém umístění.</span><span class="sxs-lookup"><span data-stu-id="7fe10-167">Keys aren't protected at rest.</span></span> <span data-ttu-id="7fe10-168">Tato složka poskytuje klíčového prstence pro všechny instance aplikace v jednom slotu nasazení.</span><span class="sxs-lookup"><span data-stu-id="7fe10-168">This folder supplies the key ring to all instances of an app in a single deployment slot.</span></span> <span data-ttu-id="7fe10-169">Samostatné sloty nasazení, jako je například pracovní a produkční, nesdílejte klíč Ring.</span><span class="sxs-lookup"><span data-stu-id="7fe10-169">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span>

<span data-ttu-id="7fe10-170">Při záměně mezi sloty nasazení nebude možné dešifrovat uložená data pomocí nástroje Data Protection, a to pomocí klíčeového kruhu v předchozí pozici.</span><span class="sxs-lookup"><span data-stu-id="7fe10-170">When swapping between deployment slots, any system using data protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="7fe10-171">Middleware souborů cookie ASP.NET používá ochranu dat k ochraně souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="7fe10-171">ASP.NET Cookie Middleware uses data protection to protect its cookies.</span></span> <span data-ttu-id="7fe10-172">To vede k tomu, že se uživatelé odhlásí z aplikace, která používá standardní middleware souborů cookie ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="7fe10-172">This leads to users being signed out of an app that uses the standard ASP.NET Cookie Middleware.</span></span> <span data-ttu-id="7fe10-173">Pro řešení Key Ring nezávislé na slotech použijte externího poskytovatele služby Key Ring, například:</span><span class="sxs-lookup"><span data-stu-id="7fe10-173">For a slot-independent key ring solution, use an external key ring provider, such as:</span></span>

* <span data-ttu-id="7fe10-174">Azure Blob Storage</span><span class="sxs-lookup"><span data-stu-id="7fe10-174">Azure Blob Storage</span></span>
* <span data-ttu-id="7fe10-175">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="7fe10-175">Azure Key Vault</span></span>
* <span data-ttu-id="7fe10-176">Úložiště SQL</span><span class="sxs-lookup"><span data-stu-id="7fe10-176">SQL store</span></span>
* <span data-ttu-id="7fe10-177">Redis Cache</span><span class="sxs-lookup"><span data-stu-id="7fe10-177">Redis cache</span></span>

<span data-ttu-id="7fe10-178">Další informace naleznete v tématu <xref:security/data-protection/implementation/key-storage-providers>.</span><span class="sxs-lookup"><span data-stu-id="7fe10-178">For more information, see <xref:security/data-protection/implementation/key-storage-providers>.</span></span>
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>
<!-- revert this after 3.0 supported
## Deploy ASP.NET Core preview release to Azure App Service

Use one of the following approaches if the app relies on a preview release of .NET Core:

* [Install the preview site extension](#install-the-preview-site-extension).
* [Deploy a self-contained preview app](#deploy-a-self-contained-preview-app).
* [Use Docker with Web Apps for containers](#use-docker-with-web-apps-for-containers).
-->
## <a name="deploy-aspnet-core-30-to-azure-app-service"></a><span data-ttu-id="7fe10-179">Nasazení ASP.NET Core 3,0 do Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7fe10-179">Deploy ASP.NET Core 3.0 to Azure App Service</span></span>

<span data-ttu-id="7fe10-180">Doufáme, že bude mít ASP.NET Core 3,0 k dispozici v Azure App Service brzo.</span><span class="sxs-lookup"><span data-stu-id="7fe10-180">We hope to have ASP.NET Core 3.0 available on Azure App Service soon.</span></span>

<span data-ttu-id="7fe10-181">Pokud aplikace spoléhá na rozhraní .NET Core 3,0, použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="7fe10-181">Use one of the following approaches if the app relies on .NET Core 3.0:</span></span>

* <span data-ttu-id="7fe10-182">[Nainstalujte rozšíření webu verze Preview](#install-the-preview-site-extension).</span><span class="sxs-lookup"><span data-stu-id="7fe10-182">[Install the preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="7fe10-183">[Nasaďte samostatně obsaženou aplikaci pro náhled](#deploy-a-self-contained-preview-app).</span><span class="sxs-lookup"><span data-stu-id="7fe10-183">[Deploy a self-contained preview app](#deploy-a-self-contained-preview-app).</span></span>
* <span data-ttu-id="7fe10-184">[Pro kontejnery použijte Docker s Web Apps](#use-docker-with-web-apps-for-containers).</span><span class="sxs-lookup"><span data-stu-id="7fe10-184">[Use Docker with Web Apps for containers](#use-docker-with-web-apps-for-containers).</span></span>

### <a name="install-the-preview-site-extension"></a><span data-ttu-id="7fe10-185">Nainstalovat rozšíření webu verze Preview</span><span class="sxs-lookup"><span data-stu-id="7fe10-185">Install the preview site extension</span></span>

<span data-ttu-id="7fe10-186">Pokud dojde k potížím pomocí rozšíření webu verze Preview, otevřete [problém ASPNET/AspNetCore](https://github.com/aspnet/AspNetCore/issues).</span><span class="sxs-lookup"><span data-stu-id="7fe10-186">If a problem occurs using the preview site extension, open an [aspnet/AspNetCore issue](https://github.com/aspnet/AspNetCore/issues).</span></span>

1. <span data-ttu-id="7fe10-187">Na webu Azure Portal přejděte na App Service.</span><span class="sxs-lookup"><span data-stu-id="7fe10-187">From the Azure Portal, navigate to the App Service.</span></span>
1. <span data-ttu-id="7fe10-188">Vyberte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7fe10-188">Select the web app.</span></span>
1. <span data-ttu-id="7fe10-189">Do vyhledávacího pole zadejte "ex", chcete-li filtrovat "rozšíření", nebo se posuňte dolů v seznamu nástrojů pro správu.</span><span class="sxs-lookup"><span data-stu-id="7fe10-189">Type "ex" in the search box to filter for "Extensions" or scroll down the list of management tools.</span></span>
1. <span data-ttu-id="7fe10-190">Vyberte **rozšíření**.</span><span class="sxs-lookup"><span data-stu-id="7fe10-190">Select **Extensions**.</span></span>
1. <span data-ttu-id="7fe10-191">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="7fe10-191">Select **Add**.</span></span>
1. <span data-ttu-id="7fe10-192">V seznamu vyberte rozšíření **runtime ASP.NET Core {X. Y} ({x64 | x86})** , kde `{X.Y}` je verze Preview ASP.NET Core a `{x64|x86}` specifikuje platformu.</span><span class="sxs-lookup"><span data-stu-id="7fe10-192">Select the **ASP.NET Core {X.Y} ({x64|x86}) Runtime** extension from the list, where `{X.Y}` is the ASP.NET Core preview version and `{x64|x86}` specifies the platform.</span></span>
1. <span data-ttu-id="7fe10-193">Kliknutím na **OK** přijměte právní podmínky.</span><span class="sxs-lookup"><span data-stu-id="7fe10-193">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="7fe10-194">Pro instalaci rozšíření vyberte **OK** .</span><span class="sxs-lookup"><span data-stu-id="7fe10-194">Select **OK** to install the extension.</span></span>

<span data-ttu-id="7fe10-195">Po dokončení operace je nainstalovaná nejnovější verze .NET Core Preview.</span><span class="sxs-lookup"><span data-stu-id="7fe10-195">When the operation completes, the latest .NET Core preview is installed.</span></span> <span data-ttu-id="7fe10-196">Ověřte instalaci:</span><span class="sxs-lookup"><span data-stu-id="7fe10-196">Verify the installation:</span></span>

1. <span data-ttu-id="7fe10-197">Vyberte **Rozšířené nástroje**.</span><span class="sxs-lookup"><span data-stu-id="7fe10-197">Select **Advanced Tools**.</span></span>
1. <span data-ttu-id="7fe10-198">Vyberte **Přejít** v **pokročilých nástrojích**.</span><span class="sxs-lookup"><span data-stu-id="7fe10-198">Select **Go** in **Advanced Tools**.</span></span>
1. <span data-ttu-id="7fe10-199">Vyberte položku nabídky**prostředí PowerShell** **konzoly** > ladění.</span><span class="sxs-lookup"><span data-stu-id="7fe10-199">Select the **Debug console** > **PowerShell** menu item.</span></span>
1. <span data-ttu-id="7fe10-200">Na příkazovém řádku PowerShellu spusťte následující příkaz.</span><span class="sxs-lookup"><span data-stu-id="7fe10-200">At the PowerShell prompt, execute the following command.</span></span> <span data-ttu-id="7fe10-201">Nahraďte verzi ASP.NET Core Runtime pro `{X.Y}` a platformu pro `{PLATFORM}` v příkazu:</span><span class="sxs-lookup"><span data-stu-id="7fe10-201">Substitute the ASP.NET Core runtime version for `{X.Y}` and the platform for `{PLATFORM}` in the command:</span></span>

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   <span data-ttu-id="7fe10-202">Příkaz vrátí `True` , když je nainstalován modul runtime x64 Preview.</span><span class="sxs-lookup"><span data-stu-id="7fe10-202">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="7fe10-203">Architektura platformy (x86/x64) aplikace App Services je nastavená v nastavení aplikace na webu Azure Portal pro aplikace, které jsou hostované na výpočetní úrovni řady A-Series nebo lepší úrovně hostování.</span><span class="sxs-lookup"><span data-stu-id="7fe10-203">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute or better hosting tier.</span></span> <span data-ttu-id="7fe10-204">Pokud je aplikace spuštěná v režimu v procesu a architektura platformy je nakonfigurovaná pro 64-bit (x64), modul ASP.NET Core používá 64 runtime Preview, pokud je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="7fe10-204">If the app is run in in-process mode and the platform architecture is configured for 64-bit (x64), the ASP.NET Core Module uses the 64-bit preview runtime, if present.</span></span> <span data-ttu-id="7fe10-205">Nainstalujte rozšíření **Runtime ASP.NET Core {X. Y} (x64)** .</span><span class="sxs-lookup"><span data-stu-id="7fe10-205">Install the **ASP.NET Core {X.Y} (x64) Runtime** extension.</span></span>
>
> <span data-ttu-id="7fe10-206">Po instalaci prostředí runtime x64 Preview spusťte následující příkaz v příkazovém okně prostředí Kudu PowerShell a ověřte instalaci.</span><span class="sxs-lookup"><span data-stu-id="7fe10-206">After installing the x64 preview runtime, run the following command in the Kudu PowerShell command window to verify the installation.</span></span> <span data-ttu-id="7fe10-207">V příkazu nahraďte verzi ASP.NET Core `{X.Y}` runtime pro:</span><span class="sxs-lookup"><span data-stu-id="7fe10-207">Substitute the ASP.NET Core runtime version for `{X.Y}` in the command:</span></span>
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> <span data-ttu-id="7fe10-208">Příkaz vrátí `True` , když je nainstalován modul runtime x64 Preview.</span><span class="sxs-lookup"><span data-stu-id="7fe10-208">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="7fe10-209">**Rozšíření ASP.NET Core** umožňují pro ASP.NET Core v Azure App Services další funkce, jako je například povolení protokolování Azure.</span><span class="sxs-lookup"><span data-stu-id="7fe10-209">**ASP.NET Core Extensions** enables additional functionality for ASP.NET Core on Azure App Services, such as enabling Azure logging.</span></span> <span data-ttu-id="7fe10-210">Rozšíření se nainstaluje automaticky při nasazení ze sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7fe10-210">The extension is installed automatically when deploying from Visual Studio.</span></span> <span data-ttu-id="7fe10-211">Pokud rozšíření není nainstalované, nainstalujte ho pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7fe10-211">If the extension isn't installed, install it for the app.</span></span>

<span data-ttu-id="7fe10-212">**Použití rozšíření webu ve verzi Preview se šablonou ARM**</span><span class="sxs-lookup"><span data-stu-id="7fe10-212">**Use the preview site extension with an ARM template**</span></span>

<span data-ttu-id="7fe10-213">Pokud se k vytváření a nasazování aplikací používá šablona ARM, `siteextensions` typ prostředku se dá použít k přidání rozšíření webu do webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="7fe10-213">If an ARM template is used to create and deploy apps, the `siteextensions` resource type can be used to add the site extension to a web app.</span></span> <span data-ttu-id="7fe10-214">Příklad:</span><span class="sxs-lookup"><span data-stu-id="7fe10-214">For example:</span></span>

[!code-json[](index/sample/arm.json?highlight=2)]

### <a name="deploy-a-self-contained-preview-app"></a><span data-ttu-id="7fe10-215">Nasazení samostatné aplikace ve verzi Preview</span><span class="sxs-lookup"><span data-stu-id="7fe10-215">Deploy a self-contained preview app</span></span>

<span data-ttu-id="7fe10-216">[Samostatné nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) , které cílí na modul runtime verze Preview, přenáší modul runtime verze Preview do nasazení.</span><span class="sxs-lookup"><span data-stu-id="7fe10-216">A [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) that targets a preview runtime carries the preview runtime in the deployment.</span></span>

<span data-ttu-id="7fe10-217">Při nasazení samostatně obsažené aplikace:</span><span class="sxs-lookup"><span data-stu-id="7fe10-217">When deploying a self-contained app:</span></span>

* <span data-ttu-id="7fe10-218">Lokalita v Azure App Service nevyžaduje [rozšíření webu verze Preview](#install-the-preview-site-extension).</span><span class="sxs-lookup"><span data-stu-id="7fe10-218">The site in Azure App Service doesn't require the [preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="7fe10-219">Aplikace musí být publikována při jiném přístupu než při publikování pro [nasazení závislé na rozhraní (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="7fe10-219">The app must be published following a different approach than when publishing for a [framework-dependent deployment (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="7fe10-220">Postupujte podle pokynů v části [nasazení samostatně obsaženého oddílu aplikace](#deploy-the-app-self-contained) .</span><span class="sxs-lookup"><span data-stu-id="7fe10-220">Follow the guidance in the [Deploy the app self-contained](#deploy-the-app-self-contained) section.</span></span>

### <a name="use-docker-with-web-apps-for-containers"></a><span data-ttu-id="7fe10-221">Použití Docker s Web Apps pro kontejnery</span><span class="sxs-lookup"><span data-stu-id="7fe10-221">Use Docker with Web Apps for containers</span></span>

<span data-ttu-id="7fe10-222">[Dokovací centrum](https://hub.docker.com/r/microsoft/aspnetcore/) obsahuje nejnovější image Docker Preview.</span><span class="sxs-lookup"><span data-stu-id="7fe10-222">The [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contains the latest preview Docker images.</span></span> <span data-ttu-id="7fe10-223">Obrázky lze použít jako základní bitovou kopii.</span><span class="sxs-lookup"><span data-stu-id="7fe10-223">The images can be used as a base image.</span></span> <span data-ttu-id="7fe10-224">Použijte image a nasaďte ji normálně pro Web Apps kontejnerů.</span><span class="sxs-lookup"><span data-stu-id="7fe10-224">Use the image and deploy to Web Apps for Containers normally.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="7fe10-225">Publikování a nasazení aplikace</span><span class="sxs-lookup"><span data-stu-id="7fe10-225">Publish and deploy the app</span></span>

### <a name="deploy-the-app-framework-dependent"></a><span data-ttu-id="7fe10-226">Nasazení závislého rozhraní App Framework</span><span class="sxs-lookup"><span data-stu-id="7fe10-226">Deploy the app framework-dependent</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="7fe10-227">Pro 64 [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="7fe10-227">For a 64-bit [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

* <span data-ttu-id="7fe10-228">K vytvoření 64 aplikace použijte 64 .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="7fe10-228">Use a 64-bit .NET Core SDK to build a 64-bit app.</span></span>
* <span data-ttu-id="7fe10-229">V**obecných nastaveních** **Konfigurace** > App Service nastavte **platformou** na **64 bit** .</span><span class="sxs-lookup"><span data-stu-id="7fe10-229">Set the **Platform** to **64 Bit** in the App Service's **Configuration** > **General settings**.</span></span> <span data-ttu-id="7fe10-230">Aby bylo možné vybrat bitová verze platformy, musí aplikace používat základní nebo vyšší plán služby.</span><span class="sxs-lookup"><span data-stu-id="7fe10-230">The app must use a Basic or higher service plan to enable the choice of platform bitness.</span></span>

::: moniker-end

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7fe10-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7fe10-231">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="7fe10-232">Vyberte **sestavení** > **Publikovat {název aplikace}** z panelu nástrojů sady Visual Studio nebo klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="7fe10-232">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="7fe10-233">V dialogovém okně **vybrat cíl publikování** potvrďte, že je vybrána možnost **App Service** .</span><span class="sxs-lookup"><span data-stu-id="7fe10-233">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="7fe10-234">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="7fe10-234">Select **Advanced**.</span></span> <span data-ttu-id="7fe10-235">Otevře se dialogové okno **publikovat** .</span><span class="sxs-lookup"><span data-stu-id="7fe10-235">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="7fe10-236">V **publikovat** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="7fe10-236">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="7fe10-237">Potvrďte, že je vybraná konfigurace **vydané verze** .</span><span class="sxs-lookup"><span data-stu-id="7fe10-237">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="7fe10-238">Otevřete rozevírací seznam **režim nasazení** a vyberte možnost závislé na **rozhraní**.</span><span class="sxs-lookup"><span data-stu-id="7fe10-238">Open the **Deployment Mode** drop-down list and select **Framework-Dependent**.</span></span>
   * <span data-ttu-id="7fe10-239">Jako **cílový modul runtime**vyberte **přenosný** .</span><span class="sxs-lookup"><span data-stu-id="7fe10-239">Select **Portable** as the **Target Runtime**.</span></span>
   * <span data-ttu-id="7fe10-240">Pokud potřebujete po nasazení odebrat další soubory, otevřete **Možnosti publikování souborů** a zaškrtnutím políčka odeberte další soubory v cílovém umístění.</span><span class="sxs-lookup"><span data-stu-id="7fe10-240">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="7fe10-241">Vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="7fe10-241">Select **Save**.</span></span>
1. <span data-ttu-id="7fe10-242">Pomocí zbývajících výzev Průvodce publikováním vytvořte novou lokalitu nebo aktualizujte existující web.</span><span class="sxs-lookup"><span data-stu-id="7fe10-242">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="7fe10-243">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="7fe10-243">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="7fe10-244">V souboru projektu nezadávejte [identifikátor modulu runtime (RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="7fe10-244">In the project file, don't specify a [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

1. <span data-ttu-id="7fe10-245">V příkazovém prostředí publikujte aplikaci v konfiguraci vydaných verzí pomocí příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="7fe10-245">From a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="7fe10-246">V následujícím příkladu je aplikace publikována jako aplikace závislá na rozhraní:</span><span class="sxs-lookup"><span data-stu-id="7fe10-246">In the following example, the app is published as a framework-dependent app:</span></span>

   ```console
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="7fe10-247">Přesuňte obsah adresáře *bin/Release/{Target Framework}/Publish* do lokality v App Service.</span><span class="sxs-lookup"><span data-stu-id="7fe10-247">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="7fe10-248">Pokud přetáhnete obsah složky pro *publikování* z místního pevného disku nebo sdílené síťové složky přímo do App Service v konzole [Kudu](https://github.com/projectkudu/kudu/wiki) , přetáhněte `D:\home\site\wwwroot` soubory do složky v konzole Kudu.</span><span class="sxs-lookup"><span data-stu-id="7fe10-248">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the [Kudu](https://github.com/projectkudu/kudu/wiki) console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

### <a name="deploy-the-app-self-contained"></a><span data-ttu-id="7fe10-249">Nasazení samostatně obsažené aplikace</span><span class="sxs-lookup"><span data-stu-id="7fe10-249">Deploy the app self-contained</span></span>

<span data-ttu-id="7fe10-250">Použijte nástroje sady Visual Studio nebo rozhraní příkazového řádku (CLI) pro [samostatně uzavřené nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="7fe10-250">Use Visual Studio or the command-line interface (CLI) tools for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7fe10-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7fe10-251">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="7fe10-252">Vyberte **sestavení** > **Publikovat {název aplikace}** z panelu nástrojů sady Visual Studio nebo klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="7fe10-252">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="7fe10-253">V dialogovém okně **vybrat cíl publikování** potvrďte, že je vybrána možnost **App Service** .</span><span class="sxs-lookup"><span data-stu-id="7fe10-253">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="7fe10-254">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="7fe10-254">Select **Advanced**.</span></span> <span data-ttu-id="7fe10-255">Otevře se dialogové okno **publikovat** .</span><span class="sxs-lookup"><span data-stu-id="7fe10-255">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="7fe10-256">V **publikovat** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="7fe10-256">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="7fe10-257">Potvrďte, že je vybraná konfigurace **vydané verze** .</span><span class="sxs-lookup"><span data-stu-id="7fe10-257">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="7fe10-258">Otevřete rozevírací seznam **režim nasazení** a vyberte možnost **samostatně obsaženo**.</span><span class="sxs-lookup"><span data-stu-id="7fe10-258">Open the **Deployment Mode** drop-down list and select **Self-Contained**.</span></span>
   * <span data-ttu-id="7fe10-259">Z rozevíracího seznamu **cílový modul runtime** vyberte cílový modul runtime.</span><span class="sxs-lookup"><span data-stu-id="7fe10-259">Select the target runtime from the **Target Runtime** drop-down list.</span></span> <span data-ttu-id="7fe10-260">Výchozí hodnota je `win-x86`.</span><span class="sxs-lookup"><span data-stu-id="7fe10-260">The default is `win-x86`.</span></span>
   * <span data-ttu-id="7fe10-261">Pokud potřebujete po nasazení odebrat další soubory, otevřete **Možnosti publikování souborů** a zaškrtnutím políčka odeberte další soubory v cílovém umístění.</span><span class="sxs-lookup"><span data-stu-id="7fe10-261">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="7fe10-262">Vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="7fe10-262">Select **Save**.</span></span>
1. <span data-ttu-id="7fe10-263">Pomocí zbývajících výzev Průvodce publikováním vytvořte novou lokalitu nebo aktualizujte existující web.</span><span class="sxs-lookup"><span data-stu-id="7fe10-263">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="7fe10-264">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="7fe10-264">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="7fe10-265">V souboru projektu zadejte jeden nebo více identifikátorů [modulu runtime (identifikátorů RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="7fe10-265">In the project file, specify one or more [Runtime Identifiers (RIDs)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="7fe10-266">Pro `<RuntimeIdentifier>` jeden identifikátor RID použijte (jednotné číslo), nebo `<RuntimeIdentifiers>` použijte (plural) k poskytnutí seznamu identifikátorů ridů oddělených středníkem.</span><span class="sxs-lookup"><span data-stu-id="7fe10-266">Use `<RuntimeIdentifier>` (singular) for a single RID, or use `<RuntimeIdentifiers>` (plural) to provide a semicolon-delimited list of RIDs.</span></span> <span data-ttu-id="7fe10-267">V následujícím příkladu `win-x86` je zadáno RID:</span><span class="sxs-lookup"><span data-stu-id="7fe10-267">In the following example, the `win-x86` RID is specified:</span></span>

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. <span data-ttu-id="7fe10-268">Z příkazového prostředí publikujte aplikaci v konfiguraci vydání pro modul runtime hostitele pomocí příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="7fe10-268">From a command shell, publish the app in Release configuration for the host's runtime with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="7fe10-269">V následujícím příkladu je aplikace publikována pro `win-x86` identifikátor RID.</span><span class="sxs-lookup"><span data-stu-id="7fe10-269">In the following example, the app is published for the `win-x86` RID.</span></span> <span data-ttu-id="7fe10-270">Identifikátor RID zadaný do `--runtime` možnosti musí být uveden `<RuntimeIdentifier>` ve vlastnosti (nebo `<RuntimeIdentifiers>`) v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="7fe10-270">The RID supplied to the `--runtime` option must be provided in the `<RuntimeIdentifier>` (or `<RuntimeIdentifiers>`) property in the project file.</span></span>

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. <span data-ttu-id="7fe10-271">Přesuňte obsah složky *bin/Release/{Target Framework}/{runtime identifikátor}/Publish* do lokality v App Service.</span><span class="sxs-lookup"><span data-stu-id="7fe10-271">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="7fe10-272">Pokud přetáhnete obsah složky pro *publikování* z místního pevného disku nebo sdílené síťové složky přímo do App Service v konzole Kudu, přetáhněte soubory do `D:\home\site\wwwroot` složky v konzole Kudu.</span><span class="sxs-lookup"><span data-stu-id="7fe10-272">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the Kudu console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

## <a name="protocol-settings-https"></a><span data-ttu-id="7fe10-273">Nastavení protokolu (HTTPS)</span><span class="sxs-lookup"><span data-stu-id="7fe10-273">Protocol settings (HTTPS)</span></span>

<span data-ttu-id="7fe10-274">Vazby zabezpečeného protokolu umožňují zadat certifikát, který se má použít při reagování na požadavky přes protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7fe10-274">Secure protocol bindings allow you specify a certificate to use when responding to requests over HTTPS.</span></span> <span data-ttu-id="7fe10-275">Vazba vyžaduje platný privátní certifikát ( *. pfx*), který byl vydán pro konkrétní název hostitele.</span><span class="sxs-lookup"><span data-stu-id="7fe10-275">Binding requires a valid private certificate (*.pfx*) issued for the specific hostname.</span></span> <span data-ttu-id="7fe10-276">Další informace najdete v tématu [kurz: Navažte existující vlastní certifikát SSL na Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="7fe10-276">For more information, see [Tutorial: Bind an existing custom SSL certificate to Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="7fe10-277">Transformace souboru web.config</span><span class="sxs-lookup"><span data-stu-id="7fe10-277">Transform web.config</span></span>

<span data-ttu-id="7fe10-278">Pokud potřebujete transformovat *Web. config* při publikování (například nastavit proměnné prostředí na základě konfigurace, profilu nebo prostředí), přečtěte si téma <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="7fe10-278">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7fe10-279">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="7fe10-279">Additional resources</span></span>

* [<span data-ttu-id="7fe10-280">Přehled App Service</span><span class="sxs-lookup"><span data-stu-id="7fe10-280">App Service overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="7fe10-281">Azure App Service: Nejlepší místo pro hostování aplikací .NET (video s přehledem 55-minut)</span><span class="sxs-lookup"><span data-stu-id="7fe10-281">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span></span>](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [<span data-ttu-id="7fe10-282">Azure pátek: Prostředí pro řešení potíží s diagnostikou a Azure App Service (video s 12 minutami)</span><span class="sxs-lookup"><span data-stu-id="7fe10-282">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [<span data-ttu-id="7fe10-283">Přehled diagnostiky Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7fe10-283">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

<span data-ttu-id="7fe10-284">Azure App Service na Windows serveru používá [Internetová informační služba (IIS)](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="7fe10-284">Azure App Service on Windows Server uses [Internet Information Services (IIS)](https://www.iis.net/).</span></span> <span data-ttu-id="7fe10-285">Následující témata se týkají základní technologie IIS:</span><span class="sxs-lookup"><span data-stu-id="7fe10-285">The following topics pertain to the underlying IIS technology:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [<span data-ttu-id="7fe10-286">Windows Server – obsah správce IT pro aktuální a předchozí verze</span><span class="sxs-lookup"><span data-stu-id="7fe10-286">Windows Server - IT administrator content for current and previous releases</span></span>](/windows-server/windows-server-versions)
