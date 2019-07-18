---
title: Nasazení aplikací ASP.NET Core pro Azure App Service
author: guardrex
description: Tento článek obsahuje odkazy na hostitele Azure a nasazení prostředků.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/16/2019
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: bbdb3e92b6b8afb44d9c0c95c240002c7b7c17db
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308145"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a><span data-ttu-id="d6568-103">Nasazení aplikací ASP.NET Core pro Azure App Service</span><span class="sxs-lookup"><span data-stu-id="d6568-103">Deploy ASP.NET Core apps to Azure App Service</span></span>

<span data-ttu-id="d6568-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) je [platforma cloud computingu od Microsoftu](https://azure.microsoft.com/) pro hostování webových aplikací, včetně ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d6568-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span>

## <a name="useful-resources"></a><span data-ttu-id="d6568-105">Užitečné zdroje</span><span class="sxs-lookup"><span data-stu-id="d6568-105">Useful resources</span></span>

<span data-ttu-id="d6568-106">[Dokumentace k App Service](/azure/app-service/) je Domovská stránka pro Azure Apps, kurzy, ukázky, návody a další materiály.</span><span class="sxs-lookup"><span data-stu-id="d6568-106">[App Service Documentation](/azure/app-service/) is the home for Azure Apps documentation, tutorials, samples, how-to guides, and other resources.</span></span> <span data-ttu-id="d6568-107">Existují dva významné kurzy, které se týkají hostování ASP.NET Corech aplikací:</span><span class="sxs-lookup"><span data-stu-id="d6568-107">Two notable tutorials that pertain to hosting ASP.NET Core apps are:</span></span>

[<span data-ttu-id="d6568-108">Vytvoření webové aplikace v ASP.NET Core v Azure</span><span class="sxs-lookup"><span data-stu-id="d6568-108">Create an ASP.NET Core web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)  
<span data-ttu-id="d6568-109">Pomocí sady Visual Studio můžete vytvořit a nasadit webovou aplikaci ASP.NET Core pro Azure App Service ve Windows.</span><span class="sxs-lookup"><span data-stu-id="d6568-109">Use Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service on Windows.</span></span>

[<span data-ttu-id="d6568-110">Vytvoření aplikace ASP.NET Core v App Service v systému Linux</span><span class="sxs-lookup"><span data-stu-id="d6568-110">Create an ASP.NET Core app in App Service on Linux</span></span>](/azure/app-service/containers/quickstart-dotnetcore)  
<span data-ttu-id="d6568-111">Pomocí příkazového řádku můžete vytvořit a nasadit webovou aplikaci ASP.NET Core pro Azure App Service v systému Linux.</span><span class="sxs-lookup"><span data-stu-id="d6568-111">Use the command line to create and deploy an ASP.NET Core web app to Azure App Service on Linux.</span></span>

<span data-ttu-id="d6568-112">V dokumentaci k ASP.NET Core jsou k dispozici následující články:</span><span class="sxs-lookup"><span data-stu-id="d6568-112">The following articles are available in ASP.NET Core documentation:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="d6568-113">Zjistěte, jak publikovat aplikace ASP.NET Core do služby Azure App Service pomocí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d6568-113">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
<span data-ttu-id="d6568-114">Naučte se, jak vytvořit webovou aplikaci ASP.NET Core pomocí sady Visual Studio a nasadit ji do Azure App Service pomocí Gitu pro průběžné nasazování.</span><span class="sxs-lookup"><span data-stu-id="d6568-114">Learn how to create an ASP.NET Core web app using Visual Studio and deploy it to Azure App Service using Git for continuous deployment.</span></span>

[<span data-ttu-id="d6568-115">Vytvoření prvního kanálu</span><span class="sxs-lookup"><span data-stu-id="d6568-115">Create your first pipeline</span></span>](/azure/devops/pipelines/get-started-yaml)  
<span data-ttu-id="d6568-116">Nastavte sestavení CI pro aplikaci ASP.NET Core a pak vytvořte Azure App Service vydanou verzi pro průběžné nasazování.</span><span class="sxs-lookup"><span data-stu-id="d6568-116">Set up a CI build for an ASP.NET Core app, then create a continuous deployment release to Azure App Service.</span></span>

