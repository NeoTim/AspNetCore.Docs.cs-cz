---
title: Hostitele ASP.NET Core v Linuxu pomocí Apache
description: Zjistěte, jak nastavit službu Apache jako reverzní proxy server na CentOS pro přesměrování přenosu dat protokolu HTTP k webové aplikaci ASP.NET Core spuštěnou v prostředí Kestrel.
author: spboyer
ms.author: spboyer
ms.custom: mvc
ms.date: 02/27/2019
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 69026997b2c269a4fb56ed2a79fa42ae218368e1
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345934"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a><span data-ttu-id="30fa6-103">Hostitele ASP.NET Core v Linuxu pomocí Apache</span><span class="sxs-lookup"><span data-stu-id="30fa6-103">Host ASP.NET Core on Linux with Apache</span></span>

<span data-ttu-id="30fa6-104">Podle [Shayne Boyer](https://github.com/spboyer)</span><span class="sxs-lookup"><span data-stu-id="30fa6-104">By [Shayne Boyer](https://github.com/spboyer)</span></span>

<span data-ttu-id="30fa6-105">Pomocí této příručky, zjistěte, jak nastavit [Apache](https://httpd.apache.org/) jako reverzní proxy server na [CentOS 7](https://www.centos.org/) pro přesměrování přenosu dat protokolu HTTP pro webovou aplikaci ASP.NET Core využívající [Kestrel](xref:fundamentals/servers/kestrel) serveru.</span><span class="sxs-lookup"><span data-stu-id="30fa6-105">Using this guide, learn how to set up [Apache](https://httpd.apache.org/) as a reverse proxy server on [CentOS 7](https://www.centos.org/) to redirect HTTP traffic to an ASP.NET Core web app running on [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="30fa6-106">[Mod_proxy rozšíření](http://httpd.apache.org/docs/2.4/mod/mod_proxy.html) a související moduly vytvořit reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="30fa6-106">The [mod_proxy extension](http://httpd.apache.org/docs/2.4/mod/mod_proxy.html) and related modules create the server's reverse proxy.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="30fa6-107">Požadavky</span><span class="sxs-lookup"><span data-stu-id="30fa6-107">Prerequisites</span></span>

* <span data-ttu-id="30fa6-108">Server se systémem CentOS 7 pomocí standardního uživatelského účtu s oprávněními sudo.</span><span class="sxs-lookup"><span data-stu-id="30fa6-108">Server running CentOS 7 with a standard user account with sudo privilege.</span></span>
* <span data-ttu-id="30fa6-109">Nainstalujte modul runtime .NET Core na serveru.</span><span class="sxs-lookup"><span data-stu-id="30fa6-109">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="30fa6-110">Přejděte [.NET Core všechny soubory ke stažení stránky](https://www.microsoft.com/net/download/all).</span><span class="sxs-lookup"><span data-stu-id="30fa6-110">Visit the [.NET Core All Downloads page](https://www.microsoft.com/net/download/all).</span></span>
   1. <span data-ttu-id="30fa6-111">Vyberte nejnovější modul runtime – ve verzi preview ze seznamu **Runtime**.</span><span class="sxs-lookup"><span data-stu-id="30fa6-111">Select the latest non-preview runtime from the list under **Runtime**.</span></span>
   1. <span data-ttu-id="30fa6-112">Vyberte a postupujte podle pokynů pro CentOS nebo Oracle.</span><span class="sxs-lookup"><span data-stu-id="30fa6-112">Select and follow the instructions for CentOS/Oracle.</span></span>
* <span data-ttu-id="30fa6-113">Stávající aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="30fa6-113">An existing ASP.NET Core app.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="30fa6-114">Publikování a zkopírujte myší na aplikaci</span><span class="sxs-lookup"><span data-stu-id="30fa6-114">Publish and copy over the app</span></span>

<span data-ttu-id="30fa6-115">Konfigurace aplikace pro [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="30fa6-115">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="30fa6-116">Spustit [dotnet publikovat](/dotnet/core/tools/dotnet-publish) z vývojového prostředí pro balíček aplikace do adresáře (například *bin/Release/&lt;target_framework_moniker&gt;/ publish*), který můžete Spusťte na serveru:</span><span class="sxs-lookup"><span data-stu-id="30fa6-116">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```console
dotnet publish --configuration Release
```

<span data-ttu-id="30fa6-117">Aplikace můžete také publikovat jako [samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) Pokud nechcete zachovat modulu runtime .NET Core na serveru.</span><span class="sxs-lookup"><span data-stu-id="30fa6-117">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="30fa6-118">Aplikace ASP.NET Core zkopírujte na server pomocí nástroje, které se integruje do pracovního postupu organizace (třeba spojovací bod služby, SFTP).</span><span class="sxs-lookup"><span data-stu-id="30fa6-118">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="30fa6-119">Je běžné vyhledejte webové aplikace v rámci *var* adresář (třeba *www/var/helloapp*).</span><span class="sxs-lookup"><span data-stu-id="30fa6-119">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="30fa6-120">V případě produkčního nasazení pracovního postupu průběžné integrace funguje publikování aplikace a kopírování prostředky na server.</span><span class="sxs-lookup"><span data-stu-id="30fa6-120">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

## <a name="configure-a-proxy-server"></a><span data-ttu-id="30fa6-121">Konfigurace proxy serveru</span><span class="sxs-lookup"><span data-stu-id="30fa6-121">Configure a proxy server</span></span>

<span data-ttu-id="30fa6-122">Reverzní proxy server je společné nastavení pro poskytování dynamické webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="30fa6-122">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="30fa6-123">Reverzní proxy server ukončí požadavek HTTP a předá ji do aplikace ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="30fa6-123">The reverse proxy terminates the HTTP request and forwards it to the ASP.NET app.</span></span>

<span data-ttu-id="30fa6-124">Proxy server je znak, který se předává požadavky klientů na jiný server místo samotného plnění požadavků.</span><span class="sxs-lookup"><span data-stu-id="30fa6-124">A proxy server is one which forwards client requests to another server instead of fulfilling requests itself.</span></span> <span data-ttu-id="30fa6-125">Reverzní proxy server předává do pevné umístění, obvykle jménem libovolného klientů.</span><span class="sxs-lookup"><span data-stu-id="30fa6-125">A reverse proxy forwards to a fixed destination, typically on behalf of arbitrary clients.</span></span> <span data-ttu-id="30fa6-126">V této příručce Apache nakonfigurovaný jako reverzní proxy server běží na stejném serveru, aby Kestrel obsluhuje aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="30fa6-126">In this guide, Apache is configured as the reverse proxy running on the same server that Kestrel is serving the ASP.NET Core app.</span></span>

<span data-ttu-id="30fa6-127">Vzhledem k tomu, že žádosti jsou předávány podle reverzní proxy server, použít [předané Middleware záhlaví](xref:host-and-deploy/proxy-load-balancer) z [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="30fa6-127">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="30fa6-128">Middleware aktualizace `Request.Scheme`, použije `X-Forwarded-Proto` záhlaví tak, že identifikátory URI pro přesměrování a další zásady zabezpečení pracovat správně.</span><span class="sxs-lookup"><span data-stu-id="30fa6-128">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="30fa6-129">Jakékoli součásti, která závisí na schéma, jako je například ověřování, generování odkazů, přesměrování a zeměpisná poloha, musí být umístěn po vyvolání Middleware předané záhlaví.</span><span class="sxs-lookup"><span data-stu-id="30fa6-129">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span> <span data-ttu-id="30fa6-130">Jako obecné pravidlo by měla předávat Middleware záhlaví spustit před dalším middlewarem s výjimkou diagnostiky a middleware pro zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="30fa6-130">As a general rule, Forwarded Headers Middleware should run before other middleware except diagnostics and error handling middleware.</span></span> <span data-ttu-id="30fa6-131">Toto uspořádání zajistí, že middleware spoléhání se na informace předávané záhlaví může spotřebovat hodnoty hlavičky pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="30fa6-131">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="30fa6-132">Vyvolat <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metoda `Startup.Configure` před voláním <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> nebo podobné režimu middleware ověřování.</span><span class="sxs-lookup"><span data-stu-id="30fa6-132">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or similar authentication scheme middleware.</span></span> <span data-ttu-id="30fa6-133">Nakonfigurujte middleware předávat `X-Forwarded-For` a `X-Forwarded-Proto` hlavičky:</span><span class="sxs-lookup"><span data-stu-id="30fa6-133">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="30fa6-134">Vyvolat <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metoda `Startup.Configure` před voláním <xref:Microsoft.AspNetCore.Builder.BuilderExtensions.UseIdentity*> a <xref:Microsoft.AspNetCore.Builder.FacebookAppBuilderExtensions.UseFacebookAuthentication*> nebo podobné režimu middleware ověřování.</span><span class="sxs-lookup"><span data-stu-id="30fa6-134">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling <xref:Microsoft.AspNetCore.Builder.BuilderExtensions.UseIdentity*> and <xref:Microsoft.AspNetCore.Builder.FacebookAppBuilderExtensions.UseFacebookAuthentication*> or similar authentication scheme middleware.</span></span> <span data-ttu-id="30fa6-135">Nakonfigurujte middleware předávat `X-Forwarded-For` a `X-Forwarded-Proto` hlavičky:</span><span class="sxs-lookup"><span data-stu-id="30fa6-135">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

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

<span data-ttu-id="30fa6-136">Pokud ne <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> jsou určené pro middleware, jsou výchozí hlavičky pro předávání `None`.</span><span class="sxs-lookup"><span data-stu-id="30fa6-136">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="30fa6-137">Proxy servery, které běží na adresu zpětné smyčky (127.0.0.0/8, [:: 1]), včetně standardní localhost adresu (127.0.0.1), ve výchozím nastavení jsou důvěryhodné.</span><span class="sxs-lookup"><span data-stu-id="30fa6-137">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="30fa6-138">Pokud jiné důvěryhodné proxy nebo sítěmi v rámci popisovač požadavky organizace mezi Internetem a webový server, je přidat do seznamu <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> nebo <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span><span class="sxs-lookup"><span data-stu-id="30fa6-138">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="30fa6-139">Následující příklad přidá důvěryhodným proxy serveru na IP adrese 10.0.0.100 s Middlewarem předané záhlaví `KnownProxies` v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="30fa6-139">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="30fa6-140">Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="30fa6-140">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-apache"></a><span data-ttu-id="30fa6-141">Instalace Apache</span><span class="sxs-lookup"><span data-stu-id="30fa6-141">Install Apache</span></span>

<span data-ttu-id="30fa6-142">CentOS balíčky aktualizací pro jejich nejnovější stabilní verze:</span><span class="sxs-lookup"><span data-stu-id="30fa6-142">Update CentOS packages to their latest stable versions:</span></span>

```bash
sudo yum update -y
```

<span data-ttu-id="30fa6-143">Instalace webového serveru Apache na CentOS pomocí jediného `yum` příkaz:</span><span class="sxs-lookup"><span data-stu-id="30fa6-143">Install the Apache web server on CentOS with a single `yum` command:</span></span>

```bash
sudo yum -y install httpd mod_ssl
```

<span data-ttu-id="30fa6-144">Ukázkový výstup po spuštění příkazu:</span><span class="sxs-lookup"><span data-stu-id="30fa6-144">Sample output after running the command:</span></span>

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
> <span data-ttu-id="30fa6-145">V tomto příkladu výstupu odráží httpd.86_64 od verze CentOS 7 je 64bitový.</span><span class="sxs-lookup"><span data-stu-id="30fa6-145">In this example, the output reflects httpd.86_64 since the CentOS 7 version is 64 bit.</span></span> <span data-ttu-id="30fa6-146">Chcete-li zkontrolovat, kde je nainstalován Apache, `whereis httpd` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="30fa6-146">To verify where Apache is installed, run `whereis httpd` from a command prompt.</span></span>

### <a name="configure-apache"></a><span data-ttu-id="30fa6-147">Nakonfigurovat i Apache</span><span class="sxs-lookup"><span data-stu-id="30fa6-147">Configure Apache</span></span>

<span data-ttu-id="30fa6-148">Konfigurační soubory pro Apache jsou umístěné v rámci `/etc/httpd/conf.d/` adresáře.</span><span class="sxs-lookup"><span data-stu-id="30fa6-148">Configuration files for Apache are located within the `/etc/httpd/conf.d/` directory.</span></span> <span data-ttu-id="30fa6-149">Žádný soubor s *.conf* rozšíření, jsou zpracovávána v abecedním pořadí kromě souborů konfigurace modulu v `/etc/httpd/conf.modules.d/`, která obsahuje veškeré konfigurace soubory nezbytné k načtení modulů.</span><span class="sxs-lookup"><span data-stu-id="30fa6-149">Any file with the *.conf* extension is processed in alphabetical order in addition to the module configuration files in `/etc/httpd/conf.modules.d/`, which contains any configuration files necessary to load modules.</span></span>

<span data-ttu-id="30fa6-150">Vytvoření konfiguračního souboru s názvem *helloapp.conf*, pro aplikace:</span><span class="sxs-lookup"><span data-stu-id="30fa6-150">Create a configuration file, named *helloapp.conf*, for the app:</span></span>

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

<span data-ttu-id="30fa6-151">`VirtualHost` Bloku se mohou objevit více než jednou, v jedné nebo více souborů na serveru.</span><span class="sxs-lookup"><span data-stu-id="30fa6-151">The `VirtualHost` block can appear multiple times, in one or more files on a server.</span></span> <span data-ttu-id="30fa6-152">V předchozím konfigurační soubor Apache přijímá veřejné provozu na portu 80.</span><span class="sxs-lookup"><span data-stu-id="30fa6-152">In the preceding configuration file, Apache accepts public traffic on port 80.</span></span> <span data-ttu-id="30fa6-153">Doména `www.example.com` se obsluhuje a `*.example.com` alias se překládá na stejné webové stránce.</span><span class="sxs-lookup"><span data-stu-id="30fa6-153">The domain `www.example.com` is being served, and the `*.example.com` alias resolves to the same website.</span></span> <span data-ttu-id="30fa6-154">Zobrazit [podporu založené na název virtuálního hostitele](https://httpd.apache.org/docs/current/vhosts/name-based.html) Další informace.</span><span class="sxs-lookup"><span data-stu-id="30fa6-154">See [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html) for more information.</span></span> <span data-ttu-id="30fa6-155">Požadavky jsou směrovány přes proxy server v kořenovém adresáři na portu 5000 serveru na 127.0.0.1.</span><span class="sxs-lookup"><span data-stu-id="30fa6-155">Requests are proxied at the root to port 5000 of the server at 127.0.0.1.</span></span> <span data-ttu-id="30fa6-156">Pro obousměrnou komunikaci `ProxyPass` a `ProxyPassReverse` jsou povinné.</span><span class="sxs-lookup"><span data-stu-id="30fa6-156">For bi-directional communication, `ProxyPass` and `ProxyPassReverse` are required.</span></span> <span data-ttu-id="30fa6-157">Chcete-li změnit Kestrel jeho IP adresa/port, [Kestrel: Konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="30fa6-157">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="30fa6-158">Nepodařilo se určit správnou [ServerName směrnice](https://httpd.apache.org/docs/current/mod/core.html#servername) v **VirtualHost** bloku zpřístupňuje aplikaci tak, aby slabá místa zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="30fa6-158">Failure to specify a proper [ServerName directive](https://httpd.apache.org/docs/current/mod/core.html#servername) in the **VirtualHost** block exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="30fa6-159">Vazby zástupný znak subdoménu (například `*.example.com`) nemá představovat toto bezpečnostní riziko, pokud řídíte celý nadřazené domény (nikoli `*.com`, což je ohrožené).</span><span class="sxs-lookup"><span data-stu-id="30fa6-159">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="30fa6-160">Zobrazit [rfc7230 části-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) Další informace.</span><span class="sxs-lookup"><span data-stu-id="30fa6-160">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="30fa6-161">Protokolování lze nastavit za `VirtualHost` pomocí `ErrorLog` a `CustomLog` direktivy.</span><span class="sxs-lookup"><span data-stu-id="30fa6-161">Logging can be configured per `VirtualHost` using `ErrorLog` and `CustomLog` directives.</span></span> <span data-ttu-id="30fa6-162">`ErrorLog` je umístění, kde server protokoluje chyby, a `CustomLog` nastaví název souboru a formát souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="30fa6-162">`ErrorLog` is the location where the server logs errors, and `CustomLog` sets the filename and format of log file.</span></span> <span data-ttu-id="30fa6-163">V takovém případě je kde zaznamenané informace o žádostech.</span><span class="sxs-lookup"><span data-stu-id="30fa6-163">In this case, this is where request information is logged.</span></span> <span data-ttu-id="30fa6-164">Existuje jeden řádek pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="30fa6-164">There's one line for each request.</span></span>

<span data-ttu-id="30fa6-165">Uložte soubor a testovací konfigurace.</span><span class="sxs-lookup"><span data-stu-id="30fa6-165">Save the file and test the configuration.</span></span> <span data-ttu-id="30fa6-166">Pokud vše projde, odpověď by měla být `Syntax [OK]`.</span><span class="sxs-lookup"><span data-stu-id="30fa6-166">If everything passes, the response should be `Syntax [OK]`.</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="30fa6-167">Restartujte Apache:</span><span class="sxs-lookup"><span data-stu-id="30fa6-167">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a><span data-ttu-id="30fa6-168">Sledování aplikace</span><span class="sxs-lookup"><span data-stu-id="30fa6-168">Monitor the app</span></span>

<span data-ttu-id="30fa6-169">Apache je nyní instalačního programu předat požadavky na `http://localhost:80` pro aplikaci ASP.NET Core spuštěnou v Kestrel na `http://127.0.0.1:5000`.</span><span class="sxs-lookup"><span data-stu-id="30fa6-169">Apache is now setup to forward requests made to `http://localhost:80` to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="30fa6-170">Apache není však nastavené ke správě procesu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="30fa6-170">However, Apache isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="30fa6-171">Použití *systemd* a vytvořit soubor služby a začít monitorovat základní webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="30fa6-171">Use *systemd* and create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="30fa6-172">*systemd* je init systém, který poskytuje řadu výkonných funkcí pro spouštění, zastavování a Správa procesů.</span><span class="sxs-lookup"><span data-stu-id="30fa6-172">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span>

### <a name="create-the-service-file"></a><span data-ttu-id="30fa6-173">Vytvoření souboru služby</span><span class="sxs-lookup"><span data-stu-id="30fa6-173">Create the service file</span></span>

<span data-ttu-id="30fa6-174">Vytvoření definičního souboru služby:</span><span class="sxs-lookup"><span data-stu-id="30fa6-174">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="30fa6-175">Příklad souboru služby pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="30fa6-175">An example service file for the app:</span></span>

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

<span data-ttu-id="30fa6-176">Pokud uživatel *apache* nepoužívá konfigurace, musíte uživatele nejprve vytvořit a zadané správné vlastnictví souborů.</span><span class="sxs-lookup"><span data-stu-id="30fa6-176">If the user *apache* isn't used by the configuration, the user must be created first and given proper ownership of files.</span></span>

<span data-ttu-id="30fa6-177">Použití `TimeoutStopSec` nakonfigurovat doba čekání na aplikaci pro vypnutí po přijetí počáteční přerušení signálu.</span><span class="sxs-lookup"><span data-stu-id="30fa6-177">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="30fa6-178">Pokud aplikace není v tomto období vypnout, objeví se SIGKILL ukončit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="30fa6-178">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="30fa6-179">Zadejte hodnotu unitless sekund (například `150`), časový interval hodnotu (například `2min 30s`), nebo `infinity` zakázat časový limit.</span><span class="sxs-lookup"><span data-stu-id="30fa6-179">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="30fa6-180">`TimeoutStopSec` Výchozí hodnota je hodnota `DefaultTimeoutStopSec` v konfiguračním souboru správce (*systemd system.conf*, *system.conf.d*, *systemd user.conf*,  *User.conf.d*).</span><span class="sxs-lookup"><span data-stu-id="30fa6-180">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="30fa6-181">Výchozí hodnota časového limitu pro většinu distribuce je 90 sekund.</span><span class="sxs-lookup"><span data-stu-id="30fa6-181">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="30fa6-182">Některé hodnoty (například připojovací řetězce SQL) musí být uvozena pro zprostředkovatele konfigurace pro čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="30fa6-182">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="30fa6-183">Použijte následující příkaz k vygenerování správně uvozený uvozovacím znakem hodnoty pro použití v konfiguračním souboru:</span><span class="sxs-lookup"><span data-stu-id="30fa6-183">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

<span data-ttu-id="30fa6-184">Uložte soubor a povolení služby:</span><span class="sxs-lookup"><span data-stu-id="30fa6-184">Save the file and enable the service:</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="30fa6-185">Spusťte službu a ověřte, zda je spuštěna:</span><span class="sxs-lookup"><span data-stu-id="30fa6-185">Start the service and verify that it's running:</span></span>

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

<span data-ttu-id="30fa6-186">Reverzní proxy server nakonfigurovaný a spravované přes Kestrel *systemd*, webové aplikace plně konfigurována a je přístupný z prohlížeče na místním počítači v `http://localhost`.</span><span class="sxs-lookup"><span data-stu-id="30fa6-186">With the reverse proxy configured and Kestrel managed through *systemd*, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="30fa6-187">Kontrola hlavičky odpovědi **Server** hlavička označuje, že aplikace ASP.NET Core je poskytovaný Kestrel:</span><span class="sxs-lookup"><span data-stu-id="30fa6-187">Inspecting the response headers, the **Server** header indicates that the ASP.NET Core app is served by Kestrel:</span></span>

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="30fa6-188">Zobrazení protokolů</span><span class="sxs-lookup"><span data-stu-id="30fa6-188">View logs</span></span>

<span data-ttu-id="30fa6-189">Od webové aplikace pomocí Kestrel se spravuje pomocí *systemd*, události a procesy jsou protokolovány centralizované deníku.</span><span class="sxs-lookup"><span data-stu-id="30fa6-189">Since the web app using Kestrel is managed using *systemd*, events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="30fa6-190">Ale tento deník obsahuje záznamy pro všechny služby a spravuje procesy *systemd*.</span><span class="sxs-lookup"><span data-stu-id="30fa6-190">However, this journal includes entries for all of the services and processes managed by *systemd*.</span></span> <span data-ttu-id="30fa6-191">Chcete-li zobrazit `kestrel-helloapp.service`-konkrétní položky, použijte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="30fa6-191">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="30fa6-192">Pro filtrování podle času, zadejte možnosti pomocí příkazu.</span><span class="sxs-lookup"><span data-stu-id="30fa6-192">For time filtering, specify time options with the command.</span></span> <span data-ttu-id="30fa6-193">Například použít `--since today` filtrovat aktuální den nebo `--until 1 hour ago` zobrazíte položky do předchozí hodiny.</span><span class="sxs-lookup"><span data-stu-id="30fa6-193">For example, use `--since today` to filter for the current day or `--until 1 hour ago` to see the previous hour's entries.</span></span> <span data-ttu-id="30fa6-194">Další informace najdete v tématu [man stránka journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span><span class="sxs-lookup"><span data-stu-id="30fa6-194">For more information, see the [man page for journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="30fa6-195">Ochrana dat</span><span class="sxs-lookup"><span data-stu-id="30fa6-195">Data protection</span></span>

<span data-ttu-id="30fa6-196">[Ochranu dat ASP.NET Core zásobníku](xref:security/data-protection/introduction) používá několik ASP.NET Core [middlewares](xref:fundamentals/middleware/index), včetně middleware ověřování (například middlewaru souboru cookie.) a mezi weby (CSRF) proti padělání požadavků ochranu.</span><span class="sxs-lookup"><span data-stu-id="30fa6-196">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="30fa6-197">I v případě, že Data Protection API nejsou volané kódem uživatele, ochranu dat by měl být povolen vytvořit trvalé kryptografických [úložiště klíčů](xref:security/data-protection/implementation/key-management).</span><span class="sxs-lookup"><span data-stu-id="30fa6-197">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="30fa6-198">Pokud není nakonfigurovaná ochrana dat, jsou klíče uložené v paměti a při restartování aplikace.</span><span class="sxs-lookup"><span data-stu-id="30fa6-198">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="30fa6-199">Pokud kanál klíče jsou uloženy v paměti, při restartování aplikace:</span><span class="sxs-lookup"><span data-stu-id="30fa6-199">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="30fa6-200">Všechny tokeny ověřování na základě souborů cookie nejsou zneplatněny.</span><span class="sxs-lookup"><span data-stu-id="30fa6-200">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="30fa6-201">Uživatelé se musí znovu přihlásit v jejich další požadavek.</span><span class="sxs-lookup"><span data-stu-id="30fa6-201">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="30fa6-202">Všechna data chráněná pomocí aktualizační kanál, který klíč můžete už nebude možné dešifrovat.</span><span class="sxs-lookup"><span data-stu-id="30fa6-202">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="30fa6-203">To může zahrnovat [CSRF tokeny](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [soubory cookie v ASP.NET Core MVC TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="30fa6-203">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="30fa6-204">Konfigurace ochrany dat zachovat a aktualizační kanál, který klíč šifrování, najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="30fa6-204">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a><span data-ttu-id="30fa6-205">Zabezpečení aplikace</span><span class="sxs-lookup"><span data-stu-id="30fa6-205">Secure the app</span></span>

### <a name="configure-firewall"></a><span data-ttu-id="30fa6-206">Konfigurace brány firewall</span><span class="sxs-lookup"><span data-stu-id="30fa6-206">Configure firewall</span></span>

<span data-ttu-id="30fa6-207">*Firewalld* je dynamické démon ke správě brány firewall s podporou zón sítě.</span><span class="sxs-lookup"><span data-stu-id="30fa6-207">*Firewalld* is a dynamic daemon to manage the firewall with support for network zones.</span></span> <span data-ttu-id="30fa6-208">Porty a filtrování paketů můžete dál spravovat iptables.</span><span class="sxs-lookup"><span data-stu-id="30fa6-208">Ports and packet filtering can still be managed by iptables.</span></span> <span data-ttu-id="30fa6-209">*Firewalld* by měl být ve výchozím nastavení nainstalovaná.</span><span class="sxs-lookup"><span data-stu-id="30fa6-209">*Firewalld* should be installed by default.</span></span> <span data-ttu-id="30fa6-210">`yum` slouží k instalaci balíčku nebo ověřte, že je nainstalovaná.</span><span class="sxs-lookup"><span data-stu-id="30fa6-210">`yum` can be used to install the package or verify it's installed.</span></span>

```bash
sudo yum install firewalld -y
```

<span data-ttu-id="30fa6-211">Použití `firewalld` otevřít pouze porty potřebné pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="30fa6-211">Use `firewalld` to open only the ports needed for the app.</span></span> <span data-ttu-id="30fa6-212">V takovém případě je to port 80 a 443 jsou používány.</span><span class="sxs-lookup"><span data-stu-id="30fa6-212">In this case, port 80 and 443 are used.</span></span> <span data-ttu-id="30fa6-213">Následující příkazy trvale nastavte porty 80 a 443. Chcete-li otevřít:</span><span class="sxs-lookup"><span data-stu-id="30fa6-213">The following commands permanently set ports 80 and 443 to open:</span></span>

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

<span data-ttu-id="30fa6-214">Znovu načte nastavení brány firewall.</span><span class="sxs-lookup"><span data-stu-id="30fa6-214">Reload the firewall settings.</span></span> <span data-ttu-id="30fa6-215">Zkontrolujte dostupné služby a porty ve výchozí zóně.</span><span class="sxs-lookup"><span data-stu-id="30fa6-215">Check the available services and ports in the default zone.</span></span> <span data-ttu-id="30fa6-216">Možnosti jsou k dispozici zkontrolováním `firewall-cmd -h`.</span><span class="sxs-lookup"><span data-stu-id="30fa6-216">Options are available by inspecting `firewall-cmd -h`.</span></span>

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

### <a name="https-configuration"></a><span data-ttu-id="30fa6-217">Konfigurace protokolu HTTPS</span><span class="sxs-lookup"><span data-stu-id="30fa6-217">HTTPS configuration</span></span>

<span data-ttu-id="30fa6-218">Chcete-li nakonfigurovat i Apache pro protokol HTTPS, *mod_ssl* modul se používá.</span><span class="sxs-lookup"><span data-stu-id="30fa6-218">To configure Apache for HTTPS, the *mod_ssl* module is used.</span></span> <span data-ttu-id="30fa6-219">Když *httpd* modul byl nainstalován, *mod_ssl* také nainstalován modul.</span><span class="sxs-lookup"><span data-stu-id="30fa6-219">When the *httpd* module was installed, the *mod_ssl* module was also installed.</span></span> <span data-ttu-id="30fa6-220">Pokud nebyla nainstalována, použijte `yum` přidejte do konfigurace.</span><span class="sxs-lookup"><span data-stu-id="30fa6-220">If it wasn't installed, use `yum` to add it to the configuration.</span></span>

```bash
sudo yum install mod_ssl
```

<span data-ttu-id="30fa6-221">Vynucení protokolu HTTPS, nainstalujte `mod_rewrite` modulu, který chcete-li povolit přepisování adres URL:</span><span class="sxs-lookup"><span data-stu-id="30fa6-221">To enforce HTTPS, install the `mod_rewrite` module to enable URL rewriting:</span></span>

```bash
sudo yum install mod_rewrite
```

<span data-ttu-id="30fa6-222">Upravit *helloapp.conf* soubor povolit přepisování adres URL a zabezpečenou komunikaci na portu 443:</span><span class="sxs-lookup"><span data-stu-id="30fa6-222">Modify the *helloapp.conf* file to enable URL rewriting and secure communication on port 443:</span></span>

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
> <span data-ttu-id="30fa6-223">Tento příklad používá místně vygeneruje certifikát.</span><span class="sxs-lookup"><span data-stu-id="30fa6-223">This example is using a locally-generated certificate.</span></span> <span data-ttu-id="30fa6-224">**SSLCertificateFile** by měl být soubor primárního certifikátu pro název domény.</span><span class="sxs-lookup"><span data-stu-id="30fa6-224">**SSLCertificateFile** should be the primary certificate file for the domain name.</span></span> <span data-ttu-id="30fa6-225">**SSLCertificateKeyFile** by měl být soubor s klíčem vygenerován při vytvoření žádosti o podepsání certifikátu.</span><span class="sxs-lookup"><span data-stu-id="30fa6-225">**SSLCertificateKeyFile** should be the key file generated when CSR is created.</span></span> <span data-ttu-id="30fa6-226">**SSLCertificateChainFile** by měl být soubor zprostředkující certifikát (pokud existuje), který byl zadán certifikační autoritou.</span><span class="sxs-lookup"><span data-stu-id="30fa6-226">**SSLCertificateChainFile** should be the intermediate certificate file (if any) that was supplied by the certificate authority.</span></span>

<span data-ttu-id="30fa6-227">Uložte soubor a otestujte konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="30fa6-227">Save the file and test the configuration:</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="30fa6-228">Restartujte Apache:</span><span class="sxs-lookup"><span data-stu-id="30fa6-228">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a><span data-ttu-id="30fa6-229">Další návrhy Apache</span><span class="sxs-lookup"><span data-stu-id="30fa6-229">Additional Apache suggestions</span></span>

### <a name="additional-headers"></a><span data-ttu-id="30fa6-230">Dodatečné hlavičky</span><span class="sxs-lookup"><span data-stu-id="30fa6-230">Additional headers</span></span>

<span data-ttu-id="30fa6-231">Myslet při zabezpečování před škodlivými útoky, existuje pár hlavičky, které se musí buď být přidá nebo upraví.</span><span class="sxs-lookup"><span data-stu-id="30fa6-231">In order to secure against malicious attacks, there are a few headers that should either be modified or added.</span></span> <span data-ttu-id="30fa6-232">Ujistěte se, `mod_headers` je nainstalován modul:</span><span class="sxs-lookup"><span data-stu-id="30fa6-232">Ensure that the `mod_headers` module is installed:</span></span>

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a><span data-ttu-id="30fa6-233">Zabezpečení před útoky útoků typu clickjacking Apache</span><span class="sxs-lookup"><span data-stu-id="30fa6-233">Secure Apache from clickjacking attacks</span></span>

<span data-ttu-id="30fa6-234">[Útoků typu Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), označované také jako *uživatelského rozhraní zjednávání nápravy útoku*, je napadením se zlými úmysly, kde návštěvníků webu je nalákaní, odkaz nebo tlačítko na stránce jiné než aktuálně navštívený.</span><span class="sxs-lookup"><span data-stu-id="30fa6-234">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="30fa6-235">Použití `X-FRAME-OPTIONS` k zabezpečení webu.</span><span class="sxs-lookup"><span data-stu-id="30fa6-235">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="30fa6-236">Ke zmírnění útoků typu clickjacking útoků:</span><span class="sxs-lookup"><span data-stu-id="30fa6-236">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="30fa6-237">Upravit *httpd.conf* souboru:</span><span class="sxs-lookup"><span data-stu-id="30fa6-237">Edit the *httpd.conf* file:</span></span>

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   <span data-ttu-id="30fa6-238">Přidejte řádek `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span><span class="sxs-lookup"><span data-stu-id="30fa6-238">Add the line `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span></span>
1. <span data-ttu-id="30fa6-239">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="30fa6-239">Save the file.</span></span>
1. <span data-ttu-id="30fa6-240">Restartujte Apache.</span><span class="sxs-lookup"><span data-stu-id="30fa6-240">Restart Apache.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="30fa6-241">Typ MIME pro analýzu sítě</span><span class="sxs-lookup"><span data-stu-id="30fa6-241">MIME-type sniffing</span></span>

<span data-ttu-id="30fa6-242">`X-Content-Type-Options` Záhlaví brání aplikaci Internet Explorer z *MIME pro analýzu sítě* (určení souboru `Content-Type` z obsahu souboru).</span><span class="sxs-lookup"><span data-stu-id="30fa6-242">The `X-Content-Type-Options` header prevents Internet Explorer from *MIME-sniffing* (determining a file's `Content-Type` from the file's content).</span></span> <span data-ttu-id="30fa6-243">Pokud server nastaví `Content-Type` záhlaví `text/html` s `nosniff` sadu možností, Internet Explorer vykreslí obsah jako `text/html` bez ohledu na jeho obsah.</span><span class="sxs-lookup"><span data-stu-id="30fa6-243">If the server sets the `Content-Type` header to `text/html` with the `nosniff` option set, Internet Explorer renders the content as `text/html` regardless of the file's content.</span></span>

<span data-ttu-id="30fa6-244">Upravit *httpd.conf* souboru:</span><span class="sxs-lookup"><span data-stu-id="30fa6-244">Edit the *httpd.conf* file:</span></span>

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

<span data-ttu-id="30fa6-245">Přidejte řádek `Header set X-Content-Type-Options "nosniff"`.</span><span class="sxs-lookup"><span data-stu-id="30fa6-245">Add the line `Header set X-Content-Type-Options "nosniff"`.</span></span> <span data-ttu-id="30fa6-246">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="30fa6-246">Save the file.</span></span> <span data-ttu-id="30fa6-247">Restartujte Apache.</span><span class="sxs-lookup"><span data-stu-id="30fa6-247">Restart Apache.</span></span>

### <a name="load-balancing"></a><span data-ttu-id="30fa6-248">Vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="30fa6-248">Load Balancing</span></span>

<span data-ttu-id="30fa6-249">Tento příklad ukazuje, jak nainstalovat a nakonfigurovat Apache na CentOS 7 a Kestrel ve stejném počítači instance.</span><span class="sxs-lookup"><span data-stu-id="30fa6-249">This example shows how to setup and configure Apache on CentOS 7 and Kestrel on the same instance machine.</span></span> <span data-ttu-id="30fa6-250">Aby bylo možné, není nutné jediný bod selhání; pomocí *mod_proxy_balancer* a úpravy **VirtualHost** by umožňoval správu více instancí služby web apps za proxy serverem Apache.</span><span class="sxs-lookup"><span data-stu-id="30fa6-250">In order to not have a single point of failure; using *mod_proxy_balancer* and modifying the **VirtualHost** would allow for managing multiple instances of the web apps behind the Apache proxy server.</span></span>

```bash
sudo yum install mod_proxy_balancer
```

<span data-ttu-id="30fa6-251">V konfiguračním souboru je znázorněno níže, další instanci `helloapp` je nastaven na spuštění port 5001.</span><span class="sxs-lookup"><span data-stu-id="30fa6-251">In the configuration file shown below, an additional instance of the `helloapp` is set up to run on port 5001.</span></span> <span data-ttu-id="30fa6-252">*Proxy* části je nastaven s konfigurací nástroje pro vyrovnávání se dvěma členy pro vyrovnávání zatížení *byrequests*.</span><span class="sxs-lookup"><span data-stu-id="30fa6-252">The *Proxy* section is set with a balancer configuration with two members to load balance *byrequests*.</span></span>

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

### <a name="rate-limits"></a><span data-ttu-id="30fa6-253">Omezení přenosové rychlosti</span><span class="sxs-lookup"><span data-stu-id="30fa6-253">Rate Limits</span></span>

<span data-ttu-id="30fa6-254">Pomocí *mod_ratelimit*, které je součástí *httpd* modulu, šířky pásma klientů může být omezená:</span><span class="sxs-lookup"><span data-stu-id="30fa6-254">Using *mod_ratelimit*, which is included in the *httpd* module, the bandwidth of clients can be limited:</span></span>

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

<span data-ttu-id="30fa6-255">Příklad souboru omezuje šířku pásma jako 600 KB/s v části kořenový adresář:</span><span class="sxs-lookup"><span data-stu-id="30fa6-255">The example file limits bandwidth as 600 KB/sec under the root location:</span></span>

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a><span data-ttu-id="30fa6-256">Pole hlavičky dlouhou žádost</span><span class="sxs-lookup"><span data-stu-id="30fa6-256">Long request header fields</span></span>

<span data-ttu-id="30fa6-257">Pokud aplikace vyžaduje pole hlavičky požadavku delší než povolená ve výchozím nastavení proxy serveru, nastavení (obvykle 8,190 bajtů), upravte hodnotu [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) směrnice.</span><span class="sxs-lookup"><span data-stu-id="30fa6-257">If the app requires request header fields longer than permitted by the proxy server's default setting (typically 8,190 bytes), adjust the value of the [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) directive.</span></span> <span data-ttu-id="30fa6-258">Použít hodnotu závisí na scénáři.</span><span class="sxs-lookup"><span data-stu-id="30fa6-258">The value to apply is scenario-dependent.</span></span> <span data-ttu-id="30fa6-259">Další informace najdete v dokumentaci k serveru.</span><span class="sxs-lookup"><span data-stu-id="30fa6-259">For more information, see your server's documentation.</span></span>

> [!WARNING]
> <span data-ttu-id="30fa6-260">Není výchozí hodnotu zvýšit `LimitRequestFieldSize` není-li nezbytné.</span><span class="sxs-lookup"><span data-stu-id="30fa6-260">Don't increase the default value of `LimitRequestFieldSize` unless necessary.</span></span> <span data-ttu-id="30fa6-261">Zvýšení hodnoty zvyšují riziko přetečení vyrovnávací paměti (přetečení) a uživateli se zlými úmysly útoků s cílem odepření služby (DoS).</span><span class="sxs-lookup"><span data-stu-id="30fa6-261">Increasing the value increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="30fa6-262">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="30fa6-262">Additional resources</span></span>

* [<span data-ttu-id="30fa6-263">Požadavky pro .NET Core v Linuxu</span><span class="sxs-lookup"><span data-stu-id="30fa6-263">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
