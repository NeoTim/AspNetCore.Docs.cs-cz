---
title: Nasazení aplikace ASP.NET Core do Azure App Service
author: guardrex
description: Tento článek obsahuje odkazy na hostiteli Azure a nasazení prostředků.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: c55a5202643bb947b3f38f67aec55ee5cf7b1496
ms.sourcegitcommit: c43a6f1fe72d7c2db4b5815fd532f2b45d964e07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244746"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a><span data-ttu-id="7aca1-103">Nasazení aplikace ASP.NET Core do Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7aca1-103">Deploy ASP.NET Core apps to Azure App Service</span></span>

<span data-ttu-id="7aca1-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) je [Microsoft cloud computingu platformová služba](https://azure.microsoft.com/) pro hostování webových aplikací, včetně ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7aca1-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span>

## <a name="useful-resources"></a><span data-ttu-id="7aca1-105">Užitečné zdroje</span><span class="sxs-lookup"><span data-stu-id="7aca1-105">Useful resources</span></span>

<span data-ttu-id="7aca1-106">Azure [dokumentace k Web Apps](/azure/app-service/) je domovská stránka pro aplikace Azure dokumentace, kurzy, ukázky, návody a další prostředky.</span><span class="sxs-lookup"><span data-stu-id="7aca1-106">The Azure [Web Apps Documentation](/azure/app-service/) is the home for Azure Apps documentation, tutorials, samples, how-to guides, and other resources.</span></span> <span data-ttu-id="7aca1-107">Jsou dva důležité kurzy, které se vztahují k hostování aplikací ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="7aca1-107">Two notable tutorials that pertain to hosting ASP.NET Core apps are:</span></span>

[<span data-ttu-id="7aca1-108">Rychlý start: Vytvoření webové aplikace ASP.NET Core v Azure</span><span class="sxs-lookup"><span data-stu-id="7aca1-108">Quickstart: Create an ASP.NET Core web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)  
<span data-ttu-id="7aca1-109">Vytvoření a nasazení webové aplikace ASP.NET Core do služby Azure App Service ve Windows pomocí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7aca1-109">Use Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service on Windows.</span></span>

[<span data-ttu-id="7aca1-110">Rychlý start: Vytvoření webové aplikace .NET Core ve službě App Service v Linuxu</span><span class="sxs-lookup"><span data-stu-id="7aca1-110">Quickstart: Create a .NET Core web app in App Service on Linux</span></span>](/azure/app-service/containers/quickstart-dotnetcore)  
<span data-ttu-id="7aca1-111">Vytvoření a nasazení webové aplikace ASP.NET Core do služby Azure App Service v Linuxu pomocí příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="7aca1-111">Use the command line to create and deploy an ASP.NET Core web app to Azure App Service on Linux.</span></span>

<span data-ttu-id="7aca1-112">Jsou k dispozici v dokumentaci k ASP.NET Core v následujících článcích:</span><span class="sxs-lookup"><span data-stu-id="7aca1-112">The following articles are available in ASP.NET Core documentation:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="7aca1-113">Zjistěte, jak publikovat aplikace ASP.NET Core do služby Azure App Service pomocí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7aca1-113">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
<span data-ttu-id="7aca1-114">Zjistěte, jak vytvořit webovou aplikaci ASP.NET Core pomocí sady Visual Studio a nasaďte ji do služby Azure App Service pro průběžné nasazování pomocí Gitu.</span><span class="sxs-lookup"><span data-stu-id="7aca1-114">Learn how to create an ASP.NET Core web app using Visual Studio and deploy it to Azure App Service using Git for continuous deployment.</span></span>

[<span data-ttu-id="7aca1-115">Vytvořit svůj první kanál s kanály Azure</span><span class="sxs-lookup"><span data-stu-id="7aca1-115">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)  
<span data-ttu-id="7aca1-116">Nastavení sestavení CI pro aplikace ASP.NET Core a potom vytvořte vydání průběžné nasazování do služby Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="7aca1-116">Set up a CI build for an ASP.NET Core app, then create a continuous deployment release to Azure App Service.</span></span>

