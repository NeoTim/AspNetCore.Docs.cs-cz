---
title: Hostitele ASP.NET Core ve Windows se službou IIS
author: guardrex
description: Zjistěte, jak hostovat aplikace ASP.NET Core na Windows serveru Internetové informační služby (IIS).
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2018
uid: host-and-deploy/iis/index
ms.openlocfilehash: 77fa6e1ef6a7fc707c2665826d3c1f4c2691979c
ms.sourcegitcommit: e9b99854b0a8021dafabee0db5e1338067f250a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52450798"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="92ff3-103">Hostitele ASP.NET Core ve Windows se službou IIS</span><span class="sxs-lookup"><span data-stu-id="92ff3-103">Host ASP.NET Core on Windows with IIS</span></span>

<span data-ttu-id="92ff3-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="92ff3-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[<span data-ttu-id="92ff3-105">Instalace .NET Core, který je hostitelem svazku</span><span class="sxs-lookup"><span data-stu-id="92ff3-105">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

> [!NOTE]
> <span data-ttu-id="92ff3-106">Testujeme použitelnost navrhované nové struktury pro obsah ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92ff3-106">We’re testing the usability of a proposed new structure for the ASP.NET Core table of contents.</span></span>  <span data-ttu-id="92ff3-107">Pokud máte pár minut a chcete si vyzkoušet cvičení, které spočívá ve vyhledání 7 různých témat v aktuálním nebo navrhovaném obsahu, [klikněte prosím sem a zúčastněte se studie](https://dpk4xbh5.optimalworkshop.com/treejack/rps16hd5).</span><span class="sxs-lookup"><span data-stu-id="92ff3-107">If you have a few minutes to try an exercise of finding 7 different topics in the current or proposed table of contents, please [click here to participate in the study](https://dpk4xbh5.optimalworkshop.com/treejack/rps16hd5).</span></span>

## <a name="supported-operating-systems"></a><span data-ttu-id="92ff3-108">Podporované operační systémy</span><span class="sxs-lookup"><span data-stu-id="92ff3-108">Supported operating systems</span></span>

<span data-ttu-id="92ff3-109">Následující operační systémy se podporují:</span><span class="sxs-lookup"><span data-stu-id="92ff3-109">The following operating systems are supported:</span></span>

* <span data-ttu-id="92ff3-110">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="92ff3-110">Windows 7 or later</span></span>
* <span data-ttu-id="92ff3-111">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="92ff3-111">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="92ff3-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) (dříve se označovaly jako [WebListener](xref:fundamentals/servers/weblistener)) nebude fungovat v konfigurace reverzního proxy serveru se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="92ff3-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called [WebListener](xref:fundamentals/servers/weblistener)) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="92ff3-113">Použití [Kestrel server](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="92ff3-113">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="92ff3-114">Informace o hostování v Azure najdete v tématu <xref:host-and-deploy/azure-apps/index>.</span><span class="sxs-lookup"><span data-stu-id="92ff3-114">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="92ff3-115">Konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="92ff3-115">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="92ff3-116">Povolit IISIntegration součásti</span><span class="sxs-lookup"><span data-stu-id="92ff3-116">Enable the IISIntegration components</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="92ff3-117">Typické *Program.cs* volání <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> zahájíte nastavení hostitele:</span><span class="sxs-lookup"><span data-stu-id="92ff3-117">A typical *Program.cs* calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to begin setting up a host:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="92ff3-118">Typické *Program.cs* volání <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> zahájíte nastavení hostitele:</span><span class="sxs-lookup"><span data-stu-id="92ff3-118">A typical *Program.cs* calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to begin setting up a host:</span></span>

```csharp
public static IWebHost BuildWebHost(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="92ff3-119">**Model hostingu v procesu**</span><span class="sxs-lookup"><span data-stu-id="92ff3-119">**In-process hosting model**</span></span>

<span data-ttu-id="92ff3-120">`CreateDefaultBuilder` volání `UseIIS` metoda pro spuštění [CoreCLR](/dotnet/standard/glossary#coreclr) a hostování aplikace v rámci pracovní proces služby IIS (*w3wp.exe* nebo *iisexpress.exe*).</span><span class="sxs-lookup"><span data-stu-id="92ff3-120">`CreateDefaultBuilder` calls the `UseIIS` method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="92ff3-121">Testy výkonu zjistí, že hostování .NET Core aplikace v procesu poskytuje výrazně vyšší propustnost žádostí, které jsou ve srovnání s žádostí aplikace na více instancí procesu a využívání proxy serverů k hostování [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="92ff3-121">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="92ff3-122">**Model hostingu mimo proces**</span><span class="sxs-lookup"><span data-stu-id="92ff3-122">**Out-of-process hosting model**</span></span>

<span data-ttu-id="92ff3-123">Pro hostování mimo proces se službou IIS, `CreateDefaultBuilder` nakonfiguruje [Kestrel](xref:fundamentals/servers/kestrel) jako integrace webového serveru a umožňuje služby IIS tím, že nakonfigurujete základní cesty a port pro [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="92ff3-123">For out-of-process hosting with IIS, `CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) as the web server and enables IIS integration by configuring the base path and port for the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module).</span></span>

<span data-ttu-id="92ff3-124">Modul ASP.NET Core generuje dynamický port přiřadit k procesu back-endu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-124">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="92ff3-125">`CreateDefaultBuilder` volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> metody.</span><span class="sxs-lookup"><span data-stu-id="92ff3-125">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> method.</span></span> <span data-ttu-id="92ff3-126">`UseIISIntegration` Nakonfiguruje Kestrel k naslouchání na dynamický port na IP adresu místního hostitele (`127.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="92ff3-126">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="92ff3-127">Pokud dynamický port je 1234, Kestrel naslouchá na `127.0.0.1:1234`.</span><span class="sxs-lookup"><span data-stu-id="92ff3-127">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="92ff3-128">Tato konfigurace nahrazuje jiné konfigurace adresy URL poskytnuté:</span><span class="sxs-lookup"><span data-stu-id="92ff3-128">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="92ff3-129">Rozhraní API od kestrel naslouchání</span><span class="sxs-lookup"><span data-stu-id="92ff3-129">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="92ff3-130">[Konfigurace](xref:fundamentals/configuration/index) (nebo [možnost příkazového řádku--adresy URL](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="92ff3-130">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="92ff3-131">Volání `UseUrls` nebo jeho Kestrel `Listen` rozhraní API nejsou povinné, když pomocí modulu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-131">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="92ff3-132">Pokud `UseUrls` nebo `Listen` nazývá Kestrel naslouchá na porty určené pouze při spuštěné aplikaci bez služby IIS.</span><span class="sxs-lookup"><span data-stu-id="92ff3-132">If `UseUrls` or `Listen` is called, Kestrel listens on the ports specified only when running the app without IIS.</span></span>

<span data-ttu-id="92ff3-133">Další informace o modelech hostování a mimo proces, naleznete v tématu [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) a [odkaz Konfigurace modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="92ff3-133">For more information on the in-process and out-of-process hosting models, see [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) and [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="92ff3-134">`CreateDefaultBuilder` nakonfiguruje [Kestrel](xref:fundamentals/servers/kestrel) jako integrace webového serveru a umožňuje služby IIS tím, že nakonfigurujete základní cesty a port pro [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="92ff3-134">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) as the web server and enables IIS integration by configuring the base path and port for the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module).</span></span>

<span data-ttu-id="92ff3-135">Modul ASP.NET Core generuje dynamický port přiřadit k procesu back-endu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-135">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="92ff3-136">`CreateDefaultBuilder` volání [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) metody.</span><span class="sxs-lookup"><span data-stu-id="92ff3-136">`CreateDefaultBuilder` calls the [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) method.</span></span> <span data-ttu-id="92ff3-137">`UseIISIntegration` Nakonfiguruje Kestrel k naslouchání na dynamický port na IP adresu místního hostitele (`127.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="92ff3-137">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="92ff3-138">Pokud dynamický port je 1234, Kestrel naslouchá na `127.0.0.1:1234`.</span><span class="sxs-lookup"><span data-stu-id="92ff3-138">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="92ff3-139">Tato konfigurace nahrazuje jiné konfigurace adresy URL poskytnuté:</span><span class="sxs-lookup"><span data-stu-id="92ff3-139">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="92ff3-140">Rozhraní API od kestrel naslouchání</span><span class="sxs-lookup"><span data-stu-id="92ff3-140">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="92ff3-141">[Konfigurace](xref:fundamentals/configuration/index) (nebo [možnost příkazového řádku--adresy URL](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="92ff3-141">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="92ff3-142">Volání `UseUrls` nebo jeho Kestrel `Listen` rozhraní API nejsou povinné, když pomocí modulu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-142">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="92ff3-143">Pokud `UseUrls` nebo `Listen` nazývá Kestrel naslouchá na portu zadat jenom při spuštěné aplikaci bez služby IIS.</span><span class="sxs-lookup"><span data-stu-id="92ff3-143">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="92ff3-144">`CreateDefaultBuilder` nakonfiguruje [Kestrel](xref:fundamentals/servers/kestrel) jako integrace webového serveru a umožňuje služby IIS tím, že nakonfigurujete základní cesty a port pro [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="92ff3-144">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) as the web server and enables IIS integration by configuring the base path and port for the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module).</span></span>

