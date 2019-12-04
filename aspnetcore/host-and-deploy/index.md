---
title: ASP.NET Core hostitele a nasazení
author: guardrex
description: Naučte se nastavit hostující prostředí a nasazovat aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: host-and-deploy/index
ms.openlocfilehash: 8c7c131ca328f3118c45e822d6d5c86f0d44001f
ms.sourcegitcommit: b3e1e31e5d8bdd94096cf27444594d4a7b065525
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803263"
---
# <a name="host-and-deploy-aspnet-core"></a><span data-ttu-id="d76b3-103">ASP.NET Core hostitele a nasazení</span><span class="sxs-lookup"><span data-stu-id="d76b3-103">Host and deploy ASP.NET Core</span></span>

<span data-ttu-id="d76b3-104">Obecně nasazení aplikace ASP.NET Core do hostitelského prostředí:</span><span class="sxs-lookup"><span data-stu-id="d76b3-104">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="d76b3-105">Nasaďte publikovanou aplikaci do složky na hostitelském serveru.</span><span class="sxs-lookup"><span data-stu-id="d76b3-105">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="d76b3-106">Nastavte správce procesů, který spustí aplikaci, když žádosti dorazí a restartuje aplikaci po jejím zhroucení nebo restartování serveru.</span><span class="sxs-lookup"><span data-stu-id="d76b3-106">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="d76b3-107">Pro konfiguraci reverzního proxy serveru nastavte reverzní proxy server pro přeposílání požadavků do aplikace.</span><span class="sxs-lookup"><span data-stu-id="d76b3-107">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="d76b3-108">Publikovat do složky</span><span class="sxs-lookup"><span data-stu-id="d76b3-108">Publish to a folder</span></span>

<span data-ttu-id="d76b3-109">Příkaz [dotnet Publish](/dotnet/core/tools/dotnet-publish) zkompiluje kód aplikace a zkopíruje soubory potřebné ke spuštění aplikace do složky pro *publikování* .</span><span class="sxs-lookup"><span data-stu-id="d76b3-109">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="d76b3-110">Při nasazování ze sady Visual Studio probíhá `dotnet publish` krok automaticky předtím, než se soubory zkopírují do cíle nasazení.</span><span class="sxs-lookup"><span data-stu-id="d76b3-110">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="d76b3-111">Obsah složky</span><span class="sxs-lookup"><span data-stu-id="d76b3-111">Folder contents</span></span>

<span data-ttu-id="d76b3-112">Složka *Publish* obsahuje jeden nebo více souborů sestavení aplikace, závislosti a volitelně rozhraní .NET Runtime.</span><span class="sxs-lookup"><span data-stu-id="d76b3-112">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="d76b3-113">Aplikace .NET Core se dá publikovat jako *samostatné nasazení* nebo *nasazení závislé na rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="d76b3-113">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="d76b3-114">Pokud je aplikace samostatná, soubory sestavení obsahující modul runtime .NET jsou zahrnuty ve složce pro *publikování* .</span><span class="sxs-lookup"><span data-stu-id="d76b3-114">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="d76b3-115">Pokud je aplikace závislá na rozhraní, soubory modulu runtime .NET nejsou zahrnuty, protože aplikace odkazuje na verzi rozhraní .NET, která je nainstalována na serveru.</span><span class="sxs-lookup"><span data-stu-id="d76b3-115">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="d76b3-116">Výchozí model nasazení je závislý na rozhraní.</span><span class="sxs-lookup"><span data-stu-id="d76b3-116">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="d76b3-117">Další informace najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="d76b3-117">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="d76b3-118">Kromě souborů *. exe* a *. dll* složka pro *publikování* pro aplikaci ASP.NET Core obvykle obsahuje konfigurační soubory, statické prostředky a zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="d76b3-118">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="d76b3-119">Další informace najdete v tématu <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="d76b3-119">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="d76b3-120">Nastavení správce procesů</span><span class="sxs-lookup"><span data-stu-id="d76b3-120">Set up a process manager</span></span>