[<span data-ttu-id="7aca1-117">Azure sandboxu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="7aca1-117">Azure Web App sandbox</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
<span data-ttu-id="7aca1-118">Objevte omezení spuštění modulu runtime služby Azure App Service vynucovaných příslušnou platformou aplikace Azure.</span><span class="sxs-lookup"><span data-stu-id="7aca1-118">Discover Azure App Service runtime execution limitations enforced by the Azure Apps platform.</span></span>

::: moniker range=">= aspnetcore-2.0"

## <a name="application-configuration"></a><span data-ttu-id="7aca1-119">Konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="7aca1-119">Application configuration</span></span>

<span data-ttu-id="7aca1-120">Následující balíčky NuGet poskytují funkce automatického protokolování pro aplikace nasazené do služby Azure App Service:</span><span class="sxs-lookup"><span data-stu-id="7aca1-120">The following NuGet packages provide automatic logging features for apps deployed to Azure App Service:</span></span>

* <span data-ttu-id="7aca1-121">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) používá [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) k zajištění integrace světla nahoru ASP.NET Core pomocí služby Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="7aca1-121">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) uses [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) to provide ASP.NET Core light-up integration with Azure App Service.</span></span> <span data-ttu-id="7aca1-122">Poskytované funkce přidání protokolování `Microsoft.AspNetCore.AzureAppServicesIntegration` balíčku.</span><span class="sxs-lookup"><span data-stu-id="7aca1-122">The added logging features are provided by the `Microsoft.AspNetCore.AzureAppServicesIntegration` package.</span></span>
* <span data-ttu-id="7aca1-123">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) spustí [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) Přidání poskytovatelů protokolování diagnostiky služby Azure App Service v `Microsoft.Extensions.Logging.AzureAppServices` balíčku.</span><span class="sxs-lookup"><span data-stu-id="7aca1-123">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executes [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) to add Azure App Service diagnostics logging providers in the `Microsoft.Extensions.Logging.AzureAppServices` package.</span></span>
* <span data-ttu-id="7aca1-124">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) poskytuje implementace protokolovací nástroj pro podporu funkcí streamování protokolů a protokoly diagnostiky Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="7aca1-124">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) provides logger implementations to support Azure App Service diagnostics logs and log streaming features.</span></span>