<span data-ttu-id="92ff3-145">Modul ASP.NET Core generuje dynamický port přiřadit k procesu back-endu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-145">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="92ff3-146">`CreateDefaultBuilder` volání [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) metody.</span><span class="sxs-lookup"><span data-stu-id="92ff3-146">`CreateDefaultBuilder` calls the [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) method.</span></span> <span data-ttu-id="92ff3-147">`UseIISIntegration` Nakonfiguruje Kestrel k naslouchání na dynamický port na IP adresu místního hostitele (`localhost`).</span><span class="sxs-lookup"><span data-stu-id="92ff3-147">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`localhost`).</span></span> <span data-ttu-id="92ff3-148">Pokud dynamický port je 1234, Kestrel naslouchá na `localhost:1234`.</span><span class="sxs-lookup"><span data-stu-id="92ff3-148">If the dynamic port is 1234, Kestrel listens at `localhost:1234`.</span></span> <span data-ttu-id="92ff3-149">Tato konfigurace nahrazuje jiné konfigurace adresy URL poskytnuté:</span><span class="sxs-lookup"><span data-stu-id="92ff3-149">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="92ff3-150">Rozhraní API od kestrel naslouchání</span><span class="sxs-lookup"><span data-stu-id="92ff3-150">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="92ff3-151">[Konfigurace](xref:fundamentals/configuration/index) (nebo [možnost příkazového řádku--adresy URL](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="92ff3-151">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="92ff3-152">Volání `UseUrls` nebo jeho Kestrel `Listen` rozhraní API nejsou povinné, když pomocí modulu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-152">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="92ff3-153">Pokud `UseUrls` nebo `Listen` nazývá Kestrel naslouchá na portu zadat jenom při spuštěné aplikaci bez služby IIS.</span><span class="sxs-lookup"><span data-stu-id="92ff3-153">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="92ff3-154">Zahrnout závislost [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/) balíčku v závislosti aplikaci.</span><span class="sxs-lookup"><span data-stu-id="92ff3-154">Include a dependency on the [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/) package in the app's dependencies.</span></span> <span data-ttu-id="92ff3-155">Používat middleware pro integraci služby IIS tak, že přidáte [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) metodu rozšíření k [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder):</span><span class="sxs-lookup"><span data-stu-id="92ff3-155">Use IIS Integration middleware by adding the [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) extension method to [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder):</span></span>

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .UseIISIntegration()
    ...
```

<span data-ttu-id="92ff3-156">Obě [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) a [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) jsou povinné.</span><span class="sxs-lookup"><span data-stu-id="92ff3-156">Both [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) and [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) are required.</span></span> <span data-ttu-id="92ff3-157">Volání kódu `UseIISIntegration` nemá vliv na přenositelnost kódu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-157">Code calling `UseIISIntegration` doesn't affect code portability.</span></span> <span data-ttu-id="92ff3-158">Pokud aplikace není spuštěna za služby IIS (například spuštění aplikace přímo na Kestrel), `UseIISIntegration` nepracuje.</span><span class="sxs-lookup"><span data-stu-id="92ff3-158">If the app isn't run behind IIS (for example, the app is run directly on Kestrel), `UseIISIntegration` doesn't operate.</span></span>

<span data-ttu-id="92ff3-159">Modul ASP.NET Core generuje dynamický port přiřadit k procesu back-endu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-159">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="92ff3-160">`UseIISIntegration` Nakonfiguruje Kestrel k naslouchání na dynamický port na IP adresu místního hostitele (`localhost`).</span><span class="sxs-lookup"><span data-stu-id="92ff3-160">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`localhost`).</span></span> <span data-ttu-id="92ff3-161">Pokud dynamický port je 1234, Kestrel naslouchá na `localhost:1234`.</span><span class="sxs-lookup"><span data-stu-id="92ff3-161">If the dynamic port is 1234, Kestrel listens at `localhost:1234`.</span></span> <span data-ttu-id="92ff3-162">Tato konfigurace nahrazuje jiné konfigurace adresy URL poskytnuté:</span><span class="sxs-lookup"><span data-stu-id="92ff3-162">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* <span data-ttu-id="92ff3-163">[Konfigurace](xref:fundamentals/configuration/index) (nebo [možnost příkazového řádku--adresy URL](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="92ff3-163">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="92ff3-164">Volání `UseUrls` není povinné, při použití modulu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-164">A call to `UseUrls` isn't required when using the module.</span></span> <span data-ttu-id="92ff3-165">Pokud `UseUrls` nazývá Kestrel naslouchá na portu zadat jenom při spuštěné aplikaci bez služby IIS.</span><span class="sxs-lookup"><span data-stu-id="92ff3-165">If `UseUrls` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="92ff3-166">Pokud `UseUrls` je volána v aplikaci ASP.NET Core 1.0, volání **před** volání `UseIISIntegration` tak, aby port nakonfigurovaný modul není přepsán.</span><span class="sxs-lookup"><span data-stu-id="92ff3-166">If `UseUrls` is called in an ASP.NET Core 1.0 app, call it **before** calling `UseIISIntegration` so that the module-configured port isn't overwritten.</span></span> <span data-ttu-id="92ff3-167">Toto pořadí volání není požadován spolu s ASP.NET Core 1.1, protože modul nastavení přepíše `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="92ff3-167">This calling order isn't required with ASP.NET Core 1.1 because the module setting overrides `UseUrls`.</span></span>

::: moniker-end

<span data-ttu-id="92ff3-168">Další informace o hostování najdete v tématu [hostitele v ASP.NET Core](xref:fundamentals/host/index).</span><span class="sxs-lookup"><span data-stu-id="92ff3-168">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/host/index).</span></span>

### <a name="iis-options"></a><span data-ttu-id="92ff3-169">Možnosti služby IIS</span><span class="sxs-lookup"><span data-stu-id="92ff3-169">IIS options</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="92ff3-170">**Model hostingu v procesu**</span><span class="sxs-lookup"><span data-stu-id="92ff3-170">**In-process hosting model**</span></span>

