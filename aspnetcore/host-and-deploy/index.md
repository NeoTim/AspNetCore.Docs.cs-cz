---
title: ASP.NET Core hostitele a nasazení
author: rick-anderson
description: Naučte se nastavit hostující prostředí a nasazovat aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/index
ms.openlocfilehash: 0111b7944bc95932937f0fd74524e3960c5fb681
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634615"
---
# <a name="host-and-deploy-aspnet-core"></a><span data-ttu-id="39975-103">ASP.NET Core hostitele a nasazení</span><span class="sxs-lookup"><span data-stu-id="39975-103">Host and deploy ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="39975-104">Obecně nasazení aplikace ASP.NET Core do hostitelského prostředí:</span><span class="sxs-lookup"><span data-stu-id="39975-104">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="39975-105">Nasaďte publikovanou aplikaci do složky na hostitelském serveru.</span><span class="sxs-lookup"><span data-stu-id="39975-105">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="39975-106">Nastavte správce procesů, který spustí aplikaci, když žádosti dorazí a restartuje aplikaci po jejím zhroucení nebo restartování serveru.</span><span class="sxs-lookup"><span data-stu-id="39975-106">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="39975-107">Pro konfiguraci reverzního proxy serveru nastavte reverzní proxy server pro přeposílání požadavků do aplikace.</span><span class="sxs-lookup"><span data-stu-id="39975-107">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="39975-108">Publikovat do složky</span><span class="sxs-lookup"><span data-stu-id="39975-108">Publish to a folder</span></span>

<span data-ttu-id="39975-109">Příkaz [dotnet Publish](/dotnet/core/tools/dotnet-publish) zkompiluje kód aplikace a zkopíruje soubory potřebné ke spuštění aplikace do složky pro *publikování* .</span><span class="sxs-lookup"><span data-stu-id="39975-109">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="39975-110">Při nasazování ze sady Visual Studio `dotnet publish` proběhne krok automaticky předtím, než se soubory zkopírují do cíle nasazení.</span><span class="sxs-lookup"><span data-stu-id="39975-110">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="39975-111">Obsah složky</span><span class="sxs-lookup"><span data-stu-id="39975-111">Folder contents</span></span>

<span data-ttu-id="39975-112">Složka *Publish* obsahuje jeden nebo více souborů sestavení aplikace, závislosti a volitelně rozhraní .NET Runtime.</span><span class="sxs-lookup"><span data-stu-id="39975-112">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="39975-113">Aplikace .NET Core se dá publikovat jako *samostatné nasazení* nebo *nasazení závislé na rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="39975-113">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="39975-114">Pokud je aplikace samostatná, soubory sestavení obsahující modul runtime .NET jsou zahrnuty ve složce pro *publikování* .</span><span class="sxs-lookup"><span data-stu-id="39975-114">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="39975-115">Pokud je aplikace závislá na rozhraní, soubory modulu runtime .NET nejsou zahrnuty, protože aplikace odkazuje na verzi rozhraní .NET, která je nainstalována na serveru.</span><span class="sxs-lookup"><span data-stu-id="39975-115">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="39975-116">Výchozí model nasazení je závislý na rozhraní.</span><span class="sxs-lookup"><span data-stu-id="39975-116">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="39975-117">Další informace najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="39975-117">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="39975-118">Kromě souborů *. exe* a *. dll* složka pro *publikování* pro aplikaci ASP.NET Core obvykle obsahuje konfigurační soubory, statické prostředky a zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="39975-118">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="39975-119">Další informace naleznete v tématu <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="39975-119">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="39975-120">Nastavení správce procesů</span><span class="sxs-lookup"><span data-stu-id="39975-120">Set up a process manager</span></span>

