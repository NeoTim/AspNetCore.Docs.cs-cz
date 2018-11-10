---
title: Hostitele ASP.NET Core ve Windows se službou IIS
author: guardrex
description: Zjistěte, jak hostovat aplikace ASP.NET Core na Windows serveru Internetové informační služby (IIS).
ms.author: riande
ms.custom: mvc
ms.date: 11/05/2018
uid: host-and-deploy/iis/index
ms.openlocfilehash: aa821b4923d2a5495ab3e9973142e1e97371ec91
ms.sourcegitcommit: c3fa5aded0bf76a7414047d50b8a2311d27ee1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51276167"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="3bbce-103">Hostitele ASP.NET Core ve Windows se službou IIS</span><span class="sxs-lookup"><span data-stu-id="3bbce-103">Host ASP.NET Core on Windows with IIS</span></span>

<span data-ttu-id="3bbce-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3bbce-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[<span data-ttu-id="3bbce-105">Instalace .NET Core, který je hostitelem svazku</span><span class="sxs-lookup"><span data-stu-id="3bbce-105">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="3bbce-106">Podporované operační systémy</span><span class="sxs-lookup"><span data-stu-id="3bbce-106">Supported operating systems</span></span>

<span data-ttu-id="3bbce-107">Následující operační systémy se podporují:</span><span class="sxs-lookup"><span data-stu-id="3bbce-107">The following operating systems are supported:</span></span>

* <span data-ttu-id="3bbce-108">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="3bbce-108">Windows 7 or later</span></span>
* <span data-ttu-id="3bbce-109">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="3bbce-109">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="3bbce-110">[HTTP.sys server](xref:fundamentals/servers/httpsys) (dříve se označovaly jako [WebListener](xref:fundamentals/servers/weblistener)) nebude fungovat v konfigurace reverzního proxy serveru se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="3bbce-110">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called [WebListener](xref:fundamentals/servers/weblistener)) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="3bbce-111">Použití [Kestrel server](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="3bbce-111">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="3bbce-112">Informace o hostování v Azure najdete v tématu <xref:host-and-deploy/azure-apps/index>.</span><span class="sxs-lookup"><span data-stu-id="3bbce-112">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="3bbce-113">Konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="3bbce-113">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="3bbce-114">Povolit IISIntegration součásti</span><span class="sxs-lookup"><span data-stu-id="3bbce-114">Enable the IISIntegration components</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="3bbce-115">Typické *Program.cs* volání <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> zahájíte nastavení hostitele:</span><span class="sxs-lookup"><span data-stu-id="3bbce-115">A typical *Program.cs* calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to begin setting up a host:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="3bbce-116">Typické *Program.cs* volání <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> zahájíte nastavení hostitele:</span><span class="sxs-lookup"><span data-stu-id="3bbce-116">A typical *Program.cs* calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to begin setting up a host:</span></span>

```csharp
public static IWebHost BuildWebHost(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="3bbce-117">**Model hostingu v procesu**</span><span class="sxs-lookup"><span data-stu-id="3bbce-117">**In-process hosting model**</span></span>

<span data-ttu-id="3bbce-118">`CreateDefaultBuilder` volání `UseIIS` metoda pro spuštění [CoreCLR](/dotnet/standard/glossary#coreclr) a hostování aplikace v rámci pracovní proces služby IIS (*w3wp.exe* nebo *iisexpress.exe*).</span><span class="sxs-lookup"><span data-stu-id="3bbce-118">`CreateDefaultBuilder` calls the `UseIIS` method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="3bbce-119">Testy výkonu zjistí, že hostování .NET Core aplikace v procesu poskytuje výrazně vyšší propustnost žádostí, které jsou ve srovnání s žádostí aplikace na více instancí procesu a využívání proxy serverů k hostování [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="3bbce-119">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="3bbce-120">**Model hostingu mimo proces**</span><span class="sxs-lookup"><span data-stu-id="3bbce-120">**Out-of-process hosting model**</span></span>

<span data-ttu-id="3bbce-121">Pro hostování mimo proces se službou IIS, `CreateDefaultBuilder` nakonfiguruje [Kestrel](xref:fundamentals/servers/kestrel) jako integrace webového serveru a umožňuje služby IIS tím, že nakonfigurujete základní cesty a port pro [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="3bbce-121">For out-of-process hosting with IIS, `CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) as the web server and enables IIS integration by configuring the base path and port for the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module).</span></span>

<span data-ttu-id="3bbce-122">Modul ASP.NET Core generuje dynamický port přiřadit k procesu back-endu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-122">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="3bbce-123">`CreateDefaultBuilder` volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> metody.</span><span class="sxs-lookup"><span data-stu-id="3bbce-123">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> method.</span></span> <span data-ttu-id="3bbce-124">`UseIISIntegration` Nakonfiguruje Kestrel k naslouchání na dynamický port na IP adresu místního hostitele (`127.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="3bbce-124">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="3bbce-125">Pokud dynamický port je 1234, Kestrel naslouchá na `127.0.0.1:1234`.</span><span class="sxs-lookup"><span data-stu-id="3bbce-125">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="3bbce-126">Tato konfigurace nahrazuje jiné konfigurace adresy URL poskytnuté:</span><span class="sxs-lookup"><span data-stu-id="3bbce-126">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="3bbce-127">Rozhraní API od kestrel naslouchání</span><span class="sxs-lookup"><span data-stu-id="3bbce-127">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="3bbce-128">[Konfigurace](xref:fundamentals/configuration/index) (nebo [možnost příkazového řádku--adresy URL](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="3bbce-128">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="3bbce-129">Volání `UseUrls` nebo jeho Kestrel `Listen` rozhraní API nejsou povinné, když pomocí modulu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-129">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="3bbce-130">Pokud `UseUrls` nebo `Listen` nazývá Kestrel naslouchá na porty určené pouze při spuštěné aplikaci bez služby IIS.</span><span class="sxs-lookup"><span data-stu-id="3bbce-130">If `UseUrls` or `Listen` is called, Kestrel listens on the ports specified only when running the app without IIS.</span></span>

<span data-ttu-id="3bbce-131">Další informace o modelech hostování a mimo proces, naleznete v tématu [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) a [odkaz Konfigurace modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="3bbce-131">For more information on the in-process and out-of-process hosting models, see [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) and [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="3bbce-132">`CreateDefaultBuilder` nakonfiguruje [Kestrel](xref:fundamentals/servers/kestrel) jako integrace webového serveru a umožňuje služby IIS tím, že nakonfigurujete základní cesty a port pro [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="3bbce-132">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) as the web server and enables IIS integration by configuring the base path and port for the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module).</span></span>

<span data-ttu-id="3bbce-133">Modul ASP.NET Core generuje dynamický port přiřadit k procesu back-endu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-133">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="3bbce-134">`CreateDefaultBuilder` volání [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) metody.</span><span class="sxs-lookup"><span data-stu-id="3bbce-134">`CreateDefaultBuilder` calls the [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) method.</span></span> <span data-ttu-id="3bbce-135">`UseIISIntegration` Nakonfiguruje Kestrel k naslouchání na dynamický port na IP adresu místního hostitele (`127.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="3bbce-135">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="3bbce-136">Pokud dynamický port je 1234, Kestrel naslouchá na `127.0.0.1:1234`.</span><span class="sxs-lookup"><span data-stu-id="3bbce-136">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="3bbce-137">Tato konfigurace nahrazuje jiné konfigurace adresy URL poskytnuté:</span><span class="sxs-lookup"><span data-stu-id="3bbce-137">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="3bbce-138">Rozhraní API od kestrel naslouchání</span><span class="sxs-lookup"><span data-stu-id="3bbce-138">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="3bbce-139">[Konfigurace](xref:fundamentals/configuration/index) (nebo [možnost příkazového řádku--adresy URL](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="3bbce-139">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="3bbce-140">Volání `UseUrls` nebo jeho Kestrel `Listen` rozhraní API nejsou povinné, když pomocí modulu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-140">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="3bbce-141">Pokud `UseUrls` nebo `Listen` nazývá Kestrel naslouchá na portu zadat jenom při spuštěné aplikaci bez služby IIS.</span><span class="sxs-lookup"><span data-stu-id="3bbce-141">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="3bbce-142">`CreateDefaultBuilder` nakonfiguruje [Kestrel](xref:fundamentals/servers/kestrel) jako integrace webového serveru a umožňuje služby IIS tím, že nakonfigurujete základní cesty a port pro [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="3bbce-142">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) as the web server and enables IIS integration by configuring the base path and port for the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module).</span></span>

