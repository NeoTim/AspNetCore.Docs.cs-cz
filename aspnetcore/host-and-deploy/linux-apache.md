---
title: Host ASP.NET Core na Linuxu s Apache
author: rick-anderson
description: Naučte se nastavit Apache jako reverzní proxy server na CentOS přesměrovat HTTP provoz na ASP.NET webové aplikace Core běží na Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 02/05/2020
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 3a3edd961b08c1952e6ded8038ed7ada381c54b0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657897"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a><span data-ttu-id="698e9-103">Host ASP.NET Core na Linuxu s Apache</span><span class="sxs-lookup"><span data-stu-id="698e9-103">Host ASP.NET Core on Linux with Apache</span></span>

<span data-ttu-id="698e9-104">Podle [Shayne Boyer](https://github.com/spboyer)</span><span class="sxs-lookup"><span data-stu-id="698e9-104">By [Shayne Boyer](https://github.com/spboyer)</span></span>

<span data-ttu-id="698e9-105">Pomocí této příručky se [dozvíte,](https://httpd.apache.org/) jak nastavit Apache jako reverzní proxy server na [CentOS 7](https://www.centos.org/) pro přesměrování http provozu na ASP.NET webovou aplikaci Core spuštěnou na serveru [Kestrel.](xref:fundamentals/servers/kestrel)</span><span class="sxs-lookup"><span data-stu-id="698e9-105">Using this guide, learn how to set up [Apache](https://httpd.apache.org/) as a reverse proxy server on [CentOS 7](https://www.centos.org/) to redirect HTTP traffic to an ASP.NET Core web app running on [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="698e9-106">[Rozšíření mod_proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) a související moduly vytvářejí reverzní proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="698e9-106">The [mod_proxy extension](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) and related modules create the server's reverse proxy.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="698e9-107">Požadavky</span><span class="sxs-lookup"><span data-stu-id="698e9-107">Prerequisites</span></span>

* <span data-ttu-id="698e9-108">Server se systémem CentOS 7 se standardním uživatelským účtem s oprávněním sudo.</span><span class="sxs-lookup"><span data-stu-id="698e9-108">Server running CentOS 7 with a standard user account with sudo privilege.</span></span>
* <span data-ttu-id="698e9-109">Nainstalujte na server runtime jádra .NET.</span><span class="sxs-lookup"><span data-stu-id="698e9-109">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="698e9-110">Navštivte [stránku Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="698e9-110">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="698e9-111">Vyberte nejnovější verzi jádra .NET bez náhledu.</span><span class="sxs-lookup"><span data-stu-id="698e9-111">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="698e9-112">Stáhněte si nejnovější runtime bez náhledu v tabulce v části **Spustit aplikace - Runtime**.</span><span class="sxs-lookup"><span data-stu-id="698e9-112">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="698e9-113">Vyberte odkaz **pokyny správce balíčků** pro Linux a postupujte podle pokynů CentOS.</span><span class="sxs-lookup"><span data-stu-id="698e9-113">Select the Linux **Package manager instructions** link and follow the CentOS instructions.</span></span>
* <span data-ttu-id="698e9-114">Existující aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="698e9-114">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="698e9-115">Kdykoli v budoucnu po upgradu sdíleného rozhraní restartujte aplikace ASP.NET Core hostované serverem.</span><span class="sxs-lookup"><span data-stu-id="698e9-115">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="698e9-116">Publikování a kopírování přes aplikaci</span><span class="sxs-lookup"><span data-stu-id="698e9-116">Publish and copy over the app</span></span>

<span data-ttu-id="698e9-117">Nakonfigurujte aplikaci pro [nasazení závislé na rámci](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="698e9-117">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="698e9-118">Pokud je aplikace spuštěná místně a není nakonfigurovaná pro zabezpečená připojení (HTTPS), přijměte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="698e9-118">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="698e9-119">Nakonfigurujte aplikaci tak, aby zpracovávala zabezpečená místní připojení.</span><span class="sxs-lookup"><span data-stu-id="698e9-119">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="698e9-120">Další informace naleznete v části [konfigurace protokolu HTTPS.](#https-configuration)</span><span class="sxs-lookup"><span data-stu-id="698e9-120">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="698e9-121">Odebrat `https://localhost:5001` (pokud je `applicationUrl` k dispozici) z vlastnosti v *properties/launchSettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="698e9-121">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="698e9-122">Spusťte [publikování dotnet](/dotnet/core/tools/dotnet-publish) z vývojového prostředí a zabalte aplikaci do adresáře (například *&lt;bin/Release/ target_framework_moniker&gt;/publish),* který lze spustit na serveru:</span><span class="sxs-lookup"><span data-stu-id="698e9-122">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="698e9-123">Aplikace může být také publikována jako [samostatné nasazení,](/dotnet/core/deploying/#self-contained-deployments-scd) pokud nechcete udržovat runtime .NET Core na serveru.</span><span class="sxs-lookup"><span data-stu-id="698e9-123">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="698e9-124">Zkopírujte aplikaci ASP.NET Core na server pomocí nástroje, který se integruje do pracovního postupu organizace (například SCP, SFTP).</span><span class="sxs-lookup"><span data-stu-id="698e9-124">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="698e9-125">Je běžné vyhledat webové aplikace v adresáři *var* (například *var/www/helloapp*).</span><span class="sxs-lookup"><span data-stu-id="698e9-125">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="698e9-126">Podle scénáře nasazení v produkčním prostředí provádí pracovní postup průběžné integrace publikování aplikace a kopírování datových zdrojů na server.</span><span class="sxs-lookup"><span data-stu-id="698e9-126">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

## <a name="configure-a-proxy-server"></a><span data-ttu-id="698e9-127">Konfigurace serveru proxy</span><span class="sxs-lookup"><span data-stu-id="698e9-127">Configure a proxy server</span></span>

<span data-ttu-id="698e9-128">Reverzní proxy je běžné nastavení pro zobrazování dynamických webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="698e9-128">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="698e9-129">Reverzní proxy server ukončí požadavek HTTP a předá jej ASP.NET aplikaci.</span><span class="sxs-lookup"><span data-stu-id="698e9-129">The reverse proxy terminates the HTTP request and forwards it to the ASP.NET app.</span></span>

<span data-ttu-id="698e9-130">Proxy server je server, který předává požadavky klientů na jiný server namísto plnění požadavků samotných.</span><span class="sxs-lookup"><span data-stu-id="698e9-130">A proxy server is one which forwards client requests to another server instead of fulfilling requests itself.</span></span> <span data-ttu-id="698e9-131">Reverzní proxy předá do pevného cíle, obvykle jménem libovolných klientů.</span><span class="sxs-lookup"><span data-stu-id="698e9-131">A reverse proxy forwards to a fixed destination, typically on behalf of arbitrary clients.</span></span> <span data-ttu-id="698e9-132">V této příručce je Apache nakonfigurován jako reverzní proxy server běžící na stejném serveru, který Kestrel obsluhuje aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="698e9-132">In this guide, Apache is configured as the reverse proxy running on the same server that Kestrel is serving the ASP.NET Core app.</span></span>

<span data-ttu-id="698e9-133">Vzhledem k tomu, že požadavky jsou předávány reverzním proxy serverem, použijte [middleware s předanými hlavičkami](xref:host-and-deploy/proxy-load-balancer) z balíčku [Microsoft.AspNetCore.HttpOverrides.](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/)</span><span class="sxs-lookup"><span data-stu-id="698e9-133">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="698e9-134">Middleware aktualizuje `Request.Scheme`, pomocí `X-Forwarded-Proto` záhlaví, takže přesměrování IDENTIFIKÁTORŮ URI a dalších zásad zabezpečení pracovat správně.</span><span class="sxs-lookup"><span data-stu-id="698e9-134">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="698e9-135">Každá součást, která závisí na schématu, jako je ověřování, generování propojení, přesměrování a geografické umístění, musí být umístěna po vyvolání middlewaru předávaných záhlaví.</span><span class="sxs-lookup"><span data-stu-id="698e9-135">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span> <span data-ttu-id="698e9-136">Obecně platí, že middleware s předaným záhlavím by měl být spuštěn před jiným middlewarem s výjimkou diagnostiky a zpracování chyb middlewaru.</span><span class="sxs-lookup"><span data-stu-id="698e9-136">As a general rule, Forwarded Headers Middleware should run before other middleware except diagnostics and error handling middleware.</span></span> <span data-ttu-id="698e9-137">Toto pořadí zajišťuje, že middleware spoléhající na předávané informace záhlaví může spotřebovat hodnoty záhlaví pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="698e9-137">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span>

<span data-ttu-id="698e9-138">Vyvolat <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodu `Startup.Configure` v <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> před voláním nebo podobné schéma ověřování middleware.</span><span class="sxs-lookup"><span data-stu-id="698e9-138">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or similar authentication scheme middleware.</span></span> <span data-ttu-id="698e9-139">Nakonfigurujte middleware tak, aby předával záhlaví `X-Forwarded-For` a: `X-Forwarded-Proto`</span><span class="sxs-lookup"><span data-stu-id="698e9-139">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="698e9-140">Pokud <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> middlewaru nezadali, výchozí hlavičky `None`pro předávání jsou .</span><span class="sxs-lookup"><span data-stu-id="698e9-140">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="698e9-141">Proxy servery spuštěné na adresách zpětné smyčky (127.0.0.0/8, [::1]), včetně standardní adresy localhost (127.0.0.1), jsou ve výchozím nastavení důvěryhodné.</span><span class="sxs-lookup"><span data-stu-id="698e9-141">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="698e9-142">Pokud jiné důvěryhodné servery proxy nebo sítě v rámci organizace zpracovávají požadavky mezi <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> Internetem a webovým serverem, přidejte je do seznamu nebo <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>aplikací .</span><span class="sxs-lookup"><span data-stu-id="698e9-142">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="698e9-143">Následující příklad přidá důvěryhodný proxy server na adrese IP 10.0.0.100 `KnownProxies` do `Startup.ConfigureServices`middlewaru Předávaných záhlaví v :</span><span class="sxs-lookup"><span data-stu-id="698e9-143">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="698e9-144">Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="698e9-144">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-apache"></a><span data-ttu-id="698e9-145">Instalace Apache</span><span class="sxs-lookup"><span data-stu-id="698e9-145">Install Apache</span></span>

<span data-ttu-id="698e9-146">Aktualizujte balíčky CentOS na jejich nejnovější stabilní verze:</span><span class="sxs-lookup"><span data-stu-id="698e9-146">Update CentOS packages to their latest stable versions:</span></span>

```bash
sudo yum update -y
```

<span data-ttu-id="698e9-147">Nainstalujte webový server Apache na CentOS pomocí jediného `yum` příkazu:</span><span class="sxs-lookup"><span data-stu-id="698e9-147">Install the Apache web server on CentOS with a single `yum` command:</span></span>

```bash
sudo yum -y install httpd mod_ssl
```

<span data-ttu-id="698e9-148">Ukázkový výstup po spuštění příkazu:</span><span class="sxs-lookup"><span data-stu-id="698e9-148">Sample output after running the command:</span></span>

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
> <span data-ttu-id="698e9-149">V tomto příkladu výstup odráží httpd.86_64, protože centos 7 verze je 64 bit.</span><span class="sxs-lookup"><span data-stu-id="698e9-149">In this example, the output reflects httpd.86_64 since the CentOS 7 version is 64 bit.</span></span> <span data-ttu-id="698e9-150">Chcete-li ověřit, kde `whereis httpd` je Apache nainstalován, spusťte z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="698e9-150">To verify where Apache is installed, run `whereis httpd` from a command prompt.</span></span>

### <a name="configure-apache"></a><span data-ttu-id="698e9-151">Konfigurace Apache</span><span class="sxs-lookup"><span data-stu-id="698e9-151">Configure Apache</span></span>

<span data-ttu-id="698e9-152">Konfigurační soubory pro `/etc/httpd/conf.d/` Apache jsou umístěny v adresáři.</span><span class="sxs-lookup"><span data-stu-id="698e9-152">Configuration files for Apache are located within the `/etc/httpd/conf.d/` directory.</span></span> <span data-ttu-id="698e9-153">Každý soubor s příponou *.conf* je zpracován v abecedním pořadí kromě konfiguračních souborů modulu v `/etc/httpd/conf.modules.d/`aplikaci , který obsahuje všechny konfigurační soubory potřebné k načtení modulů.</span><span class="sxs-lookup"><span data-stu-id="698e9-153">Any file with the *.conf* extension is processed in alphabetical order in addition to the module configuration files in `/etc/httpd/conf.modules.d/`, which contains any configuration files necessary to load modules.</span></span>

<span data-ttu-id="698e9-154">Vytvořte pro aplikaci konfigurační soubor s názvem *helloapp.conf:*</span><span class="sxs-lookup"><span data-stu-id="698e9-154">Create a configuration file, named *helloapp.conf*, for the app:</span></span>

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

<span data-ttu-id="698e9-155">Blok `VirtualHost` se může zobrazit vícekrát v jednom nebo více souborech na serveru.</span><span class="sxs-lookup"><span data-stu-id="698e9-155">The `VirtualHost` block can appear multiple times, in one or more files on a server.</span></span> <span data-ttu-id="698e9-156">V předchozím konfiguračním souboru Apache přijímá veřejný provoz na portu 80.</span><span class="sxs-lookup"><span data-stu-id="698e9-156">In the preceding configuration file, Apache accepts public traffic on port 80.</span></span> <span data-ttu-id="698e9-157">Doména `www.example.com` je obsluhována `*.example.com` a alias se překládá na stejný web.</span><span class="sxs-lookup"><span data-stu-id="698e9-157">The domain `www.example.com` is being served, and the `*.example.com` alias resolves to the same website.</span></span> <span data-ttu-id="698e9-158">Další informace najdete [v tématu Podpora virtuálního hostitele založená na názvu.](https://httpd.apache.org/docs/current/vhosts/name-based.html)</span><span class="sxs-lookup"><span data-stu-id="698e9-158">See [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html) for more information.</span></span> <span data-ttu-id="698e9-159">Požadavky jsou proxied na kořenový port 5000 serveru na 127.0.0.1.</span><span class="sxs-lookup"><span data-stu-id="698e9-159">Requests are proxied at the root to port 5000 of the server at 127.0.0.1.</span></span> <span data-ttu-id="698e9-160">Pro obousměrnou komunikaci `ProxyPass` a `ProxyPassReverse` jsou vyžadovány.</span><span class="sxs-lookup"><span data-stu-id="698e9-160">For bi-directional communication, `ProxyPass` and `ProxyPassReverse` are required.</span></span> <span data-ttu-id="698e9-161">Chcete-li změnit protokol Kestrel IP/port, přečtěte si část [Kestrel: Konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="698e9-161">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="698e9-162">Pokud se nepodaří zadat správnou [direktivu Název_serveru](https://httpd.apache.org/docs/current/mod/core.html#servername) v bloku **VirtualHost,** aplikace bude vystavena slabým místům zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="698e9-162">Failure to specify a proper [ServerName directive](https://httpd.apache.org/docs/current/mod/core.html#servername) in the **VirtualHost** block exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="698e9-163">Vazba se zástupnými znaky subdomény (například) nepředstavuje toto bezpečnostní riziko, `*.example.com` `*.com`pokud řídíte celou nadřazenou doménu (na rozdíl od , která je zranitelná).</span><span class="sxs-lookup"><span data-stu-id="698e9-163">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="698e9-164">Viz [rfc7230 sekce-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) pro více informací.</span><span class="sxs-lookup"><span data-stu-id="698e9-164">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="698e9-165">Protokolování lze `VirtualHost` nakonfigurovat podle použití `ErrorLog` a `CustomLog` direktivy.</span><span class="sxs-lookup"><span data-stu-id="698e9-165">Logging can be configured per `VirtualHost` using `ErrorLog` and `CustomLog` directives.</span></span> <span data-ttu-id="698e9-166">`ErrorLog`je umístění, kde server zaznamenává `CustomLog` chyby a nastavuje název souboru a formát souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="698e9-166">`ErrorLog` is the location where the server logs errors, and `CustomLog` sets the filename and format of log file.</span></span> <span data-ttu-id="698e9-167">V tomto případě je zaznamenána informace požadavku.</span><span class="sxs-lookup"><span data-stu-id="698e9-167">In this case, this is where request information is logged.</span></span> <span data-ttu-id="698e9-168">Pro každou žádost je jeden řádek.</span><span class="sxs-lookup"><span data-stu-id="698e9-168">There's one line for each request.</span></span>

<span data-ttu-id="698e9-169">Uložte soubor a otestujte konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="698e9-169">Save the file and test the configuration.</span></span> <span data-ttu-id="698e9-170">Pokud vše projde, odpověď `Syntax [OK]`by měla být .</span><span class="sxs-lookup"><span data-stu-id="698e9-170">If everything passes, the response should be `Syntax [OK]`.</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="698e9-171">Restartujte Apache:</span><span class="sxs-lookup"><span data-stu-id="698e9-171">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a><span data-ttu-id="698e9-172">Sledování aplikace</span><span class="sxs-lookup"><span data-stu-id="698e9-172">Monitor the app</span></span>

<span data-ttu-id="698e9-173">Apache je nyní nastavena pro `http://localhost:80` předávání požadavků na ASP.NET aplikace `http://127.0.0.1:5000`Core spuštěná na Kestrel at .</span><span class="sxs-lookup"><span data-stu-id="698e9-173">Apache is now setup to forward requests made to `http://localhost:80` to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="698e9-174">Apache však není nastavena pro správu procesu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="698e9-174">However, Apache isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="698e9-175">Pomocí *systemd* utvařte soubor služby pro spuštění a monitorování základní webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="698e9-175">Use *systemd* and create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="698e9-176">*systemd* je init systém, který poskytuje mnoho výkonných funkcí pro spouštění, zastavování a správu procesů.</span><span class="sxs-lookup"><span data-stu-id="698e9-176">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span>

### <a name="create-the-service-file"></a><span data-ttu-id="698e9-177">Vytvoření souboru služby</span><span class="sxs-lookup"><span data-stu-id="698e9-177">Create the service file</span></span>

<span data-ttu-id="698e9-178">Vytvořte soubor definice služby:</span><span class="sxs-lookup"><span data-stu-id="698e9-178">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="698e9-179">Ukázkový soubor služby pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="698e9-179">An example service file for the app:</span></span>

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

<span data-ttu-id="698e9-180">V předchozím příkladu je uživatel, který spravuje službu, určen `User` možností.</span><span class="sxs-lookup"><span data-stu-id="698e9-180">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="698e9-181">Uživatel (`apache`) musí existovat a mít správné vlastnictví souborů aplikace.</span><span class="sxs-lookup"><span data-stu-id="698e9-181">The user (`apache`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="698e9-182">Slouží `TimeoutStopSec` ke konfiguraci doby čekání na vypnutí aplikace po přijetí signálu počátečního přerušení.</span><span class="sxs-lookup"><span data-stu-id="698e9-182">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="698e9-183">Pokud se aplikace v tomto období nevypne, je vydána aplikace SIGKILL, aby aplikaci ukončila.</span><span class="sxs-lookup"><span data-stu-id="698e9-183">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="698e9-184">Zadejte hodnotu jako sekundy `150`bez jednotky (například ), `2min 30s`hodnotu `infinity` časového rozpětí (například ) nebo zakázat časový rozsah.</span><span class="sxs-lookup"><span data-stu-id="698e9-184">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="698e9-185">`TimeoutStopSec`výchozí hodnota v `DefaultTimeoutStopSec` konfiguračním souboru správce (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span><span class="sxs-lookup"><span data-stu-id="698e9-185">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="698e9-186">Výchozí časový limit pro většinu distribucí je 90 sekund.</span><span class="sxs-lookup"><span data-stu-id="698e9-186">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="698e9-187">Některé hodnoty (například připojovací řetězce SQL) musí být uvozeny, aby zprostředkovatelé konfigurace mohli číst proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="698e9-187">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="698e9-188">Pomocí následujícího příkazu vygenerujte správně uvozenou hodnotu pro použití v konfiguračním souboru:</span><span class="sxs-lookup"><span data-stu-id="698e9-188">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

<span data-ttu-id="698e9-189">Oddělovače dvojtečky (`:`) nejsou v názvech proměnných prostředí podporovány.</span><span class="sxs-lookup"><span data-stu-id="698e9-189">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="698e9-190">Místo dvojtečky`__`použijte dvojité podtržítko ( ).</span><span class="sxs-lookup"><span data-stu-id="698e9-190">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="698e9-191">[Zprostředkovatel konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) převádí dvojité podtržítka na dvojtečky při čtení proměnných prostředí do konfigurace.</span><span class="sxs-lookup"><span data-stu-id="698e9-191">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="698e9-192">V následujícím příkladu je `ConnectionStrings:DefaultConnection` klíč připojovacího `ConnectionStrings__DefaultConnection`řetězce nastaven do souboru definice služby jako :</span><span class="sxs-lookup"><span data-stu-id="698e9-192">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="698e9-193">Uložte soubor a povolte službu:</span><span class="sxs-lookup"><span data-stu-id="698e9-193">Save the file and enable the service:</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="698e9-194">Spusťte službu a ověřte, zda je spuštěná:</span><span class="sxs-lookup"><span data-stu-id="698e9-194">Start the service and verify that it's running:</span></span>

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

<span data-ttu-id="698e9-195">S reverzní proxy nakonfigurován a Kestrel spravuje prostřednictvím *systemd*, webová aplikace je plně `http://localhost`nakonfigurován a lze přistupovat z prohlížeče na místním počítači na .</span><span class="sxs-lookup"><span data-stu-id="698e9-195">With the reverse proxy configured and Kestrel managed through *systemd*, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="698e9-196">Při kontrole záhlaví odpovědí záhlaví **serveru** označuje, že aplikace ASP.NET Core je obsluhována kestrelem:</span><span class="sxs-lookup"><span data-stu-id="698e9-196">Inspecting the response headers, the **Server** header indicates that the ASP.NET Core app is served by Kestrel:</span></span>

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="698e9-197">Zobrazení protokolů</span><span class="sxs-lookup"><span data-stu-id="698e9-197">View logs</span></span>

<span data-ttu-id="698e9-198">Vzhledem k tomu, že webová aplikace pomocí Kestrel je spravována pomocí *systemd*, události a procesy jsou protokolovány do centralizovaného deníku.</span><span class="sxs-lookup"><span data-stu-id="698e9-198">Since the web app using Kestrel is managed using *systemd*, events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="698e9-199">Tento deník však obsahuje položky pro všechny služby a procesy spravované *systemd*.</span><span class="sxs-lookup"><span data-stu-id="698e9-199">However, this journal includes entries for all of the services and processes managed by *systemd*.</span></span> <span data-ttu-id="698e9-200">Chcete-li `kestrel-helloapp.service`zobrazit specifické položky, použijte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="698e9-200">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="698e9-201">Pro filtrování času zadejte volby času pomocí příkazu.</span><span class="sxs-lookup"><span data-stu-id="698e9-201">For time filtering, specify time options with the command.</span></span> <span data-ttu-id="698e9-202">Slouží `--since today` například k filtrování pro `--until 1 hour ago` aktuální den nebo k zobrazení položek předchozí hodiny.</span><span class="sxs-lookup"><span data-stu-id="698e9-202">For example, use `--since today` to filter for the current day or `--until 1 hour ago` to see the previous hour's entries.</span></span> <span data-ttu-id="698e9-203">Další informace naleznete na [stránce man pro deník .](https://www.unix.com/man-page/centos/1/journalctl/)</span><span class="sxs-lookup"><span data-stu-id="698e9-203">For more information, see the [man page for journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="698e9-204">Ochrana dat</span><span class="sxs-lookup"><span data-stu-id="698e9-204">Data protection</span></span>

<span data-ttu-id="698e9-205">Zásobník [ASP.NET Core Data Protection](xref:security/data-protection/introduction) používá několik [middlewares](xref:fundamentals/middleware/index)ASP.NET Core , včetně autentizačního middlewaru (například middleware souborů cookie) a ochrany padělání požadavků na více webů (CSRF).</span><span class="sxs-lookup"><span data-stu-id="698e9-205">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="698e9-206">I v případě, že rozhraní API ochrany dat nejsou volána uživatelským kódem, ochrana dat by měla být nakonfigurována tak, aby vytvořila trvalé úložiště kryptografických [klíčů](xref:security/data-protection/implementation/key-management).</span><span class="sxs-lookup"><span data-stu-id="698e9-206">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="698e9-207">Pokud ochrana dat není nakonfigurována, klíče jsou uloženy v paměti a po restartování aplikace zahozeny.</span><span class="sxs-lookup"><span data-stu-id="698e9-207">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="698e9-208">Pokud je kroužek klíče uložen v paměti při restartování aplikace:</span><span class="sxs-lookup"><span data-stu-id="698e9-208">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="698e9-209">Všechny ověřovací tokeny založené na souborech cookie jsou zrušeny.</span><span class="sxs-lookup"><span data-stu-id="698e9-209">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="698e9-210">Uživatelé se musí znovu přihlásit při dalším požadavku.</span><span class="sxs-lookup"><span data-stu-id="698e9-210">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="698e9-211">Všechna data chráněná kroužkem klíče již nelze dešifrovat.</span><span class="sxs-lookup"><span data-stu-id="698e9-211">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="698e9-212">To může zahrnovat [tokeny CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [ASP.NET základní soubory cookie MVC TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="698e9-212">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="698e9-213">Chcete-li nakonfigurovat ochranu dat tak, aby byla zachována a zašifrována kroužek klíče, přečtěte si následující:</span><span class="sxs-lookup"><span data-stu-id="698e9-213">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a><span data-ttu-id="698e9-214">Zabezpečení aplikace</span><span class="sxs-lookup"><span data-stu-id="698e9-214">Secure the app</span></span>

### <a name="configure-firewall"></a><span data-ttu-id="698e9-215">Konfigurace brány firewall</span><span class="sxs-lookup"><span data-stu-id="698e9-215">Configure firewall</span></span>

<span data-ttu-id="698e9-216">*Firewalld* je dynamický daemon pro správu brány firewall s podporou síťových zón.</span><span class="sxs-lookup"><span data-stu-id="698e9-216">*Firewalld* is a dynamic daemon to manage the firewall with support for network zones.</span></span> <span data-ttu-id="698e9-217">Porty a filtrování paketů lze stále spravovat pomocí iptables.</span><span class="sxs-lookup"><span data-stu-id="698e9-217">Ports and packet filtering can still be managed by iptables.</span></span> <span data-ttu-id="698e9-218">*Firewalld* by měl být nainstalován ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="698e9-218">*Firewalld* should be installed by default.</span></span> <span data-ttu-id="698e9-219">`yum`lze použít k instalaci balíčku nebo ověření, že je nainstalován.</span><span class="sxs-lookup"><span data-stu-id="698e9-219">`yum` can be used to install the package or verify it's installed.</span></span>

```bash
sudo yum install firewalld -y
```

<span data-ttu-id="698e9-220">Slouží `firewalld` k otevření pouze portů potřebných pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="698e9-220">Use `firewalld` to open only the ports needed for the app.</span></span> <span data-ttu-id="698e9-221">V tomto případě se používá port 80 a 443.</span><span class="sxs-lookup"><span data-stu-id="698e9-221">In this case, port 80 and 443 are used.</span></span> <span data-ttu-id="698e9-222">Následující příkazy trvale nastavit porty 80 a 443 otevřít:</span><span class="sxs-lookup"><span data-stu-id="698e9-222">The following commands permanently set ports 80 and 443 to open:</span></span>

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

<span data-ttu-id="698e9-223">Znovu načtěte nastavení brány firewall.</span><span class="sxs-lookup"><span data-stu-id="698e9-223">Reload the firewall settings.</span></span> <span data-ttu-id="698e9-224">Zkontrolujte dostupné služby a porty ve výchozí zóně.</span><span class="sxs-lookup"><span data-stu-id="698e9-224">Check the available services and ports in the default zone.</span></span> <span data-ttu-id="698e9-225">Možnosti jsou k `firewall-cmd -h`dispozici kontrolou .</span><span class="sxs-lookup"><span data-stu-id="698e9-225">Options are available by inspecting `firewall-cmd -h`.</span></span>

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

### <a name="https-configuration"></a><span data-ttu-id="698e9-226">Konfigurace PROTOKOLU HTTPS</span><span class="sxs-lookup"><span data-stu-id="698e9-226">HTTPS configuration</span></span>

<span data-ttu-id="698e9-227">**Konfigurace aplikace pro zabezpečená místní připojení (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="698e9-227">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="698e9-228">Příkaz [dotnet run](/dotnet/core/tools/dotnet-run) používá soubor *Vlastnosti/launchSettings.json* aplikace, který konfiguruje aplikaci tak, aby naslouchala adresám URL poskytovaným `applicationUrl` vlastností (například ). `https://localhost:5001;http://localhost:5000`</span><span class="sxs-lookup"><span data-stu-id="698e9-228">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="698e9-229">Nakonfigurujte aplikaci tak, `dotnet run` aby používala certifikát ve vývoji pro prostředí příkazů nebo vývoje (F5 nebo Ctrl+F5 v kódu Visual Studia) pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="698e9-229">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="698e9-230">[Nahrazení výchozího certifikátu z konfigurace](xref:fundamentals/servers/kestrel#configuration) *(doporučeno)*</span><span class="sxs-lookup"><span data-stu-id="698e9-230">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="698e9-231">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="698e9-231">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="698e9-232">**Konfigurace reverzního proxy serveru pro zabezpečená připojení klientů (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="698e9-232">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

<span data-ttu-id="698e9-233">Pro konfiguraci Apache pro HTTPS se používá *modul mod_ssl.*</span><span class="sxs-lookup"><span data-stu-id="698e9-233">To configure Apache for HTTPS, the *mod_ssl* module is used.</span></span> <span data-ttu-id="698e9-234">Když byl nainstalován *modul httpd,* byl nainstalován také *modul mod_ssl.*</span><span class="sxs-lookup"><span data-stu-id="698e9-234">When the *httpd* module was installed, the *mod_ssl* module was also installed.</span></span> <span data-ttu-id="698e9-235">Pokud nebyla nainstalována, `yum` použijte k jeho přidání do konfigurace.</span><span class="sxs-lookup"><span data-stu-id="698e9-235">If it wasn't installed, use `yum` to add it to the configuration.</span></span>

```bash
sudo yum install mod_ssl
```

<span data-ttu-id="698e9-236">Chcete-li vynutit protokol HTTPS, nainstalujte modul a `mod_rewrite` povolte přepisování adres URL:</span><span class="sxs-lookup"><span data-stu-id="698e9-236">To enforce HTTPS, install the `mod_rewrite` module to enable URL rewriting:</span></span>

```bash
sudo yum install mod_rewrite
```

<span data-ttu-id="698e9-237">Upravte soubor *helloapp.conf* a povolte přepis ování adres URL a zabezpečenou komunikaci na portu 443:</span><span class="sxs-lookup"><span data-stu-id="698e9-237">Modify the *helloapp.conf* file to enable URL rewriting and secure communication on port 443:</span></span>

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
> <span data-ttu-id="698e9-238">Tento příklad používá místně generovaný certifikát.</span><span class="sxs-lookup"><span data-stu-id="698e9-238">This example is using a locally-generated certificate.</span></span> <span data-ttu-id="698e9-239">**SSLCertificateFile** by měl být primárnísoubor certifikátu pro název domény.</span><span class="sxs-lookup"><span data-stu-id="698e9-239">**SSLCertificateFile** should be the primary certificate file for the domain name.</span></span> <span data-ttu-id="698e9-240">**SSLCertificateKeyFile** by měl být soubor klíče generovaný při vytvoření zástupce.</span><span class="sxs-lookup"><span data-stu-id="698e9-240">**SSLCertificateKeyFile** should be the key file generated when CSR is created.</span></span> <span data-ttu-id="698e9-241">**SSLCertificateChainFile** by měl být zprostředkující soubor certifikátu (pokud existuje), který byl dodán certifikační autoritou.</span><span class="sxs-lookup"><span data-stu-id="698e9-241">**SSLCertificateChainFile** should be the intermediate certificate file (if any) that was supplied by the certificate authority.</span></span>

<span data-ttu-id="698e9-242">Uložte soubor a otestujte konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="698e9-242">Save the file and test the configuration:</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="698e9-243">Restartujte Apache:</span><span class="sxs-lookup"><span data-stu-id="698e9-243">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a><span data-ttu-id="698e9-244">Další návrhy Apache</span><span class="sxs-lookup"><span data-stu-id="698e9-244">Additional Apache suggestions</span></span>

### <a name="restart-apps-with-shared-framework-updates"></a><span data-ttu-id="698e9-245">Restartování aplikací pomocí sdílených aktualizací architektury</span><span class="sxs-lookup"><span data-stu-id="698e9-245">Restart apps with shared framework updates</span></span>

<span data-ttu-id="698e9-246">Po upgradu sdíleného rozhraní na serveru restartujte aplikace ASP.NET Core hostované serverem.</span><span class="sxs-lookup"><span data-stu-id="698e9-246">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

### <a name="additional-headers"></a><span data-ttu-id="698e9-247">Další záhlaví</span><span class="sxs-lookup"><span data-stu-id="698e9-247">Additional headers</span></span>

<span data-ttu-id="698e9-248">Chcete-li zabezpečit proti škodlivým útokům, existuje několik záhlaví, které by měly být změněny nebo přidány.</span><span class="sxs-lookup"><span data-stu-id="698e9-248">In order to secure against malicious attacks, there are a few headers that should either be modified or added.</span></span> <span data-ttu-id="698e9-249">Ujistěte `mod_headers` se, že je modul nainstalován:</span><span class="sxs-lookup"><span data-stu-id="698e9-249">Ensure that the `mod_headers` module is installed:</span></span>

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a><span data-ttu-id="698e9-250">Zabezpečte Apache z clickjacking útoky</span><span class="sxs-lookup"><span data-stu-id="698e9-250">Secure Apache from clickjacking attacks</span></span>

<span data-ttu-id="698e9-251">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), také známý jako *útok na nápravu ui*, je škodlivý útok, kdy je návštěvník webových stránek podveden, aby klikl na odkaz nebo tlačítko na jiné stránce, než je v současné době na návštěvě.</span><span class="sxs-lookup"><span data-stu-id="698e9-251">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="698e9-252">Slouží `X-FRAME-OPTIONS` k zabezpečení webu.</span><span class="sxs-lookup"><span data-stu-id="698e9-252">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="698e9-253">Chcete-li zmírnit clickjacking útoky:</span><span class="sxs-lookup"><span data-stu-id="698e9-253">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="698e9-254">Upravte soubor *httpd.conf:*</span><span class="sxs-lookup"><span data-stu-id="698e9-254">Edit the *httpd.conf* file:</span></span>

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   <span data-ttu-id="698e9-255">Přidejte řádek `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span><span class="sxs-lookup"><span data-stu-id="698e9-255">Add the line `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span></span>
1. <span data-ttu-id="698e9-256">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="698e9-256">Save the file.</span></span>
1. <span data-ttu-id="698e9-257">Restartujte Apache.</span><span class="sxs-lookup"><span data-stu-id="698e9-257">Restart Apache.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="698e9-258">Čichání typu MIME</span><span class="sxs-lookup"><span data-stu-id="698e9-258">MIME-type sniffing</span></span>

<span data-ttu-id="698e9-259">Záhlaví `X-Content-Type-Options` zabraňuje aplikaci Internet Explorer v *tom, aby očichávání MIME* (určení souboru `Content-Type` z obsahu souboru).</span><span class="sxs-lookup"><span data-stu-id="698e9-259">The `X-Content-Type-Options` header prevents Internet Explorer from *MIME-sniffing* (determining a file's `Content-Type` from the file's content).</span></span> <span data-ttu-id="698e9-260">Pokud server nastaví `Content-Type` `text/html` záhlaví `nosniff` s nastavenou možností, aplikace `text/html` Internet Explorer vykreslí obsah bez ohledu na obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="698e9-260">If the server sets the `Content-Type` header to `text/html` with the `nosniff` option set, Internet Explorer renders the content as `text/html` regardless of the file's content.</span></span>

<span data-ttu-id="698e9-261">Upravte soubor *httpd.conf:*</span><span class="sxs-lookup"><span data-stu-id="698e9-261">Edit the *httpd.conf* file:</span></span>

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

<span data-ttu-id="698e9-262">Přidejte řádek `Header set X-Content-Type-Options "nosniff"`.</span><span class="sxs-lookup"><span data-stu-id="698e9-262">Add the line `Header set X-Content-Type-Options "nosniff"`.</span></span> <span data-ttu-id="698e9-263">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="698e9-263">Save the file.</span></span> <span data-ttu-id="698e9-264">Restartujte Apache.</span><span class="sxs-lookup"><span data-stu-id="698e9-264">Restart Apache.</span></span>

### <a name="load-balancing"></a><span data-ttu-id="698e9-265">Vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="698e9-265">Load Balancing</span></span>

<span data-ttu-id="698e9-266">Tento příklad ukazuje, jak nastavit a nakonfigurovat Apache na CentOS 7 a Kestrel na stejném počítači instance.</span><span class="sxs-lookup"><span data-stu-id="698e9-266">This example shows how to setup and configure Apache on CentOS 7 and Kestrel on the same instance machine.</span></span> <span data-ttu-id="698e9-267">Aby nedošlo k jedinému bodu selhání; použití *mod_proxy_balancer* a úprava **VirtualHost** by umožnilo správu více instancí webových aplikací za proxy serverapache.</span><span class="sxs-lookup"><span data-stu-id="698e9-267">In order to not have a single point of failure; using *mod_proxy_balancer* and modifying the **VirtualHost** would allow for managing multiple instances of the web apps behind the Apache proxy server.</span></span>

```bash
sudo yum install mod_proxy_balancer
```

<span data-ttu-id="698e9-268">V konfiguračním souboru uvedeném níže `helloapp` je další instance nastavena tak, aby byla spuštěna na portu 5001.</span><span class="sxs-lookup"><span data-stu-id="698e9-268">In the configuration file shown below, an additional instance of the `helloapp` is set up to run on port 5001.</span></span> <span data-ttu-id="698e9-269">Oddíl *Proxy* je nastaven s konfigurací nástroje pro vyrovnávání zatížení se dvěma členy pro *vyrovnávání*zatížení požadavky .</span><span class="sxs-lookup"><span data-stu-id="698e9-269">The *Proxy* section is set with a balancer configuration with two members to load balance *byrequests*.</span></span>

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

### <a name="rate-limits"></a><span data-ttu-id="698e9-270">Omezení sazeb</span><span class="sxs-lookup"><span data-stu-id="698e9-270">Rate Limits</span></span>

<span data-ttu-id="698e9-271">Pomocí *mod_ratelimit*, který je součástí *modulu httpd,* může být šířka pásma klientů omezena:</span><span class="sxs-lookup"><span data-stu-id="698e9-271">Using *mod_ratelimit*, which is included in the *httpd* module, the bandwidth of clients can be limited:</span></span>

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

<span data-ttu-id="698e9-272">Ukázkový soubor omezuje šířku pásma na 600 KB/s pod kořenovým umístěním:</span><span class="sxs-lookup"><span data-stu-id="698e9-272">The example file limits bandwidth as 600 KB/sec under the root location:</span></span>

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a><span data-ttu-id="698e9-273">Pole záhlaví dlouhého požadavku</span><span class="sxs-lookup"><span data-stu-id="698e9-273">Long request header fields</span></span>

<span data-ttu-id="698e9-274">Výchozí nastavení serveru proxy obvykle omezují pole hlaviček požadavku na 8 190 bajtů.</span><span class="sxs-lookup"><span data-stu-id="698e9-274">Proxy server default settings typically limit request header fields to 8,190 bytes.</span></span> <span data-ttu-id="698e9-275">Aplikace může vyžadovat pole delší než výchozí (například aplikace, které používají [Azure Active Directory).](https://azure.microsoft.com/services/active-directory/)</span><span class="sxs-lookup"><span data-stu-id="698e9-275">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="698e9-276">Pokud jsou požadována delší pole, vyžaduje direktivu [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) proxy serveru úpravu.</span><span class="sxs-lookup"><span data-stu-id="698e9-276">If longer fields are required, the proxy server's [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) directive requires adjustment.</span></span> <span data-ttu-id="698e9-277">Hodnota, která má být použít, závisí na scénáři.</span><span class="sxs-lookup"><span data-stu-id="698e9-277">The value to apply depends on the scenario.</span></span> <span data-ttu-id="698e9-278">Další informace naleznete v dokumentaci k serveru.</span><span class="sxs-lookup"><span data-stu-id="698e9-278">For more information, see your server's documentation.</span></span>

> [!WARNING]
> <span data-ttu-id="698e9-279">Nezvyšujte výchozí hodnotu, `LimitRequestFieldSize` pokud to není nutné.</span><span class="sxs-lookup"><span data-stu-id="698e9-279">Don't increase the default value of `LimitRequestFieldSize` unless necessary.</span></span> <span data-ttu-id="698e9-280">Zvýšení hodnoty zvyšuje riziko přetečení vyrovnávací paměti (přetečení) a odmítnutí služby (DoS) útoky uživatelů se zlými úmysly.</span><span class="sxs-lookup"><span data-stu-id="698e9-280">Increasing the value increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="698e9-281">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="698e9-281">Additional resources</span></span>

* [<span data-ttu-id="698e9-282">Předpoklady pro .NET Core na Linuxu</span><span class="sxs-lookup"><span data-stu-id="698e9-282">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