[<span data-ttu-id="d6568-117">Sandbox webové aplikace Azure</span><span class="sxs-lookup"><span data-stu-id="d6568-117">Azure Web App sandbox</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
<span data-ttu-id="d6568-118">Zjištění omezení spouštění Azure App Service runtime, která vynutila platforma Azure Apps</span><span class="sxs-lookup"><span data-stu-id="d6568-118">Discover Azure App Service runtime execution limitations enforced by the Azure Apps platform.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="d6568-119">Konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="d6568-119">Application configuration</span></span>

### <a name="platform"></a><span data-ttu-id="d6568-120">Platforma</span><span class="sxs-lookup"><span data-stu-id="d6568-120">Platform</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="d6568-121">V Azure App Service jsou k dispozici moduly runtime pro 64 (x64) a 32-bit (x86) aplikací.</span><span class="sxs-lookup"><span data-stu-id="d6568-121">Runtimes for 64-bit (x64) and 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="d6568-122">[.NET Core SDK](/dotnet/core/sdk) k dispozici v App Service je 32-bit, ale můžete nasadit 64 aplikace sestavené místně pomocí konzoly [Kudu](https://github.com/projectkudu/kudu/wiki) nebo procesu publikování v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d6568-122">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit, but you can deploy 64-bit apps built locally using the [Kudu](https://github.com/projectkudu/kudu/wiki) console or the publish process in Visual Studio.</span></span> <span data-ttu-id="d6568-123">Další informace najdete v části [publikování a nasazení aplikace](#publish-and-deploy-the-app) .</span><span class="sxs-lookup"><span data-stu-id="d6568-123">For more information, see the [Publish and deploy the app](#publish-and-deploy-the-app) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="d6568-124">Pro aplikace s nativními závislostmi jsou moduly runtime pro 32 aplikace (x86) k dispozici na Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="d6568-124">For apps with native dependencies, runtimes for 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="d6568-125">[.NET Core SDK](/dotnet/core/sdk) k dispozici v App Service je 32-bit.</span><span class="sxs-lookup"><span data-stu-id="d6568-125">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit.</span></span>

::: moniker-end

<span data-ttu-id="d6568-126">Další informace o komponentách rozhraní .NET Core Framework a metodách distribuce, jako jsou informace o modulu runtime .NET Core a .NET Core SDK, [najdete v tématu o .NET Core: Složení](/dotnet/core/about#composition).</span><span class="sxs-lookup"><span data-stu-id="d6568-126">For more information on .NET Core framework components and distribution methods, such as information on the .NET Core runtime and the .NET Core SDK, see [About .NET Core: Composition](/dotnet/core/about#composition).</span></span>

### <a name="packages"></a><span data-ttu-id="d6568-127">Balíčky</span><span class="sxs-lookup"><span data-stu-id="d6568-127">Packages</span></span>

<span data-ttu-id="d6568-128">Zahrňte následující balíčky NuGet pro poskytování automatických funkcí protokolování pro aplikace nasazené do Azure App Service:</span><span class="sxs-lookup"><span data-stu-id="d6568-128">Include the following NuGet packages to provide automatic logging features for apps deployed to Azure App Service:</span></span>

* <span data-ttu-id="d6568-129">[Microsoft. AspNetCore. AzureAppServices. HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) využívá [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) k poskytování ASP.NET Coreho urychlené integrace s Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="d6568-129">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) uses [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) to provide ASP.NET Core light-up integration with Azure App Service.</span></span> <span data-ttu-id="d6568-130">Přidané funkce protokolování poskytuje `Microsoft.AspNetCore.AzureAppServicesIntegration` balíček.</span><span class="sxs-lookup"><span data-stu-id="d6568-130">The added logging features are provided by the `Microsoft.AspNetCore.AzureAppServicesIntegration` package.</span></span>
* <span data-ttu-id="d6568-131">[Microsoft. AspNetCore. AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) spustí [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) pro Azure App Service přidání `Microsoft.Extensions.Logging.AzureAppServices` zprostředkovatelů protokolování diagnostiky do balíčku.</span><span class="sxs-lookup"><span data-stu-id="d6568-131">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executes [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) to add Azure App Service diagnostics logging providers in the `Microsoft.Extensions.Logging.AzureAppServices` package.</span></span>
* <span data-ttu-id="d6568-132">[Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) poskytuje implementace protokolovacích nástrojů pro podporu Azure App Servicech diagnostických protokolů a funkcí streamování protokolů.</span><span class="sxs-lookup"><span data-stu-id="d6568-132">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) provides logger implementations to support Azure App Service diagnostics logs and log streaming features.</span></span>

<span data-ttu-id="d6568-133">Předchozí balíčky nejsou k dispozici ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="d6568-133">The preceding packages aren't available from the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="d6568-134">Aplikace, které .NET Framework cílí na Metapackage `Microsoft.AspNetCore.App` nebo odkazují na, musí explicitně odkazovat na jednotlivé balíčky v souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="d6568-134">Apps that target .NET Framework or reference the `Microsoft.AspNetCore.App` metapackage must explicitly reference the individual packages in the app's project file.</span></span>

## <a name="override-app-configuration-using-the-azure-portal"></a><span data-ttu-id="d6568-135">Přepsání konfigurace aplikace pomocí webu Azure Portal</span><span class="sxs-lookup"><span data-stu-id="d6568-135">Override app configuration using the Azure Portal</span></span>