<span data-ttu-id="d76b3-121">ASP.NET Core aplikace je Konzolová aplikace, která se musí spustit, když se Server spustí a restartuje, pokud dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="d76b3-121">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="d76b3-122">Pro automatizaci spuštění a restartování je vyžadován správce procesů.</span><span class="sxs-lookup"><span data-stu-id="d76b3-122">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="d76b3-123">Nejběžnějšími správci procesů pro ASP.NET Core jsou:</span><span class="sxs-lookup"><span data-stu-id="d76b3-123">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="d76b3-124">Linux</span><span class="sxs-lookup"><span data-stu-id="d76b3-124">Linux</span></span>
  * [<span data-ttu-id="d76b3-125">Nginx</span><span class="sxs-lookup"><span data-stu-id="d76b3-125">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="d76b3-126">Apache</span><span class="sxs-lookup"><span data-stu-id="d76b3-126">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="d76b3-127">Windows</span><span class="sxs-lookup"><span data-stu-id="d76b3-127">Windows</span></span>
  * [<span data-ttu-id="d76b3-128">SLUŽBA IIS</span><span class="sxs-lookup"><span data-stu-id="d76b3-128">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="d76b3-129">Windows Service</span><span class="sxs-lookup"><span data-stu-id="d76b3-129">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="d76b3-130">Nastavení reverzního proxy serveru</span><span class="sxs-lookup"><span data-stu-id="d76b3-130">Set up a reverse proxy</span></span>

<span data-ttu-id="d76b3-131">Pokud aplikace používá server [Kestrel](xref:fundamentals/servers/kestrel) , [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache)nebo [IIS](xref:host-and-deploy/iis/index) , dá se použít jako reverzní proxy server.</span><span class="sxs-lookup"><span data-stu-id="d76b3-131">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="d76b3-132">Reverzní proxy server přijímá požadavky HTTP z Internetu a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d76b3-132">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="d76b3-133">Konfigurace hostování je&mdash;s nebo bez&mdash;proxy server s reverzním.</span><span class="sxs-lookup"><span data-stu-id="d76b3-133">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="d76b3-134">Další informace najdete v tématu [kdy používat Kestrel s reverzním proxy serverem](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="d76b3-134">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d76b3-135">Proxy server a scénáře pro nástroj pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="d76b3-135">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d76b3-136">Pro aplikace hostované za proxy servery a nástroji pro vyrovnávání zatížení může být vyžadována další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="d76b3-136">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="d76b3-137">Bez další konfigurace může aplikace mít přístup ke schématu (HTTP/HTTPS) a vzdálené IP adrese, kde požadavek vznikl.</span><span class="sxs-lookup"><span data-stu-id="d76b3-137">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="d76b3-138">Další informace najdete v tématu [konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="d76b3-138">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="d76b3-139">Použití sady Visual Studio a nástroje MSBuild k automatizaci nasazení</span><span class="sxs-lookup"><span data-stu-id="d76b3-139">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="d76b3-140">Nasazení často vyžaduje další úlohy kromě kopírování výstupu z [dotnet Publish](/dotnet/core/tools/dotnet-publish) na server.</span><span class="sxs-lookup"><span data-stu-id="d76b3-140">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="d76b3-141">Například další soubory mohou být vyžadovány nebo vyloučeny ze složky pro *publikování* .</span><span class="sxs-lookup"><span data-stu-id="d76b3-141">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="d76b3-142">Visual Studio používá MSBuild pro nasazení webu a MSBuild se dá přizpůsobit tak, aby během nasazení provedl mnoho dalších úloh.</span><span class="sxs-lookup"><span data-stu-id="d76b3-142">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="d76b3-143">Další informace naleznete v tématu <xref:host-and-deploy/visual-studio-publish-profiles> a v příručce pro [sestavení pomocí nástroje MSBuild a Team Foundation](http://msbuildbook.com/) .</span><span class="sxs-lookup"><span data-stu-id="d76b3-143">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="d76b3-144">Pomocí [funkce Publikovat web](xref:tutorials/publish-to-azure-webapp-using-vs) nebo [integrovanou podporu pro Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment)je možné aplikace nasadit přímo ze sady Visual Studio do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="d76b3-144">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="d76b3-145">Azure DevOps Services podporuje [průběžné nasazování do Azure App Service](/azure/devops/pipelines/targets/webapp).</span><span class="sxs-lookup"><span data-stu-id="d76b3-145">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="d76b3-146">Další informace najdete v tématu [DevOps with ASP.NET Core a Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="d76b3-146">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="d76b3-147">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="d76b3-147">Publish to Azure</span></span>

<span data-ttu-id="d76b3-148">Pokyny k publikování aplikace do Azure pomocí sady Visual Studio najdete v tématu <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="d76b3-148">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="d76b3-149">K dispozici je další příklad [Vytvoření webové aplikace ASP.NET Core v Azure](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="d76b3-149">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="d76b3-150">Publikování pomocí MSDeploy ve Windows</span><span class="sxs-lookup"><span data-stu-id="d76b3-150">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="d76b3-151">Pokyny k publikování aplikace pomocí profilu publikování sady Visual Studio, včetně příkazového řádku Windows pomocí příkazu [dotnet MSBuild](/dotnet/core/tools/dotnet-msbuild) , najdete v tématu <xref:host-and-deploy/visual-studio-publish-profiles>.</span><span class="sxs-lookup"><span data-stu-id="d76b3-151">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="d76b3-152">Internet Information Services (IIS)</span><span class="sxs-lookup"><span data-stu-id="d76b3-152">Internet Information Services (IIS)</span></span>

<span data-ttu-id="d76b3-153">Pro nasazení, která se Internetová informační služba (IIS) s konfigurací poskytnutou souborem *Web. config* , se podívejte na články v části <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="d76b3-153">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="d76b3-154">Hostování ve webové farmě</span><span class="sxs-lookup"><span data-stu-id="d76b3-154">Host in a web farm</span></span>

<span data-ttu-id="d76b3-155">Informace o konfiguraci pro hostování ASP.NET Core aplikací v prostředí webové farmy (například nasazení více instancí aplikace v rámci škálovatelnosti) najdete v tématu <xref:host-and-deploy/web-farm>.</span><span class="sxs-lookup"><span data-stu-id="d76b3-155">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="perform-health-checks"></a><span data-ttu-id="d76b3-156">Provádět kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="d76b3-156">Perform health checks</span></span>

<span data-ttu-id="d76b3-157">Pomocí middleware pro kontrolu stavu můžete provádět kontroly stavu aplikace a jejích závislostí.</span><span class="sxs-lookup"><span data-stu-id="d76b3-157">Use Health Check Middleware to perform health checks on an app and its dependencies.</span></span> <span data-ttu-id="d76b3-158">Další informace najdete v tématu <xref:host-and-deploy/health-checks>.</span><span class="sxs-lookup"><span data-stu-id="d76b3-158">For more information, see <xref:host-and-deploy/health-checks>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d76b3-159">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="d76b3-159">Additional resources</span></span>

* <xref:host-and-deploy/docker/index>
* <xref:test/troubleshoot>
* [<span data-ttu-id="d76b3-160">ASP.NET Hosting</span><span class="sxs-lookup"><span data-stu-id="d76b3-160">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

