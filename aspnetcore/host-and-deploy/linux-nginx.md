---
title: Host ASP.NET Core na Linuxu s Nginx
author: rick-anderson
description: Naučte se, jak nastavit Nginx jako reverzní proxy na Ubuntu 16.04 pro předávání http provozu do ASP.NET webové aplikace Core běžící na Kestrelu.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/10/2020
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: ceb2ad857649dcfa8d04420dcc37792495edc3ff
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81224021"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a><span data-ttu-id="6209d-103">Host ASP.NET Core na Linuxu s Nginx</span><span class="sxs-lookup"><span data-stu-id="6209d-103">Host ASP.NET Core on Linux with Nginx</span></span>

<span data-ttu-id="6209d-104">Podle [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="6209d-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="6209d-105">Tato příručka vysvětluje nastavení prostředí ASP.NET Core připraveného pro produkční prostředí na serveru Ubuntu 16.04.</span><span class="sxs-lookup"><span data-stu-id="6209d-105">This guide explains setting up a production-ready ASP.NET Core environment on an Ubuntu 16.04 server.</span></span> <span data-ttu-id="6209d-106">Tyto pokyny pravděpodobně pracují s novějšími verzemi Ubuntu, ale pokyny nebyly testovány s novějšími verzemi.</span><span class="sxs-lookup"><span data-stu-id="6209d-106">These instructions likely work with newer versions of Ubuntu, but the instructions haven't been tested with newer versions.</span></span>

<span data-ttu-id="6209d-107">Informace o dalších linuxových distribucích podporovaných ASP.NET Core najdete [v tématu Požadavky pro .NET Core v Linuxu](/dotnet/core/linux-prerequisites).</span><span class="sxs-lookup"><span data-stu-id="6209d-107">For information on other Linux distributions supported by ASP.NET Core, see [Prerequisites for .NET Core on Linux](/dotnet/core/linux-prerequisites).</span></span>

> [!NOTE]
> <span data-ttu-id="6209d-108">Pro Ubuntu 14.04 se doporučuje *dohledu* jako řešení pro sledování procesu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6209d-108">For Ubuntu 14.04, *supervisord* is recommended as a solution for monitoring the Kestrel process.</span></span> <span data-ttu-id="6209d-109">*systemd* není k dispozici na Ubuntu 14.04.</span><span class="sxs-lookup"><span data-stu-id="6209d-109">*systemd* isn't available on Ubuntu 14.04.</span></span> <span data-ttu-id="6209d-110">Pokyny k Ubuntu 14.04 najdete v [předchozí verzi tohoto tématu](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span><span class="sxs-lookup"><span data-stu-id="6209d-110">For Ubuntu 14.04 instructions, see the [previous version of this topic](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span></span>

<span data-ttu-id="6209d-111">Tato příručka:</span><span class="sxs-lookup"><span data-stu-id="6209d-111">This guide:</span></span>

* <span data-ttu-id="6209d-112">Umístí existující aplikaci ASP.NET Core za reverzní proxy server.</span><span class="sxs-lookup"><span data-stu-id="6209d-112">Places an existing ASP.NET Core app behind a reverse proxy server.</span></span>
* <span data-ttu-id="6209d-113">Nastaví reverzní proxy server pro předávání požadavků na webový server Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6209d-113">Sets up the reverse proxy server to forward requests to the Kestrel web server.</span></span>
* <span data-ttu-id="6209d-114">Zajišťuje, že webová aplikace běží při spuštění jako daemon.</span><span class="sxs-lookup"><span data-stu-id="6209d-114">Ensures the web app runs on startup as a daemon.</span></span>
* <span data-ttu-id="6209d-115">Nakonfiguruje nástroj pro správu procesů, který pomáhá restartovat webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6209d-115">Configures a process management tool to help restart the web app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6209d-116">Požadavky</span><span class="sxs-lookup"><span data-stu-id="6209d-116">Prerequisites</span></span>

1. <span data-ttu-id="6209d-117">Přístup k serveru Ubuntu 16.04 se standardním uživatelským účtem s oprávněním sudo.</span><span class="sxs-lookup"><span data-stu-id="6209d-117">Access to an Ubuntu 16.04 server with a standard user account with sudo privilege.</span></span>
1. <span data-ttu-id="6209d-118">Nainstalujte na server runtime jádra .NET.</span><span class="sxs-lookup"><span data-stu-id="6209d-118">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="6209d-119">Navštivte [stránku Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="6209d-119">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="6209d-120">Vyberte nejnovější verzi jádra .NET bez náhledu.</span><span class="sxs-lookup"><span data-stu-id="6209d-120">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="6209d-121">Stáhněte si nejnovější runtime bez náhledu v tabulce v části **Spustit aplikace - Runtime**.</span><span class="sxs-lookup"><span data-stu-id="6209d-121">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="6209d-122">Vyberte odkaz **na pokyny správce balíčků** Linuxu a postupujte podle pokynů Ubuntu pro vaši verzi Ubuntu.</span><span class="sxs-lookup"><span data-stu-id="6209d-122">Select the Linux **Package manager instructions** link and follow the Ubuntu instructions for your version of Ubuntu.</span></span>
1. <span data-ttu-id="6209d-123">Existující aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6209d-123">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="6209d-124">Kdykoli v budoucnu po upgradu sdíleného rozhraní restartujte aplikace ASP.NET Core hostované serverem.</span><span class="sxs-lookup"><span data-stu-id="6209d-124">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="6209d-125">Publikování a kopírování přes aplikaci</span><span class="sxs-lookup"><span data-stu-id="6209d-125">Publish and copy over the app</span></span>

<span data-ttu-id="6209d-126">Nakonfigurujte aplikaci pro [nasazení závislé na rámci](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="6209d-126">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="6209d-127">Pokud je aplikace spuštěná místně a není nakonfigurovaná pro zabezpečená připojení (HTTPS), přijměte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="6209d-127">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="6209d-128">Nakonfigurujte aplikaci tak, aby zpracovávala zabezpečená místní připojení.</span><span class="sxs-lookup"><span data-stu-id="6209d-128">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="6209d-129">Další informace naleznete v části [konfigurace protokolu HTTPS.](#https-configuration)</span><span class="sxs-lookup"><span data-stu-id="6209d-129">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="6209d-130">Odebrat `https://localhost:5001` (pokud je `applicationUrl` k dispozici) z vlastnosti v *properties/launchSettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="6209d-130">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="6209d-131">Spusťte [publikování dotnet](/dotnet/core/tools/dotnet-publish) z vývojového prostředí a zabalte aplikaci do adresáře (například *&lt;bin/Release/ target_framework_moniker&gt;/publish),* který lze spustit na serveru:</span><span class="sxs-lookup"><span data-stu-id="6209d-131">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="6209d-132">Aplikace může být také publikována jako [samostatné nasazení,](/dotnet/core/deploying/#self-contained-deployments-scd) pokud nechcete udržovat runtime .NET Core na serveru.</span><span class="sxs-lookup"><span data-stu-id="6209d-132">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="6209d-133">Zkopírujte aplikaci ASP.NET Core na server pomocí nástroje, který se integruje do pracovního postupu organizace (například SCP, SFTP).</span><span class="sxs-lookup"><span data-stu-id="6209d-133">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="6209d-134">Je běžné vyhledat webové aplikace v adresáři *var* (například *var/www/helloapp*).</span><span class="sxs-lookup"><span data-stu-id="6209d-134">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="6209d-135">Podle scénáře nasazení v produkčním prostředí provádí pracovní postup průběžné integrace publikování aplikace a kopírování datových zdrojů na server.</span><span class="sxs-lookup"><span data-stu-id="6209d-135">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

<span data-ttu-id="6209d-136">Otestujte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="6209d-136">Test the app:</span></span>

1. <span data-ttu-id="6209d-137">Na příkazovém řádku spusťte aplikaci: `dotnet <app_assembly>.dll`.</span><span class="sxs-lookup"><span data-stu-id="6209d-137">From the command line, run the app: `dotnet <app_assembly>.dll`.</span></span>
1. <span data-ttu-id="6209d-138">V prohlížeči přejděte na `http://<serveraddress>:<port>` ověření, že aplikace funguje na Linuxu místně.</span><span class="sxs-lookup"><span data-stu-id="6209d-138">In a browser, navigate to `http://<serveraddress>:<port>` to verify the app works on Linux locally.</span></span>

## <a name="configure-a-reverse-proxy-server"></a><span data-ttu-id="6209d-139">Konfigurace serveru reverzního proxy serveru</span><span class="sxs-lookup"><span data-stu-id="6209d-139">Configure a reverse proxy server</span></span>

<span data-ttu-id="6209d-140">Reverzní proxy je běžné nastavení pro zobrazování dynamických webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="6209d-140">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="6209d-141">Reverzní proxy ukončí požadavek HTTP a předá jej do aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6209d-141">A reverse proxy terminates the HTTP request and forwards it to the ASP.NET Core app.</span></span>

### <a name="use-a-reverse-proxy-server"></a><span data-ttu-id="6209d-142">Použití reverzního proxy serveru</span><span class="sxs-lookup"><span data-stu-id="6209d-142">Use a reverse proxy server</span></span>

<span data-ttu-id="6209d-143">Kestrel je skvělý pro podávání dynamického obsahu z ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6209d-143">Kestrel is great for serving dynamic content from ASP.NET Core.</span></span> <span data-ttu-id="6209d-144">Možnosti webové hosvírování však nejsou tak bohaté na funkce jako servery, jako je Služba IIS, Apache nebo Nginx.</span><span class="sxs-lookup"><span data-stu-id="6209d-144">However, the web serving capabilities aren't as feature rich as servers such as IIS, Apache, or Nginx.</span></span> <span data-ttu-id="6209d-145">Reverzní proxy server může offload práce, jako je například obsluha statického obsahu, ukládání požadavků do mezipaměti, komprese požadavků a ukončení HTTPS ze serveru HTTP.</span><span class="sxs-lookup"><span data-stu-id="6209d-145">A reverse proxy server can offload work such as serving static content, caching requests, compressing requests, and HTTPS termination from the HTTP server.</span></span> <span data-ttu-id="6209d-146">Reverzní proxy server může být umístěn na vyhrazeném počítači nebo může být nasazen společně se serverem HTTP.</span><span class="sxs-lookup"><span data-stu-id="6209d-146">A reverse proxy server may reside on a dedicated machine or may be deployed alongside an HTTP server.</span></span>

<span data-ttu-id="6209d-147">Pro účely této příručky se používá jedna instance Nginx.</span><span class="sxs-lookup"><span data-stu-id="6209d-147">For the purposes of this guide, a single instance of Nginx is used.</span></span> <span data-ttu-id="6209d-148">Běží na stejném serveru, vedle http serveru.</span><span class="sxs-lookup"><span data-stu-id="6209d-148">It runs on the same server, alongside the HTTP server.</span></span> <span data-ttu-id="6209d-149">Na základě požadavků může být zvoleno jiné nastavení.</span><span class="sxs-lookup"><span data-stu-id="6209d-149">Based on requirements, a different setup may be chosen.</span></span>

<span data-ttu-id="6209d-150">Vzhledem k tomu, že požadavky jsou předávány reverzním proxy serverem, použijte [middleware s předanými hlavičkami](xref:host-and-deploy/proxy-load-balancer) z balíčku [Microsoft.AspNetCore.HttpOverrides.](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/)</span><span class="sxs-lookup"><span data-stu-id="6209d-150">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="6209d-151">Middleware aktualizuje `Request.Scheme`, pomocí `X-Forwarded-Proto` záhlaví, takže přesměrování IDENTIFIKÁTORŮ URI a dalších zásad zabezpečení pracovat správně.</span><span class="sxs-lookup"><span data-stu-id="6209d-151">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="6209d-152">Každá součást, která závisí na schématu, jako je ověřování, generování propojení, přesměrování a geografické umístění, musí být umístěna po vyvolání middlewaru předávaných záhlaví.</span><span class="sxs-lookup"><span data-stu-id="6209d-152">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span> <span data-ttu-id="6209d-153">Obecně platí, že middleware s předaným záhlavím by měl být spuštěn před jiným middlewarem s výjimkou diagnostiky a zpracování chyb middlewaru.</span><span class="sxs-lookup"><span data-stu-id="6209d-153">As a general rule, Forwarded Headers Middleware should run before other middleware except diagnostics and error handling middleware.</span></span> <span data-ttu-id="6209d-154">Toto pořadí zajišťuje, že middleware spoléhající na předávané informace záhlaví může spotřebovat hodnoty záhlaví pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="6209d-154">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span>

<span data-ttu-id="6209d-155">Vyvolat <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodu v `Startup.Configure` horní části před voláním jiné middleware.</span><span class="sxs-lookup"><span data-stu-id="6209d-155">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="6209d-156">Nakonfigurujte middleware tak, aby předával záhlaví `X-Forwarded-For` a: `X-Forwarded-Proto`</span><span class="sxs-lookup"><span data-stu-id="6209d-156">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="6209d-157">Pokud <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> middlewaru nezadali, výchozí hlavičky `None`pro předávání jsou .</span><span class="sxs-lookup"><span data-stu-id="6209d-157">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="6209d-158">Proxy servery spuštěné na adresách zpětné smyčky (127.0.0.0/8, [::1]), včetně standardní adresy localhost (127.0.0.1), jsou ve výchozím nastavení důvěryhodné.</span><span class="sxs-lookup"><span data-stu-id="6209d-158">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="6209d-159">Pokud jiné důvěryhodné servery proxy nebo sítě v rámci organizace zpracovávají požadavky mezi <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> Internetem a webovým serverem, přidejte je do seznamu nebo <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>aplikací .</span><span class="sxs-lookup"><span data-stu-id="6209d-159">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="6209d-160">Následující příklad přidá důvěryhodný proxy server na adrese IP 10.0.0.100 `KnownProxies` do `Startup.ConfigureServices`middlewaru Předávaných záhlaví v :</span><span class="sxs-lookup"><span data-stu-id="6209d-160">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="6209d-161">Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="6209d-161">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-nginx"></a><span data-ttu-id="6209d-162">Instalace Nginx</span><span class="sxs-lookup"><span data-stu-id="6209d-162">Install Nginx</span></span>

<span data-ttu-id="6209d-163">Slouží `apt-get` k instalaci Nginx.</span><span class="sxs-lookup"><span data-stu-id="6209d-163">Use `apt-get` to install Nginx.</span></span> <span data-ttu-id="6209d-164">Instalátor vytvoří *systemd* init skript, který běží Nginx jako daemon při spuštění systému.</span><span class="sxs-lookup"><span data-stu-id="6209d-164">The installer creates a *systemd* init script that runs Nginx as daemon on system startup.</span></span> <span data-ttu-id="6209d-165">Postupujte podle pokynů k instalaci pro Ubuntu na [Nginx: Oficiální Debian / Ubuntu balíčky](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span><span class="sxs-lookup"><span data-stu-id="6209d-165">Follow the installation instructions for Ubuntu at [Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span></span>

> [!NOTE]
> <span data-ttu-id="6209d-166">Pokud jsou požadovány volitelné moduly Nginx, může být vyžadována stavba Nginx ze zdroje.</span><span class="sxs-lookup"><span data-stu-id="6209d-166">If optional Nginx modules are required, building Nginx from source might be required.</span></span>

<span data-ttu-id="6209d-167">Vzhledem k tomu, že nginx byl nainstalován poprvé, explicitně spustit spuštěním:</span><span class="sxs-lookup"><span data-stu-id="6209d-167">Since Nginx was installed for the first time, explicitly start it by running:</span></span>

```bash
sudo service nginx start
```

<span data-ttu-id="6209d-168">Ověřte, zda prohlížeč zobrazuje výchozí vstupní stránku nginxu.</span><span class="sxs-lookup"><span data-stu-id="6209d-168">Verify a browser displays the default landing page for Nginx.</span></span> <span data-ttu-id="6209d-169">Vstupní stránka je dostupná na adrese `http://<server_IP_address>/index.nginx-debian.html`.</span><span class="sxs-lookup"><span data-stu-id="6209d-169">The landing page is reachable at `http://<server_IP_address>/index.nginx-debian.html`.</span></span>

### <a name="configure-nginx"></a><span data-ttu-id="6209d-170">Konfigurace služby Nginx</span><span class="sxs-lookup"><span data-stu-id="6209d-170">Configure Nginx</span></span>

<span data-ttu-id="6209d-171">Chcete-li nakonfigurovat Nginx jako reverzní proxy server pro předávání požadavků na aplikaci ASP.NET Core, upravte */etc/nginx/sites-available/default*.</span><span class="sxs-lookup"><span data-stu-id="6209d-171">To configure Nginx as a reverse proxy to forward requests to your ASP.NET Core app, modify */etc/nginx/sites-available/default*.</span></span> <span data-ttu-id="6209d-172">Otevřete jej v textovém editoru a nahraďte obsah následujícím:</span><span class="sxs-lookup"><span data-stu-id="6209d-172">Open it in a text editor, and replace the contents with the following:</span></span>

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

<span data-ttu-id="6209d-173">Pokud je aplikace aplikace Blazor Server, která závisí na <xref:host-and-deploy/blazor/server#linux-with-nginx> SignalR WebSockets, `Connection` přečtěte si informace o tom, jak nastavit záhlaví.</span><span class="sxs-lookup"><span data-stu-id="6209d-173">If the app is a Blazor Server app that relies on SignalR WebSockets, see <xref:host-and-deploy/blazor/server#linux-with-nginx> for information on how to set the `Connection` header.</span></span>

<span data-ttu-id="6209d-174">Pokud `server_name` žádná shoda, Nginx používá výchozí server.</span><span class="sxs-lookup"><span data-stu-id="6209d-174">When no `server_name` matches, Nginx uses the default server.</span></span> <span data-ttu-id="6209d-175">Pokud není definován žádný výchozí server, je výchozím serverem první server v konfiguračním souboru.</span><span class="sxs-lookup"><span data-stu-id="6209d-175">If no default server is defined, the first server in the configuration file is the default server.</span></span> <span data-ttu-id="6209d-176">Jako osvědčený postup přidejte konkrétní výchozí server, který vrátí stavový kód 444 v konfiguračním souboru.</span><span class="sxs-lookup"><span data-stu-id="6209d-176">As a best practice, add a specific default server which returns a status code of 444 in your configuration file.</span></span> <span data-ttu-id="6209d-177">Výchozí příklad konfigurace serveru je:</span><span class="sxs-lookup"><span data-stu-id="6209d-177">A default server configuration example is:</span></span>

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

<span data-ttu-id="6209d-178">S předchozím konfiguračním souborem a výchozím serverem nginx `example.com` přijímá `*.example.com`veřejný provoz na portu 80 s hlavičkou hostitele nebo .</span><span class="sxs-lookup"><span data-stu-id="6209d-178">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="6209d-179">Požadavky, které neodpovídají těmto hostitelům, nebudou předány ke kestrelu.</span><span class="sxs-lookup"><span data-stu-id="6209d-179">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="6209d-180">Nginx předá odpovídající požadavky Kestrel `http://localhost:5000`na .</span><span class="sxs-lookup"><span data-stu-id="6209d-180">Nginx forwards the matching requests to Kestrel at `http://localhost:5000`.</span></span> <span data-ttu-id="6209d-181">Podívejte [se, jak nginx zpracovává žádost o](https://nginx.org/docs/http/request_processing.html) další informace.</span><span class="sxs-lookup"><span data-stu-id="6209d-181">See [How nginx processes a request](https://nginx.org/docs/http/request_processing.html) for more information.</span></span> <span data-ttu-id="6209d-182">Chcete-li změnit protokol Kestrel IP/port, přečtěte si část [Kestrel: Konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="6209d-182">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="6209d-183">Pokud nezadáte správnou [server_name direktivu,](https://nginx.org/docs/http/server_names.html) aplikace se zpřístupní slabým místům zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="6209d-183">Failure to specify a proper [server_name directive](https://nginx.org/docs/http/server_names.html) exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="6209d-184">Vazba se zástupnými znaky subdomény (například) nepředstavuje toto bezpečnostní riziko, `*.example.com` `*.com`pokud řídíte celou nadřazenou doménu (na rozdíl od , která je zranitelná).</span><span class="sxs-lookup"><span data-stu-id="6209d-184">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="6209d-185">Viz [rfc7230 sekce-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) pro více informací.</span><span class="sxs-lookup"><span data-stu-id="6209d-185">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="6209d-186">Po navázání konfigurace Nginx `sudo nginx -t` spusťte ověření syntaxe konfiguračních souborů.</span><span class="sxs-lookup"><span data-stu-id="6209d-186">Once the Nginx configuration is established, run `sudo nginx -t` to verify the syntax of the configuration files.</span></span> <span data-ttu-id="6209d-187">Pokud je test konfiguračního souboru úspěšný, vynuťte nginx, aby změny zachytil spuštěním `sudo nginx -s reload`.</span><span class="sxs-lookup"><span data-stu-id="6209d-187">If the configuration file test is successful, force Nginx to pick up the changes by running `sudo nginx -s reload`.</span></span>

<span data-ttu-id="6209d-188">Přímé spuštění aplikace na serveru:</span><span class="sxs-lookup"><span data-stu-id="6209d-188">To directly run the app on the server:</span></span>

1. <span data-ttu-id="6209d-189">Přejděte do adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="6209d-189">Navigate to the app's directory.</span></span>
1. <span data-ttu-id="6209d-190">Spusťte `dotnet <app_assembly.dll>`aplikaci: , kde `app_assembly.dll` je název souboru sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="6209d-190">Run the app: `dotnet <app_assembly.dll>`, where `app_assembly.dll` is the assembly file name of the app.</span></span>

<span data-ttu-id="6209d-191">Pokud aplikace běží na serveru, ale nereaguje přes Internet, zkontrolujte bránu firewall serveru a zkontrolujte, zda je otevřený port 80.</span><span class="sxs-lookup"><span data-stu-id="6209d-191">If the app runs on the server but fails to respond over the Internet, check the server's firewall and confirm that port 80 is open.</span></span> <span data-ttu-id="6209d-192">Pokud používáte virtuální počítač Azure Ubuntu, přidejte pravidlo skupiny zabezpečení sítě (NSG), které umožňuje příchozí port 80 provoz.</span><span class="sxs-lookup"><span data-stu-id="6209d-192">If using an Azure Ubuntu VM, add a Network Security Group (NSG) rule that enables inbound port 80 traffic.</span></span> <span data-ttu-id="6209d-193">Není nutné povolit pravidlo odchozího portu 80, protože odchozí provoz je automaticky udělen, když je povoleno příchozí pravidlo.</span><span class="sxs-lookup"><span data-stu-id="6209d-193">There's no need to enable an outbound port 80 rule, as the outbound traffic is automatically granted when the inbound rule is enabled.</span></span>

<span data-ttu-id="6209d-194">Po dokončení testování aplikace, vypněte `Ctrl+C` aplikaci s na příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="6209d-194">When done testing the app, shut the app down with `Ctrl+C` at the command prompt.</span></span>

## <a name="monitor-the-app"></a><span data-ttu-id="6209d-195">Sledování aplikace</span><span class="sxs-lookup"><span data-stu-id="6209d-195">Monitor the app</span></span>

<span data-ttu-id="6209d-196">Server je nastaven tak, aby `http://<serveraddress>:80` předával požadavky na aplikaci ASP.NET `http://127.0.0.1:5000`Core spuštěnou na kestrelu na adrese .</span><span class="sxs-lookup"><span data-stu-id="6209d-196">The server is setup to forward requests made to `http://<serveraddress>:80` on to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="6209d-197">Nginx však není nastaven pro správu procesu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6209d-197">However, Nginx isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="6209d-198">*systemd* lze použít k vytvoření souboru služby pro spuštění a sledování základní webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="6209d-198">*systemd* can be used to create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="6209d-199">*systemd* je init systém, který poskytuje mnoho výkonných funkcí pro spouštění, zastavování a správu procesů.</span><span class="sxs-lookup"><span data-stu-id="6209d-199">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="6209d-200">Vytvoření souboru služby</span><span class="sxs-lookup"><span data-stu-id="6209d-200">Create the service file</span></span>

<span data-ttu-id="6209d-201">Vytvořte soubor definice služby:</span><span class="sxs-lookup"><span data-stu-id="6209d-201">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="6209d-202">Následuje ukázkový soubor služby pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="6209d-202">The following is an example service file for the app:</span></span>

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="6209d-203">V předchozím příkladu je uživatel, který spravuje službu, určen `User` možností.</span><span class="sxs-lookup"><span data-stu-id="6209d-203">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="6209d-204">Uživatel (`www-data`) musí existovat a mít správné vlastnictví souborů aplikace.</span><span class="sxs-lookup"><span data-stu-id="6209d-204">The user (`www-data`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="6209d-205">Slouží `TimeoutStopSec` ke konfiguraci doby čekání na vypnutí aplikace po přijetí signálu počátečního přerušení.</span><span class="sxs-lookup"><span data-stu-id="6209d-205">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="6209d-206">Pokud se aplikace v tomto období nevypne, je vydána aplikace SIGKILL, aby aplikaci ukončila.</span><span class="sxs-lookup"><span data-stu-id="6209d-206">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="6209d-207">Zadejte hodnotu jako sekundy `150`bez jednotky (například ), `2min 30s`hodnotu `infinity` časového rozpětí (například ) nebo zakázat časový rozsah.</span><span class="sxs-lookup"><span data-stu-id="6209d-207">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="6209d-208">`TimeoutStopSec`výchozí hodnota v `DefaultTimeoutStopSec` konfiguračním souboru správce (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span><span class="sxs-lookup"><span data-stu-id="6209d-208">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="6209d-209">Výchozí časový limit pro většinu distribucí je 90 sekund.</span><span class="sxs-lookup"><span data-stu-id="6209d-209">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="6209d-210">Linux má systém souborů rozlišující malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="6209d-210">Linux has a case-sensitive file system.</span></span> <span data-ttu-id="6209d-211">Nastavení ASPNETCORE_ENVIRONMENT na "Produkční" má za následek hledání nastavení konfiguračního *souboru. Production.json*, ne *appsettings.production.json*.</span><span class="sxs-lookup"><span data-stu-id="6209d-211">Setting ASPNETCORE_ENVIRONMENT to "Production" results in searching for the configuration file *appsettings.Production.json*, not *appsettings.production.json*.</span></span>

<span data-ttu-id="6209d-212">Některé hodnoty (například připojovací řetězce SQL) musí být uvozeny, aby zprostředkovatelé konfigurace mohli číst proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="6209d-212">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="6209d-213">Pomocí následujícího příkazu vygenerujte správně uvozenou hodnotu pro použití v konfiguračním souboru:</span><span class="sxs-lookup"><span data-stu-id="6209d-213">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

<span data-ttu-id="6209d-214">Oddělovače dvojtečky (`:`) nejsou v názvech proměnných prostředí podporovány.</span><span class="sxs-lookup"><span data-stu-id="6209d-214">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="6209d-215">Místo dvojtečky`__`použijte dvojité podtržítko ( ).</span><span class="sxs-lookup"><span data-stu-id="6209d-215">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="6209d-216">[Zprostředkovatel konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) převádí dvojité podtržítka na dvojtečky při čtení proměnných prostředí do konfigurace.</span><span class="sxs-lookup"><span data-stu-id="6209d-216">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="6209d-217">V následujícím příkladu je `ConnectionStrings:DefaultConnection` klíč připojovacího `ConnectionStrings__DefaultConnection`řetězce nastaven do souboru definice služby jako :</span><span class="sxs-lookup"><span data-stu-id="6209d-217">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="6209d-218">Uložte soubor a povolte službu.</span><span class="sxs-lookup"><span data-stu-id="6209d-218">Save the file and enable the service.</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="6209d-219">Spusťte službu a ověřte, zda je spuštěná.</span><span class="sxs-lookup"><span data-stu-id="6209d-219">Start the service and verify that it's running.</span></span>

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="6209d-220">S reverzní proxy nakonfigurován a Kestrel spravuje prostřednictvím systemd, webová aplikace je plně `http://localhost`nakonfigurován a lze přistupovat z prohlížeče na místním počítači na .</span><span class="sxs-lookup"><span data-stu-id="6209d-220">With the reverse proxy configured and Kestrel managed through systemd, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="6209d-221">Je také přístupný ze vzdáleného počítače, blokování firewallu, který by mohl blokovat.</span><span class="sxs-lookup"><span data-stu-id="6209d-221">It's also accessible from a remote machine, barring any firewall that might be blocking.</span></span> <span data-ttu-id="6209d-222">Při kontrole záhlaví odpovědí `Server` se v záhlaví zobrazí aplikace ASP.NET Core obsluhovaná společností Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6209d-222">Inspecting the response headers, the `Server` header shows the ASP.NET Core app being served by Kestrel.</span></span>

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="6209d-223">Zobrazení protokolů</span><span class="sxs-lookup"><span data-stu-id="6209d-223">View logs</span></span>

<span data-ttu-id="6209d-224">Vzhledem k tomu, že webová aplikace pomocí Kestrel je spravována pomocí `systemd`, všechny události a procesy jsou protokolovány do centralizovaného deníku.</span><span class="sxs-lookup"><span data-stu-id="6209d-224">Since the web app using Kestrel is managed using `systemd`, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="6209d-225">Tento deník však obsahuje všechny položky `systemd`pro všechny služby a procesy spravované společností .</span><span class="sxs-lookup"><span data-stu-id="6209d-225">However, this journal includes all entries for all services and processes managed by `systemd`.</span></span> <span data-ttu-id="6209d-226">Chcete-li `kestrel-helloapp.service`zobrazit specifické položky, použijte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="6209d-226">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="6209d-227">Pro další filtrování mohou možnosti času, například `--since today`, `--until 1 hour ago` nebo jejich kombinace snížit množství vrácených položek.</span><span class="sxs-lookup"><span data-stu-id="6209d-227">For further filtering, time options such as `--since today`, `--until 1 hour ago` or a combination of these can reduce the amount of entries returned.</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="6209d-228">Ochrana dat</span><span class="sxs-lookup"><span data-stu-id="6209d-228">Data protection</span></span>

<span data-ttu-id="6209d-229">Zásobník [ASP.NET Core Data Protection](xref:security/data-protection/introduction) používá několik [middlewares](xref:fundamentals/middleware/index)ASP.NET Core , včetně autentizačního middlewaru (například middleware souborů cookie) a ochrany padělání požadavků na více webů (CSRF).</span><span class="sxs-lookup"><span data-stu-id="6209d-229">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="6209d-230">I v případě, že rozhraní API ochrany dat nejsou volána uživatelským kódem, ochrana dat by měla být nakonfigurována tak, aby vytvořila trvalé úložiště kryptografických [klíčů](xref:security/data-protection/implementation/key-management).</span><span class="sxs-lookup"><span data-stu-id="6209d-230">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="6209d-231">Pokud ochrana dat není nakonfigurována, klíče jsou uloženy v paměti a po restartování aplikace zahozeny.</span><span class="sxs-lookup"><span data-stu-id="6209d-231">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="6209d-232">Pokud je kroužek klíče uložen v paměti při restartování aplikace:</span><span class="sxs-lookup"><span data-stu-id="6209d-232">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="6209d-233">Všechny ověřovací tokeny založené na souborech cookie jsou zrušeny.</span><span class="sxs-lookup"><span data-stu-id="6209d-233">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="6209d-234">Uživatelé se musí znovu přihlásit při dalším požadavku.</span><span class="sxs-lookup"><span data-stu-id="6209d-234">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="6209d-235">Všechna data chráněná kroužkem klíče již nelze dešifrovat.</span><span class="sxs-lookup"><span data-stu-id="6209d-235">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="6209d-236">To může zahrnovat [tokeny CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [ASP.NET základní soubory cookie MVC TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="6209d-236">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="6209d-237">Chcete-li nakonfigurovat ochranu dat tak, aby byla zachována a zašifrována kroužek klíče, přečtěte si následující:</span><span class="sxs-lookup"><span data-stu-id="6209d-237">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a><span data-ttu-id="6209d-238">Pole záhlaví dlouhého požadavku</span><span class="sxs-lookup"><span data-stu-id="6209d-238">Long request header fields</span></span>

<span data-ttu-id="6209d-239">Výchozí nastavení proxy serveru obvykle omezují pole hlaviček požadavků na 4 K nebo 8 K v závislosti na platformě.</span><span class="sxs-lookup"><span data-stu-id="6209d-239">Proxy server default settings typically limit request header fields to 4 K or 8 K depending on the platform.</span></span> <span data-ttu-id="6209d-240">Aplikace může vyžadovat pole delší než výchozí (například aplikace, které používají [Azure Active Directory).](https://azure.microsoft.com/services/active-directory/)</span><span class="sxs-lookup"><span data-stu-id="6209d-240">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="6209d-241">Pokud jsou vyžadována delší pole, výchozí nastavení proxy serveru vyžaduje úpravy.</span><span class="sxs-lookup"><span data-stu-id="6209d-241">If longer fields are required, the proxy server's default settings require adjustment.</span></span> <span data-ttu-id="6209d-242">Hodnoty, které mají být aplikovány, závisí na scénáři.</span><span class="sxs-lookup"><span data-stu-id="6209d-242">The values to apply depend on the scenario.</span></span> <span data-ttu-id="6209d-243">Další informace naleznete v dokumentaci k serveru.</span><span class="sxs-lookup"><span data-stu-id="6209d-243">For more information, see your server's documentation.</span></span>

* [<span data-ttu-id="6209d-244">proxy_buffer_size</span><span class="sxs-lookup"><span data-stu-id="6209d-244">proxy_buffer_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [<span data-ttu-id="6209d-245">proxy_buffers</span><span class="sxs-lookup"><span data-stu-id="6209d-245">proxy_buffers</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [<span data-ttu-id="6209d-246">proxy_busy_buffers_size</span><span class="sxs-lookup"><span data-stu-id="6209d-246">proxy_busy_buffers_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [<span data-ttu-id="6209d-247">large_client_header_buffers</span><span class="sxs-lookup"><span data-stu-id="6209d-247">large_client_header_buffers</span></span>](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> <span data-ttu-id="6209d-248">Pokud to není nutné, nezvyšujte výchozí hodnoty proxy vyrovnávacích pamětí.</span><span class="sxs-lookup"><span data-stu-id="6209d-248">Don't increase the default values of proxy buffers unless necessary.</span></span> <span data-ttu-id="6209d-249">Zvýšení těchto hodnot zvyšuje riziko přetečení vyrovnávací paměti (přetečení) a odmítnutí služby (DoS) útoky uživatelů se zlými úmysly.</span><span class="sxs-lookup"><span data-stu-id="6209d-249">Increasing these values increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="secure-the-app"></a><span data-ttu-id="6209d-250">Zabezpečení aplikace</span><span class="sxs-lookup"><span data-stu-id="6209d-250">Secure the app</span></span>

### <a name="enable-apparmor"></a><span data-ttu-id="6209d-251">Povolit AppArmor</span><span class="sxs-lookup"><span data-stu-id="6209d-251">Enable AppArmor</span></span>

<span data-ttu-id="6209d-252">Linux Security Modules (LSM) je framework, který je součástí linuxového jádra od Linuxu 2.6.</span><span class="sxs-lookup"><span data-stu-id="6209d-252">Linux Security Modules (LSM) is a framework that's part of the Linux kernel since Linux 2.6.</span></span> <span data-ttu-id="6209d-253">LSM podporuje různé implementace bezpečnostních modulů.</span><span class="sxs-lookup"><span data-stu-id="6209d-253">LSM supports different implementations of security modules.</span></span> <span data-ttu-id="6209d-254">[AppArmor](https://wiki.ubuntu.com/AppArmor) je LSM, který implementuje systém povinného řízení přístupu, který umožňuje omezit program na omezenou sadu prostředků.</span><span class="sxs-lookup"><span data-stu-id="6209d-254">[AppArmor](https://wiki.ubuntu.com/AppArmor) is a LSM that implements a Mandatory Access Control system which allows confining the program to a limited set of resources.</span></span> <span data-ttu-id="6209d-255">Ujistěte se, že apparmor je povolena a správně nakonfigurována.</span><span class="sxs-lookup"><span data-stu-id="6209d-255">Ensure AppArmor is enabled and properly configured.</span></span>

### <a name="configure-the-firewall"></a><span data-ttu-id="6209d-256">Konfigurace brány firewall</span><span class="sxs-lookup"><span data-stu-id="6209d-256">Configure the firewall</span></span>

<span data-ttu-id="6209d-257">Zavřete všechny externí porty, které se nepoužívají.</span><span class="sxs-lookup"><span data-stu-id="6209d-257">Close off all external ports that are not in use.</span></span> <span data-ttu-id="6209d-258">Nekomplikovaná brána firewall (ufw) poskytuje front-end `iptables` tím, že poskytuje CLI pro konfiguraci brány firewall.</span><span class="sxs-lookup"><span data-stu-id="6209d-258">Uncomplicated firewall (ufw) provides a front end for `iptables` by providing a CLI for configuring the firewall.</span></span>

> [!WARNING]
> <span data-ttu-id="6209d-259">Brána firewall zabrání přístupu k celému systému, pokud není správně nakonfigurován.</span><span class="sxs-lookup"><span data-stu-id="6209d-259">A firewall will prevent access to the whole system if not configured correctly.</span></span> <span data-ttu-id="6209d-260">Pokud nezadáte správný port SSH, efektivně vás uzamknete ze systému, pokud k němu používáte SSH.</span><span class="sxs-lookup"><span data-stu-id="6209d-260">Failure to specify the correct SSH port will effectively lock you out of the system if you are using SSH to connect to it.</span></span> <span data-ttu-id="6209d-261">Výchozí port je 22.</span><span class="sxs-lookup"><span data-stu-id="6209d-261">The default port is 22.</span></span> <span data-ttu-id="6209d-262">Další informace naleznete v [úvodu k ufw](https://help.ubuntu.com/community/UFW) a [manuálu](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span><span class="sxs-lookup"><span data-stu-id="6209d-262">For more information, see the [introduction to ufw](https://help.ubuntu.com/community/UFW) and the [manual](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span></span>

<span data-ttu-id="6209d-263">Nainstalujte `ufw` a nakonfigurujte jej tak, aby umožňoval přenos na všech potřebných portech.</span><span class="sxs-lookup"><span data-stu-id="6209d-263">Install `ufw` and configure it to allow traffic on any ports needed.</span></span>

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a><span data-ttu-id="6209d-264">Bezpečné Nginx</span><span class="sxs-lookup"><span data-stu-id="6209d-264">Secure Nginx</span></span>

#### <a name="change-the-nginx-response-name"></a><span data-ttu-id="6209d-265">Změna názvu odpovědi Nginx</span><span class="sxs-lookup"><span data-stu-id="6209d-265">Change the Nginx response name</span></span>

<span data-ttu-id="6209d-266">Upravit *src/http/ngx_http_header_filter_module.c*:</span><span class="sxs-lookup"><span data-stu-id="6209d-266">Edit *src/http/ngx_http_header_filter_module.c*:</span></span>

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a><span data-ttu-id="6209d-267">Konfigurace možností</span><span class="sxs-lookup"><span data-stu-id="6209d-267">Configure options</span></span>

<span data-ttu-id="6209d-268">Nakonfigurujte server pomocí dalších požadovaných modulů.</span><span class="sxs-lookup"><span data-stu-id="6209d-268">Configure the server with additional required modules.</span></span> <span data-ttu-id="6209d-269">Zvažte použití brány firewall webové aplikace, jako je [ModSecurity](https://www.modsecurity.org/), k posílení zabezpečení aplikace.</span><span class="sxs-lookup"><span data-stu-id="6209d-269">Consider using a web app firewall, such as [ModSecurity](https://www.modsecurity.org/), to harden the app.</span></span>

#### <a name="https-configuration"></a><span data-ttu-id="6209d-270">Konfigurace PROTOKOLU HTTPS</span><span class="sxs-lookup"><span data-stu-id="6209d-270">HTTPS configuration</span></span>

<span data-ttu-id="6209d-271">**Konfigurace aplikace pro zabezpečená místní připojení (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="6209d-271">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="6209d-272">Příkaz [dotnet run](/dotnet/core/tools/dotnet-run) používá soubor *Vlastnosti/launchSettings.json* aplikace, který konfiguruje aplikaci tak, aby naslouchala adresám URL poskytovaným `applicationUrl` vlastností (například ). `https://localhost:5001;http://localhost:5000`</span><span class="sxs-lookup"><span data-stu-id="6209d-272">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="6209d-273">Nakonfigurujte aplikaci tak, `dotnet run` aby používala certifikát ve vývoji pro prostředí příkazů nebo vývoje (F5 nebo Ctrl+F5 v kódu Visual Studia) pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="6209d-273">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="6209d-274">[Nahrazení výchozího certifikátu z konfigurace](xref:fundamentals/servers/kestrel#configuration) *(doporučeno)*</span><span class="sxs-lookup"><span data-stu-id="6209d-274">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="6209d-275">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="6209d-275">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="6209d-276">**Konfigurace reverzního proxy serveru pro zabezpečená připojení klientů (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="6209d-276">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

* <span data-ttu-id="6209d-277">Nakonfigurujte server tak, `443` aby naslouchal přenosům HTTPS na portu zadáním platného certifikátu vydaného důvěryhodnou certifikační autoritou (CA).</span><span class="sxs-lookup"><span data-stu-id="6209d-277">Configure the server to listen to HTTPS traffic on port `443` by specifying a valid certificate issued by a trusted Certificate Authority (CA).</span></span>

* <span data-ttu-id="6209d-278">Zpevněte zabezpečení použitím některých postupů zobrazených v následujícím souboru */etc/nginx/nginx.conf.*</span><span class="sxs-lookup"><span data-stu-id="6209d-278">Harden the security by employing some of the practices depicted in the following */etc/nginx/nginx.conf* file.</span></span> <span data-ttu-id="6209d-279">Mezi příklady patří výběr silnější šifry a přesměrování veškerého provozu přes PROTOKOL HTTP na protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6209d-279">Examples include choosing a stronger cipher and redirecting all traffic over HTTP to HTTPS.</span></span>

* <span data-ttu-id="6209d-280">Přidání `HTTP Strict-Transport-Security` hlavičky (HSTS) zajišťuje, že všechny následné požadavky provedené klientem jsou přes protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6209d-280">Adding an `HTTP Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS.</span></span>

* <span data-ttu-id="6209d-281">Nepřidávejte hlavičku HSTS ani `max-age` nezvolte vhodné, pokud https bude zakázán v budoucnu.</span><span class="sxs-lookup"><span data-stu-id="6209d-281">Don't add the HSTS header or chose an appropriate `max-age` if HTTPS will be disabled in the future.</span></span>

<span data-ttu-id="6209d-282">Přidejte konfigurační soubor */etc/nginx/proxy.conf:*</span><span class="sxs-lookup"><span data-stu-id="6209d-282">Add the */etc/nginx/proxy.conf* configuration file:</span></span>

[!code-nginx[](linux-nginx/proxy.conf)]

<span data-ttu-id="6209d-283">Upravte konfigurační soubor */etc/nginx/nginx.conf.*</span><span class="sxs-lookup"><span data-stu-id="6209d-283">Edit the */etc/nginx/nginx.conf* configuration file.</span></span> <span data-ttu-id="6209d-284">Příklad obsahuje `http` oddíly v `server` jednom konfiguračním souboru.</span><span class="sxs-lookup"><span data-stu-id="6209d-284">The example contains both `http` and `server` sections in one configuration file.</span></span>

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

#### <a name="secure-nginx-from-clickjacking"></a><span data-ttu-id="6209d-285">Zabezpečit Nginx z clickjacking</span><span class="sxs-lookup"><span data-stu-id="6209d-285">Secure Nginx from clickjacking</span></span>

<span data-ttu-id="6209d-286">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), také známý jako *útok na nápravu ui*, je škodlivý útok, kdy je návštěvník webových stránek podveden, aby klikl na odkaz nebo tlačítko na jiné stránce, než je v současné době na návštěvě.</span><span class="sxs-lookup"><span data-stu-id="6209d-286">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="6209d-287">Slouží `X-FRAME-OPTIONS` k zabezpečení webu.</span><span class="sxs-lookup"><span data-stu-id="6209d-287">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="6209d-288">Chcete-li zmírnit clickjacking útoky:</span><span class="sxs-lookup"><span data-stu-id="6209d-288">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="6209d-289">Upravte soubor *nginx.conf:*</span><span class="sxs-lookup"><span data-stu-id="6209d-289">Edit the *nginx.conf* file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="6209d-290">Přidejte řádek `add_header X-Frame-Options "SAMEORIGIN";`.</span><span class="sxs-lookup"><span data-stu-id="6209d-290">Add the line `add_header X-Frame-Options "SAMEORIGIN";`.</span></span>
1. <span data-ttu-id="6209d-291">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="6209d-291">Save the file.</span></span>
1. <span data-ttu-id="6209d-292">Restartujte nginx.</span><span class="sxs-lookup"><span data-stu-id="6209d-292">Restart Nginx.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="6209d-293">Čichání typu MIME</span><span class="sxs-lookup"><span data-stu-id="6209d-293">MIME-type sniffing</span></span>

<span data-ttu-id="6209d-294">Toto záhlaví zabraňuje většině prohlížečů z MIME-sniffing odpověď od deklarovaného typu obsahu, jako záhlaví pokyn prohlížeč není přepsat typ obsahu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="6209d-294">This header prevents most browsers from MIME-sniffing a response away from the declared content type, as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="6209d-295">S `nosniff` možností, pokud server říká, že obsah je "text / html", prohlížeč vykresluje jako "text / html".</span><span class="sxs-lookup"><span data-stu-id="6209d-295">With the `nosniff` option, if the server says the content is "text/html", the browser renders it as "text/html".</span></span>

<span data-ttu-id="6209d-296">Upravte soubor *nginx.conf:*</span><span class="sxs-lookup"><span data-stu-id="6209d-296">Edit the *nginx.conf* file:</span></span>

```bash
sudo nano /etc/nginx/nginx.conf
```

<span data-ttu-id="6209d-297">Přidejte `add_header X-Content-Type-Options "nosniff";` řádek a uložte soubor a restartujte Nginx.</span><span class="sxs-lookup"><span data-stu-id="6209d-297">Add the line `add_header X-Content-Type-Options "nosniff";` and save the file, then restart Nginx.</span></span>

## <a name="additional-nginx-suggestions"></a><span data-ttu-id="6209d-298">Další návrhy Nginx</span><span class="sxs-lookup"><span data-stu-id="6209d-298">Additional Nginx suggestions</span></span>

<span data-ttu-id="6209d-299">Po upgradu sdíleného rozhraní na serveru restartujte aplikace ASP.NET Core hostované serverem.</span><span class="sxs-lookup"><span data-stu-id="6209d-299">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6209d-300">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6209d-300">Additional resources</span></span>

* [<span data-ttu-id="6209d-301">Předpoklady pro .NET Core na Linuxu</span><span class="sxs-lookup"><span data-stu-id="6209d-301">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* [<span data-ttu-id="6209d-302">Nginx: Binární verze: Oficiální balíčky Debian/Ubuntu</span><span class="sxs-lookup"><span data-stu-id="6209d-302">Nginx: Binary Releases: Official Debian/Ubuntu packages</span></span>](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="6209d-303">NGINX: Použití předaných záhlaví</span><span class="sxs-lookup"><span data-stu-id="6209d-303">NGINX: Using the Forwarded header</span></span>](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
