---
title: Hostování mimo procesy pomocí služby IIS a ASP.NET Core
author: rick-anderson
description: Přečtěte si o mimoprocesových hostování se službou IIS a modulem ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
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
uid: host-and-deploy/iis/out-of-process-hosting
ms.openlocfilehash: 048a18349de4d784ae4abb33bd86a2d9c08c609d
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755274"
---
# <a name="out-of-process-hosting-with-iis-and-aspnet-core"></a><span data-ttu-id="4133e-103">Hostování mimo procesy pomocí služby IIS a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4133e-103">Out-of-process hosting with IIS and ASP.NET Core</span></span> 

<span data-ttu-id="4133e-104">Vzhledem k tomu, že ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS, modul ASP.NET Core zpracovává správu procesů.</span><span class="sxs-lookup"><span data-stu-id="4133e-104">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="4133e-105">Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud se ukončí nebo dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="4133e-105">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="4133e-106">To je v podstatě stejné chování jako u aplikací, které běží v procesu, které jsou spravovány službou was [(Windows Process Activation Service)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="4133e-106">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="4133e-107">Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací hostovanou mimo proces:</span><span class="sxs-lookup"><span data-stu-id="4133e-107">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core modul ve scénáři hostování mimo proces](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="4133e-109">Požadavky přicházející z webu do ovladače HTTP.sys režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="4133e-109">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="4133e-110">Ovladač směruje požadavky do služby IIS na konfigurovaném portu webu.</span><span class="sxs-lookup"><span data-stu-id="4133e-110">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="4133e-111">Nakonfigurovaný port je obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="4133e-111">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="4133e-112">Modul předá požadavky do Kestrel na náhodném portu pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4133e-112">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="4133e-113">Náhodný port není 80 nebo 443.</span><span class="sxs-lookup"><span data-stu-id="4133e-113">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="4133e-114">Modul ASP.NET Core Určuje port prostřednictvím proměnné prostředí při spuštění.</span><span class="sxs-lookup"><span data-stu-id="4133e-114">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="4133e-115"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A>Rozšíření nakonfiguruje server tak, aby naslouchal `http://localhost:{PORT}` .</span><span class="sxs-lookup"><span data-stu-id="4133e-115">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="4133e-116">Budou provedeny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="4133e-116">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="4133e-117">Modul nepodporuje předávání HTTPS.</span><span class="sxs-lookup"><span data-stu-id="4133e-117">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="4133e-118">Požadavky jsou předávány přes protokol HTTP i v případě, že jsou přijímány službou IIS prostřednictvím protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="4133e-118">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="4133e-119">Jakmile Kestrel požadavek z modulu přijme, požadavek se přepošle do kanálu middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4133e-119">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="4133e-120">Kanál middlewaru zpracovává požadavek a předá ho jako `HttpContext` instanci do logiky aplikace.</span><span class="sxs-lookup"><span data-stu-id="4133e-120">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="4133e-121">Middleware přidaný integrací služby IIS: aktualizace schématu, vzdálené IP adresy a pathbase pro přesměrování požadavku do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="4133e-121">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="4133e-122">Odpověď aplikace se předává zpátky do služby IIS, která je předává zpátky klientovi HTTP, který žádost inicioval.</span><span class="sxs-lookup"><span data-stu-id="4133e-122">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="4133e-123">Pokyny ke konfiguraci ASP.NET Core modulu najdete v tématu <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="4133e-123">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="4133e-124">Další informace o hostování najdete v tématu [hostitel v ASP.NET Core](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="4133e-124">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="4133e-125">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="4133e-125">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="4133e-126">Povolit komponenty IISIntegration</span><span class="sxs-lookup"><span data-stu-id="4133e-126">Enable the IISIntegration components</span></span>

<span data-ttu-id="4133e-127">Při sestavování hostitele v `CreateHostBuilder` ( `Program.cs` ) zavolejte <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> na Povolit integraci služby IIS:</span><span class="sxs-lookup"><span data-stu-id="4133e-127">When building a host in `CreateHostBuilder` (`Program.cs`), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="4133e-128">Další informace o `CreateDefaultBuilder` naleznete v tématu <xref:fundamentals/host/generic-host#default-builder-settings> .</span><span class="sxs-lookup"><span data-stu-id="4133e-128">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>


<span data-ttu-id="4133e-129">**Model hostování mimo proces**</span><span class="sxs-lookup"><span data-stu-id="4133e-129">**Out-of-process hosting model**</span></span>

<span data-ttu-id="4133e-130">Pokud chcete nakonfigurovat možnosti služby IIS, zahrňte do nástroje konfiguraci služby pro <xref:Microsoft.AspNetCore.Builder.IISOptions> <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> .</span><span class="sxs-lookup"><span data-stu-id="4133e-130">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="4133e-131">Následující příklad zabrání aplikaci v naplnění `HttpContext.Connection.ClientCertificate` :</span><span class="sxs-lookup"><span data-stu-id="4133e-131">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="4133e-132">Možnost</span><span class="sxs-lookup"><span data-stu-id="4133e-132">Option</span></span>                         | <span data-ttu-id="4133e-133">Výchozí</span><span class="sxs-lookup"><span data-stu-id="4133e-133">Default</span></span> | <span data-ttu-id="4133e-134">Nastavení</span><span class="sxs-lookup"><span data-stu-id="4133e-134">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="4133e-135">Pokud `true` [Služba IIS Integration middleware](#enable-the-iisintegration-components) nastaví ověření `HttpContext.User` [ověřováním systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="4133e-135">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="4133e-136">`false`V případě, middleware poskytuje pouze identitu pro `HttpContext.User` a reaguje na výzvy, pokud je explicitně vyžádala `AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="4133e-136">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="4133e-137">Aby mohla služba fungovat, musí být ve službě IIS povolená možnost ověřování systému Windows `AutomaticAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="4133e-137">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="4133e-138">Další informace najdete v tématu [ověřování systému Windows](xref:security/authentication/windowsauth) .</span><span class="sxs-lookup"><span data-stu-id="4133e-138">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="4133e-139">Nastaví zobrazovaný název, který se zobrazí uživatelům na přihlašovacích stránkách.</span><span class="sxs-lookup"><span data-stu-id="4133e-139">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="4133e-140">Pokud `true` je k `MS-ASPNETCORE-CLIENTCERT` dispozici hlavička žádosti, `HttpContext.Connection.ClientCertificate` je naplněna hodnota.</span><span class="sxs-lookup"><span data-stu-id="4133e-140">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |


### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="4133e-141">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="4133e-141">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="4133e-142">[Middleware pro integraci služby IIS](#enable-the-iisintegration-components) a modul ASP.NET Core jsou nakonfigurované pro přeposílání:</span><span class="sxs-lookup"><span data-stu-id="4133e-142">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="4133e-143">Schéma (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="4133e-143">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="4133e-144">Vzdálená IP adresa, ze které pochází požadavek.</span><span class="sxs-lookup"><span data-stu-id="4133e-144">Remote IP address where the request originated.</span></span>

<span data-ttu-id="4133e-145">Middleware pro [integraci služby IIS](#enable-the-iisintegration-components) konfiguruje middleware předávaných hlaviček.</span><span class="sxs-lookup"><span data-stu-id="4133e-145">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="4133e-146">Pro aplikace hostované za dalšími proxy servery a nástroji pro vyrovnávání zatížení může být vyžadována další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="4133e-146">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="4133e-147">Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="4133e-147">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>


### <a name="out-of-process-hosting-model"></a><span data-ttu-id="4133e-148">Model hostování mimo proces</span><span class="sxs-lookup"><span data-stu-id="4133e-148">Out-of-process hosting model</span></span>

<span data-ttu-id="4133e-149">Chcete-li nakonfigurovat aplikaci pro hostování mimo procesy, nastavte hodnotu `<AspNetCoreHostingModel>` vlastnosti na `OutOfProcess` soubor projektu ( `.csproj` ):</span><span class="sxs-lookup"><span data-stu-id="4133e-149">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="4133e-150">Hostování v rámci procesu je nastaveno na `InProcess` , což je výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="4133e-150">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="4133e-151">Hodnota rozlišuje `<AspNetCoreHostingModel>` malá a velká písmena, takže `inprocess` a `outofprocess` jsou platné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="4133e-151">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="4133e-152">[Kestrel](xref:fundamentals/servers/kestrel) Místo http serveru IIS () se používá server Kestrel `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="4133e-152">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="4133e-153">Pro procesy mimo proces [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> :</span><span class="sxs-lookup"><span data-stu-id="4133e-153">For out-of-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> to:</span></span>

* <span data-ttu-id="4133e-154">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4133e-154">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="4133e-155">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="4133e-155">Configure the host to capture startup errors.</span></span>

### <a name="process-name"></a><span data-ttu-id="4133e-156">Název procesu</span><span class="sxs-lookup"><span data-stu-id="4133e-156">Process name</span></span>

<span data-ttu-id="4133e-157">`Process.GetCurrentProcess().ProcessName`sestavy `w3wp` / `iisexpress` (v procesu) nebo `dotnet` (mimo proces).</span><span class="sxs-lookup"><span data-stu-id="4133e-157">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="4133e-158">Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní.</span><span class="sxs-lookup"><span data-stu-id="4133e-158">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="4133e-159">Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules> .</span><span class="sxs-lookup"><span data-stu-id="4133e-159">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="4133e-160">Modul ASP.NET Core může také:</span><span class="sxs-lookup"><span data-stu-id="4133e-160">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="4133e-161">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="4133e-161">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="4133e-162">Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.</span><span class="sxs-lookup"><span data-stu-id="4133e-162">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="4133e-163">Předejte tokeny ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="4133e-163">Forward Windows authentication tokens.</span></span>
