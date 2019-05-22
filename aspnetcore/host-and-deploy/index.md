---
title: Hostitelství a nasazení ASP.NET Core
author: guardrex
description: Zjistěte, jak nastavit hostitelská prostředí a nasazení aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/12/2019
uid: host-and-deploy/index
ms.openlocfilehash: 068c5f357b8010d78493a78a10c353e3317f3867
ms.sourcegitcommit: b4ef2b00f3e1eb287138f8b43c811cb35a100d3e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969889"
---
# <a name="host-and-deploy-aspnet-core"></a><span data-ttu-id="e4151-103">Hostitelství a nasazení ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e4151-103">Host and deploy ASP.NET Core</span></span>

<span data-ttu-id="e4151-104">Obecný postup nasazení aplikace ASP.NET Core do hostitelského prostředí:</span><span class="sxs-lookup"><span data-stu-id="e4151-104">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="e4151-105">Publikovanou aplikaci nasadíte do složky na hostitelském serveru.</span><span class="sxs-lookup"><span data-stu-id="e4151-105">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="e4151-106">Nastavte správce procesu, který se spustí aplikace při žádosti o doručení a restartuje aplikaci poté, co ho dojde k chybě nebo se server nerestartuje.</span><span class="sxs-lookup"><span data-stu-id="e4151-106">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="e4151-107">Konfigurace reverzního proxy serveru nastavení reverzní proxy server pro směrování žádostí na aplikace.</span><span class="sxs-lookup"><span data-stu-id="e4151-107">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="e4151-108">Publikovat do složky</span><span class="sxs-lookup"><span data-stu-id="e4151-108">Publish to a folder</span></span>