<span data-ttu-id="7aca1-125">Pokud cílí na .NET Core a odkazování [metabalíček Microsoft.aspnetcore.all](xref:fundamentals/metapackage), předchozí balíčky jsou zahrnuty.</span><span class="sxs-lookup"><span data-stu-id="7aca1-125">If targeting .NET Core and referencing the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), the preceding packages are included.</span></span> <span data-ttu-id="7aca1-126">Chybí balíčky z [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="7aca1-126">The packages are absent from the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="7aca1-127">Pokud cílí na rozhraní .NET Framework nebo odkazující `Microsoft.AspNetCore.App` Microsoft.aspnetcore.all, odkazují na balíčky jednotlivé protokolování.</span><span class="sxs-lookup"><span data-stu-id="7aca1-127">If targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, reference the individual logging packages.</span></span>

::: moniker-end

## <a name="override-app-configuration-using-the-azure-portal"></a><span data-ttu-id="7aca1-128">Přepsat konfiguraci aplikace pomocí webu Azure Portal</span><span class="sxs-lookup"><span data-stu-id="7aca1-128">Override app configuration using the Azure Portal</span></span>

<span data-ttu-id="7aca1-129">**Nastavení aplikace** oblasti **nastavení aplikace** okno umožňuje nastavit proměnné prostředí pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7aca1-129">The **App Settings** area of the **Application settings** blade permits you to set environment variables for the app.</span></span> <span data-ttu-id="7aca1-130">Mohou být spotřebovány proměnné prostředí [poskytovatele konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="7aca1-130">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="7aca1-131">Při vytvoření nebo úpravě na webu Azure Portal nastavení aplikace a **Uložit** se vybere tlačítko, restartování aplikace Azure.</span><span class="sxs-lookup"><span data-stu-id="7aca1-131">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="7aca1-132">Proměnná prostředí je k dispozici pro aplikace, po restartování služby.</span><span class="sxs-lookup"><span data-stu-id="7aca1-132">The environment variable is available to the app after the service restarts.</span></span>

<span data-ttu-id="7aca1-133">Pokud aplikace používá [webového hostitele](xref:fundamentals/host/web-host) a sestavení hostitele pomocí [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), použít proměnné prostředí, které konfigurace hostitele `ASPNETCORE_` předponu.</span><span class="sxs-lookup"><span data-stu-id="7aca1-133">When an app uses the [Web Host](xref:fundamentals/host/web-host) and builds the host using [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), environment variables that configure the host use the `ASPNETCORE_` prefix.</span></span> <span data-ttu-id="7aca1-134">Další informace najdete v tématu <xref:fundamentals/host/web-host> a [poskytovatele konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="7aca1-134">For more information, see <xref:fundamentals/host/web-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="7aca1-135">Pokud aplikace používá [obecný hostitele](xref:fundamentals/host/generic-host), proměnné prostředí nejsou načtené do konfigurace vaší aplikace ve výchozím nastavení a poskytovatel konfigurace je třeba přidat pomocí vývojáře.</span><span class="sxs-lookup"><span data-stu-id="7aca1-135">When an app uses the [Generic Host](xref:fundamentals/host/generic-host), environment variables aren't loaded into an app's configuration by default and the configuration provider must be added by the developer.</span></span> <span data-ttu-id="7aca1-136">Vývojář Určuje předponu proměnné prostředí po přidání zprostředkovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="7aca1-136">The developer determines the environment variable prefix when the configuration provider is added.</span></span> <span data-ttu-id="7aca1-137">Další informace najdete v tématu <xref:fundamentals/host/generic-host> a [poskytovatele konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="7aca1-137">For more information, see <xref:fundamentals/host/generic-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="7aca1-138">Proxy server a scénáře pro nástroj pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="7aca1-138">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="7aca1-139">Middleware pro integraci služby IIS, který nakonfiguruje předané Middleware záhlaví a že modul ASP.NET Core jsou konfigurovány pro předávání schématu (HTTP/HTTPS) a Vzdálená IP adresa původu žádosti.</span><span class="sxs-lookup"><span data-stu-id="7aca1-139">The IIS Integration Middleware, which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="7aca1-140">Další konfigurace může být nezbytný pro aplikací hostovaných za službou další proxy servery a nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="7aca1-140">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="7aca1-141">Další informace najdete v tématu [konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="7aca1-141">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="monitoring-and-logging"></a><span data-ttu-id="7aca1-142">Monitorování a protokolování</span><span class="sxs-lookup"><span data-stu-id="7aca1-142">Monitoring and logging</span></span>

<span data-ttu-id="7aca1-143">Sledování, protokolování a informace o odstraňování potíží najdete v následujících článcích:</span><span class="sxs-lookup"><span data-stu-id="7aca1-143">For monitoring, logging, and troubleshooting information, see the following articles:</span></span>

[<span data-ttu-id="7aca1-144">Postupy: sledování aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7aca1-144">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)  
<span data-ttu-id="7aca1-145">Zjistěte, jak kontrolovat kvóty a metriky pro aplikace a plány služby App Service.</span><span class="sxs-lookup"><span data-stu-id="7aca1-145">Learn how to review quotas and metrics for apps and App Service plans.</span></span>

[<span data-ttu-id="7aca1-146">Povolení protokolování diagnostiky pro webové aplikace ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7aca1-146">Enable diagnostics logging for web apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)  
<span data-ttu-id="7aca1-147">Zjistěte, jak povolit a přístup k protokolování diagnostiky pro stavové kódy HTTP, neúspěšných požadavků a aktivity webového serveru.</span><span class="sxs-lookup"><span data-stu-id="7aca1-147">Discover how to enable and access diagnostic logging for HTTP status codes, failed requests, and web server activity.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="7aca1-148">Seznamte se s běžné přístupy k zpracování chyb v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7aca1-148">Understand common approaches to handling errors in ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-apps/troubleshoot>  
<span data-ttu-id="7aca1-149">Zjistěte, jak diagnostikovat problémy s nasazením služby Azure App Service s aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7aca1-149">Learn how to diagnose issues with Azure App Service deployments with ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-iis-errors-reference>  
<span data-ttu-id="7aca1-150">V tématu běžné chyby konfigurace nasazení pro aplikace hostované pomocí Azure App Service/IIS s Poradce při potížích.</span><span class="sxs-lookup"><span data-stu-id="7aca1-150">See the common deployment configuration errors for apps hosted by Azure App Service/IIS with troubleshooting advice.</span></span>

## <a name="data-protection-key-ring-and-deployment-slots"></a><span data-ttu-id="7aca1-151">Aktualizační kanál klíč ochrany dat a sloty nasazení</span><span class="sxs-lookup"><span data-stu-id="7aca1-151">Data Protection key ring and deployment slots</span></span>

<span data-ttu-id="7aca1-152">[Klíče ochrany dat](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) jsou zachované *%HOME%\ASP.NET\DataProtection-Keys* složky.</span><span class="sxs-lookup"><span data-stu-id="7aca1-152">[Data Protection keys](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="7aca1-153">Tato složka je zajištěná síťového úložiště a synchronizují ve všech počítačích, který je hostitelem aplikace.</span><span class="sxs-lookup"><span data-stu-id="7aca1-153">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span> <span data-ttu-id="7aca1-154">Klíče nejsou chráněné v klidovém stavu.</span><span class="sxs-lookup"><span data-stu-id="7aca1-154">Keys aren't protected at rest.</span></span> <span data-ttu-id="7aca1-155">Tato složka poskytuje kanál klíč na všechny instance aplikace ve slotu jedno nasazení.</span><span class="sxs-lookup"><span data-stu-id="7aca1-155">This folder supplies the key ring to all instances of an app in a single deployment slot.</span></span> <span data-ttu-id="7aca1-156">Samostatné nasazovacích slotů, jako je například přípravným a produkčním prostředím, Nesdílejte klíč kanál.</span><span class="sxs-lookup"><span data-stu-id="7aca1-156">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span>

<span data-ttu-id="7aca1-157">Po záměně mezi sloty nasazení, jakéhokoli systému pomocí ochrany dat nebude možné dešifrovat uloženými daty pracujete pomocí aktualizační kanál, který klíč uvnitř předchozí slot.</span><span class="sxs-lookup"><span data-stu-id="7aca1-157">When swapping between deployment slots, any system using data protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="7aca1-158">Middlewaru souboru Cookie. technologie ASP.NET používá ochranu dat k ochraně souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="7aca1-158">ASP.NET Cookie Middleware uses data protection to protect its cookies.</span></span> <span data-ttu-id="7aca1-159">To vede k uživatelům se odhlásili z aplikace, která používá standardní middlewaru souboru Cookie. technologie ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="7aca1-159">This leads to users being signed out of an app that uses the standard ASP.NET Cookie Middleware.</span></span> <span data-ttu-id="7aca1-160">Pro kanál klíč slotu nezávislé na řešení využívají, jako poskytovatele vnější prstenec klíč:</span><span class="sxs-lookup"><span data-stu-id="7aca1-160">For a slot-independent key ring solution, use an external key ring provider, such as:</span></span>

* <span data-ttu-id="7aca1-161">Úložiště objektů Blob v Azure</span><span class="sxs-lookup"><span data-stu-id="7aca1-161">Azure Blob Storage</span></span>
* <span data-ttu-id="7aca1-162">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="7aca1-162">Azure Key Vault</span></span>
* <span data-ttu-id="7aca1-163">Úložiště SQL</span><span class="sxs-lookup"><span data-stu-id="7aca1-163">SQL store</span></span>
* <span data-ttu-id="7aca1-164">Redis cache</span><span class="sxs-lookup"><span data-stu-id="7aca1-164">Redis cache</span></span>

<span data-ttu-id="7aca1-165">Další informace naleznete v tématu <xref:security/data-protection/implementation/key-storage-providers>.</span><span class="sxs-lookup"><span data-stu-id="7aca1-165">For more information, see <xref:security/data-protection/implementation/key-storage-providers>.</span></span>

## <a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a><span data-ttu-id="7aca1-166">Nasazení ve verzi preview ASP.NET Core do služby Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7aca1-166">Deploy ASP.NET Core preview release to Azure App Service</span></span>

<span data-ttu-id="7aca1-167">Použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="7aca1-167">Use one of the following approaches:</span></span>

* <span data-ttu-id="7aca1-168">[Nainstalujte rozšíření náhledu webu](#install-the-preview-site-extension).</span><span class="sxs-lookup"><span data-stu-id="7aca1-168">[Install the preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="7aca1-169">[Nasazení samostatné aplikace](#deploy-the-app-self-contained).</span><span class="sxs-lookup"><span data-stu-id="7aca1-169">[Deploy the app self-contained](#deploy-the-app-self-contained).</span></span>
* <span data-ttu-id="7aca1-170">[Pomocí webové aplikace Docker pro kontejnery](#use-docker-with-web-apps-for-containers).</span><span class="sxs-lookup"><span data-stu-id="7aca1-170">[Use Docker with Web Apps for containers](#use-docker-with-web-apps-for-containers).</span></span>

### <a name="install-the-preview-site-extension"></a><span data-ttu-id="7aca1-171">Instalace rozšíření webu ve verzi preview</span><span class="sxs-lookup"><span data-stu-id="7aca1-171">Install the preview site extension</span></span>

<span data-ttu-id="7aca1-172">Pokud dojde k potížím s pomocí rozšíření webu ve verzi preview, otevřete problém na [Githubu](https://github.com/aspnet/azureintegration/issues/new).</span><span class="sxs-lookup"><span data-stu-id="7aca1-172">If a problem occurs using the preview site extension, open an issue on [GitHub](https://github.com/aspnet/azureintegration/issues/new).</span></span>

1. <span data-ttu-id="7aca1-173">Na webu Azure Portal přejděte do okna služby App Service.</span><span class="sxs-lookup"><span data-stu-id="7aca1-173">From the Azure Portal, navigate to the App Service blade.</span></span>
1. <span data-ttu-id="7aca1-174">Vyberte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7aca1-174">Select the web app.</span></span>
1. <span data-ttu-id="7aca1-175">Zadejte "ex" vyhledávacího pole zadejte nebo přejděte dolů v seznamu management oddíly pro **nástroje pro vývoj**.</span><span class="sxs-lookup"><span data-stu-id="7aca1-175">Type "ex" in the search box or scroll down the list of management sections to **DEVELOPMENT TOOLS**.</span></span>
1. <span data-ttu-id="7aca1-176">Vyberte **nástroje pro vývoj** > **rozšíření**.</span><span class="sxs-lookup"><span data-stu-id="7aca1-176">Select **DEVELOPMENT TOOLS** > **Extensions**.</span></span>
1. <span data-ttu-id="7aca1-177">Vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="7aca1-177">Select **Add**.</span></span>
1. <span data-ttu-id="7aca1-178">Vyberte **ASP.NET Core &lt;x.y&gt; (x86) Runtime** rozšíření ze seznamu, kde `<x.y>` je verze preview ASP.NET Core (například **Runtime ASP.NET Core 2.2 (x86)**).</span><span class="sxs-lookup"><span data-stu-id="7aca1-178">Select the **ASP.NET Core &lt;x.y&gt; (x86) Runtime** extension from the list, where `<x.y>` is the ASP.NET Core preview version (for example, **ASP.NET Core 2.2 (x86) Runtime**).</span></span> <span data-ttu-id="7aca1-179">X86 modulu runtime je vhodný pro [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-deployments-fdd) , které využívají mimo proces hostování modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7aca1-179">The x86 runtime is appropriate for [framework-dependent deployments](/dotnet/core/deploying/#framework-dependent-deployments-fdd) that rely on out-of-process hosting by the ASP.NET Core Module.</span></span>
1. <span data-ttu-id="7aca1-180">Vyberte **OK** přijměte právní podmínky.</span><span class="sxs-lookup"><span data-stu-id="7aca1-180">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="7aca1-181">Vyberte **OK** nainstalovat rozšíření.</span><span class="sxs-lookup"><span data-stu-id="7aca1-181">Select **OK** to install the extension.</span></span>

<span data-ttu-id="7aca1-182">Po dokončení operace, je nainstalovaná nejnovější verze preview .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7aca1-182">When the operation completes, the latest .NET Core preview is installed.</span></span> <span data-ttu-id="7aca1-183">Ověření instalace:</span><span class="sxs-lookup"><span data-stu-id="7aca1-183">Verify the installation:</span></span>

1. <span data-ttu-id="7aca1-184">Vyberte **Rozšířené nástroje** pod **nástroje pro vývoj**.</span><span class="sxs-lookup"><span data-stu-id="7aca1-184">Select **Advanced Tools** under **DEVELOPMENT TOOLS**.</span></span>
1. <span data-ttu-id="7aca1-185">Vyberte **Přejít** na **Rozšířené nástroje** okno.</span><span class="sxs-lookup"><span data-stu-id="7aca1-185">Select **Go** on the **Advanced Tools** blade.</span></span>
1. <span data-ttu-id="7aca1-186">Vyberte **konzolou pro ladění** > **Powershellu** položky nabídky.</span><span class="sxs-lookup"><span data-stu-id="7aca1-186">Select the **Debug console** > **PowerShell** menu item.</span></span>
1. <span data-ttu-id="7aca1-187">Na příkazovém řádku Powershellu spusťte následující příkaz.</span><span class="sxs-lookup"><span data-stu-id="7aca1-187">At the PowerShell prompt, execute the following command.</span></span> <span data-ttu-id="7aca1-188">Nahraďte verze modulu runtime ASP.NET Core pro `<x.y>` v příkazu:</span><span class="sxs-lookup"><span data-stu-id="7aca1-188">Substitute the ASP.NET Core runtime version for `<x.y>` in the command:</span></span>

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.<x.y>.x86\
   ```
   <span data-ttu-id="7aca1-189">Pokud modul runtime nainstalovaný ve verzi preview je určený pro ASP.NET Core 2.2, příkaz je:</span><span class="sxs-lookup"><span data-stu-id="7aca1-189">If the installed preview runtime is for ASP.NET Core 2.2, the command is:</span></span>
   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.2.2.x86\
   ```
   <span data-ttu-id="7aca1-190">Příkaz vrátí `True` při x64 je nainstalován modul runtime ve verzi preview.</span><span class="sxs-lookup"><span data-stu-id="7aca1-190">The command returns `True` when the x64 preview runtime is installed.</span></span>

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="7aca1-191">Architektura platformy (x86/x64) aplikace služby App Services je nastavena v **nastavení aplikace** okně v části **obecné nastavení** pro aplikace, které jsou hostované na výpočetní prostředky řady A-series nebo lepší hostování vrstvy.</span><span class="sxs-lookup"><span data-stu-id="7aca1-191">The platform architecture (x86/x64) of an App Services app is set in the **Application Settings** blade under **General Settings** for apps that are hosted on an A-series compute or better hosting tier.</span></span> <span data-ttu-id="7aca1-192">Pokud aplikace běží v režimu v procesu a architektura platformy je nakonfigurovaný pro (x64) 64-bit, používá modul ASP.NET Core runtime 64bitová verze preview, pokud jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="7aca1-192">If the app is run in in-process mode and the platform architecture is configured for 64-bit (x64), the ASP.NET Core Module uses the 64-bit preview runtime, if present.</span></span> <span data-ttu-id="7aca1-193">Nainstalujte **ASP.NET Core &lt;x.y&gt; (x64) Runtime** rozšíření (například **modulu Runtime ASP.NET Core 2.2 (x64)**).</span><span class="sxs-lookup"><span data-stu-id="7aca1-193">Install the **ASP.NET Core &lt;x.y&gt; (x64) Runtime** extension (for example, **ASP.NET Core 2.2 (x64) Runtime**).</span></span>
>
> <span data-ttu-id="7aca1-194">Po instalaci x64 ve verzi preview modulu runtime, spusťte následující příkaz v příkazovém okně prostředí PowerShell Kudu k ověření instalace.</span><span class="sxs-lookup"><span data-stu-id="7aca1-194">After installing the x64 preview runtime, run the following command in the Kudu PowerShell command window to verify the installation.</span></span> <span data-ttu-id="7aca1-195">Nahraďte verze modulu runtime ASP.NET Core pro `<x.y>` v příkazu:</span><span class="sxs-lookup"><span data-stu-id="7aca1-195">Substitute the ASP.NET Core runtime version for `<x.y>` in the command:</span></span>
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.<x.y>.x64\
> ```
> <span data-ttu-id="7aca1-196">Pokud modul runtime nainstalovaný ve verzi preview je určený pro ASP.NET Core 2.2, příkaz je:</span><span class="sxs-lookup"><span data-stu-id="7aca1-196">If the installed preview runtime is for ASP.NET Core 2.2, the command is:</span></span>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.2.2.x64\
> ```
> <span data-ttu-id="7aca1-197">Příkaz vrátí `True` při x64 je nainstalován modul runtime ve verzi preview.</span><span class="sxs-lookup"><span data-stu-id="7aca1-197">The command returns `True` when the x64 preview runtime is installed.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="7aca1-198">**Rozšíření ASP.NET Core** povoluje další funkce pro ASP.NET Core v Azure App Services, jako například povolení protokolování v Azure.</span><span class="sxs-lookup"><span data-stu-id="7aca1-198">**ASP.NET Core Extensions** enables additional functionality for ASP.NET Core on Azure App Services, such as enabling Azure logging.</span></span> <span data-ttu-id="7aca1-199">Rozšíření se nainstaluje automaticky při nasazení ze sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7aca1-199">The extension is installed automatically when deploying from Visual Studio.</span></span> <span data-ttu-id="7aca1-200">Pokud rozšíření není nainstalovaný, nainstalujte ho pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7aca1-200">If the extension isn't installed, install it for the app.</span></span>

<span data-ttu-id="7aca1-201">**Použití rozšíření webu ve verzi preview pomocí šablony ARM**</span><span class="sxs-lookup"><span data-stu-id="7aca1-201">**Use the preview site extension with an ARM template**</span></span>

<span data-ttu-id="7aca1-202">Pokud šablonu ARM se používá k vytvoření a nasazení aplikace, `siteextensions` typ prostředku je možné přidat rozšíření webu do webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="7aca1-202">If an ARM template is used to create and deploy apps, the `siteextensions` resource type can be used to add the site extension to a web app.</span></span> <span data-ttu-id="7aca1-203">Příklad:</span><span class="sxs-lookup"><span data-stu-id="7aca1-203">For example:</span></span>

[!code-json[](index/sample/arm.json?highlight=2)]

### <a name="deploy-the-app-self-contained"></a><span data-ttu-id="7aca1-204">Nasazení samostatné aplikace</span><span class="sxs-lookup"><span data-stu-id="7aca1-204">Deploy the app self-contained</span></span>

<span data-ttu-id="7aca1-205">A [samostatné nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) , která se zaměřuje náhled runtime vyvolá modul runtime náhled v nasazení.</span><span class="sxs-lookup"><span data-stu-id="7aca1-205">A [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) that targets a preview runtime carries the preview runtime in the deployment.</span></span>

<span data-ttu-id="7aca1-206">Při nasazování samostatnou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="7aca1-206">When deploying a self-contained app:</span></span>

* <span data-ttu-id="7aca1-207">Na webu v Azure App Service nevyžaduje [náhled rozšíření webu](#install-the-preview-site-extension).</span><span class="sxs-lookup"><span data-stu-id="7aca1-207">The site in Azure App Service doesn't require the [preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="7aca1-208">Aplikace musí být zveřejněno po použití jiné metody než při publikování [framework závislé nasazení (chyba)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="7aca1-208">The app must be published following a different approach than when publishing for a [framework-dependent deployment (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span></span>

#### <a name="publish-from-visual-studio"></a><span data-ttu-id="7aca1-209">Publikování z aplikace Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7aca1-209">Publish from Visual Studio</span></span>

1. <span data-ttu-id="7aca1-210">Vyberte **sestavení** > **{název_aplikace} publikovat** z panelu nástrojů sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7aca1-210">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar.</span></span>
1. <span data-ttu-id="7aca1-211">V **vyberte cíl publikování** dialogového okna, ujistěte se, že **služby App Service** zaškrtnuto.</span><span class="sxs-lookup"><span data-stu-id="7aca1-211">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="7aca1-212">Vyberte **Advanced**.</span><span class="sxs-lookup"><span data-stu-id="7aca1-212">Select **Advanced**.</span></span> <span data-ttu-id="7aca1-213">**Publikovat** otevře se dialogové okno.</span><span class="sxs-lookup"><span data-stu-id="7aca1-213">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="7aca1-214">V **publikovat** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="7aca1-214">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="7aca1-215">Ujistěte se, že **vydání** vybrané konfigurace.</span><span class="sxs-lookup"><span data-stu-id="7aca1-215">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="7aca1-216">Otevřít **režim nasazení** rozevíracího seznamu a vyberte **samostatná**.</span><span class="sxs-lookup"><span data-stu-id="7aca1-216">Open the **Deployment Mode** drop-down list and select **Self-Contained**.</span></span>
   * <span data-ttu-id="7aca1-217">Vyberte cílový modul runtime z **cílový modul Runtime** rozevíracího seznamu.</span><span class="sxs-lookup"><span data-stu-id="7aca1-217">Select the target runtime from the **Target Runtime** drop-down list.</span></span> <span data-ttu-id="7aca1-218">Výchozí hodnota je `win-x86`.</span><span class="sxs-lookup"><span data-stu-id="7aca1-218">The default is `win-x86`.</span></span>
   * <span data-ttu-id="7aca1-219">Pokud je potřeba odebrat další soubory při nasazování, otevřete **možností publikování souboru** a zaškrtněte políčko, chcete-li odebrat další soubory v cílovém umístění.</span><span class="sxs-lookup"><span data-stu-id="7aca1-219">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="7aca1-220">Vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="7aca1-220">Select **Save**.</span></span>
1. <span data-ttu-id="7aca1-221">Vytvoření nového webu nebo aktualizovat existující lokalitu podle zbývajících výzev Průvodce publikováním.</span><span class="sxs-lookup"><span data-stu-id="7aca1-221">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

#### <a name="publish-using-command-line-interface-cli-tools"></a><span data-ttu-id="7aca1-222">Publikování pomocí nástrojů rozhraní příkazového řádku (CLI)</span><span class="sxs-lookup"><span data-stu-id="7aca1-222">Publish using command-line interface (CLI) tools</span></span>

1. <span data-ttu-id="7aca1-223">V souboru projektu, zadejte jeden nebo více [Runtime identifikátorů (RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="7aca1-223">In the project file, specify one or more [Runtime Identifiers (RIDs)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="7aca1-224">Použít `<RuntimeIdentifier>` (singulární) pro jeden identifikátorů RID, nebo použijte `<RuntimeIdentifiers>` zadejte středníkem oddělený seznam identifikátorů RID (Plurální).</span><span class="sxs-lookup"><span data-stu-id="7aca1-224">Use `<RuntimeIdentifier>` (singular) for a single RID, or use `<RuntimeIdentifiers>` (plural) to provide a semicolon-delimited list of RIDs.</span></span> <span data-ttu-id="7aca1-225">V následujícím příkladu `win-x86` zadaný identifikátorů RID:</span><span class="sxs-lookup"><span data-stu-id="7aca1-225">In the following example, the `win-x86` RID is specified:</span></span>

   ```xml
   <PropertyGroup>
     <TargetFramework>netcoreapp2.1</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```
1. <span data-ttu-id="7aca1-226">Z příkazového prostředí, publikujte aplikaci v rámci konfigurace verze modulu runtime hostitele s [dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkazu.</span><span class="sxs-lookup"><span data-stu-id="7aca1-226">From a command shell, publish the app in Release configuration for the host's runtime with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="7aca1-227">V následujícím příkladu je aplikace publikována pro `win-x86` identifikátorů RID.</span><span class="sxs-lookup"><span data-stu-id="7aca1-227">In the following example, the app is published for the `win-x86` RID.</span></span> <span data-ttu-id="7aca1-228">Identifikátor RID zadaný pro `--runtime` ve musí být Zadaná možnost `<RuntimeIdentifier>` (nebo `<RuntimeIdentifiers>`) vlastnost v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="7aca1-228">The RID supplied to the `--runtime` option must be provided in the `<RuntimeIdentifier>` (or `<RuntimeIdentifiers>`) property in the project file.</span></span>

   ```console
   dotnet publish --configuration Release --runtime win-x86
   ```
1. <span data-ttu-id="7aca1-229">Přesunout obsah *bin/Release / {TARGET FRAMEWORK} / {IDENTIFIKÁTOR modulu RUNTIME} / publish* adresář k webu ve službě App Service.</span><span class="sxs-lookup"><span data-stu-id="7aca1-229">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory to the site in App Service.</span></span>

### <a name="use-docker-with-web-apps-for-containers"></a><span data-ttu-id="7aca1-230">Použití Docker pro kontejnery s Web Apps</span><span class="sxs-lookup"><span data-stu-id="7aca1-230">Use Docker with Web Apps for containers</span></span>

<span data-ttu-id="7aca1-231">[Docker Hubu](https://hub.docker.com/r/microsoft/aspnetcore/) obsahuje nejnovější Image Dockeru ve verzi preview.</span><span class="sxs-lookup"><span data-stu-id="7aca1-231">The [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contains the latest preview Docker images.</span></span> <span data-ttu-id="7aca1-232">Image můžete použít jako základní image.</span><span class="sxs-lookup"><span data-stu-id="7aca1-232">The images can be used as a base image.</span></span> <span data-ttu-id="7aca1-233">Použít bitovou kopii a nasazení do Web Apps for Containers normálně.</span><span class="sxs-lookup"><span data-stu-id="7aca1-233">Use the image and deploy to Web Apps for Containers normally.</span></span>

## <a name="protocol-settings-https"></a><span data-ttu-id="7aca1-234">Nastavení protokolu (HTTPS)</span><span class="sxs-lookup"><span data-stu-id="7aca1-234">Protocol settings (HTTPS)</span></span>

<span data-ttu-id="7aca1-235">Zabezpečený protokol vazby umožňují že zadat certifikát má použít při reakci na požadavky přes protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7aca1-235">Secure protocol bindings allow you specify a certificate to use when responding to requests over HTTPS.</span></span> <span data-ttu-id="7aca1-236">Vazba vyžaduje platný privátní certifikát (*.pfx*) vydaný pro konkrétní název hostitele.</span><span class="sxs-lookup"><span data-stu-id="7aca1-236">Binding requires a valid private certificate (*.pfx*) issued for the specific hostname.</span></span> <span data-ttu-id="7aca1-237">Další informace najdete v tématu [kurz: vytvoření vazby existujícího vlastního certifikátu SSL k Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="7aca1-237">For more information, see [Tutorial: Bind an existing custom SSL certificate to Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7aca1-238">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="7aca1-238">Additional resources</span></span>

* [<span data-ttu-id="7aca1-239">Přehled Web Apps (5minutové video s přehledem)</span><span class="sxs-lookup"><span data-stu-id="7aca1-239">Web Apps overview (5-minute overview video)</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="7aca1-240">Azure App Service: Nejlepší místo k hostování aplikací .NET (55 minutu video s přehledem)</span><span class="sxs-lookup"><span data-stu-id="7aca1-240">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span></span>](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [<span data-ttu-id="7aca1-241">Azure Friday: Azure App Service diagnostiku a řešení potíží s prostředí (12 pětiminutové video)</span><span class="sxs-lookup"><span data-stu-id="7aca1-241">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [<span data-ttu-id="7aca1-242">Přehled diagnostiky služby Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7aca1-242">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

<span data-ttu-id="7aca1-243">Služba Azure App Service v systému Windows Server používá [Internetové informační služby (IIS)](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="7aca1-243">Azure App Service on Windows Server uses [Internet Information Services (IIS)](https://www.iis.net/).</span></span> <span data-ttu-id="7aca1-244">Následující témata se týkají základní technologie služby IIS:</span><span class="sxs-lookup"><span data-stu-id="7aca1-244">The following topics pertain to the underlying IIS technology:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:fundamentals/servers/aspnet-core-module>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [<span data-ttu-id="7aca1-245">Microsoft TechNet Library: Windows Server</span><span class="sxs-lookup"><span data-stu-id="7aca1-245">Microsoft TechNet Library: Windows Server</span></span>](/windows-server/windows-server-versions)