<span data-ttu-id="39975-121">ASP.NET Core aplikace je Konzolová aplikace, která se musí spustit, když se Server spustí a restartuje, pokud dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="39975-121">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="39975-122">Pro automatizaci spuštění a restartování je vyžadován správce procesů.</span><span class="sxs-lookup"><span data-stu-id="39975-122">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="39975-123">Nejběžnějšími správci procesů pro ASP.NET Core jsou:</span><span class="sxs-lookup"><span data-stu-id="39975-123">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="39975-124">Linux</span><span class="sxs-lookup"><span data-stu-id="39975-124">Linux</span></span>
  * [<span data-ttu-id="39975-125">Nginx</span><span class="sxs-lookup"><span data-stu-id="39975-125">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="39975-126">Apache</span><span class="sxs-lookup"><span data-stu-id="39975-126">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="39975-127">Windows</span><span class="sxs-lookup"><span data-stu-id="39975-127">Windows</span></span>
  * [<span data-ttu-id="39975-128">IIS</span><span class="sxs-lookup"><span data-stu-id="39975-128">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="39975-129">Služba systému Windows</span><span class="sxs-lookup"><span data-stu-id="39975-129">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="39975-130">Nastavení reverzního proxy serveru</span><span class="sxs-lookup"><span data-stu-id="39975-130">Set up a reverse proxy</span></span>

<span data-ttu-id="39975-131">Pokud aplikace používá server [Kestrel](xref:fundamentals/servers/kestrel) , [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache)nebo [IIS](xref:host-and-deploy/iis/index) , dá se použít jako reverzní proxy server.</span><span class="sxs-lookup"><span data-stu-id="39975-131">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="39975-132">Reverzní proxy server přijímá požadavky HTTP z Internetu a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="39975-132">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="39975-133">Konfigurace &mdash; hostování je buď s nebo bez proxy server pro zpětný &mdash; provoz.</span><span class="sxs-lookup"><span data-stu-id="39975-133">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="39975-134">Další informace najdete v tématu [kdy používat Kestrel s reverzním proxy serverem](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="39975-134">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="39975-135">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="39975-135">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="39975-136">Pro aplikace hostované za proxy servery a nástroji pro vyrovnávání zatížení může být vyžadována další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="39975-136">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="39975-137">Bez další konfigurace může aplikace mít přístup ke schématu (HTTP/HTTPS) a vzdálené IP adrese, kde požadavek vznikl.</span><span class="sxs-lookup"><span data-stu-id="39975-137">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="39975-138">Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="39975-138">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="39975-139">Použití sady Visual Studio a nástroje MSBuild k automatizaci nasazení</span><span class="sxs-lookup"><span data-stu-id="39975-139">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="39975-140">Nasazení často vyžaduje další úlohy kromě kopírování výstupu z [dotnet Publish](/dotnet/core/tools/dotnet-publish) na server.</span><span class="sxs-lookup"><span data-stu-id="39975-140">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="39975-141">Například další soubory mohou být vyžadovány nebo vyloučeny ze složky pro *publikování* .</span><span class="sxs-lookup"><span data-stu-id="39975-141">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="39975-142">Visual Studio používá MSBuild pro nasazení webu a MSBuild se dá přizpůsobit tak, aby během nasazení provedl mnoho dalších úloh.</span><span class="sxs-lookup"><span data-stu-id="39975-142">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="39975-143">Další informace naleznete v tématu <xref:host-and-deploy/visual-studio-publish-profiles> a v příručce pro [sestavení pomocí nástroje MSBuild a Team Foundation](http://msbuildbook.com/) .</span><span class="sxs-lookup"><span data-stu-id="39975-143">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="39975-144">Pomocí [funkce Publikovat web](xref:tutorials/publish-to-azure-webapp-using-vs) nebo [integrovanou podporu pro Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment)je možné aplikace nasadit přímo ze sady Visual Studio do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="39975-144">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="39975-145">Azure DevOps Services podporuje [průběžné nasazování do Azure App Service](/azure/devops/pipelines/targets/webapp).</span><span class="sxs-lookup"><span data-stu-id="39975-145">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="39975-146">Další informace najdete v tématu [DevOps with ASP.NET Core a Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="39975-146">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="39975-147">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="39975-147">Publish to Azure</span></span>

<span data-ttu-id="39975-148"><xref:tutorials/publish-to-azure-webapp-using-vs>Pokyny k publikování aplikace do Azure pomocí sady Visual Studio najdete v tématu.</span><span class="sxs-lookup"><span data-stu-id="39975-148">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="39975-149">K dispozici je další příklad [Vytvoření webové aplikace ASP.NET Core v Azure](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="39975-149">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="39975-150">Publikování pomocí MSDeploy ve Windows</span><span class="sxs-lookup"><span data-stu-id="39975-150">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="39975-151"><xref:host-and-deploy/visual-studio-publish-profiles>Pokyny k publikování aplikace s publikačním profilem sady Visual Studio, včetně z příkazového řádku Windows pomocí příkazu [dotnet MSBuild](/dotnet/core/tools/dotnet-msbuild) , najdete v tématu.</span><span class="sxs-lookup"><span data-stu-id="39975-151">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="39975-152">Internet Information Services (IIS)</span><span class="sxs-lookup"><span data-stu-id="39975-152">Internet Information Services (IIS)</span></span>

<span data-ttu-id="39975-153">Pro nasazení, která Internetová informační služba (IIS) s konfigurací poskytovanou souborem *web.config* , se podívejte na články v části <xref:host-and-deploy/iis/index> .</span><span class="sxs-lookup"><span data-stu-id="39975-153">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="39975-154">Hostování ve webové farmě</span><span class="sxs-lookup"><span data-stu-id="39975-154">Host in a web farm</span></span>

<span data-ttu-id="39975-155">Informace o konfiguraci pro hostování ASP.NET Core aplikací v prostředí webové farmy (například nasazení více instancí aplikace v rámci škálovatelnosti) najdete v tématu <xref:host-and-deploy/web-farm> .</span><span class="sxs-lookup"><span data-stu-id="39975-155">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="39975-156">Hostitel v Docker</span><span class="sxs-lookup"><span data-stu-id="39975-156">Host on Docker</span></span>

<span data-ttu-id="39975-157">Další informace naleznete v tématu <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="39975-157">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="perform-health-checks"></a><span data-ttu-id="39975-158">Provádět kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="39975-158">Perform health checks</span></span>

<span data-ttu-id="39975-159">Pomocí middleware pro kontrolu stavu můžete provádět kontroly stavu aplikace a jejích závislostí.</span><span class="sxs-lookup"><span data-stu-id="39975-159">Use Health Check Middleware to perform health checks on an app and its dependencies.</span></span> <span data-ttu-id="39975-160">Další informace naleznete v tématu <xref:host-and-deploy/health-checks>.</span><span class="sxs-lookup"><span data-stu-id="39975-160">For more information, see <xref:host-and-deploy/health-checks>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="39975-161">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="39975-161">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="39975-162">Hostování ASP.NET</span><span class="sxs-lookup"><span data-stu-id="39975-162">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="39975-163">Obecně nasazení aplikace ASP.NET Core do hostitelského prostředí:</span><span class="sxs-lookup"><span data-stu-id="39975-163">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="39975-164">Nasaďte publikovanou aplikaci do složky na hostitelském serveru.</span><span class="sxs-lookup"><span data-stu-id="39975-164">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="39975-165">Nastavte správce procesů, který spustí aplikaci, když žádosti dorazí a restartuje aplikaci po jejím zhroucení nebo restartování serveru.</span><span class="sxs-lookup"><span data-stu-id="39975-165">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="39975-166">Pro konfiguraci reverzního proxy serveru nastavte reverzní proxy server pro přeposílání požadavků do aplikace.</span><span class="sxs-lookup"><span data-stu-id="39975-166">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="39975-167">Publikovat do složky</span><span class="sxs-lookup"><span data-stu-id="39975-167">Publish to a folder</span></span>

<span data-ttu-id="39975-168">Příkaz [dotnet Publish](/dotnet/core/tools/dotnet-publish) zkompiluje kód aplikace a zkopíruje soubory potřebné ke spuštění aplikace do složky pro *publikování* .</span><span class="sxs-lookup"><span data-stu-id="39975-168">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="39975-169">Při nasazování ze sady Visual Studio `dotnet publish` proběhne krok automaticky předtím, než se soubory zkopírují do cíle nasazení.</span><span class="sxs-lookup"><span data-stu-id="39975-169">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="39975-170">Obsah složky</span><span class="sxs-lookup"><span data-stu-id="39975-170">Folder contents</span></span>

<span data-ttu-id="39975-171">Složka *Publish* obsahuje jeden nebo více souborů sestavení aplikace, závislosti a volitelně rozhraní .NET Runtime.</span><span class="sxs-lookup"><span data-stu-id="39975-171">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="39975-172">Aplikace .NET Core se dá publikovat jako *samostatné nasazení* nebo *nasazení závislé na rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="39975-172">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="39975-173">Pokud je aplikace samostatná, soubory sestavení obsahující modul runtime .NET jsou zahrnuty ve složce pro *publikování* .</span><span class="sxs-lookup"><span data-stu-id="39975-173">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="39975-174">Pokud je aplikace závislá na rozhraní, soubory modulu runtime .NET nejsou zahrnuty, protože aplikace odkazuje na verzi rozhraní .NET, která je nainstalována na serveru.</span><span class="sxs-lookup"><span data-stu-id="39975-174">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="39975-175">Výchozí model nasazení je závislý na rozhraní.</span><span class="sxs-lookup"><span data-stu-id="39975-175">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="39975-176">Další informace najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="39975-176">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="39975-177">Kromě souborů *. exe* a *. dll* složka pro *publikování* pro aplikaci ASP.NET Core obvykle obsahuje konfigurační soubory, statické prostředky a zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="39975-177">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="39975-178">Další informace naleznete v tématu <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="39975-178">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="39975-179">Nastavení správce procesů</span><span class="sxs-lookup"><span data-stu-id="39975-179">Set up a process manager</span></span>

<span data-ttu-id="39975-180">ASP.NET Core aplikace je Konzolová aplikace, která se musí spustit, když se Server spustí a restartuje, pokud dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="39975-180">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="39975-181">Pro automatizaci spuštění a restartování je vyžadován správce procesů.</span><span class="sxs-lookup"><span data-stu-id="39975-181">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="39975-182">Nejběžnějšími správci procesů pro ASP.NET Core jsou:</span><span class="sxs-lookup"><span data-stu-id="39975-182">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="39975-183">Linux</span><span class="sxs-lookup"><span data-stu-id="39975-183">Linux</span></span>
  * [<span data-ttu-id="39975-184">Nginx</span><span class="sxs-lookup"><span data-stu-id="39975-184">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="39975-185">Apache</span><span class="sxs-lookup"><span data-stu-id="39975-185">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="39975-186">Windows</span><span class="sxs-lookup"><span data-stu-id="39975-186">Windows</span></span>
  * [<span data-ttu-id="39975-187">IIS</span><span class="sxs-lookup"><span data-stu-id="39975-187">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="39975-188">Služba systému Windows</span><span class="sxs-lookup"><span data-stu-id="39975-188">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="39975-189">Nastavení reverzního proxy serveru</span><span class="sxs-lookup"><span data-stu-id="39975-189">Set up a reverse proxy</span></span>

<span data-ttu-id="39975-190">Pokud aplikace používá server [Kestrel](xref:fundamentals/servers/kestrel) , [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache)nebo [IIS](xref:host-and-deploy/iis/index) , dá se použít jako reverzní proxy server.</span><span class="sxs-lookup"><span data-stu-id="39975-190">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="39975-191">Reverzní proxy server přijímá požadavky HTTP z Internetu a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="39975-191">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="39975-192">Konfigurace &mdash; hostování je buď s nebo bez proxy server pro zpětný &mdash; provoz.</span><span class="sxs-lookup"><span data-stu-id="39975-192">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="39975-193">Další informace najdete v tématu [kdy používat Kestrel s reverzním proxy serverem](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="39975-193">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="39975-194">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="39975-194">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="39975-195">Pro aplikace hostované za proxy servery a nástroji pro vyrovnávání zatížení může být vyžadována další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="39975-195">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="39975-196">Bez další konfigurace může aplikace mít přístup ke schématu (HTTP/HTTPS) a vzdálené IP adrese, kde požadavek vznikl.</span><span class="sxs-lookup"><span data-stu-id="39975-196">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="39975-197">Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="39975-197">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="39975-198">Použití sady Visual Studio a nástroje MSBuild k automatizaci nasazení</span><span class="sxs-lookup"><span data-stu-id="39975-198">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="39975-199">Nasazení často vyžaduje další úlohy kromě kopírování výstupu z [dotnet Publish](/dotnet/core/tools/dotnet-publish) na server.</span><span class="sxs-lookup"><span data-stu-id="39975-199">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="39975-200">Například další soubory mohou být vyžadovány nebo vyloučeny ze složky pro *publikování* .</span><span class="sxs-lookup"><span data-stu-id="39975-200">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="39975-201">Visual Studio používá MSBuild pro nasazení webu a MSBuild se dá přizpůsobit tak, aby během nasazení provedl mnoho dalších úloh.</span><span class="sxs-lookup"><span data-stu-id="39975-201">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="39975-202">Další informace naleznete v tématu <xref:host-and-deploy/visual-studio-publish-profiles> a v příručce pro [sestavení pomocí nástroje MSBuild a Team Foundation](http://msbuildbook.com/) .</span><span class="sxs-lookup"><span data-stu-id="39975-202">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="39975-203">Pomocí [funkce Publikovat web](xref:tutorials/publish-to-azure-webapp-using-vs) nebo [integrovanou podporu pro Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment)je možné aplikace nasadit přímo ze sady Visual Studio do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="39975-203">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="39975-204">Azure DevOps Services podporuje [průběžné nasazování do Azure App Service](/azure/devops/pipelines/targets/webapp).</span><span class="sxs-lookup"><span data-stu-id="39975-204">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="39975-205">Další informace najdete v tématu [DevOps with ASP.NET Core a Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="39975-205">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="39975-206">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="39975-206">Publish to Azure</span></span>

<span data-ttu-id="39975-207"><xref:tutorials/publish-to-azure-webapp-using-vs>Pokyny k publikování aplikace do Azure pomocí sady Visual Studio najdete v tématu.</span><span class="sxs-lookup"><span data-stu-id="39975-207">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="39975-208">K dispozici je další příklad [Vytvoření webové aplikace ASP.NET Core v Azure](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="39975-208">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="39975-209">Publikování pomocí MSDeploy ve Windows</span><span class="sxs-lookup"><span data-stu-id="39975-209">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="39975-210"><xref:host-and-deploy/visual-studio-publish-profiles>Pokyny k publikování aplikace s publikačním profilem sady Visual Studio, včetně z příkazového řádku Windows pomocí příkazu [dotnet MSBuild](/dotnet/core/tools/dotnet-msbuild) , najdete v tématu.</span><span class="sxs-lookup"><span data-stu-id="39975-210">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="39975-211">Internet Information Services (IIS)</span><span class="sxs-lookup"><span data-stu-id="39975-211">Internet Information Services (IIS)</span></span>

<span data-ttu-id="39975-212">Pro nasazení, která Internetová informační služba (IIS) s konfigurací poskytovanou souborem *web.config* , se podívejte na články v části <xref:host-and-deploy/iis/index> .</span><span class="sxs-lookup"><span data-stu-id="39975-212">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="39975-213">Hostování ve webové farmě</span><span class="sxs-lookup"><span data-stu-id="39975-213">Host in a web farm</span></span>

<span data-ttu-id="39975-214">Informace o konfiguraci pro hostování ASP.NET Core aplikací v prostředí webové farmy (například nasazení více instancí aplikace v rámci škálovatelnosti) najdete v tématu <xref:host-and-deploy/web-farm> .</span><span class="sxs-lookup"><span data-stu-id="39975-214">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="39975-215">Hostitel v Docker</span><span class="sxs-lookup"><span data-stu-id="39975-215">Host on Docker</span></span>

<span data-ttu-id="39975-216">Další informace naleznete v tématu <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="39975-216">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="39975-217">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="39975-217">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="39975-218">Hostování ASP.NET</span><span class="sxs-lookup"><span data-stu-id="39975-218">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
