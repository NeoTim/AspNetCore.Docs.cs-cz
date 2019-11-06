---
title: Hostování ASP.NET Core v systému Linux pomocí Apache
author: guardrex
description: Naučte se, jak nastavit Apache jako reverzní proxy server v CentOS pro přesměrování provozu HTTP do ASP.NET Core webové aplikace běžící na Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 11/05/2019
uid: host-and-deploy/linux-apache
ms.openlocfilehash: fce91db736908e433ba6803319aa8984bb68a554
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73659889"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a><span data-ttu-id="eb9cd-103">Hostování ASP.NET Core v systému Linux pomocí Apache</span><span class="sxs-lookup"><span data-stu-id="eb9cd-103">Host ASP.NET Core on Linux with Apache</span></span>

<span data-ttu-id="eb9cd-104">Od [Shayne Boyer](https://github.com/spboyer)</span><span class="sxs-lookup"><span data-stu-id="eb9cd-104">By [Shayne Boyer](https://github.com/spboyer)</span></span>

<span data-ttu-id="eb9cd-105">Pomocí této příručky se naučíte, jak nastavit [Apache](https://httpd.apache.org/) jako reverzní proxy server na [CentOS 7](https://www.centos.org/) pro přesměrování provozu http do ASP.NET Core webové aplikace běžící na serveru [Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="eb9cd-105">Using this guide, learn how to set up [Apache](https://httpd.apache.org/) as a reverse proxy server on [CentOS 7](https://www.centos.org/) to redirect HTTP traffic to an ASP.NET Core web app running on [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="eb9cd-106">[Rozšíření mod_proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) a související moduly vytvoří reverzní proxy server serveru.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-106">The [mod_proxy extension](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) and related modules create the server's reverse proxy.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="eb9cd-107">Požadavky</span><span class="sxs-lookup"><span data-stu-id="eb9cd-107">Prerequisites</span></span>

* <span data-ttu-id="eb9cd-108">Server se systémem CentOS 7 se standardním uživatelským účtem s oprávněním sudo.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-108">Server running CentOS 7 with a standard user account with sudo privilege.</span></span>
* <span data-ttu-id="eb9cd-109">Nainstalujte modul runtime .NET Core na server.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-109">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="eb9cd-110">Navštivte [stránku všechny soubory ke stažení pro .NET Core](https://www.microsoft.com/net/download/all).</span><span class="sxs-lookup"><span data-stu-id="eb9cd-110">Visit the [.NET Core All Downloads page](https://www.microsoft.com/net/download/all).</span></span>
   1. <span data-ttu-id="eb9cd-111">V seznamu pod položkou **runtime**vyberte nejnovější modul runtime, který není ve verzi Preview.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-111">Select the latest non-preview runtime from the list under **Runtime**.</span></span>
   1. <span data-ttu-id="eb9cd-112">Vyberte a postupujte podle pokynů pro CentOS/Oracle.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-112">Select and follow the instructions for CentOS/Oracle.</span></span>
* <span data-ttu-id="eb9cd-113">Existující aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-113">An existing ASP.NET Core app.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="eb9cd-114">Publikování a kopírování přes aplikaci</span><span class="sxs-lookup"><span data-stu-id="eb9cd-114">Publish and copy over the app</span></span>

<span data-ttu-id="eb9cd-115">Nakonfigurujte aplikaci pro [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="eb9cd-115">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="eb9cd-116">Pokud je aplikace spuštěná místně a není nakonfigurovaná tak, aby přijímala zabezpečené připojení (HTTPS), proveďte jednu z následujících metod:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-116">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="eb9cd-117">Nakonfigurujte aplikaci tak, aby zpracovávala Zabezpečená místní připojení.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-117">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="eb9cd-118">Další informace najdete v části [konfigurace https](#https-configuration) .</span><span class="sxs-lookup"><span data-stu-id="eb9cd-118">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="eb9cd-119">Z vlastnosti `applicationUrl` v souboru *Properties/launchSettings. JSON* odeberte `https://localhost:5001` (Pokud je k dispozici).</span><span class="sxs-lookup"><span data-stu-id="eb9cd-119">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="eb9cd-120">Spuštěním [dotnet Publish](/dotnet/core/tools/dotnet-publish) z vývojového prostředí zabalíte aplikaci do adresáře (například *bin/Release/&lt;target_framework_moniker&gt;/Publish*), která se dají spustit na serveru:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-120">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="eb9cd-121">Pokud nechcete zachovat modul runtime .NET Core na serveru, můžete aplikaci publikovat také jako samostatné [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) .</span><span class="sxs-lookup"><span data-stu-id="eb9cd-121">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="eb9cd-122">Zkopírujte aplikaci ASP.NET Core na server pomocí nástroje, který se integruje do pracovního postupu organizace (například SCP, SFTP).</span><span class="sxs-lookup"><span data-stu-id="eb9cd-122">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="eb9cd-123">Je běžné najít webové aplikace v adresáři *var* (například *var/www/helloapp*).</span><span class="sxs-lookup"><span data-stu-id="eb9cd-123">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="eb9cd-124">V rámci scénáře nasazení v produkčním prostředí provádí pracovní postup průběžné integrace publikování aplikace a zkopírování prostředků na server.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-124">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

## <a name="configure-a-proxy-server"></a><span data-ttu-id="eb9cd-125">Konfigurace proxy server</span><span class="sxs-lookup"><span data-stu-id="eb9cd-125">Configure a proxy server</span></span>

<span data-ttu-id="eb9cd-126">Reverzní proxy je běžné nastavení pro obsluhu dynamických webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-126">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="eb9cd-127">Reverzní proxy ukončí požadavek HTTP a předá ho do aplikace ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-127">The reverse proxy terminates the HTTP request and forwards it to the ASP.NET app.</span></span>

<span data-ttu-id="eb9cd-128">Proxy server je ten, který přepošle požadavky klienta na jiný server místo toho, aby splňovaly samotné požadavky.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-128">A proxy server is one which forwards client requests to another server instead of fulfilling requests itself.</span></span> <span data-ttu-id="eb9cd-129">Reverzní proxy server předává pevnému cíli, obvykle jménem libovolných klientů.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-129">A reverse proxy forwards to a fixed destination, typically on behalf of arbitrary clients.</span></span> <span data-ttu-id="eb9cd-130">V této příručce je Apache nakonfigurovaná jako reverzní proxy server běžící na stejném serveru, který Kestrel obsluhuje aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-130">In this guide, Apache is configured as the reverse proxy running on the same server that Kestrel is serving the ASP.NET Core app.</span></span>

<span data-ttu-id="eb9cd-131">Vzhledem k tomu, že požadavky jsou předávány reverzním proxy, použijte [middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) z balíčku [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) .</span><span class="sxs-lookup"><span data-stu-id="eb9cd-131">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="eb9cd-132">Middleware aktualizuje `Request.Scheme`pomocí hlavičky `X-Forwarded-Proto`, aby identifikátory URI pro přesměrování a další zásady zabezpečení fungovaly správně.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-132">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="eb9cd-133">Po vyvolání middlewaru předávaných hlaviček musí být všechny komponenty, které jsou závislé na schématu, jako je ověřování, generace odkazů, přesměrování a zeměpisná poloha, umístěny.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-133">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span> <span data-ttu-id="eb9cd-134">Jako obecné pravidlo by měl middleware předaných hlaviček běžet před jiným middlewarem, kromě diagnostiky a middlewaru pro zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-134">As a general rule, Forwarded Headers Middleware should run before other middleware except diagnostics and error handling middleware.</span></span> <span data-ttu-id="eb9cd-135">Toto řazení zajišťuje, aby middleware spoléhající se na předané informace hlaviček mohl spotřebovat hodnoty hlaviček pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-135">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span>

<span data-ttu-id="eb9cd-136">Vyvolejte metodu <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> v `Startup.Configure` před voláním <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> nebo podobného middlewaru schématu ověřování.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-136">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or similar authentication scheme middleware.</span></span> <span data-ttu-id="eb9cd-137">Nakonfigurujte middleware pro přeposílání `X-Forwarded-For` a `X-Forwarded-Proto` hlavičky:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-137">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="eb9cd-138">Pokud pro middlewari nejsou zadány žádné <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>, jsou výchozí hlavičky pro přeposílání `None`.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-138">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="eb9cd-139">Proxy servery běžící na adresách zpětné smyčky (127.0.0.0/8, [:: 1]), včetně standardní adresy localhost (127.0.0.1), jsou ve výchozím nastavení důvěryhodné.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-139">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="eb9cd-140">Pokud jiné důvěryhodné proxy servery nebo sítě v rámci organizace zařídí žádosti mezi Internetem a webovým serverem, přidejte je do seznamu <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> nebo <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> pomocí <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-140">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="eb9cd-141">Následující příklad přidá důvěryhodnou proxy server na IP adresu 10.0.0.100 k `KnownProxies` middlewaru s přesměrovanými hlavičkami v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-141">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="eb9cd-142">Další informace najdete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-142">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-apache"></a><span data-ttu-id="eb9cd-143">Nainstalovat Apache</span><span class="sxs-lookup"><span data-stu-id="eb9cd-143">Install Apache</span></span>

<span data-ttu-id="eb9cd-144">Aktualizujte balíčky CentOS na nejnovější stabilní verze:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-144">Update CentOS packages to their latest stable versions:</span></span>

```bash
sudo yum update -y
```

<span data-ttu-id="eb9cd-145">Nainstalujte webový server Apache na CentOS jediným příkazem `yum`:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-145">Install the Apache web server on CentOS with a single `yum` command:</span></span>

```bash
sudo yum -y install httpd mod_ssl
```

<span data-ttu-id="eb9cd-146">Ukázkový výstup po spuštění příkazu:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-146">Sample output after running the command:</span></span>

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> <span data-ttu-id="eb9cd-147">V tomto příkladu výstup odráží httpd. 86_64, protože verze CentOS 7 je 64 bitů.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-147">In this example, the output reflects httpd.86_64 since the CentOS 7 version is 64 bit.</span></span> <span data-ttu-id="eb9cd-148">Chcete-li ověřit, kde je nainstalována Apache, spusťte `whereis httpd` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-148">To verify where Apache is installed, run `whereis httpd` from a command prompt.</span></span>

### <a name="configure-apache"></a><span data-ttu-id="eb9cd-149">Konfigurace Apache</span><span class="sxs-lookup"><span data-stu-id="eb9cd-149">Configure Apache</span></span>

<span data-ttu-id="eb9cd-150">Konfigurační soubory pro Apache se nacházejí v adresáři `/etc/httpd/conf.d/`.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-150">Configuration files for Apache are located within the `/etc/httpd/conf.d/` directory.</span></span> <span data-ttu-id="eb9cd-151">Libovolný soubor s příponou *. conf* je zpracováván v abecedním pořadí vedle konfiguračních souborů modulu v `/etc/httpd/conf.modules.d/`, který obsahuje všechny konfigurační soubory potřebné pro načtení modulů.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-151">Any file with the *.conf* extension is processed in alphabetical order in addition to the module configuration files in `/etc/httpd/conf.modules.d/`, which contains any configuration files necessary to load modules.</span></span>

<span data-ttu-id="eb9cd-152">Pro aplikaci vytvořte konfigurační soubor s názvem *helloapp. conf*:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-152">Create a configuration file, named *helloapp.conf*, for the app:</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}helloapp-error.log
    CustomLog ${APACHE_LOG_DIR}helloapp-access.log common
</VirtualHost>
```

<span data-ttu-id="eb9cd-153">Blok `VirtualHost` se může objevit několikrát, v jednom nebo více souborech na serveru.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-153">The `VirtualHost` block can appear multiple times, in one or more files on a server.</span></span> <span data-ttu-id="eb9cd-154">V předchozím konfiguračním souboru akceptuje Apache veřejný provoz na portu 80.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-154">In the preceding configuration file, Apache accepts public traffic on port 80.</span></span> <span data-ttu-id="eb9cd-155">Doména `www.example.com` se zpracovává a alias `*.example.com` se překládá na stejný web.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-155">The domain `www.example.com` is being served, and the `*.example.com` alias resolves to the same website.</span></span> <span data-ttu-id="eb9cd-156">Další informace najdete v tématu [Podpora virtuálních hostitelů založených na názvech](https://httpd.apache.org/docs/current/vhosts/name-based.html) .</span><span class="sxs-lookup"><span data-stu-id="eb9cd-156">See [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html) for more information.</span></span> <span data-ttu-id="eb9cd-157">Žádosti jsou proxy servery v kořenovém adresáři na port 5000 serveru na adrese 127.0.0.1.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-157">Requests are proxied at the root to port 5000 of the server at 127.0.0.1.</span></span> <span data-ttu-id="eb9cd-158">Pro obousměrnou komunikaci se vyžadují `ProxyPass` a `ProxyPassReverse`.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-158">For bi-directional communication, `ProxyPass` and `ProxyPassReverse` are required.</span></span> <span data-ttu-id="eb9cd-159">Pokud chcete změnit IP adresu/port Kestrel, přečtěte si téma [Kestrel: konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="eb9cd-159">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="eb9cd-160">Nepovedlo se zadat správnou [direktivu servername](https://httpd.apache.org/docs/current/mod/core.html#servername) v bloku **VirtualHost** k vystavení ohrožení zabezpečení vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-160">Failure to specify a proper [ServerName directive](https://httpd.apache.org/docs/current/mod/core.html#servername) in the **VirtualHost** block exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="eb9cd-161">Vazba zástupných znaků subdomény (například `*.example.com`) nepředstavuje toto bezpečnostní riziko, pokud ovládáte celou nadřazenou doménu (na rozdíl od `*.com`, která je zranitelná).</span><span class="sxs-lookup"><span data-stu-id="eb9cd-161">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="eb9cd-162">Další informace najdete v [části rfc7230 část-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .</span><span class="sxs-lookup"><span data-stu-id="eb9cd-162">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="eb9cd-163">Protokolování lze nakonfigurovat na `VirtualHost` pomocí direktiv `ErrorLog` a `CustomLog`.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-163">Logging can be configured per `VirtualHost` using `ErrorLog` and `CustomLog` directives.</span></span> <span data-ttu-id="eb9cd-164">`ErrorLog` je umístění, kde Server protokoluje chyby, a `CustomLog` nastaví název souboru a formát souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-164">`ErrorLog` is the location where the server logs errors, and `CustomLog` sets the filename and format of log file.</span></span> <span data-ttu-id="eb9cd-165">V tomto případě se jedná o případ, kdy se zaprotokolují informace o žádosti.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-165">In this case, this is where request information is logged.</span></span> <span data-ttu-id="eb9cd-166">Pro každý požadavek je k dispozici jeden řádek.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-166">There's one line for each request.</span></span>

<span data-ttu-id="eb9cd-167">Uložte soubor a otestujte konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-167">Save the file and test the configuration.</span></span> <span data-ttu-id="eb9cd-168">Pokud vše projde, odpověď by měla být `Syntax [OK]`.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-168">If everything passes, the response should be `Syntax [OK]`.</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="eb9cd-169">Restartujte Apache:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-169">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a><span data-ttu-id="eb9cd-170">Monitorování aplikace</span><span class="sxs-lookup"><span data-stu-id="eb9cd-170">Monitor the app</span></span>

<span data-ttu-id="eb9cd-171">Apache je teď nastavený tak, aby předal požadavky na `http://localhost:80` do ASP.NET Core aplikace běžící na Kestrel na `http://127.0.0.1:5000`.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-171">Apache is now setup to forward requests made to `http://localhost:80` to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="eb9cd-172">Apache ale není nastavené na správu procesu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-172">However, Apache isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="eb9cd-173">K zahájení a monitorování základní webové aplikace můžete použít *systém* a vytvořit soubor služby.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-173">Use *systemd* and create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="eb9cd-174">*systém* je systémem init, který poskytuje mnoho výkonných funkcí pro spouštění, zastavování a správu procesů.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-174">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span>

### <a name="create-the-service-file"></a><span data-ttu-id="eb9cd-175">Vytvoření souboru služby</span><span class="sxs-lookup"><span data-stu-id="eb9cd-175">Create the service file</span></span>

<span data-ttu-id="eb9cd-176">Vytvořte definiční soubor služby:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-176">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="eb9cd-177">Příklad souboru služby pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-177">An example service file for the app:</span></span>

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="eb9cd-178">Pokud uživatel *Apache* v konfiguraci nepoužívá, musí se nejdřív vytvořit uživatel a mít odpovídající vlastnictví souborů.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-178">If the user *apache* isn't used by the configuration, the user must be created first and given proper ownership of files.</span></span>

<span data-ttu-id="eb9cd-179">Pomocí `TimeoutStopSec` můžete nastavit dobu, po kterou se má čekat na vypnutí aplikace po přijetí počátečního signálu přerušení.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-179">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="eb9cd-180">Pokud se aplikace v tomto období neukončí, SIGKILL se vydá pro ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-180">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="eb9cd-181">Zadejte hodnotu jako nejednotkové sekundy (například `150`), hodnotu časového rozsahu (například `2min 30s`) nebo `infinity`, aby byl časový limit zakázán.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-181">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="eb9cd-182">`TimeoutStopSec` výchozí hodnota `DefaultTimeoutStopSec` v konfiguračním souboru správce (*systemed-System. conf*, *System. conf. d*, *systemd-User. conf*, *User. conf. d*).</span><span class="sxs-lookup"><span data-stu-id="eb9cd-182">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="eb9cd-183">Výchozí časový limit pro většinu distribucí je 90 sekund.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-183">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="eb9cd-184">Některé hodnoty (například připojovací řetězce SQL) musí být uvozené řídicími znaky, aby poskytovatelé konfigurace mohli číst proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-184">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="eb9cd-185">Pomocí následujícího příkazu vygenerujte správně uvozenou hodnotu pro použití v konfiguračním souboru:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-185">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

<span data-ttu-id="eb9cd-186">Oddělovače dvojtečky (`:`) nejsou v názvech proměnných prostředí podporovány.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-186">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="eb9cd-187">Místo dvojtečky použijte dvojité podtržítko (`__`).</span><span class="sxs-lookup"><span data-stu-id="eb9cd-187">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="eb9cd-188">[Poskytovatel konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) převádí dvojitá podtržítka na dvojtečky, když jsou proměnné prostředí čteny do konfigurace.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-188">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="eb9cd-189">V následujícím příkladu je klíč připojovacího řetězce `ConnectionStrings:DefaultConnection` v definičním souboru služby nastaven jako `ConnectionStrings__DefaultConnection`:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-189">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="eb9cd-190">Uložte soubor a povolte službu:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-190">Save the file and enable the service:</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="eb9cd-191">Spusťte službu a ověřte, že je spuštěná:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-191">Start the service and verify that it's running:</span></span>

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="eb9cd-192">Když je server reverzní proxy nakonfigurovaný a Kestrel spravovaný prostřednictvím *systému*, je webová aplikace plně nakonfigurovaná a dá se k nim dostat z prohlížeče v místním počítači na `http://localhost`.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-192">With the reverse proxy configured and Kestrel managed through *systemd*, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="eb9cd-193">Při kontrole hlaviček odpovědi se v hlavičce **serveru** označuje, že ASP.NET Core aplikace obsluhuje Kestrel:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-193">Inspecting the response headers, the **Server** header indicates that the ASP.NET Core app is served by Kestrel:</span></span>

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="eb9cd-194">Zobrazit protokoly</span><span class="sxs-lookup"><span data-stu-id="eb9cd-194">View logs</span></span>

<span data-ttu-id="eb9cd-195">Vzhledem k tomu, že webová aplikace používající Kestrel je spravovaná pomocí *systému*, události a procesy se zaznamenávají do centralizovaného deníku.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-195">Since the web app using Kestrel is managed using *systemd*, events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="eb9cd-196">Tento deník ale obsahuje položky pro všechny služby a procesy spravované *systémem*.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-196">However, this journal includes entries for all of the services and processes managed by *systemd*.</span></span> <span data-ttu-id="eb9cd-197">Chcete-li zobrazit položky specifické pro `kestrel-helloapp.service`, použijte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-197">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="eb9cd-198">Pro filtrování času zadejte možnosti času pomocí příkazu.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-198">For time filtering, specify time options with the command.</span></span> <span data-ttu-id="eb9cd-199">Můžete například použít `--since today` k filtrování aktuálního dne nebo `--until 1 hour ago`, aby se zobrazily položky předchozí hodiny.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-199">For example, use `--since today` to filter for the current day or `--until 1 hour ago` to see the previous hour's entries.</span></span> <span data-ttu-id="eb9cd-200">Další informace najdete na [stránce muž pro journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span><span class="sxs-lookup"><span data-stu-id="eb9cd-200">For more information, see the [man page for journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="eb9cd-201">Ochrana dat</span><span class="sxs-lookup"><span data-stu-id="eb9cd-201">Data protection</span></span>

<span data-ttu-id="eb9cd-202">[Sada ASP.NET Core Data Protection Stack](xref:security/data-protection/introduction) je používána několika ASP.NET Core [middlewary](xref:fundamentals/middleware/index), včetně middlewaru ověřování (například middleware souborů cookie) a ochrany proti padělání žádostí mezi weby (CSRF).</span><span class="sxs-lookup"><span data-stu-id="eb9cd-202">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="eb9cd-203">I v případě, že rozhraní API ochrany dat nejsou volána uživatelským kódem, je třeba chránit data, aby bylo možné vytvořit trvalé úložiště kryptografických [klíčů](xref:security/data-protection/implementation/key-management).</span><span class="sxs-lookup"><span data-stu-id="eb9cd-203">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="eb9cd-204">Pokud ochrana dat není nakonfigurovaná, klíče se uchovávají v paměti a při restartování aplikace se zahodí.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-204">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="eb9cd-205">Pokud se klíčového prstence při restartu aplikace uloží do paměti:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-205">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="eb9cd-206">Všechny ověřovací tokeny založené na souborech cookie jsou neověřené.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-206">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="eb9cd-207">Uživatelé se musí znovu přihlásit na svůj další požadavek.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-207">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="eb9cd-208">Data chráněná pomocí Key ringu už nebude možné dešifrovat.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-208">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="eb9cd-209">To může zahrnovat [CSRF tokeny](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [ASP.NET Core soubory cookie TempData MVC](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="eb9cd-209">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="eb9cd-210">Pokud chcete nakonfigurovat ochranu dat, aby zachovala a zašifroval klíč Ring, přečtěte si:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-210">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a><span data-ttu-id="eb9cd-211">Zabezpečení aplikace</span><span class="sxs-lookup"><span data-stu-id="eb9cd-211">Secure the app</span></span>

### <a name="configure-firewall"></a><span data-ttu-id="eb9cd-212">Konfigurace brány firewall</span><span class="sxs-lookup"><span data-stu-id="eb9cd-212">Configure firewall</span></span>

<span data-ttu-id="eb9cd-213">*Brána firewall* je dynamickým démonem pro správu brány firewall s podporou síťových zón.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-213">*Firewalld* is a dynamic daemon to manage the firewall with support for network zones.</span></span> <span data-ttu-id="eb9cd-214">Filtrování portů a paketů je stále možné spravovat pomocí softwaru iptables.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-214">Ports and packet filtering can still be managed by iptables.</span></span> <span data-ttu-id="eb9cd-215">*Brána firewall* by měla být nainstalována ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-215">*Firewalld* should be installed by default.</span></span> <span data-ttu-id="eb9cd-216">`yum` lze použít k instalaci balíčku nebo ověření, že je nainstalován.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-216">`yum` can be used to install the package or verify it's installed.</span></span>

```bash
sudo yum install firewalld -y
```

<span data-ttu-id="eb9cd-217">K otevření jenom portů potřebných pro aplikaci použijte `firewalld`.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-217">Use `firewalld` to open only the ports needed for the app.</span></span> <span data-ttu-id="eb9cd-218">V tomto případě se používají porty 80 a 443.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-218">In this case, port 80 and 443 are used.</span></span> <span data-ttu-id="eb9cd-219">Následující příkazy trvale nastaví porty 80 a 443 na otevřené:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-219">The following commands permanently set ports 80 and 443 to open:</span></span>

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

<span data-ttu-id="eb9cd-220">Znovu načtěte nastavení brány firewall.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-220">Reload the firewall settings.</span></span> <span data-ttu-id="eb9cd-221">Ověřte dostupné služby a porty ve výchozí zóně.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-221">Check the available services and ports in the default zone.</span></span> <span data-ttu-id="eb9cd-222">K dispozici jsou možnosti kontroly `firewall-cmd -h`.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-222">Options are available by inspecting `firewall-cmd -h`.</span></span>

```bash
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="https-configuration"></a><span data-ttu-id="eb9cd-223">Konfigurace HTTPS</span><span class="sxs-lookup"><span data-stu-id="eb9cd-223">HTTPS configuration</span></span>

<span data-ttu-id="eb9cd-224">**Konfigurace místních připojení (HTTPS) aplikace pro zabezpečení**</span><span class="sxs-lookup"><span data-stu-id="eb9cd-224">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="eb9cd-225">Příkaz příkazového řádku [dotnet](/dotnet/core/tools/dotnet-run) používá soubor *Properties/launchSettings. JSON* aplikace, který nakonfiguruje aplikaci tak, aby naslouchala adresám url poskytnutým vlastností `applicationUrl` (například `https://localhost:5001; http://localhost:5000`).</span><span class="sxs-lookup"><span data-stu-id="eb9cd-225">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="eb9cd-226">Nakonfigurujte aplikaci tak, aby používala certifikát ve vývoji pro příkaz `dotnet run` nebo vývojové prostředí (F5 nebo CTRL + F5 v Visual Studio Code) pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-226">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="eb9cd-227">[Nahradit výchozí certifikát z konfigurace](xref:fundamentals/servers/kestrel#configuration) (*doporučeno*)</span><span class="sxs-lookup"><span data-stu-id="eb9cd-227">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="eb9cd-228">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="eb9cd-228">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="eb9cd-229">**Konfigurace připojení klienta reverzního proxy serveru pro zabezpečení (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="eb9cd-229">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

<span data-ttu-id="eb9cd-230">Pro konfiguraci Apache pro HTTPS se používá modul *mod_ssl* .</span><span class="sxs-lookup"><span data-stu-id="eb9cd-230">To configure Apache for HTTPS, the *mod_ssl* module is used.</span></span> <span data-ttu-id="eb9cd-231">Po instalaci modulu *httpd* byl také nainstalován modul *mod_ssl* .</span><span class="sxs-lookup"><span data-stu-id="eb9cd-231">When the *httpd* module was installed, the *mod_ssl* module was also installed.</span></span> <span data-ttu-id="eb9cd-232">Pokud není nainstalovaná, přidejte ji do konfigurace pomocí `yum`.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-232">If it wasn't installed, use `yum` to add it to the configuration.</span></span>

```bash
sudo yum install mod_ssl
```

<span data-ttu-id="eb9cd-233">Pokud chcete vynutilit protokol HTTPS, nainstalujte modul `mod_rewrite`, aby se povolilo přepsání adresy URL:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-233">To enforce HTTPS, install the `mod_rewrite` module to enable URL rewriting:</span></span>

```bash
sudo yum install mod_rewrite
```

<span data-ttu-id="eb9cd-234">Úpravou souboru *helloapp. conf* povolte přepis adresy URL a zabezpečenou komunikaci na portu 443:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-234">Modify the *helloapp.conf* file to enable URL rewriting and secure communication on port 443:</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> <span data-ttu-id="eb9cd-235">Tento příklad používá místně generovaný certifikát.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-235">This example is using a locally-generated certificate.</span></span> <span data-ttu-id="eb9cd-236">**SSLCertificateFile** by měl být primárním souborem certifikátu pro název domény.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-236">**SSLCertificateFile** should be the primary certificate file for the domain name.</span></span> <span data-ttu-id="eb9cd-237">**SSLCertificateKeyFile** by měl být soubor klíče vygenerovaný při vytvoření CSR.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-237">**SSLCertificateKeyFile** should be the key file generated when CSR is created.</span></span> <span data-ttu-id="eb9cd-238">**SSLCertificateChainFile** by měl být soubor zprostředkujícího certifikátu (pokud existuje), který poskytla certifikační autorita.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-238">**SSLCertificateChainFile** should be the intermediate certificate file (if any) that was supplied by the certificate authority.</span></span>

<span data-ttu-id="eb9cd-239">Uložte soubor a otestujte konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-239">Save the file and test the configuration:</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="eb9cd-240">Restartujte Apache:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-240">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a><span data-ttu-id="eb9cd-241">Další návrhy Apache</span><span class="sxs-lookup"><span data-stu-id="eb9cd-241">Additional Apache suggestions</span></span>

### <a name="additional-headers"></a><span data-ttu-id="eb9cd-242">Další záhlaví</span><span class="sxs-lookup"><span data-stu-id="eb9cd-242">Additional headers</span></span>

<span data-ttu-id="eb9cd-243">Aby bylo možné zabezpečit před škodlivými útoky, je třeba upravit nebo přidat několik hlaviček.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-243">In order to secure against malicious attacks, there are a few headers that should either be modified or added.</span></span> <span data-ttu-id="eb9cd-244">Ujistěte se, že je nainstalovaný modul `mod_headers`:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-244">Ensure that the `mod_headers` module is installed:</span></span>

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a><span data-ttu-id="eb9cd-245">Zabezpečení Apache před útoky clickjacking</span><span class="sxs-lookup"><span data-stu-id="eb9cd-245">Secure Apache from clickjacking attacks</span></span>

<span data-ttu-id="eb9cd-246">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), označovaný také jako *útok s opravou uživatelského rozhraní*, je škodlivý útok, při kterém návštěvník webu získá odkaz nebo tlačítko na jiné stránce, než se právě navštíví.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-246">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="eb9cd-247">K zabezpečení webu použijte `X-FRAME-OPTIONS`.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-247">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="eb9cd-248">Zmírnění útoků Clickjacking:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-248">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="eb9cd-249">Upravte soubor *httpd. conf* :</span><span class="sxs-lookup"><span data-stu-id="eb9cd-249">Edit the *httpd.conf* file:</span></span>

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   <span data-ttu-id="eb9cd-250">Přidejte `Header append X-FRAME-OPTIONS "SAMEORIGIN"`řádku.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-250">Add the line `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span></span>
1. <span data-ttu-id="eb9cd-251">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-251">Save the file.</span></span>
1. <span data-ttu-id="eb9cd-252">Restartujte Apache.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-252">Restart Apache.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="eb9cd-253">Sledování typu MIME</span><span class="sxs-lookup"><span data-stu-id="eb9cd-253">MIME-type sniffing</span></span>

<span data-ttu-id="eb9cd-254">Hlavička `X-Content-Type-Options` zabraňuje aplikaci Internet Explorer ze *sledování MIME* (určení `Content-Type` souboru z obsahu souboru).</span><span class="sxs-lookup"><span data-stu-id="eb9cd-254">The `X-Content-Type-Options` header prevents Internet Explorer from *MIME-sniffing* (determining a file's `Content-Type` from the file's content).</span></span> <span data-ttu-id="eb9cd-255">Pokud server nastaví hlavičku `Content-Type` na `text/html` se sadou možností `nosniff`, vykreslí Internet Explorer obsah jako `text/html` bez ohledu na obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-255">If the server sets the `Content-Type` header to `text/html` with the `nosniff` option set, Internet Explorer renders the content as `text/html` regardless of the file's content.</span></span>

<span data-ttu-id="eb9cd-256">Upravte soubor *httpd. conf* :</span><span class="sxs-lookup"><span data-stu-id="eb9cd-256">Edit the *httpd.conf* file:</span></span>

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

<span data-ttu-id="eb9cd-257">Přidejte `Header set X-Content-Type-Options "nosniff"`řádku.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-257">Add the line `Header set X-Content-Type-Options "nosniff"`.</span></span> <span data-ttu-id="eb9cd-258">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-258">Save the file.</span></span> <span data-ttu-id="eb9cd-259">Restartujte Apache.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-259">Restart Apache.</span></span>

### <a name="load-balancing"></a><span data-ttu-id="eb9cd-260">Vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="eb9cd-260">Load Balancing</span></span>

<span data-ttu-id="eb9cd-261">Tento příklad ukazuje, jak nastavit a nakonfigurovat Apache na CentOS 7 a Kestrel na stejném počítači instance.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-261">This example shows how to setup and configure Apache on CentOS 7 and Kestrel on the same instance machine.</span></span> <span data-ttu-id="eb9cd-262">Aby nedošlo k jednomu bodu selhání; použití *mod_proxy_balancer* a úpravy **VirtualHost** by umožňovalo spravovat více instancí webových aplikací za proxy server Apache.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-262">In order to not have a single point of failure; using *mod_proxy_balancer* and modifying the **VirtualHost** would allow for managing multiple instances of the web apps behind the Apache proxy server.</span></span>

```bash
sudo yum install mod_proxy_balancer
```

<span data-ttu-id="eb9cd-263">V konfiguračním souboru uvedeném níže je další instance `helloapp` nastavená tak, aby běžela na portu 5001.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-263">In the configuration file shown below, an additional instance of the `helloapp` is set up to run on port 5001.</span></span> <span data-ttu-id="eb9cd-264">Oddíl *proxy* je nastaven s konfigurací vyrovnání se dvěma členy pro vyrovnávání zatížení *byrequests*.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-264">The *Proxy* section is set with a balancer configuration with two members to load balance *byrequests*.</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a><span data-ttu-id="eb9cd-265">Omezení přenosové rychlosti</span><span class="sxs-lookup"><span data-stu-id="eb9cd-265">Rate Limits</span></span>

<span data-ttu-id="eb9cd-266">Pomocí *mod_ratelimit*, který je součástí modulu *httpd* , lze omezit šířku pásma klientů:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-266">Using *mod_ratelimit*, which is included in the *httpd* module, the bandwidth of clients can be limited:</span></span>

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

<span data-ttu-id="eb9cd-267">Ukázkový soubor omezuje šířku pásma na 600 KB/s v kořenovém umístění:</span><span class="sxs-lookup"><span data-stu-id="eb9cd-267">The example file limits bandwidth as 600 KB/sec under the root location:</span></span>

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a><span data-ttu-id="eb9cd-268">Pole hlavičky dlouhé žádosti</span><span class="sxs-lookup"><span data-stu-id="eb9cd-268">Long request header fields</span></span>

<span data-ttu-id="eb9cd-269">Výchozí nastavení proxy serveru obvykle omezují pole hlaviček požadavku na 8 190 bajtů.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-269">Proxy server default settings typically limit request header fields to 8,190 bytes.</span></span> <span data-ttu-id="eb9cd-270">Aplikace může vyžadovat pole delší než výchozí (například aplikace, které používají [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span><span class="sxs-lookup"><span data-stu-id="eb9cd-270">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="eb9cd-271">Pokud jsou požadována delší pole, vyžaduje direktiva [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) proxy server úpravu.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-271">If longer fields are required, the proxy server's [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) directive requires adjustment.</span></span> <span data-ttu-id="eb9cd-272">Hodnota, která se má použít, závisí na scénáři.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-272">The value to apply depends on the scenario.</span></span> <span data-ttu-id="eb9cd-273">Další informace najdete v dokumentaci k vašemu serveru.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-273">For more information, see your server's documentation.</span></span>

> [!WARNING]
> <span data-ttu-id="eb9cd-274">Pokud není potřeba, nezvyšte výchozí hodnotu `LimitRequestFieldSize`.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-274">Don't increase the default value of `LimitRequestFieldSize` unless necessary.</span></span> <span data-ttu-id="eb9cd-275">Zvýšení hodnoty zvyšuje riziko přetečení vyrovnávací paměti (přetečení) a útok DoS (Denial of Service) uživateli se zlými úmysly.</span><span class="sxs-lookup"><span data-stu-id="eb9cd-275">Increasing the value increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eb9cd-276">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="eb9cd-276">Additional resources</span></span>

* [<span data-ttu-id="eb9cd-277">Předpoklady pro .NET Core v systému Linux</span><span class="sxs-lookup"><span data-stu-id="eb9cd-277">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