<span data-ttu-id="92ff3-171">Konfigurace možností serveru služby IIS, zahrnují konfiguraci služby pro [IISServerOptions](/dotnet/api/microsoft.aspnetcore.builder.iisserveroptions) v [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices).</span><span class="sxs-lookup"><span data-stu-id="92ff3-171">To configure IIS Server options, include a service configuration for [IISServerOptions](/dotnet/api/microsoft.aspnetcore.builder.iisserveroptions) in [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices).</span></span> <span data-ttu-id="92ff3-172">Následující příklad zakazuje AutomaticAuthentication:</span><span class="sxs-lookup"><span data-stu-id="92ff3-172">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="92ff3-173">Možnost</span><span class="sxs-lookup"><span data-stu-id="92ff3-173">Option</span></span>                         | <span data-ttu-id="92ff3-174">Výchozí</span><span class="sxs-lookup"><span data-stu-id="92ff3-174">Default</span></span> | <span data-ttu-id="92ff3-175">Nastavení</span><span class="sxs-lookup"><span data-stu-id="92ff3-175">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="92ff3-176">Pokud `true`, nastaví Server služby IIS `HttpContext.User` ověřována [ověřování Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="92ff3-176">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="92ff3-177">Pokud `false`, server pouze poskytuje identitu `HttpContext.User` a reaguje na problémy při explicitním požadavku `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="92ff3-177">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="92ff3-178">Musí být povoleno ověřování Windows ve službě IIS pro `AutomaticAuthentication` na funkci.</span><span class="sxs-lookup"><span data-stu-id="92ff3-178">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="92ff3-179">Další informace najdete v tématu [ověřování Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="92ff3-179">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="92ff3-180">Nastaví zobrazovaný název, který se uživatelům na přihlašovací stránky zobrazí.</span><span class="sxs-lookup"><span data-stu-id="92ff3-180">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="92ff3-181">**Model hostingu mimo proces**</span><span class="sxs-lookup"><span data-stu-id="92ff3-181">**Out-of-process hosting model**</span></span>

::: moniker-end

<span data-ttu-id="92ff3-182">Ke konfiguraci možností služby IIS, zahrnují konfiguraci služby pro [IISOptions](/dotnet/api/microsoft.aspnetcore.builder.iisoptions) v [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices).</span><span class="sxs-lookup"><span data-stu-id="92ff3-182">To configure IIS options, include a service configuration for [IISOptions](/dotnet/api/microsoft.aspnetcore.builder.iisoptions) in [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices).</span></span> <span data-ttu-id="92ff3-183">Následující příklad brání aplikaci v naplnění `HttpContext.Connection.ClientCertificate`:</span><span class="sxs-lookup"><span data-stu-id="92ff3-183">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="92ff3-184">Možnost</span><span class="sxs-lookup"><span data-stu-id="92ff3-184">Option</span></span>                         | <span data-ttu-id="92ff3-185">Výchozí</span><span class="sxs-lookup"><span data-stu-id="92ff3-185">Default</span></span> | <span data-ttu-id="92ff3-186">Nastavení</span><span class="sxs-lookup"><span data-stu-id="92ff3-186">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="92ff3-187">Pokud `true`, nastaví Middleware pro integraci služby IIS `HttpContext.User` ověřována [ověřování Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="92ff3-187">If `true`, IIS Integration Middleware sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="92ff3-188">Pokud `false`, middleware pouze poskytuje identitu `HttpContext.User` a reaguje na problémy při explicitním požadavku `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="92ff3-188">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="92ff3-189">Musí být povoleno ověřování Windows ve službě IIS pro `AutomaticAuthentication` na funkci.</span><span class="sxs-lookup"><span data-stu-id="92ff3-189">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="92ff3-190">Další informace najdete v tématu [ověřování Windows](xref:security/authentication/windowsauth) tématu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-190">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="92ff3-191">Nastaví zobrazovaný název, který se uživatelům na přihlašovací stránky zobrazí.</span><span class="sxs-lookup"><span data-stu-id="92ff3-191">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="92ff3-192">Pokud `true` a `MS-ASPNETCORE-CLIENTCERT` hlavička požadavku je k dispozici, `HttpContext.Connection.ClientCertificate` naplnění.</span><span class="sxs-lookup"><span data-stu-id="92ff3-192">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="92ff3-193">Proxy server a scénáře pro nástroj pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="92ff3-193">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="92ff3-194">Middleware pro integraci služby IIS, který nakonfiguruje předané Middleware záhlaví a že modul ASP.NET Core jsou konfigurovány pro předávání schématu (HTTP/HTTPS) a Vzdálená IP adresa původu žádosti.</span><span class="sxs-lookup"><span data-stu-id="92ff3-194">The IIS Integration Middleware, which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="92ff3-195">Další konfigurace může být nezbytný pro aplikací hostovaných za službou další proxy servery a nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="92ff3-195">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="92ff3-196">Další informace najdete v tématu [konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="92ff3-196">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="92ff3-197">soubor Web.config</span><span class="sxs-lookup"><span data-stu-id="92ff3-197">web.config file</span></span>

<span data-ttu-id="92ff3-198">*Web.config* soubor nastaví [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="92ff3-198">The *web.config* file configures the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module).</span></span> <span data-ttu-id="92ff3-199">Vytváření, transformace a publikování *web.config* soubor se zpracovává souborem cíl nástroje MSBuild (`_TransformWebConfig`) při publikování projektu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-199">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="92ff3-200">Tento cíl je k dispozici v cílech Web SDK (`Microsoft.NET.Sdk.Web`).</span><span class="sxs-lookup"><span data-stu-id="92ff3-200">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="92ff3-201">V horní části souboru projektu je sada SDK:</span><span class="sxs-lookup"><span data-stu-id="92ff3-201">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="92ff3-202">Pokud *web.config* soubor není k dispozici v projektu, soubor, vytvoří se správnými *processPath* a *argumenty* ke konfiguraci [ASP.NET Core Modul](xref:fundamentals/servers/aspnet-core-module) a přesunout do [publikovaný výstup](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="92ff3-202">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="92ff3-203">Pokud *web.config* soubor je k dispozici v projektu, soubor se transformuje se správnými *processPath* a *argumenty* nakonfigurovat modul ASP.NET Core a přesunout do publikovaný výstup.</span><span class="sxs-lookup"><span data-stu-id="92ff3-203">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="92ff3-204">Transformace nezmění nastavení konfigurace služby IIS v souboru.</span><span class="sxs-lookup"><span data-stu-id="92ff3-204">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="92ff3-205">*Web.config* soubor může poskytovat další nastavení konfigurace služby IIS, která řídí aktivní moduly služby IIS.</span><span class="sxs-lookup"><span data-stu-id="92ff3-205">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="92ff3-206">Informace o službě IIS modulů, které dokáže zpracovávat požadavky s aplikací ASP.NET Core, najdete v článku [moduly IIS](xref:host-and-deploy/iis/modules) tématu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-206">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="92ff3-207">Zabránit transformace na sadu Web SDK *web.config* souboru, použijte  **\<IsTransformWebConfigDisabled >** vlastnost v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="92ff3-207">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="92ff3-208">Při zakazování Web SDK z transformaci souboru *processPath* a *argumenty* by měl být ručně nastavit vývojářem.</span><span class="sxs-lookup"><span data-stu-id="92ff3-208">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="92ff3-209">Další informace najdete v tématu [odkaz Konfigurace modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="92ff3-209">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module).</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="92ff3-210">Umístění souboru Web.config</span><span class="sxs-lookup"><span data-stu-id="92ff3-210">web.config file location</span></span>

<span data-ttu-id="92ff3-211">Pokud chcete nastavit [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) správně, *web.config* soubor musí být k dispozici v obsahu kořenové cestě (obvykle aplikace základní cesta) nasazené aplikace.</span><span class="sxs-lookup"><span data-stu-id="92ff3-211">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the content root path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="92ff3-212">Jedná se o stejné umístění jako fyzická cesta webu služby IIS k dispozici.</span><span class="sxs-lookup"><span data-stu-id="92ff3-212">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="92ff3-213">*Web.config* soubor je vyžadován v kořenovém adresáři aplikace, aby se daly publikovat víc aplikací pomocí nasazení webu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-213">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="92ff3-214">Citlivé soubory existují na fyzická cesta aplikace, jako například  *\<sestavení >. runtimeconfig.json*,  *\<sestavení > .xml* (komentáře dokumentace XML) a  *\<sestavení >. deps.json*.</span><span class="sxs-lookup"><span data-stu-id="92ff3-214">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="92ff3-215">Když *web.config* soubor je k dispozici a lokality spustí běžným způsobem, služba IIS bariéru tyto citlivé soubory, pokud jste požádali.</span><span class="sxs-lookup"><span data-stu-id="92ff3-215">When the *web.config* file is present and and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="92ff3-216">Pokud *web.config* soubor chybí, nesprávně pojmenované, nebo nelze konfigurovat lokalitu pro normální spuštění, služba IIS může poskytovat citlivé soubory veřejně.</span><span class="sxs-lookup"><span data-stu-id="92ff3-216">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="92ff3-217">***Web.config* soubor musí být k dispozici v nasazení za všech okolností, správně s názvem a nakonfigurovat web pro normální spuštění nahoru. Nikdy odebrat *web.config* soubor z produkčního nasazení.**</span><span class="sxs-lookup"><span data-stu-id="92ff3-217">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="92ff3-218">Konfigurace služby IIS</span><span class="sxs-lookup"><span data-stu-id="92ff3-218">IIS configuration</span></span>

<span data-ttu-id="92ff3-219">**Operační systémy Windows Server**</span><span class="sxs-lookup"><span data-stu-id="92ff3-219">**Windows Server operating systems**</span></span>

<span data-ttu-id="92ff3-220">Povolit **webového serveru (IIS)** role serveru a vytvoření služby rolí.</span><span class="sxs-lookup"><span data-stu-id="92ff3-220">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="92ff3-221">Použití **přidat role a funkce** z průvodce **spravovat** nabídky nebo na odkaz v **správce serveru**.</span><span class="sxs-lookup"><span data-stu-id="92ff3-221">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="92ff3-222">Na **role serveru** krok, zaškrtněte políčko u **webového serveru (IIS)**.</span><span class="sxs-lookup"><span data-stu-id="92ff3-222">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![V kroku výběr serveru role je vybrána role webového serveru IIS.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="92ff3-224">Po **funkce** kroku **služeb rolí** krok načte pro webový Server (IIS).</span><span class="sxs-lookup"><span data-stu-id="92ff3-224">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="92ff3-225">Vyberte požadovaných služeb role služby IIS nebo přijměte výchozí nastavení role služby za předpokladu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-225">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Výchozí služby rolí jsou vybrané v kroku vybrat roli služby.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="92ff3-227">**Ověřování Windows (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="92ff3-227">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="92ff3-228">Pokud chcete povolit ověřování Windows, rozbalte následující uzly: **Webový Server** > **zabezpečení**.</span><span class="sxs-lookup"><span data-stu-id="92ff3-228">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="92ff3-229">Vyberte **ověřování Windows** funkce.</span><span class="sxs-lookup"><span data-stu-id="92ff3-229">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="92ff3-230">Další informace najdete v tématu [ověřování Windows \<windowsAuthentication >](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Windows nakonfigurovat ověřování](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="92ff3-230">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="92ff3-231">**Protokoly Websocket (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="92ff3-231">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="92ff3-232">Protokoly Websocket je podporována s ASP.NET Core 1.1 nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="92ff3-232">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="92ff3-233">Pokud chcete povolit protokoly Websocket, rozbalte následující uzly: **Webový Server** > **vývoj aplikací**.</span><span class="sxs-lookup"><span data-stu-id="92ff3-233">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="92ff3-234">Vyberte **protokol WebSocket** funkce.</span><span class="sxs-lookup"><span data-stu-id="92ff3-234">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="92ff3-235">Další informace najdete v tématu [objekty Websocket](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="92ff3-235">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="92ff3-236">Pokračujte **potvrzení** krok instalace role webového serveru a služby.</span><span class="sxs-lookup"><span data-stu-id="92ff3-236">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="92ff3-237">Po instalaci se nevyžaduje restartování serveru/IIS **webového serveru (IIS)** role.</span><span class="sxs-lookup"><span data-stu-id="92ff3-237">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="92ff3-238">**Operační systémy Windows**</span><span class="sxs-lookup"><span data-stu-id="92ff3-238">**Windows desktop operating systems**</span></span>

<span data-ttu-id="92ff3-239">Povolit **konzolu pro správu IIS** a **webové služby**.</span><span class="sxs-lookup"><span data-stu-id="92ff3-239">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="92ff3-240">Přejděte do **ovládací panely** > **programy** > **programy a funkce** > **zapnout Windows funkce na nebo vypnout** (levé straně obrazovky).</span><span class="sxs-lookup"><span data-stu-id="92ff3-240">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="92ff3-241">Otevřít **Internetová informační služba** uzlu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-241">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="92ff3-242">Otevřít **nástroje webové správy** uzlu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-242">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="92ff3-243">Zaškrtněte políčko u **konzolu pro správu IIS**.</span><span class="sxs-lookup"><span data-stu-id="92ff3-243">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="92ff3-244">Zaškrtněte políčko u **webové služby**.</span><span class="sxs-lookup"><span data-stu-id="92ff3-244">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="92ff3-245">Přijměte výchozí funkce pro **webové služby** nebo přizpůsobení funkcí služby IIS.</span><span class="sxs-lookup"><span data-stu-id="92ff3-245">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="92ff3-246">**Ověřování Windows (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="92ff3-246">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="92ff3-247">Pokud chcete povolit ověřování Windows, rozbalte následující uzly: **webové služby** > **zabezpečení**.</span><span class="sxs-lookup"><span data-stu-id="92ff3-247">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="92ff3-248">Vyberte **ověřování Windows** funkce.</span><span class="sxs-lookup"><span data-stu-id="92ff3-248">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="92ff3-249">Další informace najdete v tématu [ověřování Windows \<windowsAuthentication >](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Windows nakonfigurovat ověřování](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="92ff3-249">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="92ff3-250">**Protokoly Websocket (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="92ff3-250">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="92ff3-251">Protokoly Websocket je podporována s ASP.NET Core 1.1 nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="92ff3-251">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="92ff3-252">Pokud chcete povolit protokoly Websocket, rozbalte následující uzly: **webové služby** > **funkce pro vývoj aplikací**.</span><span class="sxs-lookup"><span data-stu-id="92ff3-252">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="92ff3-253">Vyberte **protokol WebSocket** funkce.</span><span class="sxs-lookup"><span data-stu-id="92ff3-253">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="92ff3-254">Další informace najdete v tématu [objekty Websocket](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="92ff3-254">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="92ff3-255">Pokud instalace služby IIS vyžaduje restartování, restartujte systém.</span><span class="sxs-lookup"><span data-stu-id="92ff3-255">If the IIS installation requires a restart, restart the system.</span></span>

![Konzola pro správu služby IIS a webové služby jsou vybrány v funkce Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="92ff3-257">Instalace .NET Core, který je hostitelem svazku</span><span class="sxs-lookup"><span data-stu-id="92ff3-257">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="92ff3-258">Nainstalujte *sady hostování rozhraní .NET Core* v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="92ff3-258">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="92ff3-259">Nainstaluje sady .NET Core Runtime, knihovny .NET Core a [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="92ff3-259">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module).</span></span> <span data-ttu-id="92ff3-260">Povolí modul ASP.NET Core aplikací ke spuštění za služby IIS.</span><span class="sxs-lookup"><span data-stu-id="92ff3-260">The module allows ASP.NET Core apps to run behind IIS.</span></span> <span data-ttu-id="92ff3-261">Pokud systém nemá připojení k Internetu, získejte a nainstalujte [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/download/details.aspx?id=53840) před instalací sady hostování rozhraní .NET Core.</span><span class="sxs-lookup"><span data-stu-id="92ff3-261">If the system doesn't have an Internet connection, obtain and install the [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/download/details.aspx?id=53840) before installing the .NET Core Hosting Bundle.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="92ff3-262">Pokud před službou IIS instalovanou sadou hostování, je nutné opravit instalaci sady.</span><span class="sxs-lookup"><span data-stu-id="92ff3-262">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="92ff3-263">Spusťte instalační program sady hostování znovu po instalaci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="92ff3-263">Run the Hosting Bundle installer again after installing IIS.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="92ff3-264">Přímé stažení (aktuální verze)</span><span class="sxs-lookup"><span data-stu-id="92ff3-264">Direct download (current version)</span></span>

<span data-ttu-id="92ff3-265">Stažení instalačního programu pomocí následujícího odkazu:</span><span class="sxs-lookup"><span data-stu-id="92ff3-265">Download the installer using the following link:</span></span>

[<span data-ttu-id="92ff3-266">Aktuální instalační program sady hostování rozhraní .NET Core (s přímým přístupem ke stažení)</span><span class="sxs-lookup"><span data-stu-id="92ff3-266">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="92ff3-267">Starší verze instalačního programu</span><span class="sxs-lookup"><span data-stu-id="92ff3-267">Earlier versions of the installer</span></span>

<span data-ttu-id="92ff3-268">Chcete-li získat starší verzi instalačního programu:</span><span class="sxs-lookup"><span data-stu-id="92ff3-268">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="92ff3-269">Přejděte [.NET stáhnout archivy](https://www.microsoft.com/net/download/archives).</span><span class="sxs-lookup"><span data-stu-id="92ff3-269">Navigate to the [.NET download archives](https://www.microsoft.com/net/download/archives).</span></span>
1. <span data-ttu-id="92ff3-270">V části **.NET Core**, vyberte verzi .NET Core.</span><span class="sxs-lookup"><span data-stu-id="92ff3-270">Under **.NET Core**, select the .NET Core version.</span></span>
1. <span data-ttu-id="92ff3-271">V **spuštění aplikací – prostředí Runtime** sloupce, vyhledejte řádek požadované verze modulu runtime .NET Core.</span><span class="sxs-lookup"><span data-stu-id="92ff3-271">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="92ff3-272">Stáhněte si instalační program s použitím **Runtime & hostování svazek** odkaz.</span><span class="sxs-lookup"><span data-stu-id="92ff3-272">Download the installer using the **Runtime & Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="92ff3-273">Některé instalační programy obsahují verze, které bylo dosaženo jejich konci životnosti (konce řádku) a již nejsou podporovány společností Microsoft.</span><span class="sxs-lookup"><span data-stu-id="92ff3-273">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="92ff3-274">Další informace najdete v tématu [zásady podpory](https://www.microsoft.com/net/download/dotnet-core/2.0).</span><span class="sxs-lookup"><span data-stu-id="92ff3-274">For more information, see the [support policy](https://www.microsoft.com/net/download/dotnet-core/2.0).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="92ff3-275">Instalaci sady hostování</span><span class="sxs-lookup"><span data-stu-id="92ff3-275">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="92ff3-276">Spusťte instalační program na serveru.</span><span class="sxs-lookup"><span data-stu-id="92ff3-276">Run the installer on the server.</span></span> <span data-ttu-id="92ff3-277">Při spuštění instalačního programu z příkazového řádku správce jsou k dispozici následující přepínače:</span><span class="sxs-lookup"><span data-stu-id="92ff3-277">The following switches are available when running the installer from an administrator command prompt:</span></span>

   * <span data-ttu-id="92ff3-278">`OPT_NO_ANCM=1` &ndash; Instalace modulu jádra ASP.NET přeskočit.</span><span class="sxs-lookup"><span data-stu-id="92ff3-278">`OPT_NO_ANCM=1` &ndash; Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="92ff3-279">`OPT_NO_RUNTIME=1` &ndash; Instalace modulu runtime .NET Core přeskočit.</span><span class="sxs-lookup"><span data-stu-id="92ff3-279">`OPT_NO_RUNTIME=1` &ndash; Skip installing the .NET Core runtime.</span></span>
   * <span data-ttu-id="92ff3-280">`OPT_NO_SHAREDFX=1` &ndash; Přeskočit instalaci rozhraní ASP.NET sdílené (modul runtime technologie ASP.NET).</span><span class="sxs-lookup"><span data-stu-id="92ff3-280">`OPT_NO_SHAREDFX=1` &ndash; Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span>
   * <span data-ttu-id="92ff3-281">`OPT_NO_X86=1` &ndash; X86 instalace přeskočit runtimes.</span><span class="sxs-lookup"><span data-stu-id="92ff3-281">`OPT_NO_X86=1` &ndash; Skip installing x86 runtimes.</span></span> <span data-ttu-id="92ff3-282">Tento přepínač použijte, když víte, že vám nebude hostitelem 32bitových aplikací.</span><span class="sxs-lookup"><span data-stu-id="92ff3-282">Use this switch when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="92ff3-283">Pokud je pravděpodobné, že 32-bit a 64-bit aplikací budou v budoucnu umístěny, není tento přepínač a nainstalovat obě runtimes.</span><span class="sxs-lookup"><span data-stu-id="92ff3-283">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this switch and install both runtimes.</span></span>
1. <span data-ttu-id="92ff3-284">Restartování systému nebo spuštění **net stop byla /y** následovaný **net start w3svc** z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="92ff3-284">Restart the system or execute **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span> <span data-ttu-id="92ff3-285">Restartování služby IIS příjmem změnu systému provedené CESTU, která je proměnná prostředí, instalační služby.</span><span class="sxs-lookup"><span data-stu-id="92ff3-285">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="92ff3-286">Pokud instalační služby systému Windows, který je hostitelem svazku zjistí, že služba IIS dokončení instalace vyžaduje obnovení, obnoví instalační program služby IIS.</span><span class="sxs-lookup"><span data-stu-id="92ff3-286">If the Windows Hosting Bundle installer detects that IIS requires a reset in order to complete installation, the installer resets IIS.</span></span> <span data-ttu-id="92ff3-287">Pokud instalační program spustí resetování služby IIS, se restartují všechny fondy aplikací IIS a websites.</span><span class="sxs-lookup"><span data-stu-id="92ff3-287">If the installer triggers an IIS reset, all of the IIS app pools and websites are restarted.</span></span>

> [!NOTE]
> <span data-ttu-id="92ff3-288">Informace o sdílenou konfiguraci aplikaci IIS najdete v tématu [modul ASP.NET Core s sdílenou konfiguraci aplikaci IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="92ff3-288">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="92ff3-289">Nainstalujte nástroj nasazení webu při publikování pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92ff3-289">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="92ff3-290">Při nasazování aplikací na servery s [Webdeploy](/iis/publish/using-web-deploy/introduction-to-web-deploy), nainstalujte nejnovější verzi nástroje nasazení webu na serveru.</span><span class="sxs-lookup"><span data-stu-id="92ff3-290">When deploying apps to servers with [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="92ff3-291">Chcete-li nainstalujte nástroj nasazení webu, použijte [instalačního programu webové platformy (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) nebo získat přímo z instalačního programu [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span><span class="sxs-lookup"><span data-stu-id="92ff3-291">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="92ff3-292">Upřednostňovanou metodou je použití instalace webové platformy.</span><span class="sxs-lookup"><span data-stu-id="92ff3-292">The preferred method is to use WebPI.</span></span> <span data-ttu-id="92ff3-293">Instalace webové platformy nabízí samostatné instalace a konfigurace pro poskytovatele hostingu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-293">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="92ff3-294">Vytvořte web služby IIS</span><span class="sxs-lookup"><span data-stu-id="92ff3-294">Create the IIS site</span></span>

1. <span data-ttu-id="92ff3-295">V hostitelském systému vytvořte složku obsahující soubory a složky publikované aplikace.</span><span class="sxs-lookup"><span data-stu-id="92ff3-295">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="92ff3-296">Rozložení nasazení vaší aplikace je popsána v [adresářovou strukturu](xref:host-and-deploy/directory-structure) tématu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-296">An app's deployment layout is described in the [Directory Structure](xref:host-and-deploy/directory-structure) topic.</span></span>

1. <span data-ttu-id="92ff3-297">V rámci novou složku vytvořit *protokoly* složku pro uložení protokolů stdout modul ASP.NET Core, pokud je povoleno protokolování stdout.</span><span class="sxs-lookup"><span data-stu-id="92ff3-297">Within the new folder, create a *logs* folder to hold ASP.NET Core Module stdout logs when stdout logging is enabled.</span></span> <span data-ttu-id="92ff3-298">Pokud je aplikace nasazena s *protokoly* složek v datové části, tento krok přeskočit.</span><span class="sxs-lookup"><span data-stu-id="92ff3-298">If the app is deployed with a *logs* folder in the payload, skip this step.</span></span> <span data-ttu-id="92ff3-299">Pokyny o tom, jak povolit MSBuild k vytvoření *protokoly* složku automaticky, pokud je projekt je sestaven místně, najdete v článku [adresářovou strukturu](xref:host-and-deploy/directory-structure) tématu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-299">For instructions on how to enable MSBuild to create the *logs* folder automatically when the project is built locally, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="92ff3-300">Pouze pomocí protokolu stdout řešení potíží s chybami při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="92ff3-300">Only use the stdout log to troubleshoot app startup failures.</span></span> <span data-ttu-id="92ff3-301">Nikdy nepoužívejte stdout protokolování pro protokolování běžné aplikace.</span><span class="sxs-lookup"><span data-stu-id="92ff3-301">Never use stdout logging for routine app logging.</span></span> <span data-ttu-id="92ff3-302">Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="92ff3-302">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="92ff3-303">Fond aplikací musí mít oprávnění k zápisu do umístění, kde se zapisují protokoly.</span><span class="sxs-lookup"><span data-stu-id="92ff3-303">The app pool must have write access to the location where the logs are written.</span></span> <span data-ttu-id="92ff3-304">Všechny složky na cestu k umístění protokolu musí existovat.</span><span class="sxs-lookup"><span data-stu-id="92ff3-304">All of the folders on the path to the log location must exist.</span></span> <span data-ttu-id="92ff3-305">Další informace o protokolu stdout najdete v tématu [vytváření a přesměrování protokolu](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span><span class="sxs-lookup"><span data-stu-id="92ff3-305">For more information on the stdout log, see [Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span></span> <span data-ttu-id="92ff3-306">Informace o protokolování v aplikaci ASP.NET Core, najdete v článku [protokolování](xref:fundamentals/logging/index) tématu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-306">For information on logging in an ASP.NET Core app, see the [Logging](xref:fundamentals/logging/index) topic.</span></span>

1. <span data-ttu-id="92ff3-307">V **Správce služby IIS**, otevřete uzel serveru v **připojení** panelu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-307">In **IIS Manager**, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="92ff3-308">Klikněte pravým tlačítkem myši **lokality** složky.</span><span class="sxs-lookup"><span data-stu-id="92ff3-308">Right-click the **Sites** folder.</span></span> <span data-ttu-id="92ff3-309">Vyberte **přidat web** z kontextové nabídky.</span><span class="sxs-lookup"><span data-stu-id="92ff3-309">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="92ff3-310">Zadejte **název lokality** a nastavit **fyzická cesta** do složky pro nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="92ff3-310">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="92ff3-311">Zadejte **vazby** konfigurace a vytvořit na webu výběrem **OK**:</span><span class="sxs-lookup"><span data-stu-id="92ff3-311">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![Zadejte název lokality, fyzickou cestu a název hostitele v kroku přidat web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="92ff3-313">Vazby nejvyšší úrovně zástupný znak (`http://*:80/` a `http://+:80`) by měl **není** použít.</span><span class="sxs-lookup"><span data-stu-id="92ff3-313">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="92ff3-314">Vazby nejvyšší úrovně zástupný znak můžete otevřít aplikaci tak, aby slabá místa zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="92ff3-314">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="92ff3-315">To platí pro silné a slabé zástupné znaky.</span><span class="sxs-lookup"><span data-stu-id="92ff3-315">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="92ff3-316">Používejte explicitní hostitele názvy místo zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="92ff3-316">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="92ff3-317">Vazby zástupný znak subdoménu (například `*.mysub.com`) nemá toto bezpečnostní riziko, pokud celé nadřazené domény (nikoli `*.com`, což je ohrožené).</span><span class="sxs-lookup"><span data-stu-id="92ff3-317">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="92ff3-318">Zobrazit [rfc7230 části-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) Další informace.</span><span class="sxs-lookup"><span data-stu-id="92ff3-318">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="92ff3-319">V uzlu server, vyberte **fondy aplikací**.</span><span class="sxs-lookup"><span data-stu-id="92ff3-319">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="92ff3-320">Klikněte pravým tlačítkem na fond aplikací webu a vyberte **základní nastavení** z kontextové nabídky.</span><span class="sxs-lookup"><span data-stu-id="92ff3-320">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="92ff3-321">V **upravit fond aplikací** okno, nastaveno **verze .NET CLR** k **bez spravovaného kódu**:</span><span class="sxs-lookup"><span data-stu-id="92ff3-321">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![Nastavit bez spravovaného kódu pro verze .NET CLR.](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="92ff3-323">ASP.NET Core běží v samostatném procesu a spravuje modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="92ff3-323">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="92ff3-324">ASP.NET Core nemusí spoléhat na načítání desktop CLR.</span><span class="sxs-lookup"><span data-stu-id="92ff3-324">ASP.NET Core doesn't rely on loading the desktop CLR.</span></span> <span data-ttu-id="92ff3-325">Nastavení **verze .NET CLR** k **bez spravovaného kódu** je volitelný.</span><span class="sxs-lookup"><span data-stu-id="92ff3-325">Setting the **.NET CLR version** to **No Managed Code** is optional.</span></span>

1. <span data-ttu-id="92ff3-326">Potvrďte, že identita model procesu má příslušná oprávnění.</span><span class="sxs-lookup"><span data-stu-id="92ff3-326">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="92ff3-327">Pokud výchozí identita fondu aplikací (**Model procesu** > **Identity**) se změní z **ApplicationPoolIdentity** na jinou identitu, ověřte, že nové identity má požadovaná oprávnění pro přístup ke složce aplikace, databáze a ostatní požadované prostředky.</span><span class="sxs-lookup"><span data-stu-id="92ff3-327">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="92ff3-328">Například fond aplikací vyžaduje čtení a zápisu do složky, kde aplikace čte a zapisuje soubory.</span><span class="sxs-lookup"><span data-stu-id="92ff3-328">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="92ff3-329">**Konfigurace ověřování Windows (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="92ff3-329">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="92ff3-330">Další informace najdete v tématu [Windows nakonfigurovat ověřování](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="92ff3-330">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="92ff3-331">Nasazení aplikace</span><span class="sxs-lookup"><span data-stu-id="92ff3-331">Deploy the app</span></span>

<span data-ttu-id="92ff3-332">Nasazení aplikace do složky vytvořené v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="92ff3-332">Deploy the app to the folder created on the hosting system.</span></span> <span data-ttu-id="92ff3-333">[Webu nasadit](/iis/publish/using-web-deploy/introduction-to-web-deploy) je doporučené mechanismus pro nasazení.</span><span class="sxs-lookup"><span data-stu-id="92ff3-333">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="92ff3-334">Nasazení webu pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92ff3-334">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="92ff3-335">Zobrazit [profily publikování sady Visual Studio pro nasazení aplikace ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) téma a zjistěte, jak vytvořit profil publikování pro použití s nasazením webu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-335">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="92ff3-336">Pokud poskytovatel hostingu poskytuje pro vytvoření nového profilu pro publikování nebo podporu, stáhněte si svůj profil a importujte ho pomocí sady Visual Studio **publikovat** dialogového okna.</span><span class="sxs-lookup"><span data-stu-id="92ff3-336">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog.</span></span>

![Publikovat stránku dialogového okna](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="92ff3-338">Nasazení webu mimo sadu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92ff3-338">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="92ff3-339">[Webu nasadit](/iis/publish/using-web-deploy/introduction-to-web-deploy) lze také použít mimo sadu Visual Studio z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="92ff3-339">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="92ff3-340">Další informace najdete v tématu [nástroje Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span><span class="sxs-lookup"><span data-stu-id="92ff3-340">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="92ff3-341">Alternativy k webové nasazení</span><span class="sxs-lookup"><span data-stu-id="92ff3-341">Alternatives to Web Deploy</span></span>

<span data-ttu-id="92ff3-342">Použijte několik metod pro přesun aplikace k hostování systému, jako je ruční export, příkazu Xcopy, Robocopy nebo prostředí PowerShell.</span><span class="sxs-lookup"><span data-stu-id="92ff3-342">Use any of several methods to move the app to the hosting system, such as manual copy, Xcopy, Robocopy, or PowerShell.</span></span>

<span data-ttu-id="92ff3-343">Další informace o nasazení ASP.NET Core do služby IIS, najdete v článku [materiály pro nasazení pro správce služby IIS](#deployment-resources-for-iis-administrators) oddílu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-343">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="92ff3-344">Přejděte na web</span><span class="sxs-lookup"><span data-stu-id="92ff3-344">Browse the website</span></span>

![Prohlížeč Microsoft Edge načetl úvodní stránka služby IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="92ff3-346">Soubory uzamčené nasazení</span><span class="sxs-lookup"><span data-stu-id="92ff3-346">Locked deployment files</span></span>

<span data-ttu-id="92ff3-347">Při spuštění aplikace jsou zamknuté soubory ve složce pro nasazení.</span><span class="sxs-lookup"><span data-stu-id="92ff3-347">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="92ff3-348">Uzamčené soubory nemohou být přepsána během nasazení.</span><span class="sxs-lookup"><span data-stu-id="92ff3-348">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="92ff3-349">Uvolnit uzamčené soubory v nasazení, zastavte aplikaci pomocí fondu **jeden** z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="92ff3-349">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="92ff3-350">Pomocí nasazení webu a odkaz `Microsoft.NET.Sdk.Web` v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-350">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="92ff3-351">*App_offline.htm* soubor umístěn v kořenovém adresáři webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="92ff3-351">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="92ff3-352">Pokud soubor existuje, modul ASP.NET Core řádně ukončí aplikaci, slouží *app_offline.htm* souboru během nasazení.</span><span class="sxs-lookup"><span data-stu-id="92ff3-352">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="92ff3-353">Další informace najdete v tématu [odkaz Konfigurace modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="92ff3-353">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="92ff3-354">Ručně zastavte fond aplikací ve Správci služby IIS na serveru.</span><span class="sxs-lookup"><span data-stu-id="92ff3-354">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="92ff3-355">Použití Powershellu k vyřadit *app_offline.html* (vyžaduje PowerShell 5 nebo novější):</span><span class="sxs-lookup"><span data-stu-id="92ff3-355">Use PowerShell to drop *app_offline.html* (requires PowerShell 5 or later):</span></span>

  ```PowerShell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="92ff3-356">Ochrana dat</span><span class="sxs-lookup"><span data-stu-id="92ff3-356">Data protection</span></span>

<span data-ttu-id="92ff3-357">[Ochranu dat ASP.NET Core zásobníku](xref:security/data-protection/introduction) používá několik ASP.NET Core [middlewares](xref:fundamentals/middleware/index), včetně middleware použitý v ověřování.</span><span class="sxs-lookup"><span data-stu-id="92ff3-357">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="92ff3-358">I v případě, že Data Protection API nejsou volané kódem uživatele, se skriptem nasazení nebo v uživatelském kódu k vytvoření trvalé by měl nakonfigurovat ochranu dat kryptografických [úložiště klíčů](xref:security/data-protection/implementation/key-management).</span><span class="sxs-lookup"><span data-stu-id="92ff3-358">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="92ff3-359">Pokud není nakonfigurovaná ochrana dat, jsou klíče uložené v paměti a při restartování aplikace.</span><span class="sxs-lookup"><span data-stu-id="92ff3-359">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="92ff3-360">Pokud kanál klíče jsou uloženy v paměti, při restartování aplikace:</span><span class="sxs-lookup"><span data-stu-id="92ff3-360">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="92ff3-361">Všechny tokeny ověřování na základě souborů cookie nejsou zneplatněny.</span><span class="sxs-lookup"><span data-stu-id="92ff3-361">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="92ff3-362">Uživatelé se musí znovu přihlásit v jejich další požadavek.</span><span class="sxs-lookup"><span data-stu-id="92ff3-362">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="92ff3-363">Všechna data chráněná pomocí aktualizační kanál, který klíč můžete už nebude možné dešifrovat.</span><span class="sxs-lookup"><span data-stu-id="92ff3-363">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="92ff3-364">To může zahrnovat [CSRF tokeny](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [soubory cookie v ASP.NET Core MVC TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="92ff3-364">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="92ff3-365">Chcete-li konfigurovat ochranu dat v rámci služby IIS k uchování aktualizační kanál, který klíč, použijte **jeden** z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="92ff3-365">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="92ff3-366">**Vytvoření klíče registru ochrany dat**</span><span class="sxs-lookup"><span data-stu-id="92ff3-366">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="92ff3-367">Používá aplikace ASP.NET Core klíče ochrany dat jsou uložené v registru, které jsou externí vzhledem k aplikacím.</span><span class="sxs-lookup"><span data-stu-id="92ff3-367">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="92ff3-368">Pokud chcete zachovat klíče pro danou aplikaci, vytvořte klíče registru pro fond aplikací.</span><span class="sxs-lookup"><span data-stu-id="92ff3-368">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="92ff3-369">Pro samostatnou, instalace služby IIS – webové farmě, [skript prostředí PowerShell AutoGenKeys.ps1 poskytování ochrany dat](https://github.com/aspnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) lze použít pro každý fond aplikací, které jsou součástí aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92ff3-369">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/aspnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="92ff3-370">Tento skript vytvoří klíč registru HKLM registru, který je přístupný pouze pro účet pracovního procesu fondu aplikací aplikaci.</span><span class="sxs-lookup"><span data-stu-id="92ff3-370">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="92ff3-371">Klíče se zašifrují neaktivní uložená data pomocí rozhraní DPAPI klíčem celý počítač.</span><span class="sxs-lookup"><span data-stu-id="92ff3-371">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="92ff3-372">Ve webových farem lze nastavit aplikaci pro použití cesty UNC pro ukládání jeho data protection klíč kanál.</span><span class="sxs-lookup"><span data-stu-id="92ff3-372">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="92ff3-373">Ve výchozím nastavení klíče ochrany dat nejsou šifrovány.</span><span class="sxs-lookup"><span data-stu-id="92ff3-373">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="92ff3-374">Zajistěte, aby byly omezené na účet Windows, které aplikace běží pod oprávnění pro sdílené síťové složce.</span><span class="sxs-lookup"><span data-stu-id="92ff3-374">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="92ff3-375">X X509 certifikát můžete použít k ochraně klíčů v klidovém stavu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-375">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="92ff3-376">Vezměte v úvahu mechanismus pro uživatelům umožní nahrát certifikáty: místo certifikátů do důvěryhodného certifikátu uživatele ukládat a ujistěte se, jsou k dispozici na všech počítačích, ve kterém běží aplikace uživatele.</span><span class="sxs-lookup"><span data-stu-id="92ff3-376">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="92ff3-377">Zobrazit [Konfigurace ochrany dat ASP.NET Core](xref:security/data-protection/configuration/overview) podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="92ff3-377">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="92ff3-378">**Konfigurace fondu aplikací služby IIS se načíst profil uživatele**</span><span class="sxs-lookup"><span data-stu-id="92ff3-378">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="92ff3-379">Toto nastavení je v **Model procesu** části **Upřesnit nastavení** pro fond aplikací.</span><span class="sxs-lookup"><span data-stu-id="92ff3-379">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="92ff3-380">Načíst profil uživatele nastaveno `True`.</span><span class="sxs-lookup"><span data-stu-id="92ff3-380">Set Load User Profile to `True`.</span></span> <span data-ttu-id="92ff3-381">To ukládá klíče v části adresář profilu uživatele a chrání je pomocí rozhraní DPAPI klíčem specifické pro uživatelský účet je používána ve fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="92ff3-381">This stores keys under the user profile directory and protects them using DPAPI with a key specific to the user account used by the app pool.</span></span>

* <span data-ttu-id="92ff3-382">**Systém souborů jako kanál klíč úložiště**</span><span class="sxs-lookup"><span data-stu-id="92ff3-382">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="92ff3-383">Upravit kód aplikace, který [používat systém souborů jako kanál klíč úložiště](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="92ff3-383">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="92ff3-384">Použijte X509 certifikátu k ochraně klíče kanál a zajistit certifikát není důvěryhodný certifikát.</span><span class="sxs-lookup"><span data-stu-id="92ff3-384">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="92ff3-385">Pokud certifikát podepsaný svým držitelem, můžete certifikát umístěte do úložiště důvěryhodných kořenových certifikátů.</span><span class="sxs-lookup"><span data-stu-id="92ff3-385">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="92ff3-386">Pokud používáte IIS ve webové farmě:</span><span class="sxs-lookup"><span data-stu-id="92ff3-386">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="92ff3-387">Použití sdílené složky, ke kterému přístup všechny počítače.</span><span class="sxs-lookup"><span data-stu-id="92ff3-387">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="92ff3-388">Nasazení x X509 certifikát pro každý počítač.</span><span class="sxs-lookup"><span data-stu-id="92ff3-388">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="92ff3-389">Konfigurace [ochrany dat v kódu](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="92ff3-389">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="92ff3-390">**Nastavte zásady pro celý počítač pro ochranu dat**</span><span class="sxs-lookup"><span data-stu-id="92ff3-390">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="92ff3-391">Systém ochrany dat má omezenou podporu pro nastavení výchozí [celého zásad](xref:security/data-protection/configuration/machine-wide-policy) pro všechny aplikace, které používání rozhraní Data Protection API.</span><span class="sxs-lookup"><span data-stu-id="92ff3-391">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="92ff3-392">Další informace naleznete v tématu <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="92ff3-392">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="92ff3-393">Virtuální adresáře</span><span class="sxs-lookup"><span data-stu-id="92ff3-393">Virtual Directories</span></span>

<span data-ttu-id="92ff3-394">[Virtuální složky IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) nejsou podporované s aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92ff3-394">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="92ff3-395">Aplikace je možné hostovat jako [dílčí aplikace](#sub-applications).</span><span class="sxs-lookup"><span data-stu-id="92ff3-395">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="92ff3-396">Dílčí aplikace</span><span class="sxs-lookup"><span data-stu-id="92ff3-396">Sub-applications</span></span>

<span data-ttu-id="92ff3-397">Je možné hostovat aplikace ASP.NET Core jako [dílčí aplikace služby IIS (podřízeným aplikacím)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span><span class="sxs-lookup"><span data-stu-id="92ff3-397">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="92ff3-398">Sub – aplikace cesta stane součástí adresy URL kořenového aplikace.</span><span class="sxs-lookup"><span data-stu-id="92ff3-398">The sub-app's path becomes part of the root app's URL.</span></span>

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="92ff3-399">Odběr aplikace by neměl obsahovat modul ASP.NET Core jako obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="92ff3-399">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="92ff3-400">Pokud modul je přidán jako obslužná rutina sub-aplikace *web.config* souboru, *500.19 vnitřní chyba serveru* odkazující na chybného konfiguračního souboru obdržíte, když se pokusíte o procházení podřízeným aplikacím.</span><span class="sxs-lookup"><span data-stu-id="92ff3-400">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="92ff3-401">Následující příklad ukazuje publikování *web.config* sub aplikace ASP.NET Core v souboru:</span><span class="sxs-lookup"><span data-stu-id="92ff3-401">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="92ff3-402">Při hostování za nástrojem sub aplikace ASP.NET Core pod aplikace ASP.NET Core, sub-aplikace explicitně odebrat obslužnou rutinu zděděné *web.config* souboru:</span><span class="sxs-lookup"><span data-stu-id="92ff3-402">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

::: moniker-end

<span data-ttu-id="92ff3-403">Odkazy statických prostředků v rámci podřízeným aplikacím měli použít lomítko tilda (`~/`) notaci.</span><span class="sxs-lookup"><span data-stu-id="92ff3-403">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="92ff3-404">Triggery notation tilda lomítko [pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) pro předřazení pathbase, je-sub aplikace pro vykreslený relativní odkaz.</span><span class="sxs-lookup"><span data-stu-id="92ff3-404">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="92ff3-405">Pro odběr aplikace na `/subapp_path`, bitovou kopii propojené s `src="~/image.png"` se vykreslí jako `src="/subapp_path/image.png"`.</span><span class="sxs-lookup"><span data-stu-id="92ff3-405">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="92ff3-406">Middleware kořenové aplikace statické soubory nelze zpracovat požadavek statický soubor.</span><span class="sxs-lookup"><span data-stu-id="92ff3-406">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="92ff3-407">Žádost zpracovává Middleware sub aplikace statické soubory.</span><span class="sxs-lookup"><span data-stu-id="92ff3-407">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="92ff3-408">Pokud statický prostředek `src` atribut je nastaven na absolutní cestu (například `src="/image.png"`), odkaz je vykreslen bez pathbase, je-sub aplikace.</span><span class="sxs-lookup"><span data-stu-id="92ff3-408">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="92ff3-409">Middleware kořenové aplikace statické soubory pokusí sloužit asset z kořenové aplikace [webroot](xref:fundamentals/index#web-root-webroot), výsledek bude *404 - Nenalezeno* odpovědi Pokud statických prostředků není k dispozici z kořenové aplikace.</span><span class="sxs-lookup"><span data-stu-id="92ff3-409">The root app's Static File Middleware attempts to serve the asset from the root app's [webroot](xref:fundamentals/index#web-root-webroot), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="92ff3-410">K hostování aplikace v ASP.NET Core jako podřízeným aplikacím v rámci jiné aplikace ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="92ff3-410">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="92ff3-411">Vytvořte fond aplikací pro aplikaci sub.</span><span class="sxs-lookup"><span data-stu-id="92ff3-411">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="92ff3-412">Nastavte **verze .NET CLR** k **žádný spravovaný kód**.</span><span class="sxs-lookup"><span data-stu-id="92ff3-412">Set the **.NET CLR Version** to **No Managed Code**.</span></span>

1. <span data-ttu-id="92ff3-413">Přidání kořenového webu s podřízeným aplikacím v rámci kořenového webu ve Správci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="92ff3-413">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="92ff3-414">Klikněte pravým tlačítkem na složku podřízeným aplikacím ve Správci služby IIS a vyberte **převést na aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="92ff3-414">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="92ff3-415">V **přidat aplikaci** dialogového okna, použijte **vyberte** tlačítko pro **fond aplikací** přiřadit fondu aplikací, kterou jste vytvořili pro podřízeným aplikacím.</span><span class="sxs-lookup"><span data-stu-id="92ff3-415">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="92ff3-416">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="92ff3-416">Select **OK**.</span></span>

<span data-ttu-id="92ff3-417">Přiřazení fondu samostatné aplikace k podřízeným aplikacím je požadavek, pokud používáte model hostingu v procesu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-417">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="92ff3-418">Další informace o procesu model hostingu a konfigurace modulu ASP.NET Core najdete v tématu <xref:fundamentals/servers/aspnet-core-module> a <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="92ff3-418">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:fundamentals/servers/aspnet-core-module> and <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="92ff3-419">Konfigurace služby IIS pomocí souboru web.config</span><span class="sxs-lookup"><span data-stu-id="92ff3-419">Configuration of IIS with web.config</span></span>

<span data-ttu-id="92ff3-420">Konfigurace služby IIS je ovlivněno  **\<system.webServer >** část *web.config* pro tyto funkce služby IIS, které platí pro konfigurace reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="92ff3-420">IIS configuration is influenced by the **\<system.webServer>** section of *web.config* for those IIS features that apply to a reverse proxy configuration.</span></span> <span data-ttu-id="92ff3-421">Pokud je služba IIS konfigurována na úrovni serveru na použití dynamické komprese  **\<urlCompression >** element ve vaší aplikaci *web.config* souboru ji zakázat.</span><span class="sxs-lookup"><span data-stu-id="92ff3-421">If IIS is configured at the server level to use dynamic compression, the **\<urlCompression>** element in the app's *web.config* file can disable it.</span></span>

<span data-ttu-id="92ff3-422">Další informace najdete v tématu [odkaz Konfigurace pro \<system.webServer >](/iis/configuration/system.webServer/), [odkaz Konfigurace modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module), a [moduly IIS s ASP.NET Základní](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="92ff3-422">For more information, see the [configuration reference for \<system.webServer>](/iis/configuration/system.webServer/), [ASP.NET Core Module Configuration Reference](xref:host-and-deploy/aspnet-core-module), and [IIS Modules with ASP.NET Core](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="92ff3-423">Chcete-li nastavit proměnné prostředí pro jednotlivé aplikace spuštěných ve fondech izolované aplikace (podporováno pro IIS 10.0 a novější), najdete v článku *AppCmd.exe příkaz* část [proměnné prostředí \< environmentVariables >](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) téma v IIS referenční dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="92ff3-423">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="92ff3-424">Konfigurační oddíly souboru Web.config</span><span class="sxs-lookup"><span data-stu-id="92ff3-424">Configuration sections of web.config</span></span>

<span data-ttu-id="92ff3-425">Konfigurační oddíly funkce ASP.NET 4.x aplikací v *web.config* nejsou používány aplikací ASP.NET Core pro konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="92ff3-425">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* <span data-ttu-id="92ff3-426">**\<system.web>**</span><span class="sxs-lookup"><span data-stu-id="92ff3-426">**\<system.web>**</span></span>
* <span data-ttu-id="92ff3-427">**\<appSettings>**</span><span class="sxs-lookup"><span data-stu-id="92ff3-427">**\<appSettings>**</span></span>
* <span data-ttu-id="92ff3-428">**\<connectionStrings >**</span><span class="sxs-lookup"><span data-stu-id="92ff3-428">**\<connectionStrings>**</span></span>
* <span data-ttu-id="92ff3-429">**\<umístění >**</span><span class="sxs-lookup"><span data-stu-id="92ff3-429">**\<location>**</span></span>

<span data-ttu-id="92ff3-430">Aplikace ASP.NET Core jsou nakonfigurované pomocí jiných poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="92ff3-430">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="92ff3-431">Další informace najdete v tématu [konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="92ff3-431">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="92ff3-432">Fondy aplikací</span><span class="sxs-lookup"><span data-stu-id="92ff3-432">Application Pools</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="92ff3-433">Izolace fond aplikací je určeno model hostingu:</span><span class="sxs-lookup"><span data-stu-id="92ff3-433">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="92ff3-434">Proces hostování &ndash; aplikace jsou potřeba ke spouštění ve fondech samostatné aplikace.</span><span class="sxs-lookup"><span data-stu-id="92ff3-434">In-process hosting &ndash; Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="92ff3-435">Hostování mimo proces &ndash; doporučujeme izoluje aplikace od sebe navzájem spuštěním každou aplikaci ve vlastním fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="92ff3-435">Out-of-process hosting &ndash; We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="92ff3-436">IIS **přidat web** výchozí dialogové okno aplikace s jedním fondu na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="92ff3-436">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="92ff3-437">Když **název lokality** je k dispozici text je automaticky převedena do **fond aplikací** textového pole.</span><span class="sxs-lookup"><span data-stu-id="92ff3-437">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="92ff3-438">Nový fond aplikací je vytvořený pomocí názvu serveru po přidání serveru.</span><span class="sxs-lookup"><span data-stu-id="92ff3-438">A new app pool is created using the site name when the site is added.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="92ff3-439">Při hostování více webů na serveru, doporučujeme izoluje aplikace od sebe navzájem spuštěním každou aplikaci ve vlastním fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="92ff3-439">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="92ff3-440">IIS **přidat web** výchozí dialogové okno pro tuto konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="92ff3-440">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="92ff3-441">Když **název lokality** je k dispozici text je automaticky převedena do **fond aplikací** textového pole.</span><span class="sxs-lookup"><span data-stu-id="92ff3-441">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="92ff3-442">Nový fond aplikací je vytvořený pomocí názvu serveru po přidání serveru.</span><span class="sxs-lookup"><span data-stu-id="92ff3-442">A new app pool is created using the site name when the site is added.</span></span>

::: moniker-end

## <a name="application-pool-identity"></a><span data-ttu-id="92ff3-443">Identita fondu aplikací</span><span class="sxs-lookup"><span data-stu-id="92ff3-443">Application Pool Identity</span></span>

<span data-ttu-id="92ff3-444">Účet identita fondu aplikací umožňuje aplikaci běžet pod účtem jedinečný bez nutnosti vytvářet a spravovat domény nebo místní účty.</span><span class="sxs-lookup"><span data-stu-id="92ff3-444">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="92ff3-445">IIS 8.0 nebo novější procesů pro pracovníka Správce služby IIS (WAS) vytvoří virtuální účet s názvem nového fondu aplikací a aplikace spouští fondu pracovních procesů pod tímto účtem ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="92ff3-445">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="92ff3-446">V konzole pro správu služby IIS v části **Upřesnit nastavení** pro fond aplikací, ujistěte se, že **Identity** nastaveno pro použití **ApplicationPoolIdentity**:</span><span class="sxs-lookup"><span data-stu-id="92ff3-446">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![Dialogové okno pokročilé nastavení fondu aplikací](index/_static/apppool-identity.png)

<span data-ttu-id="92ff3-448">Proces správy služby IIS vytvoří zabezpečeného identifikátoru se název fondu aplikací v zabezpečení systému Windows.</span><span class="sxs-lookup"><span data-stu-id="92ff3-448">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="92ff3-449">Pomocí této identity, je možné svázat prostředky.</span><span class="sxs-lookup"><span data-stu-id="92ff3-449">Resources can be secured using this identity.</span></span> <span data-ttu-id="92ff3-450">Ale tato identita není skutečný účet a nezobrazí se v konzole pro správu uživatelů Windows.</span><span class="sxs-lookup"><span data-stu-id="92ff3-450">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="92ff3-451">Pokud pracovní proces služby IIS vyžaduje přístup k aplikaci přes se zvýšenými oprávněními, upravte seznam řízení přístupu (ACL) pro adresář obsahující aplikaci:</span><span class="sxs-lookup"><span data-stu-id="92ff3-451">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="92ff3-452">Otevřete Průzkumníka Windows a přejděte do adresáře.</span><span class="sxs-lookup"><span data-stu-id="92ff3-452">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="92ff3-453">Klikněte pravým tlačítkem na adresář a vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="92ff3-453">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="92ff3-454">V části **zabezpečení** kartu, vyberte **upravit** tlačítko a pak **přidat** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="92ff3-454">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="92ff3-455">Vyberte **umístění** tlačítko a ujistěte se, že je vybraná systému.</span><span class="sxs-lookup"><span data-stu-id="92ff3-455">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="92ff3-456">Zadejte **fondu aplikací služby IIS\\< app_pool_name >** v **zadejte názvy objektů k výběru** oblasti.</span><span class="sxs-lookup"><span data-stu-id="92ff3-456">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="92ff3-457">Vyberte **Kontrola názvů** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="92ff3-457">Select the **Check Names** button.</span></span> <span data-ttu-id="92ff3-458">Pro *DefaultAppPool* zkontrolovat názvy pomocí **IIS AppPool\DefaultAppPool**.</span><span class="sxs-lookup"><span data-stu-id="92ff3-458">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="92ff3-459">Při **Kontrola názvů** se vybere tlačítko, hodnota **DefaultAppPool** je uveden v oblasti názvy objektu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-459">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="92ff3-460">Není možné zadat název fondu aplikací přímo do oblasti názvy objektu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-460">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="92ff3-461">Použití **fondu aplikací služby IIS\\< app_pool_name >** formátování při vyhledávání pro název objektu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-461">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![Vyberte uživatele nebo skupiny dialogové okno pro složky aplikace: název fondu aplikací "DefaultAppPool" se připojí k "fondu aplikací služby IIS\" v oblasti názvy objektu před výběrem"Zkontrolujte názvy."](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="92ff3-463">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="92ff3-463">Select **OK**.</span></span>

   ![Vyberte uživatele nebo skupiny dialogové okno pro složky aplikace: Po výběru "Kontrola názvů", "DefaultAppPool" se zobrazí v objektu název objektu názvy oblasti.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="92ff3-465">Čtení &amp; provést ve výchozím nastavení by měl být udělena oprávnění.</span><span class="sxs-lookup"><span data-stu-id="92ff3-465">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="92ff3-466">Zadejte další oprávnění podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="92ff3-466">Provide additional permissions as needed.</span></span>

<span data-ttu-id="92ff3-467">Na příkazovém řádku pomocí můžete také udělit přístup **ICACLS** nástroj.</span><span class="sxs-lookup"><span data-stu-id="92ff3-467">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="92ff3-468">Použití *DefaultAppPool* jako příklad slouží následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="92ff3-468">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="92ff3-469">Další informace najdete v tématu [icacls](/windows-server/administration/windows-commands/icacls) tématu.</span><span class="sxs-lookup"><span data-stu-id="92ff3-469">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="92ff3-470">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="92ff3-470">HTTP/2 support</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="92ff3-471">[HTTP/2](https://httpwg.org/specs/rfc7540.html) se podporuje s ASP.NET Core v následujících scénářích nasazení služby IIS:</span><span class="sxs-lookup"><span data-stu-id="92ff3-471">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="92ff3-472">V procesu</span><span class="sxs-lookup"><span data-stu-id="92ff3-472">In-process</span></span>
  * <span data-ttu-id="92ff3-473">Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="92ff3-473">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="92ff3-474">Protokol TLS 1.2 nebo vyšší připojení</span><span class="sxs-lookup"><span data-stu-id="92ff3-474">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="92ff3-475">Mimo proces</span><span class="sxs-lookup"><span data-stu-id="92ff3-475">Out-of-process</span></span>
  * <span data-ttu-id="92ff3-476">Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="92ff3-476">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="92ff3-477">Připojení k serveru edge veřejně přístupných pomocí protokolu HTTP/2, ale reverzní proxy server připojení k [Kestrel server](xref:fundamentals/servers/kestrel) používá HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="92ff3-477">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="92ff3-478">Protokol TLS 1.2 nebo vyšší připojení</span><span class="sxs-lookup"><span data-stu-id="92ff3-478">TLS 1.2 or later connection</span></span>

<span data-ttu-id="92ff3-479">V procesu nasazení po vytvoření připojení k protokolu HTTP/2 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="92ff3-479">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="92ff3-480">Mimo proces nasazení po vytvoření připojení k protokolu HTTP/2 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/1.1`.</span><span class="sxs-lookup"><span data-stu-id="92ff3-480">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="92ff3-481">Další informace o modelech hostování a mimo proces, najdete v článku <xref:fundamentals/servers/aspnet-core-module> tématu a <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="92ff3-481">For more information on the in-process and out-of-process hosting models, see the <xref:fundamentals/servers/aspnet-core-module> topic and the <xref:host-and-deploy/aspnet-core-module>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="92ff3-482">[HTTP/2](https://httpwg.org/specs/rfc7540.html) se podporuje pro nasazení mimo proces, které splňují základní požadavky na následující:</span><span class="sxs-lookup"><span data-stu-id="92ff3-482">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="92ff3-483">Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="92ff3-483">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="92ff3-484">Připojení k serveru edge veřejně přístupných pomocí protokolu HTTP/2, ale reverzní proxy server připojení k [Kestrel server](xref:fundamentals/servers/kestrel) používá HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="92ff3-484">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="92ff3-485">Cílová architektura: není k dispozici pro nasazení mimo proces, protože připojení HTTP/2 je zpracována zcela službou IIS.</span><span class="sxs-lookup"><span data-stu-id="92ff3-485">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="92ff3-486">Protokol TLS 1.2 nebo vyšší připojení</span><span class="sxs-lookup"><span data-stu-id="92ff3-486">TLS 1.2 or later connection</span></span>

<span data-ttu-id="92ff3-487">Pokud se připojení HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/1.1`.</span><span class="sxs-lookup"><span data-stu-id="92ff3-487">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

::: moniker-end

<span data-ttu-id="92ff3-488">HTTP/2 je standardně povolená.</span><span class="sxs-lookup"><span data-stu-id="92ff3-488">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="92ff3-489">Připojení vrátit zpět k protokolu HTTP/1.1, pokud nedojde k připojení k protokolu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="92ff3-489">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="92ff3-490">Další informace o konfiguraci protokolu HTTP/2 u nasazení ve službě IIS najdete v části [HTTP/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="92ff3-490">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="92ff3-491">Materiály pro nasazení pro správce služby IIS</span><span class="sxs-lookup"><span data-stu-id="92ff3-491">Deployment resources for IIS administrators</span></span>

<span data-ttu-id="92ff3-492">Další informace o službě IIS hlouběji v dokumentaci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="92ff3-492">Learn about IIS in-depth in the IIS documentation.</span></span>  
[<span data-ttu-id="92ff3-493">Dokumentace ke službě IIS</span><span class="sxs-lookup"><span data-stu-id="92ff3-493">IIS documentation</span></span>](/iis)

<span data-ttu-id="92ff3-494">Další informace o modelech nasazení aplikace .NET Core.</span><span class="sxs-lookup"><span data-stu-id="92ff3-494">Learn about .NET Core app deployment models.</span></span>  
[<span data-ttu-id="92ff3-495">Nasazení aplikace .NET core</span><span class="sxs-lookup"><span data-stu-id="92ff3-495">.NET Core application deployment</span></span>](/dotnet/core/deploying/)

<span data-ttu-id="92ff3-496">Zjistěte, jak modul ASP.NET Core umožňuje webovému serveru Kestrel k použití jako reverzní proxy server služby IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="92ff3-496">Learn how the ASP.NET Core Module allows the Kestrel web server to use IIS or IIS Express as a reverse proxy server.</span></span>  
[<span data-ttu-id="92ff3-497">Modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="92ff3-497">ASP.NET Core Module</span></span>](xref:fundamentals/servers/aspnet-core-module)

<span data-ttu-id="92ff3-498">Zjistěte, jak nakonfigurovat modul ASP.NET Core pro hostování aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92ff3-498">Learn how to configure the ASP.NET Core Module for hosting ASP.NET Core apps.</span></span>  
[<span data-ttu-id="92ff3-499">Referenční dokumentace k modulu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="92ff3-499">ASP.NET Core Module configuration reference</span></span>](xref:host-and-deploy/aspnet-core-module)

<span data-ttu-id="92ff3-500">Další informace o struktuře adresářů publikované aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92ff3-500">Learn about the directory structure of published ASP.NET Core apps.</span></span>  
[<span data-ttu-id="92ff3-501">Adresářová struktura</span><span class="sxs-lookup"><span data-stu-id="92ff3-501">Directory structure</span></span>](xref:host-and-deploy/directory-structure)

<span data-ttu-id="92ff3-502">Zjistěte aktivní i neaktivní moduly IIS pro aplikace ASP.NET Core a jak spravovat moduly služby IIS.</span><span class="sxs-lookup"><span data-stu-id="92ff3-502">Discover active and inactive IIS modules for ASP.NET Core apps and how to manage IIS modules.</span></span>  
[<span data-ttu-id="92ff3-503">Moduly služby IIS</span><span class="sxs-lookup"><span data-stu-id="92ff3-503">IIS modules</span></span>](xref:host-and-deploy/iis/troubleshoot)

<span data-ttu-id="92ff3-504">Zjistěte, jak diagnostikovat problémy se službou IIS nasazení aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92ff3-504">Learn how to diagnose problems with IIS deployments of ASP.NET Core apps.</span></span>  
[<span data-ttu-id="92ff3-505">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="92ff3-505">Troubleshoot</span></span>](xref:host-and-deploy/iis/troubleshoot)

<span data-ttu-id="92ff3-506">Rozlišení běžných chyb při hostování aplikací ASP.NET Core ve službě IIS.</span><span class="sxs-lookup"><span data-stu-id="92ff3-506">Distinguish common errors when hosting ASP.NET Core apps on IIS.</span></span>  
[<span data-ttu-id="92ff3-507">Referenční informace k běžným chybám u služeb Azure App Service a IIS</span><span class="sxs-lookup"><span data-stu-id="92ff3-507">Common errors reference for Azure App Service and IIS</span></span>](xref:host-and-deploy/azure-iis-errors-reference)

## <a name="additional-resources"></a><span data-ttu-id="92ff3-508">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="92ff3-508">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="92ff3-509">Úvod do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="92ff3-509">Introduction to ASP.NET Core</span></span>](xref:index)
* [<span data-ttu-id="92ff3-510">Lokality oficiální Microsoft IIS</span><span class="sxs-lookup"><span data-stu-id="92ff3-510">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="92ff3-511">Technická knihovna obsahu Windows serveru</span><span class="sxs-lookup"><span data-stu-id="92ff3-511">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="92ff3-512">HTTP/2 ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="92ff3-512">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
