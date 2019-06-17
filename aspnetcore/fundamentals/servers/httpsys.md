---
title: Implementace serveru HTTP.sys web v ASP.NET Core
author: guardrex
description: Další informace o souboru HTTP.sys, webový server pro ASP.NET Core ve Windows. Založená na ovladač HTTP.sys režimu jádra, ovladač HTTP.sys se o alternativu k Kestrel, který lze použít pro přímé připojení k Internetu bez služby IIS.
monikerRange: '>= aspnetcore-2.0'
ms.author: tdykstra
ms.custom: mvc
ms.date: 05/27/2019
uid: fundamentals/servers/httpsys
ms.openlocfilehash: 1b5e26171e5f807fdb918ccf8ae1ff1231ad5356
ms.sourcegitcommit: f5762967df3be8b8c868229e679301f2f7954679
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67048190"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="1d468-104">Implementace serveru HTTP.sys web v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1d468-104">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="1d468-105">Podle [Petr Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1d468-105">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1d468-106">[Ovladač HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) je [webového serveru pro ASP.NET Core](xref:fundamentals/servers/index) pouze spuštěného na Windows.</span><span class="sxs-lookup"><span data-stu-id="1d468-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="1d468-107">Ovladač HTTP.sys se o alternativu k [Kestrel](xref:fundamentals/servers/kestrel) serveru a nabízí některé funkce, aby Kestrel neposkytuje.</span><span class="sxs-lookup"><span data-stu-id="1d468-107">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1d468-108">Ovladač HTTP.sys není kompatibilní se systémem [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) a nelze jej použít s IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="1d468-108">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="1d468-109">Ovladač HTTP.sys podporuje následující funkce:</span><span class="sxs-lookup"><span data-stu-id="1d468-109">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="1d468-110">Ověřování Windows</span><span class="sxs-lookup"><span data-stu-id="1d468-110">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="1d468-111">Sdílení portů</span><span class="sxs-lookup"><span data-stu-id="1d468-111">Port sharing</span></span>
* <span data-ttu-id="1d468-112">Protokol HTTPS se SNI</span><span class="sxs-lookup"><span data-stu-id="1d468-112">HTTPS with SNI</span></span>
* <span data-ttu-id="1d468-113">HTTP/2 přes protokol TLS (Windows 10 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="1d468-113">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="1d468-114">Přenos souborů s přímým přístupem</span><span class="sxs-lookup"><span data-stu-id="1d468-114">Direct file transmission</span></span>
* <span data-ttu-id="1d468-115">Ukládání odpovědí do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="1d468-115">Response caching</span></span>
* <span data-ttu-id="1d468-116">Protokoly Websocket (Windows 8 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="1d468-116">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="1d468-117">Podporované verze Windows:</span><span class="sxs-lookup"><span data-stu-id="1d468-117">Supported Windows versions:</span></span>

* <span data-ttu-id="1d468-118">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="1d468-118">Windows 7 or later</span></span>
* <span data-ttu-id="1d468-119">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="1d468-119">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="1d468-120">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1d468-120">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="1d468-121">Kdy použít HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="1d468-121">When to use HTTP.sys</span></span>

<span data-ttu-id="1d468-122">Je užitečné pro nasazení HTTP.sys kde:</span><span class="sxs-lookup"><span data-stu-id="1d468-122">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="1d468-123">Je potřeba zpřístupnit server přímo k Internetu bez použití služby IIS.</span><span class="sxs-lookup"><span data-stu-id="1d468-123">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![Ovladač HTTP.sys komunikuje přímo s Internetem](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="1d468-125">Vnitřní nasazení vyžaduje funkci, není k dispozici v Kestrel, jako například [ověřování Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="1d468-125">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![Ovladač HTTP.sys komunikuje přímo s interní sítě](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="1d468-127">Ovladač HTTP.sys je Vyspělá technologie, která chrání před mnoho typů útoků a poskytuje odolnost, zabezpečení a škálovatelnost plně funkční webového serveru.</span><span class="sxs-lookup"><span data-stu-id="1d468-127">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="1d468-128">Služba IIS pracuje jako naslouchací proces protokolu HTTP na základě ovladače HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="1d468-128">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="1d468-129">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="1d468-129">HTTP/2 support</span></span>

<span data-ttu-id="1d468-130">[HTTP/2](https://httpwg.org/specs/rfc7540.html) je povolený pro aplikace ASP.NET Core, pokud tyto požadavky byly splněny:</span><span class="sxs-lookup"><span data-stu-id="1d468-130">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="1d468-131">Windows Server 2016 nebo Windows 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="1d468-131">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="1d468-132">[Vyjednávání protokolu v aplikační vrstvě (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) připojení</span><span class="sxs-lookup"><span data-stu-id="1d468-132">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="1d468-133">Protokol TLS 1.2 nebo vyšší připojení</span><span class="sxs-lookup"><span data-stu-id="1d468-133">TLS 1.2 or later connection</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="1d468-134">Pokud se připojení HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="1d468-134">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="1d468-135">Pokud se připojení HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/1.1`.</span><span class="sxs-lookup"><span data-stu-id="1d468-135">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

::: moniker-end

<span data-ttu-id="1d468-136">HTTP/2 je standardně povolená.</span><span class="sxs-lookup"><span data-stu-id="1d468-136">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="1d468-137">Pokud nedojde k připojení k protokolu HTTP/2, přejde připojení HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="1d468-137">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="1d468-138">V příští verzi Windows protokolu HTTP/2 konfigurace příznaky bude k dispozici, včetně zakázat HTTP/2 se souborem HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="1d468-138">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="1d468-139">Ověřování pomocí protokolu Kerberos v režimu jádra</span><span class="sxs-lookup"><span data-stu-id="1d468-139">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="1d468-140">Ovladač HTTP.sys delegáty pro ověřování v režimu jádra ověřování protokolem Kerberos.</span><span class="sxs-lookup"><span data-stu-id="1d468-140">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="1d468-141">Režim ověřování uživatele nepodporuje protokolů Kerberos a HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="1d468-141">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="1d468-142">Účet počítače musí být použité k dešifrování token/lístek služby Kerberos, která se získá z Active Directory a předá klienta na serveru k ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="1d468-142">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="1d468-143">Zaregistrujte hlavní název služby (SPN) příslušného hostitele není uživatel aplikace.</span><span class="sxs-lookup"><span data-stu-id="1d468-143">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="1d468-144">Jak používat ovladač HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="1d468-144">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="1d468-145">Nakonfigurovat aplikaci ASP.NET Core, aby používala HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="1d468-145">Configure the ASP.NET Core app to use HTTP.sys</span></span>

1. <span data-ttu-id="1d468-146">Odkaz na balíček v souboru projektu není povinné, při použití [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) (ASP.NET Core 2.1 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="1d468-146">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) (ASP.NET Core 2.1 or later).</span></span> <span data-ttu-id="1d468-147">Pokud nepoužíváte `Microsoft.AspNetCore.App` Microsoft.aspnetcore.all, přidejte odkaz na balíček [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span><span class="sxs-lookup"><span data-stu-id="1d468-147">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

2. <span data-ttu-id="1d468-148">Volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> rozšiřující metoda při vytváření hostitele, zadáte požadované <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>:</span><span class="sxs-lookup"><span data-stu-id="1d468-148">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>:</span></span>

   [!code-csharp[](httpsys/sample/Program.cs?name=snippet1&highlight=4-12)]

   <span data-ttu-id="1d468-149">Další konfigurace HTTP.sys probíhá prostřednictvím funkce [nastavení registru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span><span class="sxs-lookup"><span data-stu-id="1d468-149">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

   <span data-ttu-id="1d468-150">**Možnosti HTTP.sys**</span><span class="sxs-lookup"><span data-stu-id="1d468-150">**HTTP.sys options**</span></span>

   | <span data-ttu-id="1d468-151">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="1d468-151">Property</span></span> | <span data-ttu-id="1d468-152">Popis</span><span class="sxs-lookup"><span data-stu-id="1d468-152">Description</span></span> | <span data-ttu-id="1d468-153">Výchozí</span><span class="sxs-lookup"><span data-stu-id="1d468-153">Default</span></span> |
   | -------- | ----------- | :-----: |
   | [<span data-ttu-id="1d468-154">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="1d468-154">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="1d468-155">Řídí, zda je povolen synchronní vstupně výstupní `HttpContext.Request.Body` a `HttpContext.Response.Body`.</span><span class="sxs-lookup"><span data-stu-id="1d468-155">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
   | [<span data-ttu-id="1d468-156">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="1d468-156">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="1d468-157">Povolit anonymní žádosti.</span><span class="sxs-lookup"><span data-stu-id="1d468-157">Allow anonymous requests.</span></span> | `true` |
   | [<span data-ttu-id="1d468-158">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="1d468-158">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="1d468-159">Určení povolených ověřovací schémata.</span><span class="sxs-lookup"><span data-stu-id="1d468-159">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="1d468-160">Může změnit kdykoli před disposing naslouchací proces.</span><span class="sxs-lookup"><span data-stu-id="1d468-160">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="1d468-161">Hodnoty jsou poskytovány [schémata AuthenticationSchemes výčtu](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, a `NTLM`.</span><span class="sxs-lookup"><span data-stu-id="1d468-161">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
   | [<span data-ttu-id="1d468-162">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="1d468-162">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="1d468-163">Pokus o [režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) ukládání do mezipaměti pro odpovědi s oprávněné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="1d468-163">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="1d468-164">Odpověď nesmí obsahovat `Set-Cookie`, `Vary`, nebo `Pragma` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="1d468-164">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="1d468-165">Musí obsahovat `Cache-Control` záhlaví to `public` a buď `shared-max-age` nebo `max-age` hodnotu, nebo `Expires` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="1d468-165">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="1d468-166">Maximální počet souběžných přijímá.</span><span class="sxs-lookup"><span data-stu-id="1d468-166">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="1d468-167">5 &times; [prostředí.<br> ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="1d468-167">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="1d468-168">Maximální počet souběžných připojení tak, aby přijímal.</span><span class="sxs-lookup"><span data-stu-id="1d468-168">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="1d468-169">Použití `-1` pro nekonečno.</span><span class="sxs-lookup"><span data-stu-id="1d468-169">Use `-1` for infinite.</span></span> <span data-ttu-id="1d468-170">Použít `null` použít nastavení v registru počítače.</span><span class="sxs-lookup"><span data-stu-id="1d468-170">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="1d468-171">(bez omezení)</span><span class="sxs-lookup"><span data-stu-id="1d468-171">(unlimited)</span></span> |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="1d468-172">Zobrazit <a href="#maxrequestbodysize">MaxRequestBodySize</a> oddílu.</span><span class="sxs-lookup"><span data-stu-id="1d468-172">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="1d468-173">30000000 bajtů</span><span class="sxs-lookup"><span data-stu-id="1d468-173">30000000 bytes</span></span><br><span data-ttu-id="1d468-174">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="1d468-174">(~28.6 MB)</span></span> |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="1d468-175">Maximální počet požadavků, které lze zařadit do fronty.</span><span class="sxs-lookup"><span data-stu-id="1d468-175">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="1d468-176">1000</span><span class="sxs-lookup"><span data-stu-id="1d468-176">1000</span></span> |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="1d468-177">Označení Pokud zápisů tělo odpovědi odpojí selžou z důvodu klienta by měl vyvolat výjimky nebo dokončení normálně.</span><span class="sxs-lookup"><span data-stu-id="1d468-177">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="1d468-178">(obvykle dokončení)</span><span class="sxs-lookup"><span data-stu-id="1d468-178">(complete normally)</span></span> |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="1d468-179">Vystavení HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> konfigurace, která může být také nakonfigurována v registru.</span><span class="sxs-lookup"><span data-stu-id="1d468-179">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="1d468-180">Pomocí rozhraní API odkazů na další informace o každém nastavení, včetně výchozích hodnot:</span><span class="sxs-lookup"><span data-stu-id="1d468-180">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="1d468-181">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; povolený čas pro rozhraní API serveru HTTP, chcete-li vyprázdnit obsah entity pro zachování připojení.</span><span class="sxs-lookup"><span data-stu-id="1d468-181">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="1d468-182">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; povolený čas pro obsah entity žádosti k doručení.</span><span class="sxs-lookup"><span data-stu-id="1d468-182">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="1d468-183">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; povolený čas pro rozhraní API serveru HTTP k analýze záhlaví žádosti.</span><span class="sxs-lookup"><span data-stu-id="1d468-183">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="1d468-184">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; povolený čas pro nečinné připojení.</span><span class="sxs-lookup"><span data-stu-id="1d468-184">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="1d468-185">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; minimální míra pro odpověď v pro odesílání.</span><span class="sxs-lookup"><span data-stu-id="1d468-185">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; The minimum send rate for the response.</span></span></li><li><span data-ttu-id="1d468-186">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; dobu povolenou pro požadavek na zůstat ve frontě, než ji převezme ho.</span><span class="sxs-lookup"><span data-stu-id="1d468-186">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="1d468-187">Zadejte <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> registrace se souborem HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="1d468-187">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="1d468-188">Nejužitečnější je [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), který se používá k přidání předpony do kolekce.</span><span class="sxs-lookup"><span data-stu-id="1d468-188">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="1d468-189">Ty mohou změnit kdykoli před disposing naslouchací proces.</span><span class="sxs-lookup"><span data-stu-id="1d468-189">These may be modified at any time prior to disposing the listener.</span></span> |  |

   <a name="maxrequestbodysize"></a>

   <span data-ttu-id="1d468-190">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="1d468-190">**MaxRequestBodySize**</span></span>

   <span data-ttu-id="1d468-191">Maximální povolená velikost obsahu jakékoli žádosti v bajtech.</span><span class="sxs-lookup"><span data-stu-id="1d468-191">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="1d468-192">Pokud je nastavena na `null`, žádost o maximální velikost textu neomezený.</span><span class="sxs-lookup"><span data-stu-id="1d468-192">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="1d468-193">Toto omezení nemá žádný vliv na upgradované připojení, které jsou vždy neomezený počet.</span><span class="sxs-lookup"><span data-stu-id="1d468-193">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

   <span data-ttu-id="1d468-194">Doporučená metoda k přepsání omezení v aplikaci ASP.NET Core MVC pro jeden `IActionResult` , je použít <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atributu na metodu akce:</span><span class="sxs-lookup"><span data-stu-id="1d468-194">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

   ```csharp
   [RequestSizeLimit(100000000)]
   public IActionResult MyActionMethod()
   ```

   <span data-ttu-id="1d468-195">Pokud se aplikace pokusí po aplikace bylo zahájeno čtení požadavku konfigurovat omezení na vyžádání, je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="1d468-195">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="1d468-196">`IsReadOnly` Vlastnost lze použít k označení, pokud `MaxRequestBodySize` vlastnost je ve stavu jen pro čtení, to znamená Konfigurace limitu bude příliš pozdě.</span><span class="sxs-lookup"><span data-stu-id="1d468-196">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

   <span data-ttu-id="1d468-197">Pokud aplikace by měly přepsat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> na žádost, použijte <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span><span class="sxs-lookup"><span data-stu-id="1d468-197">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

   [!code-csharp[](httpsys/sample/Startup.cs?name=snippet1&highlight=6-7)]

3. <span data-ttu-id="1d468-198">Pokud používáte Visual Studio, ujistěte se, že aplikace není nakonfigurovaná pro spuštění služby IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="1d468-198">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

   <span data-ttu-id="1d468-199">V sadě Visual Studio je výchozí profil spuštění pro službu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="1d468-199">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="1d468-200">Chcete-li spustit projekt jako konzolovou aplikaci, ručně změňte vybraný profil, jak je znázorněno na následujícím snímku obrazovky:</span><span class="sxs-lookup"><span data-stu-id="1d468-200">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

   ![Vyberte profil aplikace konzoly](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="1d468-202">Konfigurace Windows serveru</span><span class="sxs-lookup"><span data-stu-id="1d468-202">Configure Windows Server</span></span>

1. <span data-ttu-id="1d468-203">Určení porty otevřete pro aplikaci a použití [brány Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) nebo [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) rutinu Powershellu otevřít porty brány firewall pro povolení provozu k dosažení HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="1d468-203">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="1d468-204">V následující příkazy a konfigurace aplikací se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="1d468-204">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="1d468-205">Při nasazování do virtuálního počítače Azure, otevřít porty ve [skupinu zabezpečení sítě](/azure/virtual-machines/windows/nsg-quickstart-portal).</span><span class="sxs-lookup"><span data-stu-id="1d468-205">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="1d468-206">V následující příkazy a konfigurace aplikací se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="1d468-206">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="1d468-207">Získat a nainstalovat certifikáty X.509, pokud je to nutné.</span><span class="sxs-lookup"><span data-stu-id="1d468-207">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="1d468-208">Na Windows, vytvořte pomocí certifikátů podepsaných svým držitelem [rutinu Powershellu New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate).</span><span class="sxs-lookup"><span data-stu-id="1d468-208">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="1d468-209">Nepodporovaná příklad naleznete v tématu [UpdateIISExpressSSLForChrome.ps1](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span><span class="sxs-lookup"><span data-stu-id="1d468-209">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="1d468-210">Nainstalovat certifikáty podepsané svým držitelem nebo podepsaný certifikační Autoritou na serveru **místního počítače** > **osobní** ukládat.</span><span class="sxs-lookup"><span data-stu-id="1d468-210">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="1d468-211">Pokud je aplikace [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-deployments-fdd), nainstalujte .NET Core a .NET Framework (Pokud aplikace cílí na rozhraní .NET Framework aplikace .NET Core).</span><span class="sxs-lookup"><span data-stu-id="1d468-211">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="1d468-212">**.NET core** &ndash; Pokud aplikace vyžaduje .NET Core, získání a spuštění **.NET Core Runtime** instalačního programu z [soubory ke stažení rozhraní .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="1d468-212">**.NET Core** &ndash; If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="1d468-213">Neinstalujte úplnou sadu SDK na serveru.</span><span class="sxs-lookup"><span data-stu-id="1d468-213">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="1d468-214">**Rozhraní .NET framework** &ndash; Pokud aplikace vyžaduje rozhraní .NET Framework, přečtěte si článek [Průvodce instalací rozhraní .NET Framework](/dotnet/framework/install/).</span><span class="sxs-lookup"><span data-stu-id="1d468-214">**.NET Framework** &ndash; If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="1d468-215">Instalace vyžaduje rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="1d468-215">Install the required .NET Framework.</span></span> <span data-ttu-id="1d468-216">Instalační program pro nejnovější rozhraní .NET Framework je k dispozici [soubory ke stažení rozhraní .NET Core](https://dotnet.microsoft.com/download) stránky.</span><span class="sxs-lookup"><span data-stu-id="1d468-216">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="1d468-217">Pokud je aplikace [samostatná nasazení](/dotnet/core/deploying/#framework-dependent-deployments-scd), tato aplikace obsahuje modul runtime v jeho nasazení.</span><span class="sxs-lookup"><span data-stu-id="1d468-217">If the app is a [self-contained deployment](/dotnet/core/deploying/#framework-dependent-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="1d468-218">Na serveru není nutné instalovat rozhraní framework.</span><span class="sxs-lookup"><span data-stu-id="1d468-218">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="1d468-219">Konfigurace adresy URL a portů v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1d468-219">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="1d468-220">Ve výchozím nastavení, ASP.NET Core váže k `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="1d468-220">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="1d468-221">Pokud chcete nakonfigurovat předpony adres URL a portů, mezi možnosti patří:</span><span class="sxs-lookup"><span data-stu-id="1d468-221">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="1d468-222">`urls` argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="1d468-222">`urls` command-line argument</span></span>
   * <span data-ttu-id="1d468-223">`ASPNETCORE_URLS` Proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="1d468-223">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="1d468-224">Následující příklad kódu ukazuje, jak používat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> s místní IP adresa serveru `10.0.0.4` na portu 443:</span><span class="sxs-lookup"><span data-stu-id="1d468-224">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/sample_snapshot/Program.cs?name=snippet1&highlight=11)]

   <span data-ttu-id="1d468-225">Výhodou `UrlPrefixes` je okamžitě vygenerována chybová zpráva pro nesprávně formátovaná předpony.</span><span class="sxs-lookup"><span data-stu-id="1d468-225">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="1d468-226">Nastavení v `UrlPrefixes` přepsat `UseUrls` / `urls` / `ASPNETCORE_URLS` nastavení.</span><span class="sxs-lookup"><span data-stu-id="1d468-226">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="1d468-227">Proto výhodou `UseUrls`, `urls`a `ASPNETCORE_URLS` proměnná prostředí je, že je snadnější přepínání mezi Kestrel a HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="1d468-227">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="1d468-228">Používá HTTP.sys [formáty řetězců HTTP Server API UrlPrefix](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span><span class="sxs-lookup"><span data-stu-id="1d468-228">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

   > [!WARNING]
   > <span data-ttu-id="1d468-229">Vazby nejvyšší úrovně zástupný znak (`http://*:80/` a `http://+:80`) by měl **není** použít.</span><span class="sxs-lookup"><span data-stu-id="1d468-229">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="1d468-230">Nejvyšší úrovně zástupné vazby vzniknout slabá místa zabezpečení aplikace.</span><span class="sxs-lookup"><span data-stu-id="1d468-230">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="1d468-231">To platí pro silné a slabé zástupné znaky.</span><span class="sxs-lookup"><span data-stu-id="1d468-231">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="1d468-232">Používejte explicitní hostitele nebo IP adresy místo zástupné znaky.</span><span class="sxs-lookup"><span data-stu-id="1d468-232">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="1d468-233">Vazby zástupný znak subdoménu (například `*.mysub.com`) není bezpečnostní riziko, pokud celé nadřazené domény (nikoli `*.com`, což je ohrožené).</span><span class="sxs-lookup"><span data-stu-id="1d468-233">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="1d468-234">Další informace najdete v tématu [RFC 7230: Oddíl 5.4: Hostitel](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="1d468-234">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="1d468-235">Preregister předpony adres URL na serveru.</span><span class="sxs-lookup"><span data-stu-id="1d468-235">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="1d468-236">Integrované nástroje pro konfiguraci HTTP.sys se *netsh.exe*.</span><span class="sxs-lookup"><span data-stu-id="1d468-236">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="1d468-237">*Netsh.exe* slouží k předpony adres URL rezervovat a přiřadit certifikáty X.509.</span><span class="sxs-lookup"><span data-stu-id="1d468-237">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="1d468-238">Nástroj vyžaduje oprávnění správce.</span><span class="sxs-lookup"><span data-stu-id="1d468-238">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="1d468-239">Použití *netsh.exe* nástroj pro registraci adresy URL pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="1d468-239">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="1d468-240">`<URL>` &ndash; Plně kvalifikovanou URL Uniform Resource Locator ().</span><span class="sxs-lookup"><span data-stu-id="1d468-240">`<URL>` &ndash; The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="1d468-241">Nepoužívejte zástupné vazby.</span><span class="sxs-lookup"><span data-stu-id="1d468-241">Don't use a wildcard binding.</span></span> <span data-ttu-id="1d468-242">Použijte platný název hostitele nebo IP adresa.</span><span class="sxs-lookup"><span data-stu-id="1d468-242">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="1d468-243">*Adresa URL musí obsahovat koncové lomítko.*</span><span class="sxs-lookup"><span data-stu-id="1d468-243">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="1d468-244">`<USER>` &ndash; Určuje jméno uživatele nebo skupiny uživatelů.</span><span class="sxs-lookup"><span data-stu-id="1d468-244">`<USER>` &ndash; Specifies the user or user-group name.</span></span>

   <span data-ttu-id="1d468-245">V následujícím příkladu je místní IP adresa serveru `10.0.0.4`:</span><span class="sxs-lookup"><span data-stu-id="1d468-245">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="1d468-246">Při registraci adresy URL nástroj odpoví `URL reservation successfully added`.</span><span class="sxs-lookup"><span data-stu-id="1d468-246">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="1d468-247">Chcete-li odstranit registrovaný adresu URL, použijte `delete urlacl` příkaz:</span><span class="sxs-lookup"><span data-stu-id="1d468-247">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="1d468-248">Zaregistrujte certifikáty X.509 na serveru.</span><span class="sxs-lookup"><span data-stu-id="1d468-248">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="1d468-249">Použití *netsh.exe* nástroj k registraci certifikátů pro aplikace:</span><span class="sxs-lookup"><span data-stu-id="1d468-249">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="1d468-250">`<IP>` &ndash; Určuje místní IP adresa pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="1d468-250">`<IP>` &ndash; Specifies the local IP address for the binding.</span></span> <span data-ttu-id="1d468-251">Nepoužívejte zástupné vazby.</span><span class="sxs-lookup"><span data-stu-id="1d468-251">Don't use a wildcard binding.</span></span> <span data-ttu-id="1d468-252">Použijte platnou IP adresu.</span><span class="sxs-lookup"><span data-stu-id="1d468-252">Use a valid IP address.</span></span>
   * <span data-ttu-id="1d468-253">`<PORT>` &ndash; Určuje port pro vazby.</span><span class="sxs-lookup"><span data-stu-id="1d468-253">`<PORT>` &ndash; Specifies the port for the binding.</span></span>
   * <span data-ttu-id="1d468-254">`<THUMBPRINT>` &ndash; Kryptografický otisk certifikátu X.509.</span><span class="sxs-lookup"><span data-stu-id="1d468-254">`<THUMBPRINT>` &ndash; The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="1d468-255">`<GUID>` &ndash; Vygeneruje pro vývojáře identifikátor GUID představující aplikace k informačním účelům.</span><span class="sxs-lookup"><span data-stu-id="1d468-255">`<GUID>` &ndash; A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="1d468-256">Pro referenční účely uložení identifikátoru GUID v aplikaci jako značku balíčku:</span><span class="sxs-lookup"><span data-stu-id="1d468-256">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="1d468-257">V sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="1d468-257">In Visual Studio:</span></span>
     * <span data-ttu-id="1d468-258">Otevřete vlastnosti projektu aplikace kliknutím pravým tlačítkem na aplikaci v **Průzkumníka řešení** a vyberete **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="1d468-258">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="1d468-259">Vyberte **balíčku** kartu.</span><span class="sxs-lookup"><span data-stu-id="1d468-259">Select the **Package** tab.</span></span>
     * <span data-ttu-id="1d468-260">Zadejte identifikátor GUID, které jste vytvořili **značky** pole.</span><span class="sxs-lookup"><span data-stu-id="1d468-260">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="1d468-261">Pokud nepoužíváte Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="1d468-261">When not using Visual Studio:</span></span>
     * <span data-ttu-id="1d468-262">Otevření souboru projektu vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="1d468-262">Open the app's project file.</span></span>
     * <span data-ttu-id="1d468-263">Přidat `<PackageTags>` vlastnost na nový nebo existující `<PropertyGroup>` identifikátor GUID, který jste vytvořili:</span><span class="sxs-lookup"><span data-stu-id="1d468-263">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="1d468-264">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="1d468-264">In the following example:</span></span>

   * <span data-ttu-id="1d468-265">Místní IP adresa serveru je `10.0.0.4`.</span><span class="sxs-lookup"><span data-stu-id="1d468-265">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="1d468-266">Poskytuje online náhodný GUID generator `appid` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="1d468-266">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="1d468-267">Při registraci certifikátu nástroj odpoví `SSL Certificate successfully added`.</span><span class="sxs-lookup"><span data-stu-id="1d468-267">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="1d468-268">Chcete-li odstranit registrace certifikátu, použijte `delete sslcert` příkaz:</span><span class="sxs-lookup"><span data-stu-id="1d468-268">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="1d468-269">Referenční dokumentace pro *netsh.exe*:</span><span class="sxs-lookup"><span data-stu-id="1d468-269">Reference documentation for *netsh.exe*:</span></span>

   * [<span data-ttu-id="1d468-270">Příkazy Netsh pro Hypertext Transfer Protocol (HTTP)</span><span class="sxs-lookup"><span data-stu-id="1d468-270">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](https://technet.microsoft.com/library/cc725882.aspx)
   * [<span data-ttu-id="1d468-271">UrlPrefix Strings</span><span class="sxs-lookup"><span data-stu-id="1d468-271">UrlPrefix Strings</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. <span data-ttu-id="1d468-272">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1d468-272">Run the app.</span></span>

   <span data-ttu-id="1d468-273">Oprávnění správce se vyžaduje ke spuštění aplikace při vytváření vazby na místního hostitele pomocí protokolu HTTP (nikoli HTTPS) s více než 1 024 číslo portu.</span><span class="sxs-lookup"><span data-stu-id="1d468-273">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="1d468-274">Pro další konfiguraci (například pomocí místní IP adresa nebo vazbu na port 443) spusťte aplikaci s oprávněním správce.</span><span class="sxs-lookup"><span data-stu-id="1d468-274">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="1d468-275">Aplikace jsou reaguje na veřejnou IP adresu serveru.</span><span class="sxs-lookup"><span data-stu-id="1d468-275">The app responds at the server's public IP address.</span></span> <span data-ttu-id="1d468-276">V tomto příkladu je server dostupný z Internetu na veřejné IP adrese `104.214.79.47`.</span><span class="sxs-lookup"><span data-stu-id="1d468-276">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="1d468-277">V tomto příkladu se používá certifikát pro vývoj.</span><span class="sxs-lookup"><span data-stu-id="1d468-277">A development certificate is used in this example.</span></span> <span data-ttu-id="1d468-278">Stránka načte bezpečně po vynechání upozornění na nedůvěryhodný certifikát prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="1d468-278">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![Načíst okno prohlížeče zobrazující indexová stránka aplikace](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="1d468-280">Proxy server a scénáře pro nástroj pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="1d468-280">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="1d468-281">Pro aplikace hostované souborem HTTP.sys, které pracují s žádostí z Internetu nebo podnikové sítě může být další konfigurace požadovaných při hostování za proxy servery a nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="1d468-281">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="1d468-282">Další informace najdete v tématu [konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="1d468-282">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1d468-283">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="1d468-283">Additional resources</span></span>

* [<span data-ttu-id="1d468-284">Povolení ověřování Windows pomocí HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="1d468-284">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="1d468-285">Server HTTP rozhraní API</span><span class="sxs-lookup"><span data-stu-id="1d468-285">HTTP Server API</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [<span data-ttu-id="1d468-286">úložiště GitHub ASPNET/HttpSysServer (zdrojového kódu)</span><span class="sxs-lookup"><span data-stu-id="1d468-286">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="1d468-287">Hostitel</span><span class="sxs-lookup"><span data-stu-id="1d468-287">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>
