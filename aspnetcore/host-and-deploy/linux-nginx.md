---
title: Hostitele ASP.NET Core v Linuxu se serverem Nginx
author: rick-anderson
description: Další informace o nastavení serveru Nginx jako reverzní proxy server na Ubuntu 16.04 směrovat provoz protokolu HTTP k webové aplikaci ASP.NET Core spuštěnou v prostředí Kestrel.
ms.author: riande
ms.custom: mvc
ms.date: 02/27/2019
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: 11754279d18a2449451364b4aaba723b7afb06d5
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345921"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a><span data-ttu-id="b709b-103">Hostitele ASP.NET Core v Linuxu se serverem Nginx</span><span class="sxs-lookup"><span data-stu-id="b709b-103">Host ASP.NET Core on Linux with Nginx</span></span>

<span data-ttu-id="b709b-104">Podle [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="b709b-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="b709b-105">Tato příručka vysvětluje nastavení prostředí připravené pro produkční prostředí ASP.NET Core na serveru se systémem Ubuntu 16.04.</span><span class="sxs-lookup"><span data-stu-id="b709b-105">This guide explains setting up a production-ready ASP.NET Core environment on an Ubuntu 16.04 server.</span></span> <span data-ttu-id="b709b-106">Tyto pokyny mohou pracovat s novějšími verzemi Ubuntu, ale pokynů nebyly testovány s novější verzí.</span><span class="sxs-lookup"><span data-stu-id="b709b-106">These instructions likely work with newer versions of Ubuntu, but the instructions haven't been tested with newer versions.</span></span>

<span data-ttu-id="b709b-107">Informace v jiné distribuce Linuxu podporuje ASP.NET Core najdete v tématu [předpoklady pro .NET Core v Linuxu](/dotnet/core/linux-prerequisites).</span><span class="sxs-lookup"><span data-stu-id="b709b-107">For information on other Linux distributions supported by ASP.NET Core, see [Prerequisites for .NET Core on Linux](/dotnet/core/linux-prerequisites).</span></span>

> [!NOTE]
> <span data-ttu-id="b709b-108">Pro Ubuntu 14.04 *supervisord* jako řešení pro monitorování procesu Kestrel se doporučuje.</span><span class="sxs-lookup"><span data-stu-id="b709b-108">For Ubuntu 14.04, *supervisord* is recommended as a solution for monitoring the Kestrel process.</span></span> <span data-ttu-id="b709b-109">*systemd* není k dispozici na Ubuntu 14.04.</span><span class="sxs-lookup"><span data-stu-id="b709b-109">*systemd* isn't available on Ubuntu 14.04.</span></span> <span data-ttu-id="b709b-110">Ubuntu 14.04 pokyny najdete v tématu [předchozí verzi tohoto tématu](https://github.com/aspnet/Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span><span class="sxs-lookup"><span data-stu-id="b709b-110">For Ubuntu 14.04 instructions, see the [previous version of this topic](https://github.com/aspnet/Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span></span>

<span data-ttu-id="b709b-111">Tento průvodce:</span><span class="sxs-lookup"><span data-stu-id="b709b-111">This guide:</span></span>

* <span data-ttu-id="b709b-112">Umístí stávající aplikace ASP.NET Core za reverzní proxy server.</span><span class="sxs-lookup"><span data-stu-id="b709b-112">Places an existing ASP.NET Core app behind a reverse proxy server.</span></span>
* <span data-ttu-id="b709b-113">Nastaví předávat požadavky na webový server Kestrel reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="b709b-113">Sets up the reverse proxy server to forward requests to the Kestrel web server.</span></span>
* <span data-ttu-id="b709b-114">Zajišťuje, že webová aplikace spuštěna při spuštění jako démon.</span><span class="sxs-lookup"><span data-stu-id="b709b-114">Ensures the web app runs on startup as a daemon.</span></span>
* <span data-ttu-id="b709b-115">Konfiguruje nástroj pro správu proces usnadňují, restartujte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b709b-115">Configures a process management tool to help restart the web app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b709b-116">Požadavky</span><span class="sxs-lookup"><span data-stu-id="b709b-116">Prerequisites</span></span>

1. <span data-ttu-id="b709b-117">Přístup k serveru se systémem Ubuntu 16.04 pomocí standardního uživatelského účtu s oprávněními sudo.</span><span class="sxs-lookup"><span data-stu-id="b709b-117">Access to an Ubuntu 16.04 server with a standard user account with sudo privilege.</span></span>
1. <span data-ttu-id="b709b-118">Nainstalujte modul runtime .NET Core na serveru.</span><span class="sxs-lookup"><span data-stu-id="b709b-118">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="b709b-119">Přejděte [.NET Core všechny soubory ke stažení stránky](https://www.microsoft.com/net/download/all).</span><span class="sxs-lookup"><span data-stu-id="b709b-119">Visit the [.NET Core All Downloads page](https://www.microsoft.com/net/download/all).</span></span>
   1. <span data-ttu-id="b709b-120">Vyberte nejnovější modul runtime – ve verzi preview ze seznamu **Runtime**.</span><span class="sxs-lookup"><span data-stu-id="b709b-120">Select the latest non-preview runtime from the list under **Runtime**.</span></span>
   1. <span data-ttu-id="b709b-121">Vyberte a postupujte podle pokynů pro Ubuntu, které odpovídají verze Ubuntu serveru.</span><span class="sxs-lookup"><span data-stu-id="b709b-121">Select and follow the instructions for Ubuntu that match the Ubuntu version of the server.</span></span>
1. <span data-ttu-id="b709b-122">Stávající aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b709b-122">An existing ASP.NET Core app.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="b709b-123">Publikování a zkopírujte myší na aplikaci</span><span class="sxs-lookup"><span data-stu-id="b709b-123">Publish and copy over the app</span></span>

<span data-ttu-id="b709b-124">Konfigurace aplikace pro [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="b709b-124">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="b709b-125">Spustit [dotnet publikovat](/dotnet/core/tools/dotnet-publish) z vývojového prostředí pro balíček aplikace do adresáře (například *bin/Release/&lt;target_framework_moniker&gt;/ publish*), který můžete Spusťte na serveru:</span><span class="sxs-lookup"><span data-stu-id="b709b-125">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```console
dotnet publish --configuration Release
```

<span data-ttu-id="b709b-126">Aplikace můžete také publikovat jako [samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) Pokud nechcete zachovat modulu runtime .NET Core na serveru.</span><span class="sxs-lookup"><span data-stu-id="b709b-126">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="b709b-127">Aplikace ASP.NET Core zkopírujte na server pomocí nástroje, které se integruje do pracovního postupu organizace (třeba spojovací bod služby, SFTP).</span><span class="sxs-lookup"><span data-stu-id="b709b-127">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="b709b-128">Je běžné vyhledejte webové aplikace v rámci *var* adresář (třeba *www/var/helloapp*).</span><span class="sxs-lookup"><span data-stu-id="b709b-128">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="b709b-129">V případě produkčního nasazení pracovního postupu průběžné integrace funguje publikování aplikace a kopírování prostředky na server.</span><span class="sxs-lookup"><span data-stu-id="b709b-129">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

<span data-ttu-id="b709b-130">Testování aplikace:</span><span class="sxs-lookup"><span data-stu-id="b709b-130">Test the app:</span></span>

1. <span data-ttu-id="b709b-131">Z příkazového řádku, spusťte aplikaci: `dotnet <app_assembly>.dll`.</span><span class="sxs-lookup"><span data-stu-id="b709b-131">From the command line, run the app: `dotnet <app_assembly>.dll`.</span></span>
1. <span data-ttu-id="b709b-132">V prohlížeči přejděte na `http://<serveraddress>:<port>` k ověření, že aplikace funguje na platformě Linux místně.</span><span class="sxs-lookup"><span data-stu-id="b709b-132">In a browser, navigate to `http://<serveraddress>:<port>` to verify the app works on Linux locally.</span></span>

## <a name="configure-a-reverse-proxy-server"></a><span data-ttu-id="b709b-133">Konfigurace reverzního proxy serveru</span><span class="sxs-lookup"><span data-stu-id="b709b-133">Configure a reverse proxy server</span></span>

<span data-ttu-id="b709b-134">Reverzní proxy server je společné nastavení pro poskytování dynamické webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="b709b-134">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="b709b-135">Reverzní proxy server ukončí požadavek HTTP a předá jej do aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b709b-135">A reverse proxy terminates the HTTP request and forwards it to the ASP.NET Core app.</span></span>

### <a name="use-a-reverse-proxy-server"></a><span data-ttu-id="b709b-136">Použít reverzní proxy server</span><span class="sxs-lookup"><span data-stu-id="b709b-136">Use a reverse proxy server</span></span>

<span data-ttu-id="b709b-137">Kestrel se skvěle hodí pro poskytování dynamický obsah z ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b709b-137">Kestrel is great for serving dynamic content from ASP.NET Core.</span></span> <span data-ttu-id="b709b-138">Však nejsou možnosti obsluhující web jako komplexní jako servery služby IIS, Apache nebo Nginx.</span><span class="sxs-lookup"><span data-stu-id="b709b-138">However, the web serving capabilities aren't as feature rich as servers such as IIS, Apache, or Nginx.</span></span> <span data-ttu-id="b709b-139">Reverzní proxy server může převzít práce, jako je například obsluhuje statický obsah, ukládání do mezipaměti požadavky, komprese požadavků a ukončení HTTPS ze serveru HTTP.</span><span class="sxs-lookup"><span data-stu-id="b709b-139">A reverse proxy server can offload work such as serving static content, caching requests, compressing requests, and HTTPS termination from the HTTP server.</span></span> <span data-ttu-id="b709b-140">Reverzní proxy server může nacházet na vyhrazený počítač nebo může být nasadí společně se službou serveru HTTP.</span><span class="sxs-lookup"><span data-stu-id="b709b-140">A reverse proxy server may reside on a dedicated machine or may be deployed alongside an HTTP server.</span></span>

<span data-ttu-id="b709b-141">Pro účely tohoto průvodce se používá jednu instanci serveru Nginx.</span><span class="sxs-lookup"><span data-stu-id="b709b-141">For the purposes of this guide, a single instance of Nginx is used.</span></span> <span data-ttu-id="b709b-142">Běží na stejném serveru, spolu s HTTP serverem.</span><span class="sxs-lookup"><span data-stu-id="b709b-142">It runs on the same server, alongside the HTTP server.</span></span> <span data-ttu-id="b709b-143">Na základě požadavků, může být zvolen jiný instalační program.</span><span class="sxs-lookup"><span data-stu-id="b709b-143">Based on requirements, a different setup may be chosen.</span></span>

<span data-ttu-id="b709b-144">Vzhledem k tomu, že žádosti jsou předávány podle reverzní proxy server, použít [předané Middleware záhlaví](xref:host-and-deploy/proxy-load-balancer) z [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="b709b-144">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="b709b-145">Middleware aktualizace `Request.Scheme`, použije `X-Forwarded-Proto` záhlaví tak, že identifikátory URI pro přesměrování a další zásady zabezpečení pracovat správně.</span><span class="sxs-lookup"><span data-stu-id="b709b-145">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="b709b-146">Jakékoli součásti, která závisí na schéma, jako je například ověřování, generování odkazů, přesměrování a zeměpisná poloha, musí být umístěn po vyvolání Middleware předané záhlaví.</span><span class="sxs-lookup"><span data-stu-id="b709b-146">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span> <span data-ttu-id="b709b-147">Jako obecné pravidlo by měla předávat Middleware záhlaví spustit před dalším middlewarem s výjimkou diagnostiky a middleware pro zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="b709b-147">As a general rule, Forwarded Headers Middleware should run before other middleware except diagnostics and error handling middleware.</span></span> <span data-ttu-id="b709b-148">Toto uspořádání zajistí, že middleware spoléhání se na informace předávané záhlaví může spotřebovat hodnoty hlavičky pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="b709b-148">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b709b-149">Vyvolat <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metoda `Startup.Configure` před voláním <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> nebo podobné režimu middleware ověřování.</span><span class="sxs-lookup"><span data-stu-id="b709b-149">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or similar authentication scheme middleware.</span></span> <span data-ttu-id="b709b-150">Nakonfigurujte middleware předávat `X-Forwarded-For` a `X-Forwarded-Proto` hlavičky:</span><span class="sxs-lookup"><span data-stu-id="b709b-150">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b709b-151">Vyvolat <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metoda `Startup.Configure` před voláním <xref:Microsoft.AspNetCore.Builder.BuilderExtensions.UseIdentity*> a <xref:Microsoft.AspNetCore.Builder.FacebookAppBuilderExtensions.UseFacebookAuthentication*> nebo podobné režimu middleware ověřování.</span><span class="sxs-lookup"><span data-stu-id="b709b-151">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling <xref:Microsoft.AspNetCore.Builder.BuilderExtensions.UseIdentity*> and <xref:Microsoft.AspNetCore.Builder.FacebookAppBuilderExtensions.UseFacebookAuthentication*> or similar authentication scheme middleware.</span></span> <span data-ttu-id="b709b-152">Nakonfigurujte middleware předávat `X-Forwarded-For` a `X-Forwarded-Proto` hlavičky:</span><span class="sxs-lookup"><span data-stu-id="b709b-152">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseIdentity();
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

::: moniker-end

<span data-ttu-id="b709b-153">Pokud ne <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> jsou určené pro middleware, jsou výchozí hlavičky pro předávání `None`.</span><span class="sxs-lookup"><span data-stu-id="b709b-153">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="b709b-154">Proxy servery, které běží na adresu zpětné smyčky (127.0.0.0/8, [:: 1]), včetně standardní localhost adresu (127.0.0.1), ve výchozím nastavení jsou důvěryhodné.</span><span class="sxs-lookup"><span data-stu-id="b709b-154">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="b709b-155">Pokud jiné důvěryhodné proxy nebo sítěmi v rámci popisovač požadavky organizace mezi Internetem a webový server, je přidat do seznamu <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> nebo <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span><span class="sxs-lookup"><span data-stu-id="b709b-155">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="b709b-156">Následující příklad přidá důvěryhodným proxy serveru na IP adrese 10.0.0.100 s Middlewarem předané záhlaví `KnownProxies` v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b709b-156">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="b709b-157">Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="b709b-157">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-nginx"></a><span data-ttu-id="b709b-158">Instalaci serveru Nginx</span><span class="sxs-lookup"><span data-stu-id="b709b-158">Install Nginx</span></span>

<span data-ttu-id="b709b-159">Použití `apt-get` nainstaluje server Nginx.</span><span class="sxs-lookup"><span data-stu-id="b709b-159">Use `apt-get` to install Nginx.</span></span> <span data-ttu-id="b709b-160">Instalační program vytvoří *systemd* init skript, který spouští server Nginx jako démon na spuštění systému.</span><span class="sxs-lookup"><span data-stu-id="b709b-160">The installer creates a *systemd* init script that runs Nginx as daemon on system startup.</span></span> <span data-ttu-id="b709b-161">Postupujte podle pokynů pro Ubuntu na [Nginx: Oficiální Debian nebo Ubuntu balíčky](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span><span class="sxs-lookup"><span data-stu-id="b709b-161">Follow the installation instructions for Ubuntu at [Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span></span>

> [!NOTE]
> <span data-ttu-id="b709b-162">Pokud volitelný moduly Nginx, může být potřeba vytváření Nginx ze zdroje.</span><span class="sxs-lookup"><span data-stu-id="b709b-162">If optional Nginx modules are required, building Nginx from source might be required.</span></span>

<span data-ttu-id="b709b-163">Protože server Nginx nainstaloval poprvé, explicitně spusťte ji spuštěním:</span><span class="sxs-lookup"><span data-stu-id="b709b-163">Since Nginx was installed for the first time, explicitly start it by running:</span></span>

```bash
sudo service nginx start
```

<span data-ttu-id="b709b-164">Ověřte, že prohlížeč zobrazí výchozí úvodní stránka pro server Nginx.</span><span class="sxs-lookup"><span data-stu-id="b709b-164">Verify a browser displays the default landing page for Nginx.</span></span> <span data-ttu-id="b709b-165">Cílová stránka je dostupný na `http://<server_IP_address>/index.nginx-debian.html`.</span><span class="sxs-lookup"><span data-stu-id="b709b-165">The landing page is reachable at `http://<server_IP_address>/index.nginx-debian.html`.</span></span>

### <a name="configure-nginx"></a><span data-ttu-id="b709b-166">Konfigurace serveru Nginx</span><span class="sxs-lookup"><span data-stu-id="b709b-166">Configure Nginx</span></span>

<span data-ttu-id="b709b-167">Chcete-li nakonfigurovat Nginx jako reverzní proxy server pro směrování požadavků do vaší aplikace ASP.NET Core, upravte */etc/nginx/sites-available/default*.</span><span class="sxs-lookup"><span data-stu-id="b709b-167">To configure Nginx as a reverse proxy to forward requests to your ASP.NET Core app, modify */etc/nginx/sites-available/default*.</span></span> <span data-ttu-id="b709b-168">Otevřete v textovém editoru a nahraďte obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="b709b-168">Open it in a text editor, and replace the contents with the following:</span></span>

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

<span data-ttu-id="b709b-169">Pokud ne `server_name` shody, Nginx používá výchozí server.</span><span class="sxs-lookup"><span data-stu-id="b709b-169">When no `server_name` matches, Nginx uses the default server.</span></span> <span data-ttu-id="b709b-170">Pokud není definovaný žádný výchozí server, první server v konfiguračním souboru je výchozí server.</span><span class="sxs-lookup"><span data-stu-id="b709b-170">If no default server is defined, the first server in the configuration file is the default server.</span></span> <span data-ttu-id="b709b-171">Jako osvědčený postup přidáte určité výchozí server, který vrátí stavový kód 444 v konfiguračním souboru.</span><span class="sxs-lookup"><span data-stu-id="b709b-171">As a best practice, add a specific default server which returns a status code of 444 in your configuration file.</span></span> <span data-ttu-id="b709b-172">Příklad konfigurace serveru výchozí je:</span><span class="sxs-lookup"><span data-stu-id="b709b-172">A default server configuration example is:</span></span>

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

<span data-ttu-id="b709b-173">S předchozím konfigurační soubor a výchozí server, server Nginx přijímá veřejné provozu na portu 80 se hlavička hostitele `example.com` nebo `*.example.com`.</span><span class="sxs-lookup"><span data-stu-id="b709b-173">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="b709b-174">Požadavky nejsou odpovídající tito hostitelé se získat předány Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b709b-174">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="b709b-175">Nginx předává požadavky odpovídající Kestrel na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="b709b-175">Nginx forwards the matching requests to Kestrel at `http://localhost:5000`.</span></span> <span data-ttu-id="b709b-176">Zobrazit [jak nginx zpracovává žádost](https://nginx.org/docs/http/request_processing.html) Další informace.</span><span class="sxs-lookup"><span data-stu-id="b709b-176">See [How nginx processes a request](https://nginx.org/docs/http/request_processing.html) for more information.</span></span> <span data-ttu-id="b709b-177">Chcete-li změnit Kestrel jeho IP adresa/port, [Kestrel: Konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="b709b-177">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="b709b-178">Nepodařilo se určit správnou [název_serveru směrnice](https://nginx.org/docs/http/server_names.html) zpřístupňuje aplikaci tak, aby slabá místa zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="b709b-178">Failure to specify a proper [server_name directive](https://nginx.org/docs/http/server_names.html) exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="b709b-179">Vazby zástupný znak subdoménu (například `*.example.com`) nemá představovat toto bezpečnostní riziko, pokud řídíte celý nadřazené domény (nikoli `*.com`, což je ohrožené).</span><span class="sxs-lookup"><span data-stu-id="b709b-179">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="b709b-180">Zobrazit [rfc7230 části-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) Další informace.</span><span class="sxs-lookup"><span data-stu-id="b709b-180">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="b709b-181">Jakmile se naváže v konfiguraci serveru Nginx, spusťte `sudo nginx -t` syntaxi konfiguračních souborů.</span><span class="sxs-lookup"><span data-stu-id="b709b-181">Once the Nginx configuration is established, run `sudo nginx -t` to verify the syntax of the configuration files.</span></span> <span data-ttu-id="b709b-182">Pokud konfigurační soubor test je úspěšný, vynutit Nginx tak, aby získaly změn spuštěním `sudo nginx -s reload`.</span><span class="sxs-lookup"><span data-stu-id="b709b-182">If the configuration file test is successful, force Nginx to pick up the changes by running `sudo nginx -s reload`.</span></span>

<span data-ttu-id="b709b-183">Pro přímé spouštění aplikace na serveru:</span><span class="sxs-lookup"><span data-stu-id="b709b-183">To directly run the app on the server:</span></span>

1. <span data-ttu-id="b709b-184">Přejděte do adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="b709b-184">Navigate to the app's directory.</span></span>
1. <span data-ttu-id="b709b-185">Spuštění aplikace: `dotnet <app_assembly.dll>`, kde `app_assembly.dll` je název souboru sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="b709b-185">Run the app: `dotnet <app_assembly.dll>`, where `app_assembly.dll` is the assembly file name of the app.</span></span>

<span data-ttu-id="b709b-186">Pokud aplikace běží na serveru, ale přestane reagovat přes Internet, zkontrolujte bránu firewall serveru a potvrďte, že je otevřený port 80.</span><span class="sxs-lookup"><span data-stu-id="b709b-186">If the app runs on the server but fails to respond over the Internet, check the server's firewall and confirm that port 80 is open.</span></span> <span data-ttu-id="b709b-187">Pokud používáte virtuální počítač Azure s Ubuntu, přidejte pravidlo skupiny zabezpečení sítě (NSG), která umožní příchozí port 80 provoz.</span><span class="sxs-lookup"><span data-stu-id="b709b-187">If using an Azure Ubuntu VM, add a Network Security Group (NSG) rule that enables inbound port 80 traffic.</span></span> <span data-ttu-id="b709b-188">Není nutné povolit pravidlo odchozí port 80 jako odchozí provoz se automaticky udělí, když je povolený příchozí pravidlo.</span><span class="sxs-lookup"><span data-stu-id="b709b-188">There's no need to enable an outbound port 80 rule, as the outbound traffic is automatically granted when the inbound rule is enabled.</span></span>

<span data-ttu-id="b709b-189">Po dokončení testování aplikace vypnout aplikaci s `Ctrl+C` příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="b709b-189">When done testing the app, shut the app down with `Ctrl+C` at the command prompt.</span></span>

## <a name="monitor-the-app"></a><span data-ttu-id="b709b-190">Sledování aplikace</span><span class="sxs-lookup"><span data-stu-id="b709b-190">Monitor the app</span></span>

<span data-ttu-id="b709b-191">Server je instalačního programu předat požadavky na `http://<serveraddress>:80` k aplikaci ASP.NET Core spuštěnou v Kestrel na `http://127.0.0.1:5000`.</span><span class="sxs-lookup"><span data-stu-id="b709b-191">The server is setup to forward requests made to `http://<serveraddress>:80` on to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="b709b-192">Server Nginx se ale nastavit ke správě procesu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b709b-192">However, Nginx isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="b709b-193">*systemd* slouží k vytvoření souboru služby ke spuštění a monitorování základní webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="b709b-193">*systemd* can be used to create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="b709b-194">*systemd* je init systém, který poskytuje řadu výkonných funkcí pro spouštění, zastavování a Správa procesů.</span><span class="sxs-lookup"><span data-stu-id="b709b-194">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="b709b-195">Vytvoření souboru služby</span><span class="sxs-lookup"><span data-stu-id="b709b-195">Create the service file</span></span>

<span data-ttu-id="b709b-196">Vytvoření definičního souboru služby:</span><span class="sxs-lookup"><span data-stu-id="b709b-196">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="b709b-197">Následuje příklad souboru služby pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="b709b-197">The following is an example service file for the app:</span></span>

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

<span data-ttu-id="b709b-198">Pokud uživatel *www-data* nepoužívá konfigurace, musí nejprve vytvořit uživatelem definované tady a pro soubory zadané správné vlastnictví.</span><span class="sxs-lookup"><span data-stu-id="b709b-198">If the user *www-data* isn't used by the configuration, the user defined here must be created first and given proper ownership for files.</span></span>

<span data-ttu-id="b709b-199">Použití `TimeoutStopSec` nakonfigurovat doba čekání na aplikaci pro vypnutí po přijetí počáteční přerušení signálu.</span><span class="sxs-lookup"><span data-stu-id="b709b-199">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="b709b-200">Pokud aplikace není v tomto období vypnout, objeví se SIGKILL ukončit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b709b-200">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="b709b-201">Zadejte hodnotu unitless sekund (například `150`), časový interval hodnotu (například `2min 30s`), nebo `infinity` zakázat časový limit.</span><span class="sxs-lookup"><span data-stu-id="b709b-201">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="b709b-202">`TimeoutStopSec` Výchozí hodnota je hodnota `DefaultTimeoutStopSec` v konfiguračním souboru správce (*systemd system.conf*, *system.conf.d*, *systemd user.conf*,  *User.conf.d*).</span><span class="sxs-lookup"><span data-stu-id="b709b-202">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="b709b-203">Výchozí hodnota časového limitu pro většinu distribuce je 90 sekund.</span><span class="sxs-lookup"><span data-stu-id="b709b-203">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="b709b-204">Linux má systém souborů s rozlišením velkých.</span><span class="sxs-lookup"><span data-stu-id="b709b-204">Linux has a case-sensitive file system.</span></span> <span data-ttu-id="b709b-205">Nastavení ASPNETCORE_ENVIRONMENT "Produkční" výsledky hledání pro konfigurační soubor *appsettings. Production.JSON*, nikoli *appsettings.production.json*.</span><span class="sxs-lookup"><span data-stu-id="b709b-205">Setting ASPNETCORE_ENVIRONMENT to "Production" results in searching for the configuration file *appsettings.Production.json*, not *appsettings.production.json*.</span></span>

<span data-ttu-id="b709b-206">Některé hodnoty (například připojovací řetězce SQL) musí být uvozena pro zprostředkovatele konfigurace pro čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="b709b-206">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="b709b-207">Použijte následující příkaz k vygenerování správně uvozený uvozovacím znakem hodnoty pro použití v konfiguračním souboru:</span><span class="sxs-lookup"><span data-stu-id="b709b-207">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

<span data-ttu-id="b709b-208">Uložte soubor a povolení služby.</span><span class="sxs-lookup"><span data-stu-id="b709b-208">Save the file and enable the service.</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="b709b-209">Spusťte službu a ověřte, zda je spuštěna.</span><span class="sxs-lookup"><span data-stu-id="b709b-209">Start the service and verify that it's running.</span></span>

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

<span data-ttu-id="b709b-210">Reverzní proxy server nakonfigurovat a spravovat prostřednictvím systemd Kestrel, webové aplikace plně konfigurována a je přístupný z prohlížeče na místním počítači v `http://localhost`.</span><span class="sxs-lookup"><span data-stu-id="b709b-210">With the reverse proxy configured and Kestrel managed through systemd, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="b709b-211">Je také přístupné ze vzdáleného počítače, blokování jakoukoli jinou bránu firewall, která může blokovat.</span><span class="sxs-lookup"><span data-stu-id="b709b-211">It's also accessible from a remote machine, barring any firewall that might be blocking.</span></span> <span data-ttu-id="b709b-212">Kontrola hlavičky odpovědi `Server` záhlaví zobrazuje obsluhuje Kestrel aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b709b-212">Inspecting the response headers, the `Server` header shows the ASP.NET Core app being served by Kestrel.</span></span>

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="b709b-213">Zobrazení protokolů</span><span class="sxs-lookup"><span data-stu-id="b709b-213">View logs</span></span>

<span data-ttu-id="b709b-214">Od webové aplikace pomocí Kestrel se spravuje pomocí `systemd`, centralizované deníku se protokolují všechny události a procesy.</span><span class="sxs-lookup"><span data-stu-id="b709b-214">Since the web app using Kestrel is managed using `systemd`, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="b709b-215">Ale tento deník obsahuje všechny položky pro všechny služby a spravuje procesy `systemd`.</span><span class="sxs-lookup"><span data-stu-id="b709b-215">However, this journal includes all entries for all services and processes managed by `systemd`.</span></span> <span data-ttu-id="b709b-216">Chcete-li zobrazit `kestrel-helloapp.service`-konkrétní položky, použijte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="b709b-216">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="b709b-217">Pro další filtrování, možnosti, jak `--since today`, `--until 1 hour ago` nebo kombinaci těchto způsobů můžete omezit množství vrácených záznamů.</span><span class="sxs-lookup"><span data-stu-id="b709b-217">For further filtering, time options such as `--since today`, `--until 1 hour ago` or a combination of these can reduce the amount of entries returned.</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="b709b-218">Ochrana dat</span><span class="sxs-lookup"><span data-stu-id="b709b-218">Data protection</span></span>

<span data-ttu-id="b709b-219">[Ochranu dat ASP.NET Core zásobníku](xref:security/data-protection/introduction) používá několik ASP.NET Core [middlewares](xref:fundamentals/middleware/index), včetně middleware ověřování (například middlewaru souboru cookie.) a mezi weby (CSRF) proti padělání požadavků ochranu.</span><span class="sxs-lookup"><span data-stu-id="b709b-219">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="b709b-220">I v případě, že Data Protection API nejsou volané kódem uživatele, ochranu dat by měl být povolen vytvořit trvalé kryptografických [úložiště klíčů](xref:security/data-protection/implementation/key-management).</span><span class="sxs-lookup"><span data-stu-id="b709b-220">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="b709b-221">Pokud není nakonfigurovaná ochrana dat, jsou klíče uložené v paměti a při restartování aplikace.</span><span class="sxs-lookup"><span data-stu-id="b709b-221">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="b709b-222">Pokud kanál klíče jsou uloženy v paměti, při restartování aplikace:</span><span class="sxs-lookup"><span data-stu-id="b709b-222">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="b709b-223">Všechny tokeny ověřování na základě souborů cookie nejsou zneplatněny.</span><span class="sxs-lookup"><span data-stu-id="b709b-223">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="b709b-224">Uživatelé se musí znovu přihlásit v jejich další požadavek.</span><span class="sxs-lookup"><span data-stu-id="b709b-224">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="b709b-225">Všechna data chráněná pomocí aktualizační kanál, který klíč můžete už nebude možné dešifrovat.</span><span class="sxs-lookup"><span data-stu-id="b709b-225">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="b709b-226">To může zahrnovat [CSRF tokeny](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [soubory cookie v ASP.NET Core MVC TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="b709b-226">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="b709b-227">Konfigurace ochrany dat zachovat a aktualizační kanál, který klíč šifrování, najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="b709b-227">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a><span data-ttu-id="b709b-228">Pole hlavičky dlouhou žádost</span><span class="sxs-lookup"><span data-stu-id="b709b-228">Long request header fields</span></span>

<span data-ttu-id="b709b-229">Pokud aplikace vyžaduje požadavek déle, než je povolený proxy server pole hlavičky výchozí nastavení (obvykle 4 kB nebo 8 kb podle platformy), následující direktivy vyžadovat úpravy.</span><span class="sxs-lookup"><span data-stu-id="b709b-229">If the app requires request header fields longer than permitted by the proxy server's default settings (typically 4K or 8K depending on the platform), the following directives require adjustment.</span></span> <span data-ttu-id="b709b-230">Hodnoty pro použití jsou závislé na scénáři.</span><span class="sxs-lookup"><span data-stu-id="b709b-230">The values to apply are scenario-dependent.</span></span> <span data-ttu-id="b709b-231">Další informace najdete v dokumentaci k serveru.</span><span class="sxs-lookup"><span data-stu-id="b709b-231">For more information, see your server's documentation.</span></span>

* [<span data-ttu-id="b709b-232">proxy_buffer_size</span><span class="sxs-lookup"><span data-stu-id="b709b-232">proxy_buffer_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [<span data-ttu-id="b709b-233">proxy_buffers</span><span class="sxs-lookup"><span data-stu-id="b709b-233">proxy_buffers</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [<span data-ttu-id="b709b-234">proxy_busy_buffers_size</span><span class="sxs-lookup"><span data-stu-id="b709b-234">proxy_busy_buffers_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [<span data-ttu-id="b709b-235">large_client_header_buffers</span><span class="sxs-lookup"><span data-stu-id="b709b-235">large_client_header_buffers</span></span>](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> <span data-ttu-id="b709b-236">Výchozí hodnoty proxy vyrovnávací paměti není zvýšit, není-li nezbytné.</span><span class="sxs-lookup"><span data-stu-id="b709b-236">Don't increase the default values of proxy buffers unless necessary.</span></span> <span data-ttu-id="b709b-237">Zvýšení tyto hodnoty zvyšují riziko přetečení vyrovnávací paměti (přetečení) a uživateli se zlými úmysly útoků s cílem odepření služby (DoS).</span><span class="sxs-lookup"><span data-stu-id="b709b-237">Increasing these values increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="secure-the-app"></a><span data-ttu-id="b709b-238">Zabezpečení aplikace</span><span class="sxs-lookup"><span data-stu-id="b709b-238">Secure the app</span></span>

### <a name="enable-apparmor"></a><span data-ttu-id="b709b-239">Povolit AppArmor</span><span class="sxs-lookup"><span data-stu-id="b709b-239">Enable AppArmor</span></span>

<span data-ttu-id="b709b-240">Linux zabezpečení moduly (LSM) je architektura, která je součástí linuxového jádra od Linux 2.6.</span><span class="sxs-lookup"><span data-stu-id="b709b-240">Linux Security Modules (LSM) is a framework that's part of the Linux kernel since Linux 2.6.</span></span> <span data-ttu-id="b709b-241">LSM podporuje různé implementace modulů zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="b709b-241">LSM supports different implementations of security modules.</span></span> <span data-ttu-id="b709b-242">[AppArmor](https://wiki.ubuntu.com/AppArmor) je LSM, která implementuje systém povinné řízení přístupu, který umožňuje uzavírání program omezenou sadu prostředků.</span><span class="sxs-lookup"><span data-stu-id="b709b-242">[AppArmor](https://wiki.ubuntu.com/AppArmor) is a LSM that implements a Mandatory Access Control system which allows confining the program to a limited set of resources.</span></span> <span data-ttu-id="b709b-243">Zajištění AppArmor je povolená a správně nakonfigurovaná.</span><span class="sxs-lookup"><span data-stu-id="b709b-243">Ensure AppArmor is enabled and properly configured.</span></span>

### <a name="configure-the-firewall"></a><span data-ttu-id="b709b-244">Konfigurace brány firewall</span><span class="sxs-lookup"><span data-stu-id="b709b-244">Configure the firewall</span></span>

<span data-ttu-id="b709b-245">Zavřete vypnout všechny externí porty, které nejsou používány.</span><span class="sxs-lookup"><span data-stu-id="b709b-245">Close off all external ports that are not in use.</span></span> <span data-ttu-id="b709b-246">Znamená přístupnější aplikaci brány firewall (ufw) poskytuje front-endu pro `iptables` tím, že poskytuje rozhraní příkazového řádku pro konfiguraci brány firewall.</span><span class="sxs-lookup"><span data-stu-id="b709b-246">Uncomplicated firewall (ufw) provides a front end for `iptables` by providing a command line interface for configuring the firewall.</span></span>

> [!WARNING]
> <span data-ttu-id="b709b-247">Brána firewall brání přístupu k celému systému, pokud není nakonfigurovaná správně.</span><span class="sxs-lookup"><span data-stu-id="b709b-247">A firewall will prevent access to the whole system if not configured correctly.</span></span> <span data-ttu-id="b709b-248">Nepodařilo se určit správný port SSH bude efektivně případě k zablokování systému jsou k němu připojit pomocí SSH.</span><span class="sxs-lookup"><span data-stu-id="b709b-248">Failure to specify the correct SSH port will effectively lock you out of the system if you are using SSH to connect to it.</span></span> <span data-ttu-id="b709b-249">Výchozí port je 22.</span><span class="sxs-lookup"><span data-stu-id="b709b-249">The default port is 22.</span></span> <span data-ttu-id="b709b-250">Další informace najdete v tématu [Úvod do ufw](https://help.ubuntu.com/community/UFW) a [ruční](http://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span><span class="sxs-lookup"><span data-stu-id="b709b-250">For more information, see the [introduction to ufw](https://help.ubuntu.com/community/UFW) and the [manual](http://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span></span>

<span data-ttu-id="b709b-251">Nainstalujte `ufw` a nakonfigurujte ho chcete povolit přenosy přes všechny porty potřebné.</span><span class="sxs-lookup"><span data-stu-id="b709b-251">Install `ufw` and configure it to allow traffic on any ports needed.</span></span>

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a><span data-ttu-id="b709b-252">Secure Nginx</span><span class="sxs-lookup"><span data-stu-id="b709b-252">Secure Nginx</span></span>

#### <a name="change-the-nginx-response-name"></a><span data-ttu-id="b709b-253">Změnit název odpovědi serveru Nginx</span><span class="sxs-lookup"><span data-stu-id="b709b-253">Change the Nginx response name</span></span>

<span data-ttu-id="b709b-254">Edit *src/http/ngx_http_header_filter_module.c*:</span><span class="sxs-lookup"><span data-stu-id="b709b-254">Edit *src/http/ngx_http_header_filter_module.c*:</span></span>

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a><span data-ttu-id="b709b-255">Konfigurace možností</span><span class="sxs-lookup"><span data-stu-id="b709b-255">Configure options</span></span>

<span data-ttu-id="b709b-256">Konfigurace serveru s další požadované moduly.</span><span class="sxs-lookup"><span data-stu-id="b709b-256">Configure the server with additional required modules.</span></span> <span data-ttu-id="b709b-257">Zvažte použití brány firewall webových aplikací, jako například [ModSecurity](https://www.modsecurity.org/), Posilte zabezpečení aplikace.</span><span class="sxs-lookup"><span data-stu-id="b709b-257">Consider using a web app firewall, such as [ModSecurity](https://www.modsecurity.org/), to harden the app.</span></span>

#### <a name="https-configuration"></a><span data-ttu-id="b709b-258">Konfigurace protokolu HTTPS</span><span class="sxs-lookup"><span data-stu-id="b709b-258">HTTPS configuration</span></span>

* <span data-ttu-id="b709b-259">Konfigurace serveru tak, aby naslouchala na přenosy HTTPS na portu `443` zadáním platný certifikát vydaný důvěryhodného certifikátu autority (CA).</span><span class="sxs-lookup"><span data-stu-id="b709b-259">Configure the server to listen to HTTPS traffic on port `443` by specifying a valid certificate issued by a trusted Certificate Authority (CA).</span></span>

* <span data-ttu-id="b709b-260">Posílení zabezpečení, když některé postupy uvedené v následující */etc/nginx/nginx.conf* souboru.</span><span class="sxs-lookup"><span data-stu-id="b709b-260">Harden the security by employing some of the practices depicted in the following */etc/nginx/nginx.conf* file.</span></span> <span data-ttu-id="b709b-261">Mezi příklady patří výběrem silnější šifrování a přesměrovat veškerý provoz prostřednictvím protokolu HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b709b-261">Examples include choosing a stronger cipher and redirecting all traffic over HTTP to HTTPS.</span></span>

* <span data-ttu-id="b709b-262">Přidání `HTTP Strict-Transport-Security` záhlaví (HSTS) zajišťuje, že jsou všechny následné požadavky od klienta přes protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b709b-262">Adding an `HTTP Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS.</span></span>

* <span data-ttu-id="b709b-263">Nepřidávejte HSTS záhlaví nebo zvolit odpovídající `max-age` Pokud bude v budoucnu zakázán protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b709b-263">Don't add the HSTS header or chose an appropriate `max-age` if HTTPS will be disabled in the future.</span></span>

<span data-ttu-id="b709b-264">Přidat */etc/nginx/proxy.conf* konfiguračního souboru:</span><span class="sxs-lookup"><span data-stu-id="b709b-264">Add the */etc/nginx/proxy.conf* configuration file:</span></span>

[!code-nginx[](linux-nginx/proxy.conf)]

<span data-ttu-id="b709b-265">Upravit */etc/nginx/nginx.conf* konfigurační soubor.</span><span class="sxs-lookup"><span data-stu-id="b709b-265">Edit the */etc/nginx/nginx.conf* configuration file.</span></span> <span data-ttu-id="b709b-266">Tento příklad obsahuje oba `http` a `server` oddílů v souboru jednu konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="b709b-266">The example contains both `http` and `server` sections in one configuration file.</span></span>

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

#### <a name="secure-nginx-from-clickjacking"></a><span data-ttu-id="b709b-267">Zabezpečení serveru Nginx z útoků typu clickjacking</span><span class="sxs-lookup"><span data-stu-id="b709b-267">Secure Nginx from clickjacking</span></span>

<span data-ttu-id="b709b-268">[Útoků typu Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), označované také jako *uživatelského rozhraní zjednávání nápravy útoku*, je napadením se zlými úmysly, kde návštěvníků webu je nalákaní, odkaz nebo tlačítko na stránce jiné než aktuálně navštívený.</span><span class="sxs-lookup"><span data-stu-id="b709b-268">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="b709b-269">Použití `X-FRAME-OPTIONS` k zabezpečení webu.</span><span class="sxs-lookup"><span data-stu-id="b709b-269">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="b709b-270">Ke zmírnění útoků typu clickjacking útoků:</span><span class="sxs-lookup"><span data-stu-id="b709b-270">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="b709b-271">Upravit *nginx.conf* souboru:</span><span class="sxs-lookup"><span data-stu-id="b709b-271">Edit the *nginx.conf* file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="b709b-272">Přidejte řádek `add_header X-Frame-Options "SAMEORIGIN";`.</span><span class="sxs-lookup"><span data-stu-id="b709b-272">Add the line `add_header X-Frame-Options "SAMEORIGIN";`.</span></span>
1. <span data-ttu-id="b709b-273">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="b709b-273">Save the file.</span></span>
1. <span data-ttu-id="b709b-274">Restartujte server Nginx.</span><span class="sxs-lookup"><span data-stu-id="b709b-274">Restart Nginx.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="b709b-275">Typ MIME pro analýzu sítě</span><span class="sxs-lookup"><span data-stu-id="b709b-275">MIME-type sniffing</span></span>

<span data-ttu-id="b709b-276">Tato hlavička brání většina prohlížečů z MIME pro analýzu sítě odpověď od deklarovaný typ obsahu, jako hlavičku dostane pokyn, abyste nepřepsali typ obsahu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="b709b-276">This header prevents most browsers from MIME-sniffing a response away from the declared content type, as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="b709b-277">S `nosniff` možnost, pokud server říká, že obsah je "text/html", v prohlížeči se vykreslí jako "text/html".</span><span class="sxs-lookup"><span data-stu-id="b709b-277">With the `nosniff` option, if the server says the content is "text/html", the browser renders it as "text/html".</span></span>

<span data-ttu-id="b709b-278">Upravit *nginx.conf* souboru:</span><span class="sxs-lookup"><span data-stu-id="b709b-278">Edit the *nginx.conf* file:</span></span>

```bash
sudo nano /etc/nginx/nginx.conf
```

<span data-ttu-id="b709b-279">Přidejte řádek `add_header X-Content-Type-Options "nosniff";` a uložte soubor a pak restartujte server Nginx.</span><span class="sxs-lookup"><span data-stu-id="b709b-279">Add the line `add_header X-Content-Type-Options "nosniff";` and save the file, then restart Nginx.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b709b-280">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b709b-280">Additional resources</span></span>

* [<span data-ttu-id="b709b-281">Požadavky pro .NET Core v Linuxu</span><span class="sxs-lookup"><span data-stu-id="b709b-281">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* [<span data-ttu-id="b709b-282">Server Nginx: Binární verze: Oficiální balíčky Debian nebo Ubuntu</span><span class="sxs-lookup"><span data-stu-id="b709b-282">Nginx: Binary Releases: Official Debian/Ubuntu packages</span></span>](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="b709b-283">NGINX: Pomocí hlavičky předané</span><span class="sxs-lookup"><span data-stu-id="b709b-283">NGINX: Using the Forwarded header</span></span>](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