<span data-ttu-id="d6568-136">Nastavení aplikace na webu Azure Portal umožňuje nastavit proměnné prostředí pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d6568-136">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="d6568-137">Proměnné prostředí mohou být spotřebovány [poskytovatelem konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d6568-137">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="d6568-138">Když se na webu Azure Portal vytvoří nebo upraví nastavení aplikace a vybere se tlačítko **Save (Uložit** ), aplikace Azure se restartuje.</span><span class="sxs-lookup"><span data-stu-id="d6568-138">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="d6568-139">Proměnná prostředí je k dispozici aplikaci po restartování služby.</span><span class="sxs-lookup"><span data-stu-id="d6568-139">The environment variable is available to the app after the service restarts.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d6568-140">Když aplikace používá [obecného hostitele](xref:fundamentals/host/generic-host), proměnné prostředí se ve výchozím nastavení načtou do konfigurace aplikace a Poskytovatel konfigurace musí být přidaný vývojářem.</span><span class="sxs-lookup"><span data-stu-id="d6568-140">When an app uses the [Generic Host](xref:fundamentals/host/generic-host), environment variables aren't loaded into an app's configuration by default and the configuration provider must be added by the developer.</span></span> <span data-ttu-id="d6568-141">Vývojář určí předponu proměnné prostředí při přidání poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="d6568-141">The developer determines the environment variable prefix when the configuration provider is added.</span></span> <span data-ttu-id="d6568-142">Další informace naleznete v tématu <xref:fundamentals/host/generic-host> a [zprostředkovatel konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d6568-142">For more information, see <xref:fundamentals/host/generic-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d6568-143">Když aplikace vytvoří hostitele pomocí webhost [. CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), proměnné prostředí, které konfigurují hostitele, používají `ASPNETCORE_` předponu.</span><span class="sxs-lookup"><span data-stu-id="d6568-143">When an app builds the host using [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), environment variables that configure the host use the `ASPNETCORE_` prefix.</span></span> <span data-ttu-id="d6568-144">Další informace naleznete v tématu <xref:fundamentals/host/web-host> a [zprostředkovatel konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d6568-144">For more information, see <xref:fundamentals/host/web-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d6568-145">Proxy server a scénáře pro nástroj pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="d6568-145">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d6568-146">[IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), který konfiguruje předávaný middleware hlaviček při hostování [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model)a modul ASP.NET Core je nakonfigurovaný pro přeposílání schématu (http/https) a vzdálené IP adresy, na které pochází požadavek .</span><span class="sxs-lookup"><span data-stu-id="d6568-146">The [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), which configures Forwarded Headers Middleware when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="d6568-147">Další konfigurace může být nezbytný pro aplikací hostovaných za službou další proxy servery a nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="d6568-147">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="d6568-148">Další informace najdete v tématu [konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="d6568-148">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="monitoring-and-logging"></a><span data-ttu-id="d6568-149">Monitorování a protokolování</span><span class="sxs-lookup"><span data-stu-id="d6568-149">Monitoring and logging</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d6568-150">Aplikace ASP.NET Core nasazené pro App Service automaticky dostanou rozšíření App Service **ASP.NET Core Integration Logging**.</span><span class="sxs-lookup"><span data-stu-id="d6568-150">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Integration**.</span></span> <span data-ttu-id="d6568-151">Rozšíření umožňuje integraci protokolování pro aplikace ASP.NET Core v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="d6568-151">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d6568-152">Aplikace ASP.NET Core nasazené pro App Service automaticky dostanou rozšíření App Service **ASP.NET Core rozšíření protokolování**.</span><span class="sxs-lookup"><span data-stu-id="d6568-152">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Extensions**.</span></span> <span data-ttu-id="d6568-153">Rozšíření umožňuje integraci protokolování pro aplikace ASP.NET Core v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="d6568-153">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

<span data-ttu-id="d6568-154">Informace o monitorování, protokolování a odstraňování potíží najdete v následujících článcích:</span><span class="sxs-lookup"><span data-stu-id="d6568-154">For monitoring, logging, and troubleshooting information, see the following articles:</span></span>

[<span data-ttu-id="d6568-155">Monitorování aplikací v Azure App Service</span><span class="sxs-lookup"><span data-stu-id="d6568-155">Monitor apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)  
<span data-ttu-id="d6568-156">Naučte se kontrolovat kvóty a metriky pro aplikace a App Service plány.</span><span class="sxs-lookup"><span data-stu-id="d6568-156">Learn how to review quotas and metrics for apps and App Service plans.</span></span>

[<span data-ttu-id="d6568-157">Povolit protokolování diagnostiky pro aplikace v Azure App Service</span><span class="sxs-lookup"><span data-stu-id="d6568-157">Enable diagnostics logging for apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)  
<span data-ttu-id="d6568-158">Zjistěte, jak povolit a získat přístup k protokolování diagnostiky pro stavové kódy HTTP, neúspěšné žádosti a aktivitu webového serveru.</span><span class="sxs-lookup"><span data-stu-id="d6568-158">Discover how to enable and access diagnostic logging for HTTP status codes, failed requests, and web server activity.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="d6568-159">Seznamte se s běžnými přístupy ke zpracování chyb v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d6568-159">Understand common approaches to handling errors in ASP.NET Core apps.</span></span>

<xref:test/troubleshoot-azure-iis>  
<span data-ttu-id="d6568-160">Naučte se diagnostikovat problémy s Azure App Service nasazeními s ASP.NET Core aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="d6568-160">Learn how to diagnose issues with Azure App Service deployments with ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-iis-errors-reference>  
<span data-ttu-id="d6568-161">Projděte si nejčastější chyby konfigurace nasazení pro aplikace hostované službou Azure App Service/IIS s pokyny k řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="d6568-161">See the common deployment configuration errors for apps hosted by Azure App Service/IIS with troubleshooting advice.</span></span>

## <a name="data-protection-key-ring-and-deployment-slots"></a><span data-ttu-id="d6568-162">Kroužky klíčů pro ochranu dat a sloty nasazení</span><span class="sxs-lookup"><span data-stu-id="d6568-162">Data Protection key ring and deployment slots</span></span>

<span data-ttu-id="d6568-163">[Klíče ochrany dat](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) jsou trvale uložené ve složce *%Home%\ASP.NET\DataProtection-Keys* .</span><span class="sxs-lookup"><span data-stu-id="d6568-163">[Data Protection keys](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="d6568-164">Tato složka je zajištěná síťovým úložištěm a je synchronizovaná napříč všemi počítači hostujícími aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d6568-164">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span> <span data-ttu-id="d6568-165">Klíče nejsou chráněny v klidovém umístění.</span><span class="sxs-lookup"><span data-stu-id="d6568-165">Keys aren't protected at rest.</span></span> <span data-ttu-id="d6568-166">Tato složka poskytuje klíčového prstence pro všechny instance aplikace v jednom slotu nasazení.</span><span class="sxs-lookup"><span data-stu-id="d6568-166">This folder supplies the key ring to all instances of an app in a single deployment slot.</span></span> <span data-ttu-id="d6568-167">Samostatné sloty nasazení, jako je například pracovní a produkční, nesdílejte klíč Ring.</span><span class="sxs-lookup"><span data-stu-id="d6568-167">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span>

<span data-ttu-id="d6568-168">Při záměně mezi sloty nasazení nebude možné dešifrovat uložená data pomocí nástroje Data Protection, a to pomocí klíčeového kruhu v předchozí pozici.</span><span class="sxs-lookup"><span data-stu-id="d6568-168">When swapping between deployment slots, any system using data protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="d6568-169">Middleware souborů cookie ASP.NET používá ochranu dat k ochraně souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="d6568-169">ASP.NET Cookie Middleware uses data protection to protect its cookies.</span></span> <span data-ttu-id="d6568-170">To vede k tomu, že se uživatelé odhlásí z aplikace, která používá standardní middleware souborů cookie ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="d6568-170">This leads to users being signed out of an app that uses the standard ASP.NET Cookie Middleware.</span></span> <span data-ttu-id="d6568-171">Pro řešení Key Ring nezávislé na slotech použijte externího poskytovatele služby Key Ring, například:</span><span class="sxs-lookup"><span data-stu-id="d6568-171">For a slot-independent key ring solution, use an external key ring provider, such as:</span></span>

* <span data-ttu-id="d6568-172">Azure Blob Storage</span><span class="sxs-lookup"><span data-stu-id="d6568-172">Azure Blob Storage</span></span>
* <span data-ttu-id="d6568-173">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d6568-173">Azure Key Vault</span></span>
* <span data-ttu-id="d6568-174">Úložiště SQL</span><span class="sxs-lookup"><span data-stu-id="d6568-174">SQL store</span></span>
* <span data-ttu-id="d6568-175">Redis Cache</span><span class="sxs-lookup"><span data-stu-id="d6568-175">Redis cache</span></span>

<span data-ttu-id="d6568-176">Další informace naleznete v tématu <xref:security/data-protection/implementation/key-storage-providers>.</span><span class="sxs-lookup"><span data-stu-id="d6568-176">For more information, see <xref:security/data-protection/implementation/key-storage-providers>.</span></span>

## <a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a><span data-ttu-id="d6568-177">Nasazení verze Preview ASP.NET Core do Azure App Service</span><span class="sxs-lookup"><span data-stu-id="d6568-177">Deploy ASP.NET Core preview release to Azure App Service</span></span>

<span data-ttu-id="d6568-178">Pokud se aplikace spoléhá na verzi Preview rozhraní .NET Core, použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="d6568-178">Use one of the following approaches if the app relies on a preview release of .NET Core:</span></span>

* <span data-ttu-id="d6568-179">[Nainstalujte rozšíření webu verze Preview](#install-the-preview-site-extension).</span><span class="sxs-lookup"><span data-stu-id="d6568-179">[Install the preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="d6568-180">[Nasaďte samostatně obsaženou aplikaci pro náhled](#deploy-a-self-contained-preview-app).</span><span class="sxs-lookup"><span data-stu-id="d6568-180">[Deploy a self-contained preview app](#deploy-a-self-contained-preview-app).</span></span>
* <span data-ttu-id="d6568-181">[Pro kontejnery použijte Docker s Web Apps](#use-docker-with-web-apps-for-containers).</span><span class="sxs-lookup"><span data-stu-id="d6568-181">[Use Docker with Web Apps for containers](#use-docker-with-web-apps-for-containers).</span></span>

### <a name="install-the-preview-site-extension"></a><span data-ttu-id="d6568-182">Nainstalovat rozšíření webu verze Preview</span><span class="sxs-lookup"><span data-stu-id="d6568-182">Install the preview site extension</span></span>

<span data-ttu-id="d6568-183">Pokud dojde k potížím pomocí rozšíření webu verze Preview, otevřete [problém ASPNET/AspNetCore](https://github.com/aspnet/AspNetCore/issues).</span><span class="sxs-lookup"><span data-stu-id="d6568-183">If a problem occurs using the preview site extension, open an [aspnet/AspNetCore issue](https://github.com/aspnet/AspNetCore/issues).</span></span>

1. <span data-ttu-id="d6568-184">Na webu Azure Portal přejděte na App Service.</span><span class="sxs-lookup"><span data-stu-id="d6568-184">From the Azure Portal, navigate to the App Service.</span></span>
1. <span data-ttu-id="d6568-185">Vyberte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d6568-185">Select the web app.</span></span>
1. <span data-ttu-id="d6568-186">Do vyhledávacího pole zadejte "ex", chcete-li filtrovat "rozšíření", nebo se posuňte dolů v seznamu nástrojů pro správu.</span><span class="sxs-lookup"><span data-stu-id="d6568-186">Type "ex" in the search box to filter for "Extensions" or scroll down the list of management tools.</span></span>
1. <span data-ttu-id="d6568-187">Vyberte **rozšíření**.</span><span class="sxs-lookup"><span data-stu-id="d6568-187">Select **Extensions**.</span></span>
1. <span data-ttu-id="d6568-188">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="d6568-188">Select **Add**.</span></span>
1. <span data-ttu-id="d6568-189">V seznamu vyberte rozšíření **runtime ASP.NET Core {X. Y} ({x64 | x86})** , kde `{X.Y}` je verze Preview ASP.NET Core a `{x64|x86}` specifikuje platformu.</span><span class="sxs-lookup"><span data-stu-id="d6568-189">Select the **ASP.NET Core {X.Y} ({x64|x86}) Runtime** extension from the list, where `{X.Y}` is the ASP.NET Core preview version and `{x64|x86}` specifies the platform.</span></span>
1. <span data-ttu-id="d6568-190">Kliknutím na **OK** přijměte právní podmínky.</span><span class="sxs-lookup"><span data-stu-id="d6568-190">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="d6568-191">Pro instalaci rozšíření vyberte **OK** .</span><span class="sxs-lookup"><span data-stu-id="d6568-191">Select **OK** to install the extension.</span></span>

<span data-ttu-id="d6568-192">Po dokončení operace je nainstalovaná nejnovější verze .NET Core Preview.</span><span class="sxs-lookup"><span data-stu-id="d6568-192">When the operation completes, the latest .NET Core preview is installed.</span></span> <span data-ttu-id="d6568-193">Ověřte instalaci:</span><span class="sxs-lookup"><span data-stu-id="d6568-193">Verify the installation:</span></span>

1. <span data-ttu-id="d6568-194">Vyberte **Rozšířené nástroje**.</span><span class="sxs-lookup"><span data-stu-id="d6568-194">Select **Advanced Tools**.</span></span>
1. <span data-ttu-id="d6568-195">Vyberte **Přejít** v **pokročilých nástrojích**.</span><span class="sxs-lookup"><span data-stu-id="d6568-195">Select **Go** in **Advanced Tools**.</span></span>
1. <span data-ttu-id="d6568-196">Vyberte položku nabídky**prostředí PowerShell** **konzoly** > ladění.</span><span class="sxs-lookup"><span data-stu-id="d6568-196">Select the **Debug console** > **PowerShell** menu item.</span></span>
1. <span data-ttu-id="d6568-197">Na příkazovém řádku PowerShellu spusťte následující příkaz.</span><span class="sxs-lookup"><span data-stu-id="d6568-197">At the PowerShell prompt, execute the following command.</span></span> <span data-ttu-id="d6568-198">Nahraďte verzi ASP.NET Core Runtime pro `{X.Y}` a platformu pro `{PLATFORM}` v příkazu:</span><span class="sxs-lookup"><span data-stu-id="d6568-198">Substitute the ASP.NET Core runtime version for `{X.Y}` and the platform for `{PLATFORM}` in the command:</span></span>

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   <span data-ttu-id="d6568-199">Příkaz vrátí `True` , když je nainstalován modul runtime x64 Preview.</span><span class="sxs-lookup"><span data-stu-id="d6568-199">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="d6568-200">Architektura platformy (x86/x64) aplikace App Services je nastavená v nastavení aplikace na webu Azure Portal pro aplikace, které jsou hostované na výpočetní úrovni řady A-Series nebo lepší úrovně hostování.</span><span class="sxs-lookup"><span data-stu-id="d6568-200">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute or better hosting tier.</span></span> <span data-ttu-id="d6568-201">Pokud je aplikace spuštěná v režimu v procesu a architektura platformy je nakonfigurovaná pro 64-bit (x64), modul ASP.NET Core používá 64 runtime Preview, pokud je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="d6568-201">If the app is run in in-process mode and the platform architecture is configured for 64-bit (x64), the ASP.NET Core Module uses the 64-bit preview runtime, if present.</span></span> <span data-ttu-id="d6568-202">Nainstalujte rozšíření **Runtime ASP.NET Core {X. Y} (x64)** .</span><span class="sxs-lookup"><span data-stu-id="d6568-202">Install the **ASP.NET Core {X.Y} (x64) Runtime** extension.</span></span>
>
> <span data-ttu-id="d6568-203">Po instalaci prostředí runtime x64 Preview spusťte následující příkaz v příkazovém okně prostředí Kudu PowerShell a ověřte instalaci.</span><span class="sxs-lookup"><span data-stu-id="d6568-203">After installing the x64 preview runtime, run the following command in the Kudu PowerShell command window to verify the installation.</span></span> <span data-ttu-id="d6568-204">V příkazu nahraďte verzi ASP.NET Core `{X.Y}` runtime pro:</span><span class="sxs-lookup"><span data-stu-id="d6568-204">Substitute the ASP.NET Core runtime version for `{X.Y}` in the command:</span></span>
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> <span data-ttu-id="d6568-205">Příkaz vrátí `True` , když je nainstalován modul runtime x64 Preview.</span><span class="sxs-lookup"><span data-stu-id="d6568-205">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="d6568-206">**Rozšíření ASP.NET Core** umožňují pro ASP.NET Core v Azure App Services další funkce, jako je například povolení protokolování Azure.</span><span class="sxs-lookup"><span data-stu-id="d6568-206">**ASP.NET Core Extensions** enables additional functionality for ASP.NET Core on Azure App Services, such as enabling Azure logging.</span></span> <span data-ttu-id="d6568-207">Rozšíření se nainstaluje automaticky při nasazení ze sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d6568-207">The extension is installed automatically when deploying from Visual Studio.</span></span> <span data-ttu-id="d6568-208">Pokud rozšíření není nainstalované, nainstalujte ho pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d6568-208">If the extension isn't installed, install it for the app.</span></span>

<span data-ttu-id="d6568-209">**Použití rozšíření webu ve verzi Preview se šablonou ARM**</span><span class="sxs-lookup"><span data-stu-id="d6568-209">**Use the preview site extension with an ARM template**</span></span>

<span data-ttu-id="d6568-210">Pokud se k vytváření a nasazování aplikací používá šablona ARM, `siteextensions` typ prostředku se dá použít k přidání rozšíření webu do webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="d6568-210">If an ARM template is used to create and deploy apps, the `siteextensions` resource type can be used to add the site extension to a web app.</span></span> <span data-ttu-id="d6568-211">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d6568-211">For example:</span></span>

[!code-json[](index/sample/arm.json?highlight=2)]

### <a name="deploy-a-self-contained-preview-app"></a><span data-ttu-id="d6568-212">Nasazení samostatné aplikace ve verzi Preview</span><span class="sxs-lookup"><span data-stu-id="d6568-212">Deploy a self-contained preview app</span></span>

<span data-ttu-id="d6568-213">[Samostatné nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) , které cílí na modul runtime verze Preview, přenáší modul runtime verze Preview do nasazení.</span><span class="sxs-lookup"><span data-stu-id="d6568-213">A [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) that targets a preview runtime carries the preview runtime in the deployment.</span></span>

<span data-ttu-id="d6568-214">Při nasazení samostatně obsažené aplikace:</span><span class="sxs-lookup"><span data-stu-id="d6568-214">When deploying a self-contained app:</span></span>

* <span data-ttu-id="d6568-215">Lokalita v Azure App Service nevyžaduje [rozšíření webu verze Preview](#install-the-preview-site-extension).</span><span class="sxs-lookup"><span data-stu-id="d6568-215">The site in Azure App Service doesn't require the [preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="d6568-216">Aplikace musí být publikována při jiném přístupu než při publikování pro [nasazení závislé na rozhraní (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="d6568-216">The app must be published following a different approach than when publishing for a [framework-dependent deployment (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="d6568-217">Postupujte podle pokynů v části [nasazení samostatně obsaženého oddílu aplikace](#deploy-the-app-self-contained) .</span><span class="sxs-lookup"><span data-stu-id="d6568-217">Follow the guidance in the [Deploy the app self-contained](#deploy-the-app-self-contained) section.</span></span>

### <a name="use-docker-with-web-apps-for-containers"></a><span data-ttu-id="d6568-218">Použití Docker s Web Apps pro kontejnery</span><span class="sxs-lookup"><span data-stu-id="d6568-218">Use Docker with Web Apps for containers</span></span>

<span data-ttu-id="d6568-219">[Dokovací centrum](https://hub.docker.com/r/microsoft/aspnetcore/) obsahuje nejnovější image Docker Preview.</span><span class="sxs-lookup"><span data-stu-id="d6568-219">The [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contains the latest preview Docker images.</span></span> <span data-ttu-id="d6568-220">Obrázky lze použít jako základní bitovou kopii.</span><span class="sxs-lookup"><span data-stu-id="d6568-220">The images can be used as a base image.</span></span> <span data-ttu-id="d6568-221">Použijte image a nasaďte ji normálně pro Web Apps kontejnerů.</span><span class="sxs-lookup"><span data-stu-id="d6568-221">Use the image and deploy to Web Apps for Containers normally.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="d6568-222">Publikování a nasazení aplikace</span><span class="sxs-lookup"><span data-stu-id="d6568-222">Publish and deploy the app</span></span>

### <a name="deploy-the-app-framework-dependent"></a><span data-ttu-id="d6568-223">Nasazení závislého rozhraní App Framework</span><span class="sxs-lookup"><span data-stu-id="d6568-223">Deploy the app framework-dependent</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="d6568-224">Pro 64 [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="d6568-224">For a 64-bit [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

* <span data-ttu-id="d6568-225">K vytvoření 64 aplikace použijte 64 .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="d6568-225">Use a 64-bit .NET Core SDK to build a 64-bit app.</span></span>
* <span data-ttu-id="d6568-226">V**obecných nastaveních** **Konfigurace** > App Service nastavte **platformou** na **64 bit** .</span><span class="sxs-lookup"><span data-stu-id="d6568-226">Set the **Platform** to **64 Bit** in the App Service's **Configuration** > **General settings**.</span></span> <span data-ttu-id="d6568-227">Aby bylo možné vybrat bitová verze platformy, musí aplikace používat základní nebo vyšší plán služby.</span><span class="sxs-lookup"><span data-stu-id="d6568-227">The app must use a Basic or higher service plan to enable the choice of platform bitness.</span></span>

::: moniker-end

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d6568-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d6568-228">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d6568-229">Vyberte **sestavení** > **Publikovat {název aplikace}** z panelu nástrojů sady Visual Studio nebo klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="d6568-229">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="d6568-230">V dialogovém okně **vybrat cíl publikování** potvrďte, že je vybrána možnost **App Service** .</span><span class="sxs-lookup"><span data-stu-id="d6568-230">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="d6568-231">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="d6568-231">Select **Advanced**.</span></span> <span data-ttu-id="d6568-232">Otevře se dialogové okno **publikovat** .</span><span class="sxs-lookup"><span data-stu-id="d6568-232">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="d6568-233">V **publikovat** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="d6568-233">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="d6568-234">Potvrďte, že je vybraná konfigurace **vydané verze** .</span><span class="sxs-lookup"><span data-stu-id="d6568-234">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="d6568-235">Otevřete rozevírací seznam **režim nasazení** a vyberte možnost závislé na **rozhraní**.</span><span class="sxs-lookup"><span data-stu-id="d6568-235">Open the **Deployment Mode** drop-down list and select **Framework-Dependent**.</span></span>
   * <span data-ttu-id="d6568-236">Jako **cílový modul runtime**vyberte **přenosný** .</span><span class="sxs-lookup"><span data-stu-id="d6568-236">Select **Portable** as the **Target Runtime**.</span></span>
   * <span data-ttu-id="d6568-237">Pokud potřebujete po nasazení odebrat další soubory, otevřete **Možnosti publikování souborů** a zaškrtnutím políčka odeberte další soubory v cílovém umístění.</span><span class="sxs-lookup"><span data-stu-id="d6568-237">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="d6568-238">Vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="d6568-238">Select **Save**.</span></span>
1. <span data-ttu-id="d6568-239">Pomocí zbývajících výzev Průvodce publikováním vytvořte novou lokalitu nebo aktualizujte existující web.</span><span class="sxs-lookup"><span data-stu-id="d6568-239">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d6568-240">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="d6568-240">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="d6568-241">V souboru projektu nezadávejte [identifikátor modulu runtime (RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="d6568-241">In the project file, don't specify a [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

1. <span data-ttu-id="d6568-242">V příkazovém prostředí publikujte aplikaci v konfiguraci vydaných verzí pomocí příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="d6568-242">From a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="d6568-243">V následujícím příkladu je aplikace publikována jako aplikace závislá na rozhraní:</span><span class="sxs-lookup"><span data-stu-id="d6568-243">In the following example, the app is published as a framework-dependent app:</span></span>

   ```console
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="d6568-244">Přesuňte obsah adresáře *bin/Release/{Target Framework}/Publish* do lokality v App Service.</span><span class="sxs-lookup"><span data-stu-id="d6568-244">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="d6568-245">Pokud přetáhnete obsah složky pro *publikování* z místního pevného disku nebo sdílené síťové složky přímo do App Service v konzole [Kudu](https://github.com/projectkudu/kudu/wiki) , přetáhněte `D:\home\site\wwwroot` soubory do složky v konzole Kudu.</span><span class="sxs-lookup"><span data-stu-id="d6568-245">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the [Kudu](https://github.com/projectkudu/kudu/wiki) console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

### <a name="deploy-the-app-self-contained"></a><span data-ttu-id="d6568-246">Nasazení samostatně obsažené aplikace</span><span class="sxs-lookup"><span data-stu-id="d6568-246">Deploy the app self-contained</span></span>

<span data-ttu-id="d6568-247">Použijte nástroje sady Visual Studio nebo rozhraní příkazového řádku (CLI) pro [samostatně uzavřené nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="d6568-247">Use Visual Studio or the command-line interface (CLI) tools for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d6568-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d6568-248">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d6568-249">Vyberte **sestavení** > **Publikovat {název aplikace}** z panelu nástrojů sady Visual Studio nebo klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="d6568-249">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="d6568-250">V dialogovém okně **vybrat cíl publikování** potvrďte, že je vybrána možnost **App Service** .</span><span class="sxs-lookup"><span data-stu-id="d6568-250">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="d6568-251">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="d6568-251">Select **Advanced**.</span></span> <span data-ttu-id="d6568-252">Otevře se dialogové okno **publikovat** .</span><span class="sxs-lookup"><span data-stu-id="d6568-252">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="d6568-253">V **publikovat** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="d6568-253">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="d6568-254">Potvrďte, že je vybraná konfigurace **vydané verze** .</span><span class="sxs-lookup"><span data-stu-id="d6568-254">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="d6568-255">Otevřete rozevírací seznam **režim nasazení** a vyberte možnost **samostatně obsaženo**.</span><span class="sxs-lookup"><span data-stu-id="d6568-255">Open the **Deployment Mode** drop-down list and select **Self-Contained**.</span></span>
   * <span data-ttu-id="d6568-256">Z rozevíracího seznamu **cílový modul runtime** vyberte cílový modul runtime.</span><span class="sxs-lookup"><span data-stu-id="d6568-256">Select the target runtime from the **Target Runtime** drop-down list.</span></span> <span data-ttu-id="d6568-257">Výchozí hodnota je `win-x86`.</span><span class="sxs-lookup"><span data-stu-id="d6568-257">The default is `win-x86`.</span></span>
   * <span data-ttu-id="d6568-258">Pokud potřebujete po nasazení odebrat další soubory, otevřete **Možnosti publikování souborů** a zaškrtnutím políčka odeberte další soubory v cílovém umístění.</span><span class="sxs-lookup"><span data-stu-id="d6568-258">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="d6568-259">Vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="d6568-259">Select **Save**.</span></span>
1. <span data-ttu-id="d6568-260">Pomocí zbývajících výzev Průvodce publikováním vytvořte novou lokalitu nebo aktualizujte existující web.</span><span class="sxs-lookup"><span data-stu-id="d6568-260">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d6568-261">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="d6568-261">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="d6568-262">V souboru projektu zadejte jeden nebo více identifikátorů [modulu runtime (identifikátorů RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="d6568-262">In the project file, specify one or more [Runtime Identifiers (RIDs)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="d6568-263">Pro `<RuntimeIdentifier>` jeden identifikátor RID použijte (jednotné číslo), nebo `<RuntimeIdentifiers>` použijte (plural) k poskytnutí seznamu identifikátorů ridů oddělených středníkem.</span><span class="sxs-lookup"><span data-stu-id="d6568-263">Use `<RuntimeIdentifier>` (singular) for a single RID, or use `<RuntimeIdentifiers>` (plural) to provide a semicolon-delimited list of RIDs.</span></span> <span data-ttu-id="d6568-264">V následujícím příkladu `win-x86` je zadáno RID:</span><span class="sxs-lookup"><span data-stu-id="d6568-264">In the following example, the `win-x86` RID is specified:</span></span>

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. <span data-ttu-id="d6568-265">Z příkazového prostředí publikujte aplikaci v konfiguraci vydání pro modul runtime hostitele pomocí příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="d6568-265">From a command shell, publish the app in Release configuration for the host's runtime with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="d6568-266">V následujícím příkladu je aplikace publikována pro `win-x86` identifikátor RID.</span><span class="sxs-lookup"><span data-stu-id="d6568-266">In the following example, the app is published for the `win-x86` RID.</span></span> <span data-ttu-id="d6568-267">Identifikátor RID zadaný do `--runtime` možnosti musí být uveden `<RuntimeIdentifier>` ve vlastnosti (nebo `<RuntimeIdentifiers>`) v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="d6568-267">The RID supplied to the `--runtime` option must be provided in the `<RuntimeIdentifier>` (or `<RuntimeIdentifiers>`) property in the project file.</span></span>

   ```console
   dotnet publish --configuration Release --runtime win-x86
   ```

1. <span data-ttu-id="d6568-268">Přesuňte obsah složky *bin/Release/{Target Framework}/{runtime identifikátor}/Publish* do lokality v App Service.</span><span class="sxs-lookup"><span data-stu-id="d6568-268">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="d6568-269">Pokud přetáhnete obsah složky pro *publikování* z místního pevného disku nebo sdílené síťové složky přímo do App Service v konzole Kudu, přetáhněte soubory do `D:\home\site\wwwroot` složky v konzole Kudu.</span><span class="sxs-lookup"><span data-stu-id="d6568-269">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the Kudu console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

## <a name="protocol-settings-https"></a><span data-ttu-id="d6568-270">Nastavení protokolu (HTTPS)</span><span class="sxs-lookup"><span data-stu-id="d6568-270">Protocol settings (HTTPS)</span></span>

<span data-ttu-id="d6568-271">Vazby zabezpečeného protokolu umožňují zadat certifikát, který se má použít při reagování na požadavky přes protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d6568-271">Secure protocol bindings allow you specify a certificate to use when responding to requests over HTTPS.</span></span> <span data-ttu-id="d6568-272">Vazba vyžaduje platný privátní certifikát ( *. pfx*), který byl vydán pro konkrétní název hostitele.</span><span class="sxs-lookup"><span data-stu-id="d6568-272">Binding requires a valid private certificate (*.pfx*) issued for the specific hostname.</span></span> <span data-ttu-id="d6568-273">Další informace najdete v tématu [kurz: Navažte existující vlastní certifikát SSL na Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="d6568-273">For more information, see [Tutorial: Bind an existing custom SSL certificate to Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="d6568-274">Transformace souboru web.config</span><span class="sxs-lookup"><span data-stu-id="d6568-274">Transform web.config</span></span>

<span data-ttu-id="d6568-275">Pokud potřebujete transformovat *Web. config* při publikování (například nastavit proměnné prostředí na základě konfigurace, profilu nebo prostředí), přečtěte si téma <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="d6568-275">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d6568-276">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d6568-276">Additional resources</span></span>

* [<span data-ttu-id="d6568-277">Přehled App Service</span><span class="sxs-lookup"><span data-stu-id="d6568-277">App Service overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="d6568-278">Azure App Service: Nejlepší místo pro hostování aplikací .NET (video s přehledem 55-minut)</span><span class="sxs-lookup"><span data-stu-id="d6568-278">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span></span>](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [<span data-ttu-id="d6568-279">Azure pátek: Prostředí pro řešení potíží s diagnostikou a Azure App Service (video s 12 minutami)</span><span class="sxs-lookup"><span data-stu-id="d6568-279">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [<span data-ttu-id="d6568-280">Přehled diagnostiky Azure App Service</span><span class="sxs-lookup"><span data-stu-id="d6568-280">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

<span data-ttu-id="d6568-281">Azure App Service na Windows serveru používá [Internetová informační služba (IIS)](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="d6568-281">Azure App Service on Windows Server uses [Internet Information Services (IIS)](https://www.iis.net/).</span></span> <span data-ttu-id="d6568-282">Následující témata se týkají základní technologie IIS:</span><span class="sxs-lookup"><span data-stu-id="d6568-282">The following topics pertain to the underlying IIS technology:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [<span data-ttu-id="d6568-283">Windows Server – obsah správce IT pro aktuální a předchozí verze</span><span class="sxs-lookup"><span data-stu-id="d6568-283">Windows Server - IT administrator content for current and previous releases</span></span>](/windows-server/windows-server-versions)
