---
title: Hostování v rámci procesu pomocí služby IIS a ASP.NET Core
author: rick-anderson
description: Seznamte se s hostováním v procesu pomocí služby IIS a modulu ASP.NET Core.
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
uid: host-and-deploy/iis/in-process-hosting
ms.openlocfilehash: ecf873e6ad2044aae87a5e7dc07316eae0e10912
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755241"
---
# <a name="in-process-hosting-with-iis-and-aspnet-core"></a><span data-ttu-id="a3868-103">Hostování v rámci procesu pomocí služby IIS a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a3868-103">In-process hosting with IIS and ASP.NET Core</span></span> 

<span data-ttu-id="a3868-104">Hostování v rámci procesu spouští aplikaci ASP.NET Core ve stejném procesu jako její pracovní proces služby IIS.</span><span class="sxs-lookup"><span data-stu-id="a3868-104">In-process hosting runs an ASP.NET Core app in the same process as its IIS worker process.</span></span> <span data-ttu-id="a3868-105">Hostování v rámci procesů poskytují lepší výkon než hostování mimo procesy, protože požadavky nejsou proxy serverem přes adaptér zpětné smyčky, síťové rozhraní, které vrátí odchozí síťový provoz zpátky do stejného počítače.</span><span class="sxs-lookup"><span data-stu-id="a3868-105">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="a3868-106">Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací hostovanou v procesu:</span><span class="sxs-lookup"><span data-stu-id="a3868-106">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![ASP.NET Core modul ve scénáři hostování v rámci procesu](index/_static/ancm-inprocess.png)

## <a name="enable-in-process-hosting"></a><span data-ttu-id="a3868-108">Povolit hostování v procesu</span><span class="sxs-lookup"><span data-stu-id="a3868-108">Enable in-process hosting</span></span>

<span data-ttu-id="a3868-109">Vzhledem k tomu, že ASP.NET Core 3,0, je vnitroprocesové hostování ve výchozím nastavení povoleno pro všechny aplikace nasazené ve službě IIS.</span><span class="sxs-lookup"><span data-stu-id="a3868-109">Since ASP.NET Core 3.0, in-process hosting has been enabled by default for all app deployed to IIS.</span></span>