<span data-ttu-id="e4151-109">[Dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkaz zkompiluje kód aplikace a zkopíruje soubory potřebné ke spuštění aplikace do *publikovat* složky.</span><span class="sxs-lookup"><span data-stu-id="e4151-109">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="e4151-110">Při nasazení ze sady Visual Studio `dotnet publish` kroku dojde automaticky před soubory se zkopírují do cíle nasazení.</span><span class="sxs-lookup"><span data-stu-id="e4151-110">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="e4151-111">Obsah složky</span><span class="sxs-lookup"><span data-stu-id="e4151-111">Folder contents</span></span>

<span data-ttu-id="e4151-112">*Publikovat* složka obsahuje jeden nebo více souborů sestavení aplikace, závislostí a volitelně modul .NET runtime.</span><span class="sxs-lookup"><span data-stu-id="e4151-112">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="e4151-113">Aplikace .NET Core můžete publikovat jako *samostatná nasazení* nebo *nasazení závisí na architektuře*.</span><span class="sxs-lookup"><span data-stu-id="e4151-113">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="e4151-114">Pokud je samostatná aplikace, jsou součástí souborů sestavení obsahující modul .NET runtime *publikovat* složky.</span><span class="sxs-lookup"><span data-stu-id="e4151-114">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="e4151-115">Pokud je aplikace závisí na architektuře, nejsou zahrnuté soubory modulu runtime .NET, protože aplikace obsahuje odkaz na verzi rozhraní .NET, která je nainstalována na serveru.</span><span class="sxs-lookup"><span data-stu-id="e4151-115">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="e4151-116">Výchozí model nasazení je závisí na architektuře.</span><span class="sxs-lookup"><span data-stu-id="e4151-116">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="e4151-117">Další informace najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="e4151-117">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="e4151-118">Kromě *.exe* a *.dll* soubory, *publikovat* složku pro aplikace ASP.NET Core obvykle obsahuje konfigurační soubory, statických prostředků a zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="e4151-118">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="e4151-119">Další informace naleznete v tématu <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="e4151-119">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="e4151-120">Nastavit správce procesu</span><span class="sxs-lookup"><span data-stu-id="e4151-120">Set up a process manager</span></span>

<span data-ttu-id="e4151-121">Aplikace ASP.NET Core je konzolová aplikace, které musí být spuštěna, když na server se spustí a restartovaný, pokud ho dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="e4151-121">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="e4151-122">Chcete-li automatizovat spustí a restartuje, je potřeba správce procesu.</span><span class="sxs-lookup"><span data-stu-id="e4151-122">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="e4151-123">Nejběžnější vedoucí proces pro ASP.NET Core jsou:</span><span class="sxs-lookup"><span data-stu-id="e4151-123">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="e4151-124">Linux</span><span class="sxs-lookup"><span data-stu-id="e4151-124">Linux</span></span>
  * [<span data-ttu-id="e4151-125">Server Nginx</span><span class="sxs-lookup"><span data-stu-id="e4151-125">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="e4151-126">Apache</span><span class="sxs-lookup"><span data-stu-id="e4151-126">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="e4151-127">Windows</span><span class="sxs-lookup"><span data-stu-id="e4151-127">Windows</span></span>
  * [<span data-ttu-id="e4151-128">SLUŽBA IIS</span><span class="sxs-lookup"><span data-stu-id="e4151-128">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="e4151-129">Windows Service</span><span class="sxs-lookup"><span data-stu-id="e4151-129">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="e4151-130">Nastavit reverzní proxy server</span><span class="sxs-lookup"><span data-stu-id="e4151-130">Set up a reverse proxy</span></span>

<span data-ttu-id="e4151-131">Pokud aplikace využívá [Kestrel](xref:fundamentals/servers/kestrel) serveru [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), nebo [IIS](xref:host-and-deploy/iis/index) může sloužit jako reverzní proxy server.</span><span class="sxs-lookup"><span data-stu-id="e4151-131">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="e4151-132">Reverzní proxy server přijímá požadavky HTTP z Internetu a předává je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="e4151-132">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="e4151-133">Buď konfiguraci&mdash;s nebo bez něj reverzní proxy server&mdash;je na podporovanou konfiguraci pro hostování.</span><span class="sxs-lookup"><span data-stu-id="e4151-133">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="e4151-134">Další informace najdete v tématu [použití Kestrel s reverzní proxy server](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="e4151-134">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="e4151-135">Proxy server a scénáře pro nástroj pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="e4151-135">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="e4151-136">Další konfigurace může být nezbytný pro aplikací hostovaných za službou proxy servery a nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="e4151-136">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="e4151-137">Bez další konfigurace nemusí aplikaci mít přístup k schématu (HTTP/HTTPS) a Vzdálená IP adresa původu žádost.</span><span class="sxs-lookup"><span data-stu-id="e4151-137">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="e4151-138">Další informace najdete v tématu [konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="e4151-138">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="e4151-139">Automatizace nasazení pomocí sady Visual Studio a nástroje MSBuild</span><span class="sxs-lookup"><span data-stu-id="e4151-139">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="e4151-140">Nasazení často vyžaduje další úkoly kromě kopírování výstup z [dotnet publikovat](/dotnet/core/tools/dotnet-publish) na server.</span><span class="sxs-lookup"><span data-stu-id="e4151-140">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="e4151-141">Například může být nutné nebo vyloučeny ze dalších souborů *publikovat* složky.</span><span class="sxs-lookup"><span data-stu-id="e4151-141">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="e4151-142">Visual Studio používá MSBuild pro nasazení webu a je možné přizpůsobit MSBuild k provádění mnoha jiných úloh během nasazování.</span><span class="sxs-lookup"><span data-stu-id="e4151-142">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="e4151-143">Další informace najdete v tématu <xref:host-and-deploy/visual-studio-publish-profiles> a [pomocí nástroje MSBuild a Team Foundation Build](http://msbuildbook.com/) knihy.</span><span class="sxs-lookup"><span data-stu-id="e4151-143">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="e4151-144">S použitím [funkci Publikovat Web](xref:tutorials/publish-to-azure-webapp-using-vs) nebo [integrovanou podporu Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment), aplikace se dají nasadit přímo z Visual Studio do služby Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="e4151-144">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="e4151-145">Podporuje služby Azure DevOps [průběžné nasazování do služby Azure App Service](/azure/devops/pipelines/targets/webapp).</span><span class="sxs-lookup"><span data-stu-id="e4151-145">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="e4151-146">Další informace najdete v tématu [DevOps s využitím ASP.NET Core a Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="e4151-146">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="e4151-147">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="e4151-147">Publish to Azure</span></span>

<span data-ttu-id="e4151-148">Zobrazit <xref:tutorials/publish-to-azure-webapp-using-vs> pokyny o tom, jak publikovat aplikaci do Azure pomocí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e4151-148">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="e4151-149">Další příklad poskytuje [vytvoření webové aplikace ASP.NET Core v Azure](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="e4151-149">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="e4151-150">Publikování pomocí nástroje MSDeploy na Windows</span><span class="sxs-lookup"><span data-stu-id="e4151-150">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="e4151-151">V tématu <xref:host-and-deploy/visual-studio-publish-profiles> pro profil publikování pokyny o tom, jak publikovat aplikace pomocí sady Visual Studio, včetně pomocí příkazového řádku Windows [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) příkazu.</span><span class="sxs-lookup"><span data-stu-id="e4151-151">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="e4151-152">Hostování ve webové farmě</span><span class="sxs-lookup"><span data-stu-id="e4151-152">Host in a web farm</span></span>

<span data-ttu-id="e4151-153">Informace o konfiguraci pro hostování aplikací ASP.NET Core v prostředí webové farmy (například nasazení více instancí vaší aplikace pro zajištění škálovatelnosti), najdete v části <xref:host-and-deploy/web-farm>.</span><span class="sxs-lookup"><span data-stu-id="e4151-153">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="perform-health-checks"></a><span data-ttu-id="e4151-154">Provádění kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="e4151-154">Perform health checks</span></span>

<span data-ttu-id="e4151-155">Použití middlewaru zkontrolovat stav k provádění kontroly stavu aplikace a jeho závislosti.</span><span class="sxs-lookup"><span data-stu-id="e4151-155">Use Health Check Middleware to perform health checks on an app and its dependencies.</span></span> <span data-ttu-id="e4151-156">Další informace naleznete v tématu <xref:host-and-deploy/health-checks>.</span><span class="sxs-lookup"><span data-stu-id="e4151-156">For more information, see <xref:host-and-deploy/health-checks>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="e4151-157">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e4151-157">Additional resources</span></span>

* <xref:host-and-deploy/docker/index>
* <xref:test/troubleshoot>
