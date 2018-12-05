---
title: Hostitelství a nasazení ASP.NET Core
author: guardrex
description: Zjistěte, jak nastavit hostitelská prostředí a nasazení aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/01/2018
uid: host-and-deploy/index
ms.openlocfilehash: 86022c33a3c5a8b82b14ae51b98c44497f39bd16
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52862444"
---
# <a name="host-and-deploy-aspnet-core"></a><span data-ttu-id="f077f-103">Hostitelství a nasazení ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f077f-103">Host and deploy ASP.NET Core</span></span>

<span data-ttu-id="f077f-104">Obecný postup nasazení aplikace ASP.NET Core do hostitelského prostředí:</span><span class="sxs-lookup"><span data-stu-id="f077f-104">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="f077f-105">Publikovanou aplikaci nasadíte do složky na hostitelském serveru.</span><span class="sxs-lookup"><span data-stu-id="f077f-105">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="f077f-106">Nastavte správce procesu, který se spustí aplikace při žádosti o doručení a restartuje aplikaci poté, co ho dojde k chybě nebo se server nerestartuje.</span><span class="sxs-lookup"><span data-stu-id="f077f-106">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="f077f-107">Konfigurace reverzního proxy serveru nastavení reverzní proxy server pro směrování žádostí na aplikace.</span><span class="sxs-lookup"><span data-stu-id="f077f-107">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="f077f-108">Publikovat do složky</span><span class="sxs-lookup"><span data-stu-id="f077f-108">Publish to a folder</span></span>