<span data-ttu-id="3bbce-143">Modul ASP.NET Core generuje dynamický port přiřadit k procesu back-endu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-143">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="3bbce-144">`CreateDefaultBuilder` volání [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) metody.</span><span class="sxs-lookup"><span data-stu-id="3bbce-144">`CreateDefaultBuilder` calls the [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) method.</span></span> <span data-ttu-id="3bbce-145">`UseIISIntegration` Nakonfiguruje Kestrel k naslouchání na dynamický port na IP adresu místního hostitele (`localhost`).</span><span class="sxs-lookup"><span data-stu-id="3bbce-145">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`localhost`).</span></span> <span data-ttu-id="3bbce-146">Pokud dynamický port je 1234, Kestrel naslouchá na `localhost:1234`.</span><span class="sxs-lookup"><span data-stu-id="3bbce-146">If the dynamic port is 1234, Kestrel listens at `localhost:1234`.</span></span> <span data-ttu-id="3bbce-147">Tato konfigurace nahrazuje jiné konfigurace adresy URL poskytnuté:</span><span class="sxs-lookup"><span data-stu-id="3bbce-147">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="3bbce-148">Rozhraní API od kestrel naslouchání</span><span class="sxs-lookup"><span data-stu-id="3bbce-148">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="3bbce-149">[Konfigurace](xref:fundamentals/configuration/index) (nebo [možnost příkazového řádku--adresy URL](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="3bbce-149">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="3bbce-150">Volání `UseUrls` nebo jeho Kestrel `Listen` rozhraní API nejsou povinné, když pomocí modulu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-150">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="3bbce-151">Pokud `UseUrls` nebo `Listen` nazývá Kestrel naslouchá na portu zadat jenom při spuštěné aplikaci bez služby IIS.</span><span class="sxs-lookup"><span data-stu-id="3bbce-151">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="3bbce-152">Zahrnout závislost [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/) balíčku v závislosti aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3bbce-152">Include a dependency on the [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/) package in the app's dependencies.</span></span> <span data-ttu-id="3bbce-153">Používat middleware pro integraci služby IIS tak, že přidáte [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) metodu rozšíření k [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder):</span><span class="sxs-lookup"><span data-stu-id="3bbce-153">Use IIS Integration middleware by adding the [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) extension method to [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder):</span></span>

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .UseIISIntegration()
    ...
```

<span data-ttu-id="3bbce-154">Obě [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) a [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) jsou povinné.</span><span class="sxs-lookup"><span data-stu-id="3bbce-154">Both [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) and [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) are required.</span></span> <span data-ttu-id="3bbce-155">Volání kódu `UseIISIntegration` nemá vliv na přenositelnost kódu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-155">Code calling `UseIISIntegration` doesn't affect code portability.</span></span> <span data-ttu-id="3bbce-156">Pokud aplikace není spuštěna za služby IIS (například spuštění aplikace přímo na Kestrel), `UseIISIntegration` nepracuje.</span><span class="sxs-lookup"><span data-stu-id="3bbce-156">If the app isn't run behind IIS (for example, the app is run directly on Kestrel), `UseIISIntegration` doesn't operate.</span></span>

<span data-ttu-id="3bbce-157">Modul ASP.NET Core generuje dynamický port přiřadit k procesu back-endu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-157">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="3bbce-158">`UseIISIntegration` Nakonfiguruje Kestrel k naslouchání na dynamický port na IP adresu místního hostitele (`localhost`).</span><span class="sxs-lookup"><span data-stu-id="3bbce-158">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`localhost`).</span></span> <span data-ttu-id="3bbce-159">Pokud dynamický port je 1234, Kestrel naslouchá na `localhost:1234`.</span><span class="sxs-lookup"><span data-stu-id="3bbce-159">If the dynamic port is 1234, Kestrel listens at `localhost:1234`.</span></span> <span data-ttu-id="3bbce-160">Tato konfigurace nahrazuje jiné konfigurace adresy URL poskytnuté:</span><span class="sxs-lookup"><span data-stu-id="3bbce-160">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* <span data-ttu-id="3bbce-161">[Konfigurace](xref:fundamentals/configuration/index) (nebo [možnost příkazového řádku--adresy URL](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="3bbce-161">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="3bbce-162">Volání `UseUrls` není povinné, při použití modulu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-162">A call to `UseUrls` isn't required when using the module.</span></span> <span data-ttu-id="3bbce-163">Pokud `UseUrls` nazývá Kestrel naslouchá na portu zadat jenom při spuštěné aplikaci bez služby IIS.</span><span class="sxs-lookup"><span data-stu-id="3bbce-163">If `UseUrls` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="3bbce-164">Pokud `UseUrls` je volána v aplikaci ASP.NET Core 1.0, volání **před** volání `UseIISIntegration` tak, aby port nakonfigurovaný modul není přepsán.</span><span class="sxs-lookup"><span data-stu-id="3bbce-164">If `UseUrls` is called in an ASP.NET Core 1.0 app, call it **before** calling `UseIISIntegration` so that the module-configured port isn't overwritten.</span></span> <span data-ttu-id="3bbce-165">Toto pořadí volání není požadován spolu s ASP.NET Core 1.1, protože modul nastavení přepíše `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="3bbce-165">This calling order isn't required with ASP.NET Core 1.1 because the module setting overrides `UseUrls`.</span></span>

::: moniker-end

<span data-ttu-id="3bbce-166">Další informace o hostování najdete v tématu [hostitele v ASP.NET Core](xref:fundamentals/host/index).</span><span class="sxs-lookup"><span data-stu-id="3bbce-166">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/host/index).</span></span>

### <a name="iis-options"></a><span data-ttu-id="3bbce-167">Možnosti služby IIS</span><span class="sxs-lookup"><span data-stu-id="3bbce-167">IIS options</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="3bbce-168">**Model hostingu v procesu**</span><span class="sxs-lookup"><span data-stu-id="3bbce-168">**In-process hosting model**</span></span>