<span data-ttu-id="a3868-110">Chcete-li explicitně nakonfigurovat aplikaci pro hostování v rámci procesu, nastavte hodnotu `<AspNetCoreHostingModel>` vlastnosti na `InProcess` soubor projektu ( `.csproj` ):</span><span class="sxs-lookup"><span data-stu-id="a3868-110">To explicitly configure an app for in-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `InProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

## <a name="general-architecture"></a><span data-ttu-id="a3868-111">Obecná architektura</span><span class="sxs-lookup"><span data-stu-id="a3868-111">General architecture</span></span>

<span data-ttu-id="a3868-112">Obecný tok žádosti je následující:</span><span class="sxs-lookup"><span data-stu-id="a3868-112">The general flow of a request is as follows:</span></span>

1. <span data-ttu-id="a3868-113">Požadavek přijde z webu do ovladače HTTP.sys režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="a3868-113">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="a3868-114">Ovladač směruje nativní požadavek na IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="a3868-114">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="a3868-115">Modul ASP.NET Core obdrží nativní požadavek a předá ho k serveru HTTP služby IIS ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="a3868-115">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="a3868-116">HTTP server IIS je vnitroprocesové implementace v rámci procesového serveru pro službu IIS, která převádí požadavek z nativního na spravovanou.</span><span class="sxs-lookup"><span data-stu-id="a3868-116">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="a3868-117">Poté, co server HTTP služby IIS zpracuje požadavek:</span><span class="sxs-lookup"><span data-stu-id="a3868-117">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="a3868-118">Požadavek se odešle do kanálu middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a3868-118">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="a3868-119">Kanál middlewaru zpracovává požadavek a předá ho jako `HttpContext` instanci do logiky aplikace.</span><span class="sxs-lookup"><span data-stu-id="a3868-119">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="a3868-120">Odpověď aplikace se předává zpět službě IIS prostřednictvím serveru IIS HTTP.</span><span class="sxs-lookup"><span data-stu-id="a3868-120">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="a3868-121">Služba IIS odešle odpověď klientovi, který žádost inicioval.</span><span class="sxs-lookup"><span data-stu-id="a3868-121">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="a3868-122">`CreateDefaultBuilder` přidá <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instanci voláním <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> metody pro spuštění [CoreCLR](/dotnet/standard/glossary#coreclr) a hostování aplikace uvnitř pracovního procesu služby IIS ( `w3wp.exe` nebo `iisexpress.exe` ).</span><span class="sxs-lookup"><span data-stu-id="a3868-122">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (`w3wp.exe` or `iisexpress.exe`).</span></span> <span data-ttu-id="a3868-123">Testy výkonu označují, že hostování aplikace .NET Core v rámci procesu přináší výrazně vyšší propustnost žádostí v porovnání s hostováním aplikace mimo proces a požadavky na proxy server do [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="a3868-123">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="a3868-124">Aplikace publikované jako spustitelný soubor s jedním souborem nejde načíst pomocí modelu hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="a3868-124">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="a3868-125">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="a3868-125">Application configuration</span></span>

<span data-ttu-id="a3868-126">Pokud chcete nakonfigurovat možnosti služby IIS, zahrňte do nástroje konfiguraci služby pro <xref:Microsoft.AspNetCore.Builder.IISServerOptions> <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> .</span><span class="sxs-lookup"><span data-stu-id="a3868-126">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="a3868-127">Následující příklad zakáže AutomaticAuthentication:</span><span class="sxs-lookup"><span data-stu-id="a3868-127">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="a3868-128">Možnost</span><span class="sxs-lookup"><span data-stu-id="a3868-128">Option</span></span> | <span data-ttu-id="a3868-129">Výchozí</span><span class="sxs-lookup"><span data-stu-id="a3868-129">Default</span></span> | <span data-ttu-id="a3868-130">Nastavení</span><span class="sxs-lookup"><span data-stu-id="a3868-130">Setting</span></span> |
| ------ | :-----: | ------- |
| `AutomaticAuthentication` | `true` | <span data-ttu-id="a3868-131">Pokud `true` Server IIS nastaví `HttpContext.User` ověřený [ověřováním systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="a3868-131">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="a3868-132">Pokud `false` Server poskytuje pouze identitu pro `HttpContext.User` a reaguje na výzvy, pokud je explicitně vyžádala `AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="a3868-132">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="a3868-133">Aby mohla služba fungovat, musí být ve službě IIS povolená možnost ověřování systému Windows `AutomaticAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="a3868-133">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="a3868-134">Další informace najdete v tématu [ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="a3868-134">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName` | `null` | <span data-ttu-id="a3868-135">Nastaví zobrazovaný název, který se zobrazí uživatelům na přihlašovacích stránkách.</span><span class="sxs-lookup"><span data-stu-id="a3868-135">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO` | `false` | <span data-ttu-id="a3868-136">Zda je povolena synchronní I/O pro `HttpContext.Request` a `HttpContext.Response` .</span><span class="sxs-lookup"><span data-stu-id="a3868-136">Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize` | `30000000` | <span data-ttu-id="a3868-137">Získá nebo nastaví velikost textu maximálního požadavku pro `HttpRequest` .</span><span class="sxs-lookup"><span data-stu-id="a3868-137">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="a3868-138">Všimněte si, že samotná služba IIS má limit, `maxAllowedContentLength` který bude zpracován před `MaxRequestBodySize` nastavením v `IISServerOptions` .</span><span class="sxs-lookup"><span data-stu-id="a3868-138">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="a3868-139">Změna `MaxRequestBodySize` neovlivní `maxAllowedContentLength` .</span><span class="sxs-lookup"><span data-stu-id="a3868-139">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="a3868-140">Chcete-li zvýšit `maxAllowedContentLength` , přidejte položku do pole `web.config` pro nastavení `maxAllowedContentLength` na vyšší hodnotu.</span><span class="sxs-lookup"><span data-stu-id="a3868-140">To increase `maxAllowedContentLength`, add an entry in the `web.config` to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="a3868-141">Další podrobnosti najdete v tématu [Konfigurace](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span><span class="sxs-lookup"><span data-stu-id="a3868-141">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

## <a name="differences-between-in-process-and-out-of-process-hosting"></a><span data-ttu-id="a3868-142">Rozdíly mezi procesy v procesu a hostování mimo procesy</span><span class="sxs-lookup"><span data-stu-id="a3868-142">Differences between in-process and out-of-process hosting</span></span>

<span data-ttu-id="a3868-143">Následující vlastnosti platí při hostování v procesu:</span><span class="sxs-lookup"><span data-stu-id="a3868-143">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="a3868-144">`IISHttpServer`Místo serveru [Kestrel](xref:fundamentals/servers/kestrel) se používá server HTTP služby IIS ().</span><span class="sxs-lookup"><span data-stu-id="a3868-144">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="a3868-145">Pro vnitroprocesové [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> :</span><span class="sxs-lookup"><span data-stu-id="a3868-145">For in-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> to:</span></span>

  * <span data-ttu-id="a3868-146">Zaregistrujte `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="a3868-146">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="a3868-147">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a3868-147">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="a3868-148">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="a3868-148">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="a3868-149">[ `requestTimeout` Atribut](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) se nevztahuje na hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="a3868-149">The [`requestTimeout` attribute](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="a3868-150">Sdílení fondu aplikací mezi aplikacemi není podporováno.</span><span class="sxs-lookup"><span data-stu-id="a3868-150">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="a3868-151">Použijte jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a3868-151">Use one app pool per app.</span></span>

* <span data-ttu-id="a3868-152">Architektura (bitová verze) aplikace a nainstalovaného modulu runtime (x64 nebo x86) se musí shodovat s architekturou fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="a3868-152">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span> <span data-ttu-id="a3868-153">Například aplikace publikované pro 32 (x86) musí mít ve svých fondech aplikací IIS povolený 32 bitů.</span><span class="sxs-lookup"><span data-stu-id="a3868-153">For example, apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="a3868-154">Další informace najdete v části [Vytvoření webu služby IIS](xref:tutorials/publish-to-iis#create-the-iis-site) .</span><span class="sxs-lookup"><span data-stu-id="a3868-154">For more information, see the [Create the IIS site](xref:tutorials/publish-to-iis#create-the-iis-site) section.</span></span>

* <span data-ttu-id="a3868-155">Zjistila se odpojení klienta.</span><span class="sxs-lookup"><span data-stu-id="a3868-155">Client disconnects are detected.</span></span> <span data-ttu-id="a3868-156">[`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A)Token zrušení se zruší, když se klient odpojí.</span><span class="sxs-lookup"><span data-stu-id="a3868-156">The [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="a3868-157">Při hostování v procesu <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> není volána interně pro inicializaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="a3868-157">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> isn't called internally to initialize a user.</span></span> <span data-ttu-id="a3868-158">Proto <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementace používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována.</span><span class="sxs-lookup"><span data-stu-id="a3868-158">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="a3868-159">Při transformaci deklarací s <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementací zavolejte <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> na přidat ověřovací služby:</span><span class="sxs-lookup"><span data-stu-id="a3868-159">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
* <span data-ttu-id="a3868-160">[Nasazení webového balíčku (v jednom souboru)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) nejsou podporována.</span><span class="sxs-lookup"><span data-stu-id="a3868-160">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>