<span data-ttu-id="f077f-109">[Dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkaz zkompiluje kód aplikace a zkopíruje soubory potřebné ke spuštění aplikace do *publikovat* složky.</span><span class="sxs-lookup"><span data-stu-id="f077f-109">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="f077f-110">Při nasazení ze sady Visual Studio `dotnet publish` kroku dojde automaticky před soubory se zkopírují do cíle nasazení.</span><span class="sxs-lookup"><span data-stu-id="f077f-110">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="f077f-111">Obsah složky</span><span class="sxs-lookup"><span data-stu-id="f077f-111">Folder contents</span></span>

<span data-ttu-id="f077f-112">*Publikovat* složka obsahuje jeden nebo více souborů sestavení aplikace, závislostí a volitelně modul .NET runtime.</span><span class="sxs-lookup"><span data-stu-id="f077f-112">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="f077f-113">Aplikace .NET Core můžete publikovat jako *samostatná nasazení* nebo *nasazení závisí na architektuře*.</span><span class="sxs-lookup"><span data-stu-id="f077f-113">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="f077f-114">Pokud je samostatná aplikace, jsou součástí souborů sestavení obsahující modul .NET runtime *publikovat* složky.</span><span class="sxs-lookup"><span data-stu-id="f077f-114">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="f077f-115">Pokud je aplikace závisí na architektuře, nejsou zahrnuté soubory modulu runtime .NET, protože aplikace obsahuje odkaz na verzi rozhraní .NET, která je nainstalována na serveru.</span><span class="sxs-lookup"><span data-stu-id="f077f-115">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="f077f-116">Výchozí model nasazení je závisí na architektuře.</span><span class="sxs-lookup"><span data-stu-id="f077f-116">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="f077f-117">Další informace najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="f077f-117">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="f077f-118">Kromě *.exe* a *.dll* soubory, *publikovat* složku pro aplikace ASP.NET Core obvykle obsahuje konfigurační soubory, statických prostředků a zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="f077f-118">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="f077f-119">Další informace naleznete v tématu <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="f077f-119">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="f077f-120">Nastavit správce procesu</span><span class="sxs-lookup"><span data-stu-id="f077f-120">Set up a process manager</span></span>

<span data-ttu-id="f077f-121">Aplikace ASP.NET Core je konzolová aplikace, které musí být spuštěna, když na server se spustí a restartovaný, pokud ho dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="f077f-121">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="f077f-122">Chcete-li automatizovat spustí a restartuje, je potřeba správce procesu.</span><span class="sxs-lookup"><span data-stu-id="f077f-122">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="f077f-123">Nejběžnější vedoucí proces pro ASP.NET Core jsou:</span><span class="sxs-lookup"><span data-stu-id="f077f-123">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="f077f-124">Linux</span><span class="sxs-lookup"><span data-stu-id="f077f-124">Linux</span></span>
  * [<span data-ttu-id="f077f-125">Server Nginx</span><span class="sxs-lookup"><span data-stu-id="f077f-125">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="f077f-126">Apache</span><span class="sxs-lookup"><span data-stu-id="f077f-126">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="f077f-127">Windows</span><span class="sxs-lookup"><span data-stu-id="f077f-127">Windows</span></span>
  * [<span data-ttu-id="f077f-128">SLUŽBA IIS</span><span class="sxs-lookup"><span data-stu-id="f077f-128">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="f077f-129">Windows Service</span><span class="sxs-lookup"><span data-stu-id="f077f-129">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="f077f-130">Nastavit reverzní proxy server</span><span class="sxs-lookup"><span data-stu-id="f077f-130">Set up a reverse proxy</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="f077f-131">Pokud aplikace využívá [Kestrel](xref:fundamentals/servers/kestrel) serveru [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), nebo [IIS](xref:host-and-deploy/iis/index) může sloužit jako reverzní proxy server.</span><span class="sxs-lookup"><span data-stu-id="f077f-131">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="f077f-132">Reverzní proxy server přijímá požadavky HTTP z Internetu a předává je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f077f-132">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="f077f-133">Buď konfiguraci&mdash;s nebo bez něj reverzní proxy server&mdash;je hostování podporovanou konfiguraci pro ASP.NET Core 2.0 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="f077f-133">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration for ASP.NET Core 2.0 or later apps.</span></span> <span data-ttu-id="f077f-134">Další informace najdete v tématu [použití Kestrel s reverzní proxy server](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="f077f-134">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="f077f-135">Pokud aplikace využívá [Kestrel](xref:fundamentals/servers/kestrel) server a budou zveřejněny na Internetu, použijte [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), nebo [IIS](xref:host-and-deploy/iis/index) jako reverzní proxy server.</span><span class="sxs-lookup"><span data-stu-id="f077f-135">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server and will be exposed to the Internet, use [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) as a reverse proxy server.</span></span> <span data-ttu-id="f077f-136">Reverzní proxy server přijímá požadavky HTTP z Internetu a předává je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f077f-136">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span> <span data-ttu-id="f077f-137">Hlavním důvodem pro pomocí reverzního proxy je zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="f077f-137">The main reason for using a reverse proxy is security.</span></span> <span data-ttu-id="f077f-138">Další informace najdete v tématu [použití Kestrel s reverzní proxy server](xref:fundamentals/servers/kestrel?tabs=aspnetcore1x#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="f077f-138">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel?tabs=aspnetcore1x#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="f077f-139">Proxy server a scénáře pro nástroj pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="f077f-139">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="f077f-140">Další konfigurace může být nezbytný pro aplikací hostovaných za službou proxy servery a nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="f077f-140">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="f077f-141">Bez další konfigurace nemusí aplikaci mít přístup k schématu (HTTP/HTTPS) a Vzdálená IP adresa původu žádost.</span><span class="sxs-lookup"><span data-stu-id="f077f-141">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="f077f-142">Další informace najdete v tématu [konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="f077f-142">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="f077f-143">Automatizace nasazení pomocí sady Visual Studio a nástroje MSBuild</span><span class="sxs-lookup"><span data-stu-id="f077f-143">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="f077f-144">Nasazení často vyžaduje další úkoly kromě kopírování výstup z [dotnet publikovat](/dotnet/core/tools/dotnet-publish) na server.</span><span class="sxs-lookup"><span data-stu-id="f077f-144">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="f077f-145">Například může být nutné nebo vyloučeny ze dalších souborů *publikovat* složky.</span><span class="sxs-lookup"><span data-stu-id="f077f-145">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="f077f-146">Visual Studio používá MSBuild pro nasazení webu a je možné přizpůsobit MSBuild k provádění mnoha jiných úloh během nasazování.</span><span class="sxs-lookup"><span data-stu-id="f077f-146">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="f077f-147">Další informace najdete v tématu <xref:host-and-deploy/visual-studio-publish-profiles> a [pomocí nástroje MSBuild a Team Foundation Build](http://msbuildbook.com/) knihy.</span><span class="sxs-lookup"><span data-stu-id="f077f-147">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="f077f-148">S použitím [funkci Publikovat Web](xref:tutorials/publish-to-azure-webapp-using-vs) nebo [integrovanou podporu Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment), aplikace se dají nasadit přímo z Visual Studio do služby Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="f077f-148">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="f077f-149">Podporuje služby Azure DevOps [průběžné nasazování do služby Azure App Service](/azure/devops/pipelines/targets/webapp).</span><span class="sxs-lookup"><span data-stu-id="f077f-149">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="f077f-150">Další informace najdete v tématu [DevOps s využitím ASP.NET Core a Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="f077f-150">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="f077f-151">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="f077f-151">Publish to Azure</span></span>

<span data-ttu-id="f077f-152">Zobrazit <xref:tutorials/publish-to-azure-webapp-using-vs> pokyny o tom, jak publikovat aplikaci do Azure pomocí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f077f-152">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="f077f-153">Aplikace můžete publikovat také do Azure z [příkazového řádku](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="f077f-153">The app can also be published to Azure from the [command line](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="f077f-154">Hostování ve webové farmě</span><span class="sxs-lookup"><span data-stu-id="f077f-154">Host in a web farm</span></span>

<span data-ttu-id="f077f-155">Informace o konfiguraci pro hostování aplikací ASP.NET Core v prostředí webové farmy (například nasazení více instancí vaší aplikace pro zajištění škálovatelnosti), najdete v části <xref:host-and-deploy/web-farm>.</span><span class="sxs-lookup"><span data-stu-id="f077f-155">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="perform-health-checks"></a><span data-ttu-id="f077f-156">Provádění kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="f077f-156">Perform health checks</span></span>

<span data-ttu-id="f077f-157">Použití middlewaru zkontrolovat stav k provádění kontroly stavu aplikace a jeho závislosti.</span><span class="sxs-lookup"><span data-stu-id="f077f-157">Use Health Check Middleware to perform health checks on an app and its dependencies.</span></span> <span data-ttu-id="f077f-158">Další informace naleznete v tématu <xref:host-and-deploy/health-checks>.</span><span class="sxs-lookup"><span data-stu-id="f077f-158">For more information, see <xref:host-and-deploy/health-checks>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f077f-159">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f077f-159">Additional resources</span></span>

* <xref:host-and-deploy/docker/index>
* <xref:test/troubleshoot>
