---
title: Hostování ASP.NET Core v systému Linux pomocí Nginx
author: rick-anderson
description: Naučte se, jak nastavit Nginx jako reverzní proxy na Ubuntu 16,04 pro přeposílání provozu HTTP do ASP.NET Core webové aplikace běžící na Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2020
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
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: 63227f068926c4158ac8162fdc1ac11399fd65cb
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633783"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a><span data-ttu-id="31eef-103">Hostování ASP.NET Core v systému Linux pomocí Nginx</span><span class="sxs-lookup"><span data-stu-id="31eef-103">Host ASP.NET Core on Linux with Nginx</span></span>

<span data-ttu-id="31eef-104">Od [sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="31eef-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="31eef-105">V této příručce se dozvíte, jak na serveru Ubuntu 16,04 nastavit prostředí pro ASP.NET Core připravené pro produkční prostředí.</span><span class="sxs-lookup"><span data-stu-id="31eef-105">This guide explains setting up a production-ready ASP.NET Core environment on an Ubuntu 16.04 server.</span></span> <span data-ttu-id="31eef-106">Tyto pokyny pravděpodobně fungují s novějšími verzemi Ubuntu, ale pokyny nebyly testovány s novějšími verzemi.</span><span class="sxs-lookup"><span data-stu-id="31eef-106">These instructions likely work with newer versions of Ubuntu, but the instructions haven't been tested with newer versions.</span></span>

<span data-ttu-id="31eef-107">Informace o dalších distribucích systému Linux podporovaných nástrojem ASP.NET Core najdete v tématu [předpoklady pro .NET Core v systému Linux](/dotnet/core/linux-prerequisites).</span><span class="sxs-lookup"><span data-stu-id="31eef-107">For information on other Linux distributions supported by ASP.NET Core, see [Prerequisites for .NET Core on Linux](/dotnet/core/linux-prerequisites).</span></span>

> [!NOTE]
> <span data-ttu-id="31eef-108">Pro Ubuntu *14,04 se doporučuje* jako řešení pro monitorování procesu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="31eef-108">For Ubuntu 14.04, *supervisord* is recommended as a solution for monitoring the Kestrel process.</span></span> <span data-ttu-id="31eef-109">*systém* není k dispozici na Ubuntu 14,04.</span><span class="sxs-lookup"><span data-stu-id="31eef-109">*systemd* isn't available on Ubuntu 14.04.</span></span> <span data-ttu-id="31eef-110">Pokyny pro Ubuntu 14,04 najdete v [předchozí verzi tohoto tématu](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span><span class="sxs-lookup"><span data-stu-id="31eef-110">For Ubuntu 14.04 instructions, see the [previous version of this topic](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span></span>

<span data-ttu-id="31eef-111">Tato příručka:</span><span class="sxs-lookup"><span data-stu-id="31eef-111">This guide:</span></span>

* <span data-ttu-id="31eef-112">Umístí existující aplikaci ASP.NET Core za reverzní proxy server.</span><span class="sxs-lookup"><span data-stu-id="31eef-112">Places an existing ASP.NET Core app behind a reverse proxy server.</span></span>
* <span data-ttu-id="31eef-113">Nastaví reverzní proxy server pro přeposílání požadavků na webový server Kestrel.</span><span class="sxs-lookup"><span data-stu-id="31eef-113">Sets up the reverse proxy server to forward requests to the Kestrel web server.</span></span>
* <span data-ttu-id="31eef-114">Zajistí, aby se webová aplikace spouštěla při spuštění jako démon.</span><span class="sxs-lookup"><span data-stu-id="31eef-114">Ensures the web app runs on startup as a daemon.</span></span>
* <span data-ttu-id="31eef-115">Nakonfiguruje Nástroj pro správu procesů, který vám může pomáhat s restartováním webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="31eef-115">Configures a process management tool to help restart the web app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="31eef-116">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="31eef-116">Prerequisites</span></span>

1. <span data-ttu-id="31eef-117">Přístup k serveru Ubuntu 16,04 se standardním uživatelským účtem s oprávněním sudo.</span><span class="sxs-lookup"><span data-stu-id="31eef-117">Access to an Ubuntu 16.04 server with a standard user account with sudo privilege.</span></span>
1. <span data-ttu-id="31eef-118">Nainstalujte modul runtime .NET Core na server.</span><span class="sxs-lookup"><span data-stu-id="31eef-118">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="31eef-119">Navštivte [stránku stáhnout jádro .NET Core](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="31eef-119">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="31eef-120">Vyberte nejnovější verzi rozhraní .NET Core, která není ve verzi Preview.</span><span class="sxs-lookup"><span data-stu-id="31eef-120">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="31eef-121">Stáhněte si nejnovější modul runtime bez verze Preview v tabulce v části **Spustit aplikace – modul runtime**.</span><span class="sxs-lookup"><span data-stu-id="31eef-121">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="31eef-122">Vyberte odkaz **pokyny správce balíčků** pro Linux a postupujte podle pokynů pro Ubuntu pro vaši verzi Ubuntu.</span><span class="sxs-lookup"><span data-stu-id="31eef-122">Select the Linux **Package manager instructions** link and follow the Ubuntu instructions for your version of Ubuntu.</span></span>
1. <span data-ttu-id="31eef-123">Existující aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="31eef-123">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="31eef-124">V jakémkoli okamžiku v budoucnu po upgradu sdílené architektury restartujte aplikace ASP.NET Core hostované serverem.</span><span class="sxs-lookup"><span data-stu-id="31eef-124">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="31eef-125">Publikování a kopírování přes aplikaci</span><span class="sxs-lookup"><span data-stu-id="31eef-125">Publish and copy over the app</span></span>

<span data-ttu-id="31eef-126">Nakonfigurujte aplikaci pro [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="31eef-126">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="31eef-127">Pokud je aplikace spuštěná místně a není nakonfigurovaná tak, aby přijímala zabezpečené připojení (HTTPS), proveďte jednu z následujících metod:</span><span class="sxs-lookup"><span data-stu-id="31eef-127">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="31eef-128">Nakonfigurujte aplikaci tak, aby zpracovávala Zabezpečená místní připojení.</span><span class="sxs-lookup"><span data-stu-id="31eef-128">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="31eef-129">Další informace najdete v části [konfigurace https](#https-configuration) .</span><span class="sxs-lookup"><span data-stu-id="31eef-129">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="31eef-130">Odebere `https://localhost:5001` (je-li k dispozici) z `applicationUrl` vlastnosti v souboru *Properties/launchSettings.js* .</span><span class="sxs-lookup"><span data-stu-id="31eef-130">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="31eef-131">Pokud chcete zabalit aplikaci do adresáře (například *bin/Release/ &lt; target_framework_moniker &gt; /Publish*), která se dají spustit na serveru, spusťte [dotnet Publish](/dotnet/core/tools/dotnet-publish) z vývojového prostředí:</span><span class="sxs-lookup"><span data-stu-id="31eef-131">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="31eef-132">Pokud nechcete zachovat modul runtime .NET Core na serveru, můžete aplikaci publikovat také jako samostatné [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) .</span><span class="sxs-lookup"><span data-stu-id="31eef-132">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="31eef-133">Zkopírujte aplikaci ASP.NET Core na server pomocí nástroje, který se integruje do pracovního postupu organizace (například SCP, SFTP).</span><span class="sxs-lookup"><span data-stu-id="31eef-133">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="31eef-134">Je běžné najít webové aplikace v adresáři *var* (například *var/www/helloapp*).</span><span class="sxs-lookup"><span data-stu-id="31eef-134">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="31eef-135">V rámci scénáře nasazení v produkčním prostředí provádí pracovní postup průběžné integrace publikování aplikace a zkopírování prostředků na server.</span><span class="sxs-lookup"><span data-stu-id="31eef-135">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

<span data-ttu-id="31eef-136">Otestujte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="31eef-136">Test the app:</span></span>

1. <span data-ttu-id="31eef-137">Z příkazového řádku spusťte aplikaci: `dotnet <app_assembly>.dll` .</span><span class="sxs-lookup"><span data-stu-id="31eef-137">From the command line, run the app: `dotnet <app_assembly>.dll`.</span></span>
1. <span data-ttu-id="31eef-138">V prohlížeči přejděte na adresu `http://<serveraddress>:<port>` a ověřte, že aplikace funguje na platformě Linux místně.</span><span class="sxs-lookup"><span data-stu-id="31eef-138">In a browser, navigate to `http://<serveraddress>:<port>` to verify the app works on Linux locally.</span></span>

## <a name="configure-a-reverse-proxy-server"></a><span data-ttu-id="31eef-139">Konfigurace reverzního proxy server</span><span class="sxs-lookup"><span data-stu-id="31eef-139">Configure a reverse proxy server</span></span>

<span data-ttu-id="31eef-140">Reverzní proxy je běžné nastavení pro obsluhu dynamických webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="31eef-140">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="31eef-141">Reverzní proxy ukončí požadavek HTTP a předá ho do aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="31eef-141">A reverse proxy terminates the HTTP request and forwards it to the ASP.NET Core app.</span></span>

### <a name="use-a-reverse-proxy-server"></a><span data-ttu-id="31eef-142">Použít reverzní proxy server</span><span class="sxs-lookup"><span data-stu-id="31eef-142">Use a reverse proxy server</span></span>

<span data-ttu-id="31eef-143">Kestrel je ideální pro obsluhu dynamického obsahu z ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="31eef-143">Kestrel is great for serving dynamic content from ASP.NET Core.</span></span> <span data-ttu-id="31eef-144">Webový server, který obsluhuje možnosti, ale není jako funkce bohatě funkční jako servery jako IIS, Apache nebo Nginx.</span><span class="sxs-lookup"><span data-stu-id="31eef-144">However, the web serving capabilities aren't as feature rich as servers such as IIS, Apache, or Nginx.</span></span> <span data-ttu-id="31eef-145">Reverzní proxy server může přesměrovat práci, jako je například obsluhující statický obsah, požadavky na ukládání do mezipaměti, komprimace požadavků a ukončení HTTPS ze serveru HTTP.</span><span class="sxs-lookup"><span data-stu-id="31eef-145">A reverse proxy server can offload work such as serving static content, caching requests, compressing requests, and HTTPS termination from the HTTP server.</span></span> <span data-ttu-id="31eef-146">Reverzní proxy server může být umístěná na vyhrazeném počítači nebo může být nasazena spolu s HTTP serverem.</span><span class="sxs-lookup"><span data-stu-id="31eef-146">A reverse proxy server may reside on a dedicated machine or may be deployed alongside an HTTP server.</span></span>

<span data-ttu-id="31eef-147">Pro účely tohoto průvodce se používá jedna instance Nginx.</span><span class="sxs-lookup"><span data-stu-id="31eef-147">For the purposes of this guide, a single instance of Nginx is used.</span></span> <span data-ttu-id="31eef-148">Spouští se na stejném serveru společně se serverem HTTP.</span><span class="sxs-lookup"><span data-stu-id="31eef-148">It runs on the same server, alongside the HTTP server.</span></span> <span data-ttu-id="31eef-149">Na základě požadavků může být zvoleno jiné nastavení.</span><span class="sxs-lookup"><span data-stu-id="31eef-149">Based on requirements, a different setup may be chosen.</span></span>

<span data-ttu-id="31eef-150">Vzhledem k tomu, že požadavky jsou předávány reverzním proxy, použijte [middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) z balíčku [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) .</span><span class="sxs-lookup"><span data-stu-id="31eef-150">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="31eef-151">Middleware aktualizuje `Request.Scheme` pomocí `X-Forwarded-Proto` hlavičky, aby identifikátory URI pro přesměrování a další zásady zabezpečení fungovaly správně.</span><span class="sxs-lookup"><span data-stu-id="31eef-151">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>


[!INCLUDE[](~/includes/ForwardedHeaders.md)]

<span data-ttu-id="31eef-152">Volejte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodu v horní části `Startup.Configure` před voláním jiného middleware.</span><span class="sxs-lookup"><span data-stu-id="31eef-152">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="31eef-153">Nakonfigurujte middleware pro přeposílání `X-Forwarded-For` `X-Forwarded-Proto` hlaviček a:</span><span class="sxs-lookup"><span data-stu-id="31eef-153">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="31eef-154">Pokud <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> pro middleware nejsou zadány žádné, výchozí hlavičky budou předány `None` .</span><span class="sxs-lookup"><span data-stu-id="31eef-154">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="31eef-155">Proxy servery běžící na adresách zpětné smyčky (127.0.0.0/8, [:: 1]), včetně standardní adresy localhost (127.0.0.1), jsou ve výchozím nastavení důvěryhodné.</span><span class="sxs-lookup"><span data-stu-id="31eef-155">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="31eef-156">Pokud jiné důvěryhodné proxy servery nebo sítě v rámci organizace zařídí žádosti mezi Internetem a webovým serverem, přidejte je do seznamu <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> nebo <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> .</span><span class="sxs-lookup"><span data-stu-id="31eef-156">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="31eef-157">Následující příklad přidá důvěryhodnou proxy server na IP adrese 10.0.0.100 do middlewaru předávaných hlaviček `KnownProxies` v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="31eef-157">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="31eef-158">Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="31eef-158">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-nginx"></a><span data-ttu-id="31eef-159">Nainstalovat Nginx</span><span class="sxs-lookup"><span data-stu-id="31eef-159">Install Nginx</span></span>

<span data-ttu-id="31eef-160">Použijte `apt-get` k instalaci Nginx.</span><span class="sxs-lookup"><span data-stu-id="31eef-160">Use `apt-get` to install Nginx.</span></span> <span data-ttu-id="31eef-161">Instalační program vytvoří *systémový* skript init, který spustí Nginx jako démon při spuštění systému.</span><span class="sxs-lookup"><span data-stu-id="31eef-161">The installer creates a *systemd* init script that runs Nginx as daemon on system startup.</span></span> <span data-ttu-id="31eef-162">Postupujte podle pokynů k instalaci Ubuntu na [Nginx: oficiální balíčky Debian/Ubuntu](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span><span class="sxs-lookup"><span data-stu-id="31eef-162">Follow the installation instructions for Ubuntu at [Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span></span>

> [!NOTE]
> <span data-ttu-id="31eef-163">Pokud jsou vyžadovány Volitelné moduly Nginx, může být nutné sestavit Nginx ze zdroje.</span><span class="sxs-lookup"><span data-stu-id="31eef-163">If optional Nginx modules are required, building Nginx from source might be required.</span></span>

<span data-ttu-id="31eef-164">Vzhledem k tomu, že se Nginx nainstaloval poprvé, explicitně ho spusťte spuštěním:</span><span class="sxs-lookup"><span data-stu-id="31eef-164">Since Nginx was installed for the first time, explicitly start it by running:</span></span>

```bash
sudo service nginx start
```

<span data-ttu-id="31eef-165">Ověřte, že prohlížeč zobrazuje výchozí cílovou stránku pro Nginx.</span><span class="sxs-lookup"><span data-stu-id="31eef-165">Verify a browser displays the default landing page for Nginx.</span></span> <span data-ttu-id="31eef-166">Cílová stránka je dostupná na adrese `http://<server_IP_address>/index.nginx-debian.html` .</span><span class="sxs-lookup"><span data-stu-id="31eef-166">The landing page is reachable at `http://<server_IP_address>/index.nginx-debian.html`.</span></span>

### <a name="configure-nginx"></a><span data-ttu-id="31eef-167">Konfigurace služby Nginx</span><span class="sxs-lookup"><span data-stu-id="31eef-167">Configure Nginx</span></span>

<span data-ttu-id="31eef-168">Pokud chcete nakonfigurovat Nginx jako reverzní proxy server pro přeposílání požadavků do vaší aplikace ASP.NET Core, upravte */etc/Nginx/sites-available/default*.</span><span class="sxs-lookup"><span data-stu-id="31eef-168">To configure Nginx as a reverse proxy to forward requests to your ASP.NET Core app, modify */etc/nginx/sites-available/default*.</span></span> <span data-ttu-id="31eef-169">Otevřete ho v textovém editoru a nahraďte jeho obsah následujícím textem:</span><span class="sxs-lookup"><span data-stu-id="31eef-169">Open it in a text editor, and replace the contents with the following:</span></span>

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

<span data-ttu-id="31eef-170">Pokud se jedná o Blazor Server aplikaci, která spoléhá na SignalR objekty WebSockets, přečtěte si téma, <xref:blazor/host-and-deploy/server#linux-with-nginx> kde najdete informace o tom, jak nastavit `Connection` hlavičku.</span><span class="sxs-lookup"><span data-stu-id="31eef-170">If the app is a Blazor Server app that relies on SignalR WebSockets, see <xref:blazor/host-and-deploy/server#linux-with-nginx> for information on how to set the `Connection` header.</span></span>

<span data-ttu-id="31eef-171">Pokud `server_name` se neshodují, Nginx použije výchozí server.</span><span class="sxs-lookup"><span data-stu-id="31eef-171">When no `server_name` matches, Nginx uses the default server.</span></span> <span data-ttu-id="31eef-172">Pokud není definován žádný výchozí server, je první server v konfiguračním souboru výchozím serverem.</span><span class="sxs-lookup"><span data-stu-id="31eef-172">If no default server is defined, the first server in the configuration file is the default server.</span></span> <span data-ttu-id="31eef-173">Osvědčeným postupem je přidat konkrétní výchozí server, který vrátí stavový kód 444 do konfiguračního souboru.</span><span class="sxs-lookup"><span data-stu-id="31eef-173">As a best practice, add a specific default server which returns a status code of 444 in your configuration file.</span></span> <span data-ttu-id="31eef-174">Výchozím příkladem konfigurace serveru je:</span><span class="sxs-lookup"><span data-stu-id="31eef-174">A default server configuration example is:</span></span>

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

<span data-ttu-id="31eef-175">Pomocí předchozího konfiguračního souboru a výchozího serveru Nginx akceptuje veřejný provoz na portu 80 s hlavičkou hostitele `example.com` nebo `*.example.com` .</span><span class="sxs-lookup"><span data-stu-id="31eef-175">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="31eef-176">Požadavky, které se neshodují s těmito hostiteli, se nebudou přesílat na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="31eef-176">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="31eef-177">Nginx přepošle požadavky na Kestrel na `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="31eef-177">Nginx forwards the matching requests to Kestrel at `http://localhost:5000`.</span></span> <span data-ttu-id="31eef-178">Další informace najdete v tématu [jak Nginx zpracovává požadavek](https://nginx.org/docs/http/request_processing.html) .</span><span class="sxs-lookup"><span data-stu-id="31eef-178">See [How nginx processes a request](https://nginx.org/docs/http/request_processing.html) for more information.</span></span> <span data-ttu-id="31eef-179">Pokud chcete změnit IP adresu/port Kestrel, přečtěte si téma [Kestrel: konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="31eef-179">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="31eef-180">Nepovedlo se určit správnou [direktivu server_name](https://nginx.org/docs/http/server_names.html) , kterou vaše aplikace zpřístupňuje bezpečnostním hrozbám.</span><span class="sxs-lookup"><span data-stu-id="31eef-180">Failure to specify a proper [server_name directive](https://nginx.org/docs/http/server_names.html) exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="31eef-181">Vazba zástupných znaků subdomény (například `*.example.com` ) nepředstavuje toto bezpečnostní riziko, pokud řídíte celou nadřazenou doménu (na rozdíl od `*.com` , která je zranitelná).</span><span class="sxs-lookup"><span data-stu-id="31eef-181">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="31eef-182">Další informace najdete v [části rfc7230 část-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .</span><span class="sxs-lookup"><span data-stu-id="31eef-182">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="31eef-183">Po navázání konfigurace nginx spusťte příkaz `sudo nginx -t` a ověřte syntaxi konfiguračních souborů.</span><span class="sxs-lookup"><span data-stu-id="31eef-183">Once the Nginx configuration is established, run `sudo nginx -t` to verify the syntax of the configuration files.</span></span> <span data-ttu-id="31eef-184">Pokud je test konfiguračního souboru úspěšný, vynutit Nginx změny spuštěním `sudo nginx -s reload` .</span><span class="sxs-lookup"><span data-stu-id="31eef-184">If the configuration file test is successful, force Nginx to pick up the changes by running `sudo nginx -s reload`.</span></span>

<span data-ttu-id="31eef-185">Postup při přímém spuštění aplikace na serveru:</span><span class="sxs-lookup"><span data-stu-id="31eef-185">To directly run the app on the server:</span></span>

1. <span data-ttu-id="31eef-186">Přejděte do adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="31eef-186">Navigate to the app's directory.</span></span>
1. <span data-ttu-id="31eef-187">Spusťte aplikaci: `dotnet <app_assembly.dll>` , kde `app_assembly.dll` je název souboru sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="31eef-187">Run the app: `dotnet <app_assembly.dll>`, where `app_assembly.dll` is the assembly file name of the app.</span></span>

<span data-ttu-id="31eef-188">Pokud aplikace běží na serveru, ale neodpoví přes Internet, zkontrolujte bránu firewall serveru a ověřte, že je port 80 otevřený.</span><span class="sxs-lookup"><span data-stu-id="31eef-188">If the app runs on the server but fails to respond over the Internet, check the server's firewall and confirm that port 80 is open.</span></span> <span data-ttu-id="31eef-189">Pokud používáte virtuální počítač Azure Ubuntu, přidejte pravidlo skupiny zabezpečení sítě (NSG), které umožňuje příchozí provoz portu 80.</span><span class="sxs-lookup"><span data-stu-id="31eef-189">If using an Azure Ubuntu VM, add a Network Security Group (NSG) rule that enables inbound port 80 traffic.</span></span> <span data-ttu-id="31eef-190">Není nutné povolit odchozí pravidlo portu 80, protože odchozí přenosy jsou automaticky uděleny, když je povolené příchozí pravidlo.</span><span class="sxs-lookup"><span data-stu-id="31eef-190">There's no need to enable an outbound port 80 rule, as the outbound traffic is automatically granted when the inbound rule is enabled.</span></span>

<span data-ttu-id="31eef-191">Po dokončení testování aplikace ukončete aplikaci `Ctrl+C` v příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="31eef-191">When done testing the app, shut the app down with `Ctrl+C` at the command prompt.</span></span>

## <a name="monitor-the-app"></a><span data-ttu-id="31eef-192">Monitorování aplikace</span><span class="sxs-lookup"><span data-stu-id="31eef-192">Monitor the app</span></span>

<span data-ttu-id="31eef-193">Server je nastavený tak, aby předal požadavky na `http://<serveraddress>:80` aplikaci ASP.NET Core běžící na Kestrel v `http://127.0.0.1:5000` .</span><span class="sxs-lookup"><span data-stu-id="31eef-193">The server is setup to forward requests made to `http://<serveraddress>:80` on to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="31eef-194">Nginx ale není nastavené na správu procesu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="31eef-194">However, Nginx isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="31eef-195">*systém* lze použít k vytvoření souboru služby ke spuštění a sledování základní webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="31eef-195">*systemd* can be used to create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="31eef-196">*systém* je systémem init, který poskytuje mnoho výkonných funkcí pro spouštění, zastavování a správu procesů.</span><span class="sxs-lookup"><span data-stu-id="31eef-196">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="31eef-197">Vytvoření souboru služby</span><span class="sxs-lookup"><span data-stu-id="31eef-197">Create the service file</span></span>

<span data-ttu-id="31eef-198">Vytvořte definiční soubor služby:</span><span class="sxs-lookup"><span data-stu-id="31eef-198">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="31eef-199">Následuje příklad souboru služby pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="31eef-199">The following is an example service file for the app:</span></span>

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

<span data-ttu-id="31eef-200">V předchozím příkladu je uživatel, který spravuje službu, určen pomocí `User` Možnosti.</span><span class="sxs-lookup"><span data-stu-id="31eef-200">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="31eef-201">Uživatel ( `www-data` ) musí existovat a musí mít správné vlastnictví souborů aplikace.</span><span class="sxs-lookup"><span data-stu-id="31eef-201">The user (`www-data`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="31eef-202">Slouží `TimeoutStopSec` ke konfiguraci časového intervalu, po který se má čekat na vypnutí aplikace po přijetí počátečního signálu přerušení.</span><span class="sxs-lookup"><span data-stu-id="31eef-202">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="31eef-203">Pokud se aplikace v tomto období neukončí, SIGKILL se vydá pro ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="31eef-203">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="31eef-204">Zadejte hodnotu jako nejednotkové sekundy (například `150` ), hodnotu časového rozsahu (například `2min 30s` ) nebo `infinity` zakažte časový limit.</span><span class="sxs-lookup"><span data-stu-id="31eef-204">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="31eef-205">`TimeoutStopSec` ve výchozím nastavení se jedná o hodnotu `DefaultTimeoutStopSec` v konfiguračním souboru správce (*System-System. conf*, *System. conf. d*, *systemd-User. conf*, *User. conf. d*).</span><span class="sxs-lookup"><span data-stu-id="31eef-205">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="31eef-206">Výchozí časový limit pro většinu distribucí je 90 sekund.</span><span class="sxs-lookup"><span data-stu-id="31eef-206">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="31eef-207">Linux má systém souborů s rozlišováním velkých a malých písmen.</span><span class="sxs-lookup"><span data-stu-id="31eef-207">Linux has a case-sensitive file system.</span></span> <span data-ttu-id="31eef-208">Když nanastavíte ASPNETCORE_ENVIRONMENT do produkčního prostředí, výsledky hledání konfiguračního souboru *appsettings.Production.jszapnuto*, ne *appsettings.production.jszapnuté*.</span><span class="sxs-lookup"><span data-stu-id="31eef-208">Setting ASPNETCORE_ENVIRONMENT to "Production" results in searching for the configuration file *appsettings.Production.json*, not *appsettings.production.json*.</span></span>

<span data-ttu-id="31eef-209">Některé hodnoty (například připojovací řetězce SQL) musí být uvozené řídicími znaky, aby poskytovatelé konfigurace mohli číst proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="31eef-209">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="31eef-210">Pomocí následujícího příkazu vygenerujte správně uvozenou hodnotu pro použití v konfiguračním souboru:</span><span class="sxs-lookup"><span data-stu-id="31eef-210">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="31eef-211">Oddělovače dvojtečky ( `:` ) nejsou podporovány v názvech proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="31eef-211">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="31eef-212">Místo dvojtečky použijte dvojité podtržítko ( `__` ).</span><span class="sxs-lookup"><span data-stu-id="31eef-212">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="31eef-213">[Poskytovatel konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables) převádí dvojitá podtržítka na dvojtečky, když jsou proměnné prostředí čteny do konfigurace.</span><span class="sxs-lookup"><span data-stu-id="31eef-213">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="31eef-214">V následujícím příkladu je klíč připojovacího řetězce `ConnectionStrings:DefaultConnection` nastaven do souboru definice služby jako `ConnectionStrings__DefaultConnection` :</span><span class="sxs-lookup"><span data-stu-id="31eef-214">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="31eef-215">Oddělovače dvojtečky ( `:` ) nejsou podporovány v názvech proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="31eef-215">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="31eef-216">Místo dvojtečky použijte dvojité podtržítko ( `__` ).</span><span class="sxs-lookup"><span data-stu-id="31eef-216">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="31eef-217">[Poskytovatel konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) převádí dvojitá podtržítka na dvojtečky, když jsou proměnné prostředí čteny do konfigurace.</span><span class="sxs-lookup"><span data-stu-id="31eef-217">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="31eef-218">V následujícím příkladu je klíč připojovacího řetězce `ConnectionStrings:DefaultConnection` nastaven do souboru definice služby jako `ConnectionStrings__DefaultConnection` :</span><span class="sxs-lookup"><span data-stu-id="31eef-218">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="31eef-219">Uložte soubor a povolte službu.</span><span class="sxs-lookup"><span data-stu-id="31eef-219">Save the file and enable the service.</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="31eef-220">Spusťte službu a ověřte, zda je spuštěná.</span><span class="sxs-lookup"><span data-stu-id="31eef-220">Start the service and verify that it's running.</span></span>

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="31eef-221">Pomocí systému reverzního proxy serveru nakonfigurovaného a Kestrel spravovaného přes systém je webová aplikace plně nakonfigurovaná a dá se k ní dostat z prohlížeče v místním počítači `http://localhost` .</span><span class="sxs-lookup"><span data-stu-id="31eef-221">With the reverse proxy configured and Kestrel managed through systemd, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="31eef-222">Je dostupná taky ze vzdáleného počítače a znemožňuje bránu firewall, která může být zablokovaná.</span><span class="sxs-lookup"><span data-stu-id="31eef-222">It's also accessible from a remote machine, barring any firewall that might be blocking.</span></span> <span data-ttu-id="31eef-223">Při kontrole hlaviček odpovědi se v `Server` hlavičce zobrazuje ASP.NET Core aplikace, kterou obsluhuje Kestrel.</span><span class="sxs-lookup"><span data-stu-id="31eef-223">Inspecting the response headers, the `Server` header shows the ASP.NET Core app being served by Kestrel.</span></span>

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="31eef-224">Zobrazení protokolů</span><span class="sxs-lookup"><span data-stu-id="31eef-224">View logs</span></span>

<span data-ttu-id="31eef-225">Vzhledem k tomu, že webová aplikace využívající Kestrel je spravovaná pomocí `systemd` , jsou všechny události a procesy protokolovány do centralizovaného deníku.</span><span class="sxs-lookup"><span data-stu-id="31eef-225">Since the web app using Kestrel is managed using `systemd`, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="31eef-226">Tento deník ale obsahuje všechny položky pro všechny služby a procesy, které spravuje `systemd` .</span><span class="sxs-lookup"><span data-stu-id="31eef-226">However, this journal includes all entries for all services and processes managed by `systemd`.</span></span> <span data-ttu-id="31eef-227">Chcete-li zobrazit `kestrel-helloapp.service` položky specifické pro zobrazení, použijte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="31eef-227">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="31eef-228">Pro další filtrování, časová omezení, jako například `--since today` , `--until 1 hour ago` nebo kombinace těchto možností může snížit množství vrácených položek.</span><span class="sxs-lookup"><span data-stu-id="31eef-228">For further filtering, time options such as `--since today`, `--until 1 hour ago` or a combination of these can reduce the amount of entries returned.</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="31eef-229">Ochrana dat</span><span class="sxs-lookup"><span data-stu-id="31eef-229">Data protection</span></span>

<span data-ttu-id="31eef-230">[Sada ASP.NET Core Data Protection Stack](xref:security/data-protection/introduction) je používána několika ASP.NET Core [middlewary](xref:fundamentals/middleware/index), včetně middlewaru ověřování (například cookie middlewaru) a ochrany proti padělání žádostí mezi lokalitami (CSRF).</span><span class="sxs-lookup"><span data-stu-id="31eef-230">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="31eef-231">I v případě, že rozhraní API ochrany dat nejsou volána uživatelským kódem, je třeba chránit data, aby bylo možné vytvořit trvalé úložiště kryptografických [klíčů](xref:security/data-protection/implementation/key-management).</span><span class="sxs-lookup"><span data-stu-id="31eef-231">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="31eef-232">Pokud ochrana dat není nakonfigurovaná, klíče se uchovávají v paměti a při restartování aplikace se zahodí.</span><span class="sxs-lookup"><span data-stu-id="31eef-232">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="31eef-233">Pokud se klíčového prstence při restartu aplikace uloží do paměti:</span><span class="sxs-lookup"><span data-stu-id="31eef-233">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="31eef-234">U cookie tokenů ověřování na základě jsou neověřené.</span><span class="sxs-lookup"><span data-stu-id="31eef-234">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="31eef-235">Uživatelé se musí znovu přihlásit na svůj další požadavek.</span><span class="sxs-lookup"><span data-stu-id="31eef-235">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="31eef-236">Data chráněná pomocí Key ringu už nebude možné dešifrovat.</span><span class="sxs-lookup"><span data-stu-id="31eef-236">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="31eef-237">To může zahrnovat [CSRF tokeny](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [ASP.NET Core MVC TempData cookie s](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="31eef-237">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="31eef-238">Pokud chcete nakonfigurovat ochranu dat, aby zachovala a zašifroval klíč Ring, přečtěte si:</span><span class="sxs-lookup"><span data-stu-id="31eef-238">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a><span data-ttu-id="31eef-239">Pole hlavičky dlouhé žádosti</span><span class="sxs-lookup"><span data-stu-id="31eef-239">Long request header fields</span></span>

<span data-ttu-id="31eef-240">Výchozí nastavení proxy serveru obvykle omezuje pole hlaviček požadavku na 4 KB nebo 8 KB v závislosti na platformě.</span><span class="sxs-lookup"><span data-stu-id="31eef-240">Proxy server default settings typically limit request header fields to 4 K or 8 K depending on the platform.</span></span> <span data-ttu-id="31eef-241">Aplikace může vyžadovat pole delší než výchozí (například aplikace, které používají [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span><span class="sxs-lookup"><span data-stu-id="31eef-241">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="31eef-242">Pokud jsou požadována delší pole, je výchozí nastavení proxy server vyžadovat úpravu.</span><span class="sxs-lookup"><span data-stu-id="31eef-242">If longer fields are required, the proxy server's default settings require adjustment.</span></span> <span data-ttu-id="31eef-243">Hodnoty, které mají být použity, závisí na scénáři.</span><span class="sxs-lookup"><span data-stu-id="31eef-243">The values to apply depend on the scenario.</span></span> <span data-ttu-id="31eef-244">Další informace najdete v dokumentaci k vašemu serveru.</span><span class="sxs-lookup"><span data-stu-id="31eef-244">For more information, see your server's documentation.</span></span>

* [<span data-ttu-id="31eef-245">proxy_buffer_size</span><span class="sxs-lookup"><span data-stu-id="31eef-245">proxy_buffer_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [<span data-ttu-id="31eef-246">proxy_buffers</span><span class="sxs-lookup"><span data-stu-id="31eef-246">proxy_buffers</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [<span data-ttu-id="31eef-247">proxy_busy_buffers_size</span><span class="sxs-lookup"><span data-stu-id="31eef-247">proxy_busy_buffers_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [<span data-ttu-id="31eef-248">large_client_header_buffers</span><span class="sxs-lookup"><span data-stu-id="31eef-248">large_client_header_buffers</span></span>](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> <span data-ttu-id="31eef-249">Nerozšiřujte výchozí hodnoty vyrovnávací paměti proxy, pokud je to nutné.</span><span class="sxs-lookup"><span data-stu-id="31eef-249">Don't increase the default values of proxy buffers unless necessary.</span></span> <span data-ttu-id="31eef-250">Zvýšení těchto hodnot zvyšuje riziko přetečení vyrovnávací paměti (přetečení) a útok DoS (Denial of Service) uživateli se zlými úmysly.</span><span class="sxs-lookup"><span data-stu-id="31eef-250">Increasing these values increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="secure-the-app"></a><span data-ttu-id="31eef-251">Zabezpečení aplikace</span><span class="sxs-lookup"><span data-stu-id="31eef-251">Secure the app</span></span>

### <a name="enable-apparmor"></a><span data-ttu-id="31eef-252">Povolit AppArmor</span><span class="sxs-lookup"><span data-stu-id="31eef-252">Enable AppArmor</span></span>

<span data-ttu-id="31eef-253">Moduly zabezpečení Linux (LSM) jsou rozhraní, které je součástí jádra Linux od verze Linux 2,6.</span><span class="sxs-lookup"><span data-stu-id="31eef-253">Linux Security Modules (LSM) is a framework that's part of the Linux kernel since Linux 2.6.</span></span> <span data-ttu-id="31eef-254">LSM podporuje různé implementace modulů zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="31eef-254">LSM supports different implementations of security modules.</span></span> <span data-ttu-id="31eef-255">[AppArmor](https://wiki.ubuntu.com/AppArmor) je lsm, který implementuje povinný Access Control systém, který umožňuje programu confining program na omezené sady prostředků.</span><span class="sxs-lookup"><span data-stu-id="31eef-255">[AppArmor](https://wiki.ubuntu.com/AppArmor) is a LSM that implements a Mandatory Access Control system which allows confining the program to a limited set of resources.</span></span> <span data-ttu-id="31eef-256">Ujistěte se, že je povolená a správně nakonfigurovaná možnost AppArmor.</span><span class="sxs-lookup"><span data-stu-id="31eef-256">Ensure AppArmor is enabled and properly configured.</span></span>

### <a name="configure-the-firewall"></a><span data-ttu-id="31eef-257">Konfigurace brány firewall</span><span class="sxs-lookup"><span data-stu-id="31eef-257">Configure the firewall</span></span>

<span data-ttu-id="31eef-258">Zavřete všechny externí porty, které se nepoužívají.</span><span class="sxs-lookup"><span data-stu-id="31eef-258">Close off all external ports that are not in use.</span></span> <span data-ttu-id="31eef-259">Nesložitá brána firewall (UFW) poskytuje front-end pro poskytování rozhraní příkazového `iptables` řádku pro konfiguraci brány firewall.</span><span class="sxs-lookup"><span data-stu-id="31eef-259">Uncomplicated firewall (ufw) provides a front end for `iptables` by providing a CLI for configuring the firewall.</span></span>

> [!WARNING]
> <span data-ttu-id="31eef-260">Brána firewall zabrání přístupu k celému systému, pokud není správně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="31eef-260">A firewall will prevent access to the whole system if not configured correctly.</span></span> <span data-ttu-id="31eef-261">Pokud se k připojení použijete přes SSH, nebudete moct zadat správný port SSH.</span><span class="sxs-lookup"><span data-stu-id="31eef-261">Failure to specify the correct SSH port will effectively lock you out of the system if you are using SSH to connect to it.</span></span> <span data-ttu-id="31eef-262">Výchozí port je 22.</span><span class="sxs-lookup"><span data-stu-id="31eef-262">The default port is 22.</span></span> <span data-ttu-id="31eef-263">Další informace najdete v [úvodu k UFW](https://help.ubuntu.com/community/UFW) a [příručce](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span><span class="sxs-lookup"><span data-stu-id="31eef-263">For more information, see the [introduction to ufw](https://help.ubuntu.com/community/UFW) and the [manual](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span></span>

<span data-ttu-id="31eef-264">Nainstalujte `ufw` a nakonfigurujte ho tak, aby povoloval přenosy na všech potřebných portech.</span><span class="sxs-lookup"><span data-stu-id="31eef-264">Install `ufw` and configure it to allow traffic on any ports needed.</span></span>

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a><span data-ttu-id="31eef-265">Zabezpečení Nginx</span><span class="sxs-lookup"><span data-stu-id="31eef-265">Secure Nginx</span></span>

#### <a name="change-the-nginx-response-name"></a><span data-ttu-id="31eef-266">Změnit název odpovědi Nginx</span><span class="sxs-lookup"><span data-stu-id="31eef-266">Change the Nginx response name</span></span>

<span data-ttu-id="31eef-267">Upravit *Src/http/ngx_http_header_filter_module. c*:</span><span class="sxs-lookup"><span data-stu-id="31eef-267">Edit *src/http/ngx_http_header_filter_module.c*:</span></span>

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a><span data-ttu-id="31eef-268">Konfigurace možností</span><span class="sxs-lookup"><span data-stu-id="31eef-268">Configure options</span></span>

<span data-ttu-id="31eef-269">Nakonfigurujte server s dalšími požadovanými moduly.</span><span class="sxs-lookup"><span data-stu-id="31eef-269">Configure the server with additional required modules.</span></span> <span data-ttu-id="31eef-270">Zvažte použití brány firewall webových aplikací, jako je například [ModSecurity](https://www.modsecurity.org/), k posílení aplikace.</span><span class="sxs-lookup"><span data-stu-id="31eef-270">Consider using a web app firewall, such as [ModSecurity](https://www.modsecurity.org/), to harden the app.</span></span>

#### <a name="https-configuration"></a><span data-ttu-id="31eef-271">Konfigurace HTTPS</span><span class="sxs-lookup"><span data-stu-id="31eef-271">HTTPS configuration</span></span>

<span data-ttu-id="31eef-272">**Konfigurace místních připojení (HTTPS) aplikace pro zabezpečení**</span><span class="sxs-lookup"><span data-stu-id="31eef-272">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="31eef-273">Příkaz příkazového řádku [dotnet](/dotnet/core/tools/dotnet-run) používá *vlastnosti nebo launchSettings.jsaplikace v* souboru, který nakonfiguruje aplikaci tak, aby naslouchala adresám URL poskytnutým `applicationUrl` vlastností (například `https://localhost:5001;http://localhost:5000` ).</span><span class="sxs-lookup"><span data-stu-id="31eef-273">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="31eef-274">Nakonfigurujte aplikaci tak, aby používala certifikát ve vývoji pro `dotnet run` příkazové nebo vývojové prostředí (F5 nebo CTRL + F5 v Visual Studio Code), a to pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="31eef-274">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="31eef-275">[Nahradit výchozí certifikát z konfigurace](xref:fundamentals/servers/kestrel#configuration) (*doporučeno*)</span><span class="sxs-lookup"><span data-stu-id="31eef-275">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="31eef-276">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="31eef-276">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="31eef-277">**Konfigurace připojení klienta reverzního proxy serveru pro zabezpečení (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="31eef-277">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

* <span data-ttu-id="31eef-278">Nakonfigurujte server tak, aby naslouchal provozu protokolu HTTPS na portu `443` tím, že zadáte platný certifikát vydaný důvěryhodnou certifikační autoritou (CA).</span><span class="sxs-lookup"><span data-stu-id="31eef-278">Configure the server to listen to HTTPS traffic on port `443` by specifying a valid certificate issued by a trusted Certificate Authority (CA).</span></span>

* <span data-ttu-id="31eef-279">Posílit zabezpečení tím, že se využívaly některé postupy, které jsou znázorněné v následujícím souboru */etc/Nginx/Nginx.conf* .</span><span class="sxs-lookup"><span data-stu-id="31eef-279">Harden the security by employing some of the practices depicted in the following */etc/nginx/nginx.conf* file.</span></span> <span data-ttu-id="31eef-280">Mezi příklady patří výběr silnější šifry a přesměrování veškerého provozu přes protokol HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="31eef-280">Examples include choosing a stronger cipher and redirecting all traffic over HTTP to HTTPS.</span></span>

* <span data-ttu-id="31eef-281">Přidáním `HTTP Strict-Transport-Security` hlavičky (HSTS) se zajistí, že všechny následné požadavky, které klient provede, budou přes protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="31eef-281">Adding an `HTTP Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS.</span></span>

* <span data-ttu-id="31eef-282">Pokud bude v budoucnu zakázán protokol HTTPS, použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="31eef-282">If HTTPS will be disabled in the future, use one of the following approaches:</span></span>

  * <span data-ttu-id="31eef-283">Nepřidávejte hlavičku HSTS.</span><span class="sxs-lookup"><span data-stu-id="31eef-283">Don't add the HSTS header.</span></span>
  * <span data-ttu-id="31eef-284">Vyberte krátkou `max-age` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="31eef-284">Choose a short `max-age` value.</span></span>

<span data-ttu-id="31eef-285">Přidejte konfigurační soubor */etc/Nginx/proxy.conf* :</span><span class="sxs-lookup"><span data-stu-id="31eef-285">Add the */etc/nginx/proxy.conf* configuration file:</span></span>

[!code-nginx[](linux-nginx/proxy.conf)]

<span data-ttu-id="31eef-286">Upravte konfigurační soubor */etc/Nginx/Nginx.conf* .</span><span class="sxs-lookup"><span data-stu-id="31eef-286">Edit the */etc/nginx/nginx.conf* configuration file.</span></span> <span data-ttu-id="31eef-287">Příklad obsahuje i `http` `server` oddíly v jednom konfiguračním souboru.</span><span class="sxs-lookup"><span data-stu-id="31eef-287">The example contains both `http` and `server` sections in one configuration file.</span></span>

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

> [!NOTE]
> <span data-ttu-id="31eef-288">Blazor WebAssembly aplikace vyžadují větší `burst` hodnotu parametru, aby vyhovovala většímu počtu požadavků, které aplikace vytvořila.</span><span class="sxs-lookup"><span data-stu-id="31eef-288">Blazor WebAssembly apps require a larger `burst` parameter value to accommodate the larger number of requests made by an app.</span></span> <span data-ttu-id="31eef-289">Další informace naleznete v tématu <xref:blazor/host-and-deploy/webassembly#nginx>.</span><span class="sxs-lookup"><span data-stu-id="31eef-289">For more information, see <xref:blazor/host-and-deploy/webassembly#nginx>.</span></span>

#### <a name="secure-nginx-from-clickjacking"></a><span data-ttu-id="31eef-290">Zabezpečení Nginx z clickjacking</span><span class="sxs-lookup"><span data-stu-id="31eef-290">Secure Nginx from clickjacking</span></span>

<span data-ttu-id="31eef-291">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), označovaný také jako *útok s opravou uživatelského rozhraní*, je škodlivý útok, při kterém návštěvník webu získá odkaz nebo tlačítko na jiné stránce, než se právě navštíví.</span><span class="sxs-lookup"><span data-stu-id="31eef-291">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="31eef-292">Slouží `X-FRAME-OPTIONS` k zabezpečení lokality.</span><span class="sxs-lookup"><span data-stu-id="31eef-292">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="31eef-293">Zmírnění útoků Clickjacking:</span><span class="sxs-lookup"><span data-stu-id="31eef-293">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="31eef-294">Upravte soubor *Nginx. conf* :</span><span class="sxs-lookup"><span data-stu-id="31eef-294">Edit the *nginx.conf* file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="31eef-295">Přidejte řádek `add_header X-Frame-Options "SAMEORIGIN";`.</span><span class="sxs-lookup"><span data-stu-id="31eef-295">Add the line `add_header X-Frame-Options "SAMEORIGIN";`.</span></span>
1. <span data-ttu-id="31eef-296">Soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="31eef-296">Save the file.</span></span>
1. <span data-ttu-id="31eef-297">Restartujte Nginx.</span><span class="sxs-lookup"><span data-stu-id="31eef-297">Restart Nginx.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="31eef-298">Sledování typu MIME</span><span class="sxs-lookup"><span data-stu-id="31eef-298">MIME-type sniffing</span></span>

<span data-ttu-id="31eef-299">Toto záhlaví brání většině prohlížečů ze služby MIME-sledovat odpověď od deklarovaného typu obsahu, protože záhlaví instruuje prohlížeč, že nepřepisuje typ obsahu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="31eef-299">This header prevents most browsers from MIME-sniffing a response away from the declared content type, as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="31eef-300">`nosniff`Pokud server říká, že je obsahem text/HTML, prohlížeč ho vykreslí jako text/HTML.</span><span class="sxs-lookup"><span data-stu-id="31eef-300">With the `nosniff` option, if the server says the content is "text/html", the browser renders it as "text/html".</span></span>

<span data-ttu-id="31eef-301">Upravte soubor *Nginx. conf* :</span><span class="sxs-lookup"><span data-stu-id="31eef-301">Edit the *nginx.conf* file:</span></span>

```bash
sudo nano /etc/nginx/nginx.conf
```

<span data-ttu-id="31eef-302">Přidejte řádek `add_header X-Content-Type-Options "nosniff";` a uložte soubor a pak Nginx restartujte.</span><span class="sxs-lookup"><span data-stu-id="31eef-302">Add the line `add_header X-Content-Type-Options "nosniff";` and save the file, then restart Nginx.</span></span>

## <a name="additional-nginx-suggestions"></a><span data-ttu-id="31eef-303">Další návrhy Nginx</span><span class="sxs-lookup"><span data-stu-id="31eef-303">Additional Nginx suggestions</span></span>

<span data-ttu-id="31eef-304">Po upgradu sdílené architektury na serveru restartujte aplikace ASP.NET Core hostované serverem.</span><span class="sxs-lookup"><span data-stu-id="31eef-304">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="31eef-305">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="31eef-305">Additional resources</span></span>

* [<span data-ttu-id="31eef-306">Předpoklady pro .NET Core v systému Linux</span><span class="sxs-lookup"><span data-stu-id="31eef-306">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* [<span data-ttu-id="31eef-307">Nginx: binární verze: oficiální balíčky Debian/Ubuntu</span><span class="sxs-lookup"><span data-stu-id="31eef-307">Nginx: Binary Releases: Official Debian/Ubuntu packages</span></span>](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="31eef-308">NGINX: používá se předávaná hlavička.</span><span class="sxs-lookup"><span data-stu-id="31eef-308">NGINX: Using the Forwarded header</span></span>](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