<span data-ttu-id="3bbce-169">Konfigurace možností serveru služby IIS, zahrnují konfiguraci služby pro [IISServerOptions](/dotnet/api/microsoft.aspnetcore.builder.iisserveroptions) v [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices).</span><span class="sxs-lookup"><span data-stu-id="3bbce-169">To configure IIS Server options, include a service configuration for [IISServerOptions](/dotnet/api/microsoft.aspnetcore.builder.iisserveroptions) in [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices).</span></span> <span data-ttu-id="3bbce-170">Následující příklad zakazuje AutomaticAuthentication:</span><span class="sxs-lookup"><span data-stu-id="3bbce-170">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="3bbce-171">Možnost</span><span class="sxs-lookup"><span data-stu-id="3bbce-171">Option</span></span>                         | <span data-ttu-id="3bbce-172">Výchozí</span><span class="sxs-lookup"><span data-stu-id="3bbce-172">Default</span></span> | <span data-ttu-id="3bbce-173">Nastavení</span><span class="sxs-lookup"><span data-stu-id="3bbce-173">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="3bbce-174">Pokud `true`, nastaví Server služby IIS `HttpContext.User` ověřována [ověřování Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="3bbce-174">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="3bbce-175">Pokud `false`, server pouze poskytuje identitu `HttpContext.User` a reaguje na problémy při explicitním požadavku `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="3bbce-175">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="3bbce-176">Musí být povoleno ověřování Windows ve službě IIS pro `AutomaticAuthentication` na funkci.</span><span class="sxs-lookup"><span data-stu-id="3bbce-176">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="3bbce-177">Další informace najdete v tématu [ověřování Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="3bbce-177">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="3bbce-178">Nastaví zobrazovaný název, který se uživatelům na přihlašovací stránky zobrazí.</span><span class="sxs-lookup"><span data-stu-id="3bbce-178">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="3bbce-179">**Model hostingu mimo proces**</span><span class="sxs-lookup"><span data-stu-id="3bbce-179">**Out-of-process hosting model**</span></span>

::: moniker-end

<span data-ttu-id="3bbce-180">Ke konfiguraci možností služby IIS, zahrnují konfiguraci služby pro [IISOptions](/dotnet/api/microsoft.aspnetcore.builder.iisoptions) v [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices).</span><span class="sxs-lookup"><span data-stu-id="3bbce-180">To configure IIS options, include a service configuration for [IISOptions](/dotnet/api/microsoft.aspnetcore.builder.iisoptions) in [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices).</span></span> <span data-ttu-id="3bbce-181">Následující příklad brání aplikaci v naplnění `HttpContext.Connection.ClientCertificate`:</span><span class="sxs-lookup"><span data-stu-id="3bbce-181">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="3bbce-182">Možnost</span><span class="sxs-lookup"><span data-stu-id="3bbce-182">Option</span></span>                         | <span data-ttu-id="3bbce-183">Výchozí</span><span class="sxs-lookup"><span data-stu-id="3bbce-183">Default</span></span> | <span data-ttu-id="3bbce-184">Nastavení</span><span class="sxs-lookup"><span data-stu-id="3bbce-184">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="3bbce-185">Pokud `true`, nastaví Middleware pro integraci služby IIS `HttpContext.User` ověřována [ověřování Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="3bbce-185">If `true`, IIS Integration Middleware sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="3bbce-186">Pokud `false`, middleware pouze poskytuje identitu `HttpContext.User` a reaguje na problémy při explicitním požadavku `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="3bbce-186">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="3bbce-187">Musí být povoleno ověřování Windows ve službě IIS pro `AutomaticAuthentication` na funkci.</span><span class="sxs-lookup"><span data-stu-id="3bbce-187">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="3bbce-188">Další informace najdete v tématu [ověřování Windows](xref:security/authentication/windowsauth) tématu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-188">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="3bbce-189">Nastaví zobrazovaný název, který se uživatelům na přihlašovací stránky zobrazí.</span><span class="sxs-lookup"><span data-stu-id="3bbce-189">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="3bbce-190">Pokud `true` a `MS-ASPNETCORE-CLIENTCERT` hlavička požadavku je k dispozici, `HttpContext.Connection.ClientCertificate` naplnění.</span><span class="sxs-lookup"><span data-stu-id="3bbce-190">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="3bbce-191">Proxy server a scénáře pro nástroj pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="3bbce-191">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="3bbce-192">Middleware pro integraci služby IIS, který nakonfiguruje předané Middleware záhlaví a že modul ASP.NET Core jsou konfigurovány pro předávání schématu (HTTP/HTTPS) a Vzdálená IP adresa původu žádosti.</span><span class="sxs-lookup"><span data-stu-id="3bbce-192">The IIS Integration Middleware, which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="3bbce-193">Další konfigurace může být nezbytný pro aplikací hostovaných za službou další proxy servery a nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="3bbce-193">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="3bbce-194">Další informace najdete v tématu [konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="3bbce-194">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="3bbce-195">soubor Web.config</span><span class="sxs-lookup"><span data-stu-id="3bbce-195">web.config file</span></span>

<span data-ttu-id="3bbce-196">*Web.config* soubor nastaví [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="3bbce-196">The *web.config* file configures the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module).</span></span> <span data-ttu-id="3bbce-197">Vytváření, transformace a publikování *web.config* soubor se zpracovává souborem cíl nástroje MSBuild (`_TransformWebConfig`) při publikování projektu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-197">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="3bbce-198">Tento cíl je k dispozici v cílech Web SDK (`Microsoft.NET.Sdk.Web`).</span><span class="sxs-lookup"><span data-stu-id="3bbce-198">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="3bbce-199">V horní části souboru projektu je sada SDK:</span><span class="sxs-lookup"><span data-stu-id="3bbce-199">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="3bbce-200">Pokud *web.config* soubor není k dispozici v projektu, soubor, vytvoří se správnými *processPath* a *argumenty* ke konfiguraci [ASP.NET Core Modul](xref:fundamentals/servers/aspnet-core-module) a přesunout do [publikovaný výstup](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="3bbce-200">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="3bbce-201">Pokud *web.config* soubor je k dispozici v projektu, soubor se transformuje se správnými *processPath* a *argumenty* nakonfigurovat modul ASP.NET Core a přesunout do publikovaný výstup.</span><span class="sxs-lookup"><span data-stu-id="3bbce-201">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="3bbce-202">Transformace nezmění nastavení konfigurace služby IIS v souboru.</span><span class="sxs-lookup"><span data-stu-id="3bbce-202">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="3bbce-203">*Web.config* soubor může poskytovat další nastavení konfigurace služby IIS, která řídí aktivní moduly služby IIS.</span><span class="sxs-lookup"><span data-stu-id="3bbce-203">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="3bbce-204">Informace o službě IIS modulů, které dokáže zpracovávat požadavky s aplikací ASP.NET Core, najdete v článku [moduly IIS](xref:host-and-deploy/iis/modules) tématu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-204">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="3bbce-205">Zabránit transformace na sadu Web SDK *web.config* souboru, použijte  **\<IsTransformWebConfigDisabled >** vlastnost v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="3bbce-205">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="3bbce-206">Při zakazování Web SDK z transformaci souboru *processPath* a *argumenty* by měl být ručně nastavit vývojářem.</span><span class="sxs-lookup"><span data-stu-id="3bbce-206">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="3bbce-207">Další informace najdete v tématu [odkaz Konfigurace modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="3bbce-207">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module).</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="3bbce-208">Umístění souboru Web.config</span><span class="sxs-lookup"><span data-stu-id="3bbce-208">web.config file location</span></span>

<span data-ttu-id="3bbce-209">Pokud chcete nastavit [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) správně, *web.config* soubor musí být k dispozici v obsahu kořenové cestě (obvykle aplikace základní cesta) nasazené aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bbce-209">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the content root path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="3bbce-210">Jedná se o stejné umístění jako fyzická cesta webu služby IIS k dispozici.</span><span class="sxs-lookup"><span data-stu-id="3bbce-210">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="3bbce-211">*Web.config* soubor je vyžadován v kořenovém adresáři aplikace, aby se daly publikovat víc aplikací pomocí nasazení webu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-211">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="3bbce-212">Citlivé soubory existují na fyzická cesta aplikace, jako například  *\<sestavení >. runtimeconfig.json*,  *\<sestavení > .xml* (komentáře dokumentace XML) a  *\<sestavení >. deps.json*.</span><span class="sxs-lookup"><span data-stu-id="3bbce-212">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="3bbce-213">Když *web.config* soubor je k dispozici a lokality spustí běžným způsobem, služba IIS bariéru tyto citlivé soubory, pokud jste požádali.</span><span class="sxs-lookup"><span data-stu-id="3bbce-213">When the *web.config* file is present and and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="3bbce-214">Pokud *web.config* soubor chybí, nesprávně pojmenované, nebo nelze konfigurovat lokalitu pro normální spuštění, služba IIS může poskytovat citlivé soubory veřejně.</span><span class="sxs-lookup"><span data-stu-id="3bbce-214">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="3bbce-215">***Web.config* soubor musí být k dispozici v nasazení za všech okolností, správně s názvem a nakonfigurovat web pro normální spuštění nahoru. Nikdy odebrat *web.config* soubor z produkčního nasazení.**</span><span class="sxs-lookup"><span data-stu-id="3bbce-215">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="3bbce-216">Konfigurace služby IIS</span><span class="sxs-lookup"><span data-stu-id="3bbce-216">IIS configuration</span></span>

<span data-ttu-id="3bbce-217">**Operační systémy Windows Server**</span><span class="sxs-lookup"><span data-stu-id="3bbce-217">**Windows Server operating systems**</span></span>

<span data-ttu-id="3bbce-218">Povolit **webového serveru (IIS)** role serveru a vytvoření služby rolí.</span><span class="sxs-lookup"><span data-stu-id="3bbce-218">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="3bbce-219">Použití **přidat role a funkce** z průvodce **spravovat** nabídky nebo na odkaz v **správce serveru**.</span><span class="sxs-lookup"><span data-stu-id="3bbce-219">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="3bbce-220">Na **role serveru** krok, zaškrtněte políčko u **webového serveru (IIS)**.</span><span class="sxs-lookup"><span data-stu-id="3bbce-220">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![V kroku výběr serveru role je vybrána role webového serveru IIS.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="3bbce-222">Po **funkce** kroku **služeb rolí** krok načte pro webový Server (IIS).</span><span class="sxs-lookup"><span data-stu-id="3bbce-222">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="3bbce-223">Vyberte požadovaných služeb role služby IIS nebo přijměte výchozí nastavení role služby za předpokladu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-223">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Výchozí služby rolí jsou vybrané v kroku vybrat roli služby.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="3bbce-225">**Ověřování Windows (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="3bbce-225">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="3bbce-226">Pokud chcete povolit ověřování Windows, rozbalte následující uzly: **Webový Server** > **zabezpečení**.</span><span class="sxs-lookup"><span data-stu-id="3bbce-226">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="3bbce-227">Vyberte **ověřování Windows** funkce.</span><span class="sxs-lookup"><span data-stu-id="3bbce-227">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="3bbce-228">Další informace najdete v tématu [ověřování Windows \<windowsAuthentication >](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Windows nakonfigurovat ověřování](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="3bbce-228">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="3bbce-229">**Protokoly Websocket (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="3bbce-229">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="3bbce-230">Protokoly Websocket je podporována s ASP.NET Core 1.1 nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="3bbce-230">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="3bbce-231">Pokud chcete povolit protokoly Websocket, rozbalte následující uzly: **Webový Server** > **vývoj aplikací**.</span><span class="sxs-lookup"><span data-stu-id="3bbce-231">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="3bbce-232">Vyberte **protokol WebSocket** funkce.</span><span class="sxs-lookup"><span data-stu-id="3bbce-232">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="3bbce-233">Další informace najdete v tématu [objekty Websocket](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="3bbce-233">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="3bbce-234">Pokračujte **potvrzení** krok instalace role webového serveru a služby.</span><span class="sxs-lookup"><span data-stu-id="3bbce-234">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="3bbce-235">Po instalaci se nevyžaduje restartování serveru/IIS **webového serveru (IIS)** role.</span><span class="sxs-lookup"><span data-stu-id="3bbce-235">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="3bbce-236">**Operační systémy Windows**</span><span class="sxs-lookup"><span data-stu-id="3bbce-236">**Windows desktop operating systems**</span></span>

<span data-ttu-id="3bbce-237">Povolit **konzolu pro správu IIS** a **webové služby**.</span><span class="sxs-lookup"><span data-stu-id="3bbce-237">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="3bbce-238">Přejděte do **ovládací panely** > **programy** > **programy a funkce** > **zapnout Windows funkce na nebo vypnout** (levé straně obrazovky).</span><span class="sxs-lookup"><span data-stu-id="3bbce-238">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="3bbce-239">Otevřít **Internetová informační služba** uzlu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-239">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="3bbce-240">Otevřít **nástroje webové správy** uzlu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-240">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="3bbce-241">Zaškrtněte políčko u **konzolu pro správu IIS**.</span><span class="sxs-lookup"><span data-stu-id="3bbce-241">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="3bbce-242">Zaškrtněte políčko u **webové služby**.</span><span class="sxs-lookup"><span data-stu-id="3bbce-242">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="3bbce-243">Přijměte výchozí funkce pro **webové služby** nebo přizpůsobení funkcí služby IIS.</span><span class="sxs-lookup"><span data-stu-id="3bbce-243">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="3bbce-244">**Ověřování Windows (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="3bbce-244">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="3bbce-245">Pokud chcete povolit ověřování Windows, rozbalte následující uzly: **webové služby** > **zabezpečení**.</span><span class="sxs-lookup"><span data-stu-id="3bbce-245">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="3bbce-246">Vyberte **ověřování Windows** funkce.</span><span class="sxs-lookup"><span data-stu-id="3bbce-246">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="3bbce-247">Další informace najdete v tématu [ověřování Windows \<windowsAuthentication >](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Windows nakonfigurovat ověřování](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="3bbce-247">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="3bbce-248">**Protokoly Websocket (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="3bbce-248">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="3bbce-249">Protokoly Websocket je podporována s ASP.NET Core 1.1 nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="3bbce-249">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="3bbce-250">Pokud chcete povolit protokoly Websocket, rozbalte následující uzly: **webové služby** > **funkce pro vývoj aplikací**.</span><span class="sxs-lookup"><span data-stu-id="3bbce-250">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="3bbce-251">Vyberte **protokol WebSocket** funkce.</span><span class="sxs-lookup"><span data-stu-id="3bbce-251">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="3bbce-252">Další informace najdete v tématu [objekty Websocket](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="3bbce-252">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="3bbce-253">Pokud instalace služby IIS vyžaduje restartování, restartujte systém.</span><span class="sxs-lookup"><span data-stu-id="3bbce-253">If the IIS installation requires a restart, restart the system.</span></span>

![Konzola pro správu služby IIS a webové služby jsou vybrány v funkce Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="3bbce-255">Instalace .NET Core, který je hostitelem svazku</span><span class="sxs-lookup"><span data-stu-id="3bbce-255">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="3bbce-256">Nainstalujte *sady hostování rozhraní .NET Core* v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="3bbce-256">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="3bbce-257">Nainstaluje sady .NET Core Runtime, knihovny .NET Core a [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="3bbce-257">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module).</span></span> <span data-ttu-id="3bbce-258">Povolí modul ASP.NET Core aplikací ke spuštění za služby IIS.</span><span class="sxs-lookup"><span data-stu-id="3bbce-258">The module allows ASP.NET Core apps to run behind IIS.</span></span> <span data-ttu-id="3bbce-259">Pokud systém nemá připojení k Internetu, získejte a nainstalujte [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/download/details.aspx?id=53840) před instalací sady hostování rozhraní .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3bbce-259">If the system doesn't have an Internet connection, obtain and install the [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/download/details.aspx?id=53840) before installing the .NET Core Hosting Bundle.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="3bbce-260">Pokud před službou IIS instalovanou sadou hostování, je nutné opravit instalaci sady.</span><span class="sxs-lookup"><span data-stu-id="3bbce-260">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="3bbce-261">Spusťte instalační program sady hostování znovu po instalaci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="3bbce-261">Run the Hosting Bundle installer again after installing IIS.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="3bbce-262">Přímé stažení (aktuální verze)</span><span class="sxs-lookup"><span data-stu-id="3bbce-262">Direct download (current version)</span></span>

<span data-ttu-id="3bbce-263">Stažení instalačního programu pomocí následujícího odkazu:</span><span class="sxs-lookup"><span data-stu-id="3bbce-263">Download the installer using the following link:</span></span>

[<span data-ttu-id="3bbce-264">Aktuální instalační program sady hostování rozhraní .NET Core (s přímým přístupem ke stažení)</span><span class="sxs-lookup"><span data-stu-id="3bbce-264">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="3bbce-265">Starší verze instalačního programu</span><span class="sxs-lookup"><span data-stu-id="3bbce-265">Earlier versions of the installer</span></span>

<span data-ttu-id="3bbce-266">Chcete-li získat starší verzi instalačního programu:</span><span class="sxs-lookup"><span data-stu-id="3bbce-266">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="3bbce-267">Přejděte [.NET stáhnout archivy](https://www.microsoft.com/net/download/archives).</span><span class="sxs-lookup"><span data-stu-id="3bbce-267">Navigate to the [.NET download archives](https://www.microsoft.com/net/download/archives).</span></span>
1. <span data-ttu-id="3bbce-268">V části **.NET Core**, vyberte verzi .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3bbce-268">Under **.NET Core**, select the .NET Core version.</span></span>
1. <span data-ttu-id="3bbce-269">V **spuštění aplikací – prostředí Runtime** sloupce, vyhledejte řádek požadované verze modulu runtime .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3bbce-269">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="3bbce-270">Stáhněte si instalační program s použitím **Runtime & hostování svazek** odkaz.</span><span class="sxs-lookup"><span data-stu-id="3bbce-270">Download the installer using the **Runtime & Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="3bbce-271">Některé instalační programy obsahují verze, které bylo dosaženo jejich konci životnosti (konce řádku) a již nejsou podporovány společností Microsoft.</span><span class="sxs-lookup"><span data-stu-id="3bbce-271">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="3bbce-272">Další informace najdete v tématu [zásady podpory](https://www.microsoft.com/net/download/dotnet-core/2.0).</span><span class="sxs-lookup"><span data-stu-id="3bbce-272">For more information, see the [support policy](https://www.microsoft.com/net/download/dotnet-core/2.0).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="3bbce-273">Instalaci sady hostování</span><span class="sxs-lookup"><span data-stu-id="3bbce-273">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="3bbce-274">Spusťte instalační program na serveru.</span><span class="sxs-lookup"><span data-stu-id="3bbce-274">Run the installer on the server.</span></span> <span data-ttu-id="3bbce-275">Při spuštění instalačního programu z příkazového řádku správce jsou k dispozici následující přepínače:</span><span class="sxs-lookup"><span data-stu-id="3bbce-275">The following switches are available when running the installer from an administrator command prompt:</span></span>

   * <span data-ttu-id="3bbce-276">`OPT_NO_ANCM=1` &ndash; Instalace modulu jádra ASP.NET přeskočit.</span><span class="sxs-lookup"><span data-stu-id="3bbce-276">`OPT_NO_ANCM=1` &ndash; Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="3bbce-277">`OPT_NO_RUNTIME=1` &ndash; Instalace modulu runtime .NET Core přeskočit.</span><span class="sxs-lookup"><span data-stu-id="3bbce-277">`OPT_NO_RUNTIME=1` &ndash; Skip installing the .NET Core runtime.</span></span>
   * <span data-ttu-id="3bbce-278">`OPT_NO_SHAREDFX=1` &ndash; Přeskočit instalaci rozhraní ASP.NET sdílené (modul runtime technologie ASP.NET).</span><span class="sxs-lookup"><span data-stu-id="3bbce-278">`OPT_NO_SHAREDFX=1` &ndash; Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span>
   * <span data-ttu-id="3bbce-279">`OPT_NO_X86=1` &ndash; X86 instalace přeskočit runtimes.</span><span class="sxs-lookup"><span data-stu-id="3bbce-279">`OPT_NO_X86=1` &ndash; Skip installing x86 runtimes.</span></span> <span data-ttu-id="3bbce-280">Tento přepínač použijte, když víte, že vám nebude hostitelem 32bitových aplikací.</span><span class="sxs-lookup"><span data-stu-id="3bbce-280">Use this switch when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="3bbce-281">Pokud je pravděpodobné, že 32-bit a 64-bit aplikací budou v budoucnu umístěny, není tento přepínač a nainstalovat obě runtimes.</span><span class="sxs-lookup"><span data-stu-id="3bbce-281">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this switch and install both runtimes.</span></span>
1. <span data-ttu-id="3bbce-282">Restartování systému nebo spuštění **net stop byla /y** následovaný **net start w3svc** z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="3bbce-282">Restart the system or execute **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span> <span data-ttu-id="3bbce-283">Restartování služby IIS příjmem změnu systému provedené CESTU, která je proměnná prostředí, instalační služby.</span><span class="sxs-lookup"><span data-stu-id="3bbce-283">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="3bbce-284">Pokud instalační služby systému Windows, který je hostitelem svazku zjistí, že služba IIS dokončení instalace vyžaduje obnovení, obnoví instalační program služby IIS.</span><span class="sxs-lookup"><span data-stu-id="3bbce-284">If the Windows Hosting Bundle installer detects that IIS requires a reset in order to complete installation, the installer resets IIS.</span></span> <span data-ttu-id="3bbce-285">Pokud instalační program spustí resetování služby IIS, se restartují všechny fondy aplikací IIS a websites.</span><span class="sxs-lookup"><span data-stu-id="3bbce-285">If the installer triggers an IIS reset, all of the IIS app pools and websites are restarted.</span></span>

> [!NOTE]
> <span data-ttu-id="3bbce-286">Informace o sdílenou konfiguraci aplikaci IIS najdete v tématu [modul ASP.NET Core s sdílenou konfiguraci aplikaci IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="3bbce-286">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="3bbce-287">Nainstalujte nástroj nasazení webu při publikování pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3bbce-287">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="3bbce-288">Při nasazování aplikací na servery s [Webdeploy](/iis/publish/using-web-deploy/introduction-to-web-deploy), nainstalujte nejnovější verzi nástroje nasazení webu na serveru.</span><span class="sxs-lookup"><span data-stu-id="3bbce-288">When deploying apps to servers with [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="3bbce-289">Chcete-li nainstalujte nástroj nasazení webu, použijte [instalačního programu webové platformy (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) nebo získat přímo z instalačního programu [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span><span class="sxs-lookup"><span data-stu-id="3bbce-289">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="3bbce-290">Upřednostňovanou metodou je použití instalace webové platformy.</span><span class="sxs-lookup"><span data-stu-id="3bbce-290">The preferred method is to use WebPI.</span></span> <span data-ttu-id="3bbce-291">Instalace webové platformy nabízí samostatné instalace a konfigurace pro poskytovatele hostingu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-291">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="3bbce-292">Vytvořte web služby IIS</span><span class="sxs-lookup"><span data-stu-id="3bbce-292">Create the IIS site</span></span>

1. <span data-ttu-id="3bbce-293">V hostitelském systému vytvořte složku obsahující soubory a složky publikované aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bbce-293">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="3bbce-294">Rozložení nasazení vaší aplikace je popsána v [adresářovou strukturu](xref:host-and-deploy/directory-structure) tématu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-294">An app's deployment layout is described in the [Directory Structure](xref:host-and-deploy/directory-structure) topic.</span></span>

1. <span data-ttu-id="3bbce-295">V rámci novou složku vytvořit *protokoly* složku pro uložení protokolů stdout modul ASP.NET Core, pokud je povoleno protokolování stdout.</span><span class="sxs-lookup"><span data-stu-id="3bbce-295">Within the new folder, create a *logs* folder to hold ASP.NET Core Module stdout logs when stdout logging is enabled.</span></span> <span data-ttu-id="3bbce-296">Pokud je aplikace nasazena s *protokoly* složek v datové části, tento krok přeskočit.</span><span class="sxs-lookup"><span data-stu-id="3bbce-296">If the app is deployed with a *logs* folder in the payload, skip this step.</span></span> <span data-ttu-id="3bbce-297">Pokyny o tom, jak povolit MSBuild k vytvoření *protokoly* složku automaticky, pokud je projekt je sestaven místně, najdete v článku [adresářovou strukturu](xref:host-and-deploy/directory-structure) tématu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-297">For instructions on how to enable MSBuild to create the *logs* folder automatically when the project is built locally, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="3bbce-298">Pouze pomocí protokolu stdout řešení potíží s chybami při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bbce-298">Only use the stdout log to troubleshoot app startup failures.</span></span> <span data-ttu-id="3bbce-299">Nikdy nepoužívejte stdout protokolování pro protokolování běžné aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bbce-299">Never use stdout logging for routine app logging.</span></span> <span data-ttu-id="3bbce-300">Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="3bbce-300">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="3bbce-301">Fond aplikací musí mít oprávnění k zápisu do umístění, kde se zapisují protokoly.</span><span class="sxs-lookup"><span data-stu-id="3bbce-301">The app pool must have write access to the location where the logs are written.</span></span> <span data-ttu-id="3bbce-302">Všechny složky na cestu k umístění protokolu musí existovat.</span><span class="sxs-lookup"><span data-stu-id="3bbce-302">All of the folders on the path to the log location must exist.</span></span> <span data-ttu-id="3bbce-303">Další informace o protokolu stdout najdete v tématu [vytváření a přesměrování protokolu](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span><span class="sxs-lookup"><span data-stu-id="3bbce-303">For more information on the stdout log, see [Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span></span> <span data-ttu-id="3bbce-304">Informace o protokolování v aplikaci ASP.NET Core, najdete v článku [protokolování](xref:fundamentals/logging/index) tématu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-304">For information on logging in an ASP.NET Core app, see the [Logging](xref:fundamentals/logging/index) topic.</span></span>

1. <span data-ttu-id="3bbce-305">V **Správce služby IIS**, otevřete uzel serveru v **připojení** panelu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-305">In **IIS Manager**, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="3bbce-306">Klikněte pravým tlačítkem myši **lokality** složky.</span><span class="sxs-lookup"><span data-stu-id="3bbce-306">Right-click the **Sites** folder.</span></span> <span data-ttu-id="3bbce-307">Vyberte **přidat web** z kontextové nabídky.</span><span class="sxs-lookup"><span data-stu-id="3bbce-307">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="3bbce-308">Zadejte **název lokality** a nastavit **fyzická cesta** do složky pro nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bbce-308">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="3bbce-309">Zadejte **vazby** konfigurace a vytvořit na webu výběrem **OK**:</span><span class="sxs-lookup"><span data-stu-id="3bbce-309">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![Zadejte název lokality, fyzickou cestu a název hostitele v kroku přidat web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="3bbce-311">Vazby nejvyšší úrovně zástupný znak (`http://*:80/` a `http://+:80`) by měl **není** použít.</span><span class="sxs-lookup"><span data-stu-id="3bbce-311">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="3bbce-312">Vazby nejvyšší úrovně zástupný znak můžete otevřít aplikaci tak, aby slabá místa zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="3bbce-312">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="3bbce-313">To platí pro silné a slabé zástupné znaky.</span><span class="sxs-lookup"><span data-stu-id="3bbce-313">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="3bbce-314">Používejte explicitní hostitele názvy místo zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="3bbce-314">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="3bbce-315">Vazby zástupný znak subdoménu (například `*.mysub.com`) nemá toto bezpečnostní riziko, pokud celé nadřazené domény (nikoli `*.com`, což je ohrožené).</span><span class="sxs-lookup"><span data-stu-id="3bbce-315">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="3bbce-316">Zobrazit [rfc7230 části-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) Další informace.</span><span class="sxs-lookup"><span data-stu-id="3bbce-316">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="3bbce-317">V uzlu server, vyberte **fondy aplikací**.</span><span class="sxs-lookup"><span data-stu-id="3bbce-317">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="3bbce-318">Klikněte pravým tlačítkem na fond aplikací webu a vyberte **základní nastavení** z kontextové nabídky.</span><span class="sxs-lookup"><span data-stu-id="3bbce-318">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="3bbce-319">V **upravit fond aplikací** okno, nastaveno **verze .NET CLR** k **bez spravovaného kódu**:</span><span class="sxs-lookup"><span data-stu-id="3bbce-319">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![Nastavit bez spravovaného kódu pro verze .NET CLR.](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="3bbce-321">ASP.NET Core běží v samostatném procesu a spravuje modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="3bbce-321">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="3bbce-322">ASP.NET Core nemusí spoléhat na načítání desktop CLR.</span><span class="sxs-lookup"><span data-stu-id="3bbce-322">ASP.NET Core doesn't rely on loading the desktop CLR.</span></span> <span data-ttu-id="3bbce-323">Nastavení **verze .NET CLR** k **bez spravovaného kódu** je volitelný.</span><span class="sxs-lookup"><span data-stu-id="3bbce-323">Setting the **.NET CLR version** to **No Managed Code** is optional.</span></span>

1. <span data-ttu-id="3bbce-324">Potvrďte, že identita model procesu má příslušná oprávnění.</span><span class="sxs-lookup"><span data-stu-id="3bbce-324">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="3bbce-325">Pokud výchozí identita fondu aplikací (**Model procesu** > **Identity**) se změní z **ApplicationPoolIdentity** na jinou identitu, ověřte, že nové identity má požadovaná oprávnění pro přístup ke složce aplikace, databáze a ostatní požadované prostředky.</span><span class="sxs-lookup"><span data-stu-id="3bbce-325">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="3bbce-326">Například fond aplikací vyžaduje čtení a zápisu do složky, kde aplikace čte a zapisuje soubory.</span><span class="sxs-lookup"><span data-stu-id="3bbce-326">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="3bbce-327">**Konfigurace ověřování Windows (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="3bbce-327">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="3bbce-328">Další informace najdete v tématu [Windows nakonfigurovat ověřování](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="3bbce-328">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="3bbce-329">Nasazení aplikace</span><span class="sxs-lookup"><span data-stu-id="3bbce-329">Deploy the app</span></span>

<span data-ttu-id="3bbce-330">Nasazení aplikace do složky vytvořené v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="3bbce-330">Deploy the app to the folder created on the hosting system.</span></span> <span data-ttu-id="3bbce-331">[Webu nasadit](/iis/publish/using-web-deploy/introduction-to-web-deploy) je doporučené mechanismus pro nasazení.</span><span class="sxs-lookup"><span data-stu-id="3bbce-331">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="3bbce-332">Nasazení webu pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3bbce-332">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="3bbce-333">Zobrazit [profily publikování sady Visual Studio pro nasazení aplikace ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) téma a zjistěte, jak vytvořit profil publikování pro použití s nasazením webu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-333">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="3bbce-334">Pokud poskytovatel hostingu poskytuje pro vytvoření nového profilu pro publikování nebo podporu, stáhněte si svůj profil a importujte ho pomocí sady Visual Studio **publikovat** dialogového okna.</span><span class="sxs-lookup"><span data-stu-id="3bbce-334">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog.</span></span>

![Publikovat stránku dialogového okna](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="3bbce-336">Nasazení webu mimo sadu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3bbce-336">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="3bbce-337">[Webu nasadit](/iis/publish/using-web-deploy/introduction-to-web-deploy) lze také použít mimo sadu Visual Studio z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="3bbce-337">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="3bbce-338">Další informace najdete v tématu [nástroje Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span><span class="sxs-lookup"><span data-stu-id="3bbce-338">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="3bbce-339">Alternativy k webové nasazení</span><span class="sxs-lookup"><span data-stu-id="3bbce-339">Alternatives to Web Deploy</span></span>

<span data-ttu-id="3bbce-340">Použijte několik metod pro přesun aplikace k hostování systému, jako je ruční export, příkazu Xcopy, Robocopy nebo prostředí PowerShell.</span><span class="sxs-lookup"><span data-stu-id="3bbce-340">Use any of several methods to move the app to the hosting system, such as manual copy, Xcopy, Robocopy, or PowerShell.</span></span>

<span data-ttu-id="3bbce-341">Další informace o nasazení ASP.NET Core do služby IIS, najdete v článku [materiály pro nasazení pro správce služby IIS](#deployment-resources-for-iis-administrators) oddílu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-341">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="3bbce-342">Přejděte na web</span><span class="sxs-lookup"><span data-stu-id="3bbce-342">Browse the website</span></span>

![Prohlížeč Microsoft Edge načetl úvodní stránka služby IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="3bbce-344">Soubory uzamčené nasazení</span><span class="sxs-lookup"><span data-stu-id="3bbce-344">Locked deployment files</span></span>

<span data-ttu-id="3bbce-345">Při spuštění aplikace jsou zamknuté soubory ve složce pro nasazení.</span><span class="sxs-lookup"><span data-stu-id="3bbce-345">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="3bbce-346">Uzamčené soubory nemohou být přepsána během nasazení.</span><span class="sxs-lookup"><span data-stu-id="3bbce-346">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="3bbce-347">Uvolnit uzamčené soubory v nasazení, zastavte aplikaci pomocí fondu **jeden** z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="3bbce-347">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="3bbce-348">Pomocí nasazení webu a odkaz `Microsoft.NET.Sdk.Web` v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-348">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="3bbce-349">*App_offline.htm* soubor umístěn v kořenovém adresáři webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bbce-349">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="3bbce-350">Pokud soubor existuje, modul ASP.NET Core řádně ukončí aplikaci, slouží *app_offline.htm* souboru během nasazení.</span><span class="sxs-lookup"><span data-stu-id="3bbce-350">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="3bbce-351">Další informace najdete v tématu [odkaz Konfigurace modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="3bbce-351">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="3bbce-352">Ručně zastavte fond aplikací ve Správci služby IIS na serveru.</span><span class="sxs-lookup"><span data-stu-id="3bbce-352">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="3bbce-353">Použití Powershellu k vyřadit *app_offline.html* (vyžaduje PowerShell 5 nebo novější):</span><span class="sxs-lookup"><span data-stu-id="3bbce-353">Use PowerShell to drop *app_offline.html* (requires PowerShell 5 or later):</span></span>

  ```PowerShell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="3bbce-354">Ochrana dat</span><span class="sxs-lookup"><span data-stu-id="3bbce-354">Data protection</span></span>

<span data-ttu-id="3bbce-355">[Ochranu dat ASP.NET Core zásobníku](xref:security/data-protection/introduction) používá několik ASP.NET Core [middlewares](xref:fundamentals/middleware/index), včetně middleware použitý v ověřování.</span><span class="sxs-lookup"><span data-stu-id="3bbce-355">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="3bbce-356">I v případě, že Data Protection API nejsou volané kódem uživatele, se skriptem nasazení nebo v uživatelském kódu k vytvoření trvalé by měl nakonfigurovat ochranu dat kryptografických [úložiště klíčů](xref:security/data-protection/implementation/key-management).</span><span class="sxs-lookup"><span data-stu-id="3bbce-356">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="3bbce-357">Pokud není nakonfigurovaná ochrana dat, jsou klíče uložené v paměti a při restartování aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bbce-357">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="3bbce-358">Pokud kanál klíče jsou uloženy v paměti, při restartování aplikace:</span><span class="sxs-lookup"><span data-stu-id="3bbce-358">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="3bbce-359">Všechny tokeny ověřování na základě souborů cookie nejsou zneplatněny.</span><span class="sxs-lookup"><span data-stu-id="3bbce-359">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="3bbce-360">Uživatelé se musí znovu přihlásit v jejich další požadavek.</span><span class="sxs-lookup"><span data-stu-id="3bbce-360">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="3bbce-361">Všechna data chráněná pomocí aktualizační kanál, který klíč můžete už nebude možné dešifrovat.</span><span class="sxs-lookup"><span data-stu-id="3bbce-361">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="3bbce-362">To může zahrnovat [CSRF tokeny](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [soubory cookie v ASP.NET Core MVC TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="3bbce-362">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="3bbce-363">Chcete-li konfigurovat ochranu dat v rámci služby IIS k uchování aktualizační kanál, který klíč, použijte **jeden** z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="3bbce-363">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="3bbce-364">**Vytvoření klíče registru ochrany dat**</span><span class="sxs-lookup"><span data-stu-id="3bbce-364">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="3bbce-365">Používá aplikace ASP.NET Core klíče ochrany dat jsou uložené v registru, které jsou externí vzhledem k aplikacím.</span><span class="sxs-lookup"><span data-stu-id="3bbce-365">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="3bbce-366">Pokud chcete zachovat klíče pro danou aplikaci, vytvořte klíče registru pro fond aplikací.</span><span class="sxs-lookup"><span data-stu-id="3bbce-366">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="3bbce-367">Pro samostatnou, instalace služby IIS – webové farmě, [skript prostředí PowerShell AutoGenKeys.ps1 poskytování ochrany dat](https://github.com/aspnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) lze použít pro každý fond aplikací, které jsou součástí aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3bbce-367">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/aspnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="3bbce-368">Tento skript vytvoří klíč registru HKLM registru, který je přístupný pouze pro účet pracovního procesu fondu aplikací aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3bbce-368">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="3bbce-369">Klíče se zašifrují neaktivní uložená data pomocí rozhraní DPAPI klíčem celý počítač.</span><span class="sxs-lookup"><span data-stu-id="3bbce-369">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="3bbce-370">Ve webových farem lze nastavit aplikaci pro použití cesty UNC pro ukládání jeho data protection klíč kanál.</span><span class="sxs-lookup"><span data-stu-id="3bbce-370">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="3bbce-371">Ve výchozím nastavení klíče ochrany dat nejsou šifrovány.</span><span class="sxs-lookup"><span data-stu-id="3bbce-371">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="3bbce-372">Zajistěte, aby byly omezené na účet Windows, které aplikace běží pod oprávnění pro sdílené síťové složce.</span><span class="sxs-lookup"><span data-stu-id="3bbce-372">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="3bbce-373">X X509 certifikát můžete použít k ochraně klíčů v klidovém stavu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-373">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="3bbce-374">Vezměte v úvahu mechanismus pro uživatelům umožní nahrát certifikáty: místo certifikátů do důvěryhodného certifikátu uživatele ukládat a ujistěte se, jsou k dispozici na všech počítačích, ve kterém běží aplikace uživatele.</span><span class="sxs-lookup"><span data-stu-id="3bbce-374">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="3bbce-375">Zobrazit [Konfigurace ochrany dat ASP.NET Core](xref:security/data-protection/configuration/overview) podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="3bbce-375">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="3bbce-376">**Konfigurace fondu aplikací služby IIS se načíst profil uživatele**</span><span class="sxs-lookup"><span data-stu-id="3bbce-376">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="3bbce-377">Toto nastavení je v **Model procesu** části **Upřesnit nastavení** pro fond aplikací.</span><span class="sxs-lookup"><span data-stu-id="3bbce-377">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="3bbce-378">Načíst profil uživatele nastaveno `True`.</span><span class="sxs-lookup"><span data-stu-id="3bbce-378">Set Load User Profile to `True`.</span></span> <span data-ttu-id="3bbce-379">To ukládá klíče v části adresář profilu uživatele a chrání je pomocí rozhraní DPAPI klíčem specifické pro uživatelský účet je používána ve fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="3bbce-379">This stores keys under the user profile directory and protects them using DPAPI with a key specific to the user account used by the app pool.</span></span>

* <span data-ttu-id="3bbce-380">**Systém souborů jako kanál klíč úložiště**</span><span class="sxs-lookup"><span data-stu-id="3bbce-380">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="3bbce-381">Upravit kód aplikace, který [používat systém souborů jako kanál klíč úložiště](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="3bbce-381">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="3bbce-382">Použijte X509 certifikátu k ochraně klíče kanál a zajistit certifikát není důvěryhodný certifikát.</span><span class="sxs-lookup"><span data-stu-id="3bbce-382">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="3bbce-383">Pokud certifikát podepsaný svým držitelem, můžete certifikát umístěte do úložiště důvěryhodných kořenových certifikátů.</span><span class="sxs-lookup"><span data-stu-id="3bbce-383">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="3bbce-384">Pokud používáte IIS ve webové farmě:</span><span class="sxs-lookup"><span data-stu-id="3bbce-384">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="3bbce-385">Použití sdílené složky, ke kterému přístup všechny počítače.</span><span class="sxs-lookup"><span data-stu-id="3bbce-385">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="3bbce-386">Nasazení x X509 certifikát pro každý počítač.</span><span class="sxs-lookup"><span data-stu-id="3bbce-386">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="3bbce-387">Konfigurace [ochrany dat v kódu](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="3bbce-387">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="3bbce-388">**Nastavte zásady pro celý počítač pro ochranu dat**</span><span class="sxs-lookup"><span data-stu-id="3bbce-388">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="3bbce-389">Systém ochrany dat má omezenou podporu pro nastavení výchozí [celého zásad](xref:security/data-protection/configuration/machine-wide-policy) pro všechny aplikace, které používání rozhraní Data Protection API.</span><span class="sxs-lookup"><span data-stu-id="3bbce-389">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="3bbce-390">Další informace naleznete v tématu <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="3bbce-390">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="sub-application-configuration"></a><span data-ttu-id="3bbce-391">Dílčí aplikace konfigurace</span><span class="sxs-lookup"><span data-stu-id="3bbce-391">Sub-application configuration</span></span>

<span data-ttu-id="3bbce-392">Dílčí aplikace přidány pod kořenové aplikace by neměl obsahovat modul ASP.NET Core jako obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="3bbce-392">Sub-apps added under the root app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="3bbce-393">Pokud modul je přidán jako obslužná rutina sub-aplikace *web.config* souboru, *500.19 vnitřní chyba serveru* odkazující na chybného konfiguračního souboru obdržíte, když se pokusíte o procházení podřízeným aplikacím.</span><span class="sxs-lookup"><span data-stu-id="3bbce-393">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="3bbce-394">Následující příklad ukazuje publikování *web.config* sub aplikace ASP.NET Core v souboru:</span><span class="sxs-lookup"><span data-stu-id="3bbce-394">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\<assembly_name>.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="3bbce-395">Při hostování za nástrojem sub aplikace ASP.NET Core pod aplikace ASP.NET Core, explicitně odebrat zděděné obslužné rutiny v aplikaci sub *web.config* souboru:</span><span class="sxs-lookup"><span data-stu-id="3bbce-395">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app *web.config* file:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\<assembly_name>.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="3bbce-396">Další informace o konfiguraci modul ASP.NET Core, najdete v článku [Úvod do ASP.NET Core modulu](xref:fundamentals/servers/aspnet-core-module) tématu a [odkaz Konfigurace modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="3bbce-396">For more information on configuring the ASP.NET Core Module, see the [Introduction to ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) topic and the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module).</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="3bbce-397">Konfigurace služby IIS pomocí souboru web.config</span><span class="sxs-lookup"><span data-stu-id="3bbce-397">Configuration of IIS with web.config</span></span>

<span data-ttu-id="3bbce-398">Konfigurace služby IIS je ovlivněno  **\<system.webServer >** část *web.config* pro tyto funkce služby IIS, které platí pro konfigurace reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="3bbce-398">IIS configuration is influenced by the **\<system.webServer>** section of *web.config* for those IIS features that apply to a reverse proxy configuration.</span></span> <span data-ttu-id="3bbce-399">Pokud je služba IIS konfigurována na úrovni serveru na použití dynamické komprese  **\<urlCompression >** element ve vaší aplikaci *web.config* souboru ji zakázat.</span><span class="sxs-lookup"><span data-stu-id="3bbce-399">If IIS is configured at the server level to use dynamic compression, the **\<urlCompression>** element in the app's *web.config* file can disable it.</span></span>

<span data-ttu-id="3bbce-400">Další informace najdete v tématu [odkaz Konfigurace pro \<system.webServer >](/iis/configuration/system.webServer/), [odkaz Konfigurace modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module), a [moduly IIS s ASP.NET Základní](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="3bbce-400">For more information, see the [configuration reference for \<system.webServer>](/iis/configuration/system.webServer/), [ASP.NET Core Module Configuration Reference](xref:host-and-deploy/aspnet-core-module), and [IIS Modules with ASP.NET Core](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="3bbce-401">Chcete-li nastavit proměnné prostředí pro jednotlivé aplikace spuštěných ve fondech izolované aplikace (podporováno pro IIS 10.0 a novější), najdete v článku *AppCmd.exe příkaz* část [proměnné prostředí \< environmentVariables >](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) téma v IIS referenční dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="3bbce-401">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="3bbce-402">Konfigurační oddíly souboru Web.config</span><span class="sxs-lookup"><span data-stu-id="3bbce-402">Configuration sections of web.config</span></span>

<span data-ttu-id="3bbce-403">Konfigurační oddíly funkce ASP.NET 4.x aplikací v *web.config* nejsou používány aplikací ASP.NET Core pro konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="3bbce-403">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* <span data-ttu-id="3bbce-404">**\<system.web>**</span><span class="sxs-lookup"><span data-stu-id="3bbce-404">**\<system.web>**</span></span>
* <span data-ttu-id="3bbce-405">**\<appSettings>**</span><span class="sxs-lookup"><span data-stu-id="3bbce-405">**\<appSettings>**</span></span>
* <span data-ttu-id="3bbce-406">**\<connectionStrings >**</span><span class="sxs-lookup"><span data-stu-id="3bbce-406">**\<connectionStrings>**</span></span>
* <span data-ttu-id="3bbce-407">**\<umístění >**</span><span class="sxs-lookup"><span data-stu-id="3bbce-407">**\<location>**</span></span>

<span data-ttu-id="3bbce-408">Aplikace ASP.NET Core jsou nakonfigurované pomocí jiných poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="3bbce-408">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="3bbce-409">Další informace najdete v tématu [konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="3bbce-409">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="3bbce-410">Fondy aplikací</span><span class="sxs-lookup"><span data-stu-id="3bbce-410">Application Pools</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="3bbce-411">Izolace fond aplikací je určeno model hostingu:</span><span class="sxs-lookup"><span data-stu-id="3bbce-411">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="3bbce-412">Proces hostování &ndash; aplikace jsou potřeba ke spouštění ve fondech samostatné aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bbce-412">In-process hosting &ndash; Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="3bbce-413">Hostování mimo proces &ndash; doporučujeme izoluje aplikace od sebe navzájem spuštěním každou aplikaci ve vlastním fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="3bbce-413">Out-of-process hosting &ndash; We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="3bbce-414">IIS **přidat web** výchozí dialogové okno aplikace s jedním fondu na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3bbce-414">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="3bbce-415">Když **název lokality** je k dispozici text je automaticky převedena do **fond aplikací** textového pole.</span><span class="sxs-lookup"><span data-stu-id="3bbce-415">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="3bbce-416">Nový fond aplikací je vytvořený pomocí názvu serveru po přidání serveru.</span><span class="sxs-lookup"><span data-stu-id="3bbce-416">A new app pool is created using the site name when the site is added.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="3bbce-417">Při hostování více webů na serveru, doporučujeme izoluje aplikace od sebe navzájem spuštěním každou aplikaci ve vlastním fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="3bbce-417">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="3bbce-418">IIS **přidat web** výchozí dialogové okno pro tuto konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="3bbce-418">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="3bbce-419">Když **název lokality** je k dispozici text je automaticky převedena do **fond aplikací** textového pole.</span><span class="sxs-lookup"><span data-stu-id="3bbce-419">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="3bbce-420">Nový fond aplikací je vytvořený pomocí názvu serveru po přidání serveru.</span><span class="sxs-lookup"><span data-stu-id="3bbce-420">A new app pool is created using the site name when the site is added.</span></span>

::: moniker-end

## <a name="application-pool-identity"></a><span data-ttu-id="3bbce-421">Identita fondu aplikací</span><span class="sxs-lookup"><span data-stu-id="3bbce-421">Application Pool Identity</span></span>

<span data-ttu-id="3bbce-422">Účet identita fondu aplikací umožňuje aplikaci běžet pod účtem jedinečný bez nutnosti vytvářet a spravovat domény nebo místní účty.</span><span class="sxs-lookup"><span data-stu-id="3bbce-422">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="3bbce-423">IIS 8.0 nebo novější procesů pro pracovníka Správce služby IIS (WAS) vytvoří virtuální účet s názvem nového fondu aplikací a aplikace spouští fondu pracovních procesů pod tímto účtem ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="3bbce-423">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="3bbce-424">V konzole pro správu služby IIS v části **Upřesnit nastavení** pro fond aplikací, ujistěte se, že **Identity** nastaveno pro použití **ApplicationPoolIdentity**:</span><span class="sxs-lookup"><span data-stu-id="3bbce-424">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![Dialogové okno pokročilé nastavení fondu aplikací](index/_static/apppool-identity.png)

<span data-ttu-id="3bbce-426">Proces správy služby IIS vytvoří zabezpečeného identifikátoru se název fondu aplikací v zabezpečení systému Windows.</span><span class="sxs-lookup"><span data-stu-id="3bbce-426">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="3bbce-427">Pomocí této identity, je možné svázat prostředky.</span><span class="sxs-lookup"><span data-stu-id="3bbce-427">Resources can be secured using this identity.</span></span> <span data-ttu-id="3bbce-428">Ale tato identita není skutečný účet a nezobrazí se v konzole pro správu uživatelů Windows.</span><span class="sxs-lookup"><span data-stu-id="3bbce-428">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="3bbce-429">Pokud pracovní proces služby IIS vyžaduje přístup k aplikaci přes se zvýšenými oprávněními, upravte seznam řízení přístupu (ACL) pro adresář obsahující aplikaci:</span><span class="sxs-lookup"><span data-stu-id="3bbce-429">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="3bbce-430">Otevřete Průzkumníka Windows a přejděte do adresáře.</span><span class="sxs-lookup"><span data-stu-id="3bbce-430">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="3bbce-431">Klikněte pravým tlačítkem na adresář a vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="3bbce-431">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="3bbce-432">V části **zabezpečení** kartu, vyberte **upravit** tlačítko a pak **přidat** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="3bbce-432">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="3bbce-433">Vyberte **umístění** tlačítko a ujistěte se, že je vybraná systému.</span><span class="sxs-lookup"><span data-stu-id="3bbce-433">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="3bbce-434">Zadejte **fondu aplikací služby IIS\\< app_pool_name >** v **zadejte názvy objektů k výběru** oblasti.</span><span class="sxs-lookup"><span data-stu-id="3bbce-434">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="3bbce-435">Vyberte **Kontrola názvů** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="3bbce-435">Select the **Check Names** button.</span></span> <span data-ttu-id="3bbce-436">Pro *DefaultAppPool* zkontrolovat názvy pomocí **IIS AppPool\DefaultAppPool**.</span><span class="sxs-lookup"><span data-stu-id="3bbce-436">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="3bbce-437">Při **Kontrola názvů** se vybere tlačítko, hodnota **DefaultAppPool** je uveden v oblasti názvy objektu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-437">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="3bbce-438">Není možné zadat název fondu aplikací přímo do oblasti názvy objektu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-438">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="3bbce-439">Použití **fondu aplikací služby IIS\\< app_pool_name >** formátování při vyhledávání pro název objektu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-439">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![Vyberte uživatele nebo skupiny dialogové okno pro složky aplikace: název fondu aplikací "DefaultAppPool" se připojí k "fondu aplikací služby IIS\" v oblasti názvy objektu před výběrem"Zkontrolujte názvy."](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="3bbce-441">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="3bbce-441">Select **OK**.</span></span>

   ![Vyberte uživatele nebo skupiny dialogové okno pro složky aplikace: Po výběru "Kontrola názvů", "DefaultAppPool" se zobrazí v objektu název objektu názvy oblasti.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="3bbce-443">Čtení &amp; provést ve výchozím nastavení by měl být udělena oprávnění.</span><span class="sxs-lookup"><span data-stu-id="3bbce-443">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="3bbce-444">Zadejte další oprávnění podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="3bbce-444">Provide additional permissions as needed.</span></span>

<span data-ttu-id="3bbce-445">Na příkazovém řádku pomocí můžete také udělit přístup **ICACLS** nástroj.</span><span class="sxs-lookup"><span data-stu-id="3bbce-445">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="3bbce-446">Použití *DefaultAppPool* jako příklad slouží následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3bbce-446">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="3bbce-447">Další informace najdete v tématu [icacls](/windows-server/administration/windows-commands/icacls) tématu.</span><span class="sxs-lookup"><span data-stu-id="3bbce-447">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="3bbce-448">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="3bbce-448">HTTP/2 support</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="3bbce-449">[HTTP/2](https://httpwg.org/specs/rfc7540.html) se podporuje s ASP.NET Core v následujících scénářích nasazení služby IIS:</span><span class="sxs-lookup"><span data-stu-id="3bbce-449">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="3bbce-450">V procesu</span><span class="sxs-lookup"><span data-stu-id="3bbce-450">In-process</span></span>
  * <span data-ttu-id="3bbce-451">Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="3bbce-451">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="3bbce-452">Protokol TLS 1.2 nebo vyšší připojení</span><span class="sxs-lookup"><span data-stu-id="3bbce-452">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="3bbce-453">Mimo proces</span><span class="sxs-lookup"><span data-stu-id="3bbce-453">Out-of-process</span></span>
  * <span data-ttu-id="3bbce-454">Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="3bbce-454">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="3bbce-455">Připojení k serveru edge veřejně přístupných pomocí protokolu HTTP/2, ale reverzní proxy server připojení k [Kestrel server](xref:fundamentals/servers/kestrel) používá HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="3bbce-455">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="3bbce-456">Protokol TLS 1.2 nebo vyšší připojení</span><span class="sxs-lookup"><span data-stu-id="3bbce-456">TLS 1.2 or later connection</span></span>

<span data-ttu-id="3bbce-457">V procesu nasazení po vytvoření připojení k protokolu HTTP/2 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="3bbce-457">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="3bbce-458">Mimo proces nasazení po vytvoření připojení k protokolu HTTP/2 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/1.1`.</span><span class="sxs-lookup"><span data-stu-id="3bbce-458">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="3bbce-459">Další informace o modelech hostování a mimo proces, najdete v článku <xref:fundamentals/servers/aspnet-core-module> tématu a <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="3bbce-459">For more information on the in-process and out-of-process hosting models, see the <xref:fundamentals/servers/aspnet-core-module> topic and the <xref:host-and-deploy/aspnet-core-module>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="3bbce-460">[HTTP/2](https://httpwg.org/specs/rfc7540.html) se podporuje pro nasazení mimo proces, které splňují základní požadavky na následující:</span><span class="sxs-lookup"><span data-stu-id="3bbce-460">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="3bbce-461">Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="3bbce-461">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="3bbce-462">Připojení k serveru edge veřejně přístupných pomocí protokolu HTTP/2, ale reverzní proxy server připojení k [Kestrel server](xref:fundamentals/servers/kestrel) používá HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="3bbce-462">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="3bbce-463">Cílová architektura: není k dispozici pro nasazení mimo proces, protože připojení HTTP/2 je zpracována zcela službou IIS.</span><span class="sxs-lookup"><span data-stu-id="3bbce-463">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="3bbce-464">Protokol TLS 1.2 nebo vyšší připojení</span><span class="sxs-lookup"><span data-stu-id="3bbce-464">TLS 1.2 or later connection</span></span>

<span data-ttu-id="3bbce-465">Pokud se připojení HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/1.1`.</span><span class="sxs-lookup"><span data-stu-id="3bbce-465">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

::: moniker-end

<span data-ttu-id="3bbce-466">HTTP/2 je standardně povolená.</span><span class="sxs-lookup"><span data-stu-id="3bbce-466">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="3bbce-467">Připojení vrátit zpět k protokolu HTTP/1.1, pokud nedojde k připojení k protokolu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="3bbce-467">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="3bbce-468">Další informace o konfiguraci protokolu HTTP/2 u nasazení ve službě IIS najdete v části [HTTP/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="3bbce-468">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="3bbce-469">Materiály pro nasazení pro správce služby IIS</span><span class="sxs-lookup"><span data-stu-id="3bbce-469">Deployment resources for IIS administrators</span></span>

<span data-ttu-id="3bbce-470">Další informace o službě IIS hlouběji v dokumentaci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="3bbce-470">Learn about IIS in-depth in the IIS documentation.</span></span>  
[<span data-ttu-id="3bbce-471">Dokumentace ke službě IIS</span><span class="sxs-lookup"><span data-stu-id="3bbce-471">IIS documentation</span></span>](/iis)

<span data-ttu-id="3bbce-472">Další informace o modelech nasazení aplikace .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3bbce-472">Learn about .NET Core app deployment models.</span></span>  
[<span data-ttu-id="3bbce-473">Nasazení aplikace .NET core</span><span class="sxs-lookup"><span data-stu-id="3bbce-473">.NET Core application deployment</span></span>](/dotnet/core/deploying/)

<span data-ttu-id="3bbce-474">Zjistěte, jak modul ASP.NET Core umožňuje webovému serveru Kestrel k použití jako reverzní proxy server služby IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="3bbce-474">Learn how the ASP.NET Core Module allows the Kestrel web server to use IIS or IIS Express as a reverse proxy server.</span></span>  
[<span data-ttu-id="3bbce-475">Modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3bbce-475">ASP.NET Core Module</span></span>](xref:fundamentals/servers/aspnet-core-module)

<span data-ttu-id="3bbce-476">Zjistěte, jak nakonfigurovat modul ASP.NET Core pro hostování aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3bbce-476">Learn how to configure the ASP.NET Core Module for hosting ASP.NET Core apps.</span></span>  
[<span data-ttu-id="3bbce-477">Referenční dokumentace k modulu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3bbce-477">ASP.NET Core Module configuration reference</span></span>](xref:host-and-deploy/aspnet-core-module)

<span data-ttu-id="3bbce-478">Další informace o struktuře adresářů publikované aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3bbce-478">Learn about the directory structure of published ASP.NET Core apps.</span></span>  
[<span data-ttu-id="3bbce-479">Adresářová struktura</span><span class="sxs-lookup"><span data-stu-id="3bbce-479">Directory structure</span></span>](xref:host-and-deploy/directory-structure)

<span data-ttu-id="3bbce-480">Zjistěte aktivní i neaktivní moduly IIS pro aplikace ASP.NET Core a jak spravovat moduly služby IIS.</span><span class="sxs-lookup"><span data-stu-id="3bbce-480">Discover active and inactive IIS modules for ASP.NET Core apps and how to manage IIS modules.</span></span>  
[<span data-ttu-id="3bbce-481">Moduly služby IIS</span><span class="sxs-lookup"><span data-stu-id="3bbce-481">IIS modules</span></span>](xref:host-and-deploy/iis/troubleshoot)

<span data-ttu-id="3bbce-482">Zjistěte, jak diagnostikovat problémy se službou IIS nasazení aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3bbce-482">Learn how to diagnose problems with IIS deployments of ASP.NET Core apps.</span></span>  
[<span data-ttu-id="3bbce-483">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="3bbce-483">Troubleshoot</span></span>](xref:host-and-deploy/iis/troubleshoot)

<span data-ttu-id="3bbce-484">Rozlišení běžných chyb při hostování aplikací ASP.NET Core ve službě IIS.</span><span class="sxs-lookup"><span data-stu-id="3bbce-484">Distinguish common errors when hosting ASP.NET Core apps on IIS.</span></span>  
[<span data-ttu-id="3bbce-485">Referenční informace k běžným chybám u služeb Azure App Service a IIS</span><span class="sxs-lookup"><span data-stu-id="3bbce-485">Common errors reference for Azure App Service and IIS</span></span>](xref:host-and-deploy/azure-iis-errors-reference)

## <a name="additional-resources"></a><span data-ttu-id="3bbce-486">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3bbce-486">Additional resources</span></span>

* [<span data-ttu-id="3bbce-487">Úvod do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3bbce-487">Introduction to ASP.NET Core</span></span>](xref:index)
* [<span data-ttu-id="3bbce-488">Lokality oficiální Microsoft IIS</span><span class="sxs-lookup"><span data-stu-id="3bbce-488">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="3bbce-489">Technická knihovna obsahu Windows serveru</span><span class="sxs-lookup"><span data-stu-id="3bbce-489">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="3bbce-490">HTTP/2 ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="3bbce-490">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
