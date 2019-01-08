---
title: Implementace serveru HTTP.sys web v ASP.NET Core
author: guardrex
description: Další informace o souboru HTTP.sys, webový server pro ASP.NET Core ve Windows. Založená na ovladač HTTP.sys režimu jádra, ovladač HTTP.sys se o alternativu k Kestrel, který lze použít pro přímé připojení k Internetu bez služby IIS.
monikerRange: '>= aspnetcore-2.0'
ms.author: tdykstra
ms.custom: mvc
ms.date: 01/03/2019
uid: fundamentals/servers/httpsys
ms.openlocfilehash: 46538d256ae2c5f3b7e6c725fa8f29092759f69f
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098851"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="47685-104">Implementace serveru HTTP.sys web v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="47685-104">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="47685-105">Podle [Petr Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="47685-105">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="47685-106">[Ovladač HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) je [webového serveru pro ASP.NET Core](xref:fundamentals/servers/index) pouze spuštěného na Windows.</span><span class="sxs-lookup"><span data-stu-id="47685-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="47685-107">Ovladač HTTP.sys se o alternativu k [Kestrel](xref:fundamentals/servers/kestrel) serveru a nabízí některé funkce, aby Kestrel neposkytuje.</span><span class="sxs-lookup"><span data-stu-id="47685-107">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="47685-108">Ovladač HTTP.sys není kompatibilní se systémem [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) a nelze jej použít s IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="47685-108">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="47685-109">Ovladač HTTP.sys podporuje následující funkce:</span><span class="sxs-lookup"><span data-stu-id="47685-109">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="47685-110">Ověřování Windows</span><span class="sxs-lookup"><span data-stu-id="47685-110">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="47685-111">Sdílení portů</span><span class="sxs-lookup"><span data-stu-id="47685-111">Port sharing</span></span>
* <span data-ttu-id="47685-112">Protokol HTTPS se SNI</span><span class="sxs-lookup"><span data-stu-id="47685-112">HTTPS with SNI</span></span>
* <span data-ttu-id="47685-113">HTTP/2 přes protokol TLS (Windows 10 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="47685-113">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="47685-114">Přenos souborů s přímým přístupem</span><span class="sxs-lookup"><span data-stu-id="47685-114">Direct file transmission</span></span>
* <span data-ttu-id="47685-115">Ukládání odpovědí do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="47685-115">Response caching</span></span>
* <span data-ttu-id="47685-116">Protokoly Websocket (Windows 8 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="47685-116">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="47685-117">Podporované verze Windows:</span><span class="sxs-lookup"><span data-stu-id="47685-117">Supported Windows versions:</span></span>

* <span data-ttu-id="47685-118">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="47685-118">Windows 7 or later</span></span>
* <span data-ttu-id="47685-119">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="47685-119">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="47685-120">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="47685-120">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="47685-121">Kdy použít HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="47685-121">When to use HTTP.sys</span></span>

<span data-ttu-id="47685-122">Je užitečné pro nasazení HTTP.sys kde:</span><span class="sxs-lookup"><span data-stu-id="47685-122">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="47685-123">Je potřeba zpřístupnit server přímo k Internetu bez použití služby IIS.</span><span class="sxs-lookup"><span data-stu-id="47685-123">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![Ovladač HTTP.sys komunikuje přímo s Internetem](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="47685-125">Vnitřní nasazení vyžaduje funkci, není k dispozici v Kestrel, jako například [ověřování Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="47685-125">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![Ovladač HTTP.sys komunikuje přímo s interní sítě](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="47685-127">Ovladač HTTP.sys je Vyspělá technologie, která chrání před mnoho typů útoků a poskytuje odolnost, zabezpečení a škálovatelnost plně funkční webového serveru.</span><span class="sxs-lookup"><span data-stu-id="47685-127">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="47685-128">Služba IIS pracuje jako naslouchací proces protokolu HTTP na základě ovladače HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="47685-128">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="47685-129">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="47685-129">HTTP/2 support</span></span>

<span data-ttu-id="47685-130">[HTTP/2](https://httpwg.org/specs/rfc7540.html) je povolený pro aplikace ASP.NET Core, pokud tyto požadavky byly splněny:</span><span class="sxs-lookup"><span data-stu-id="47685-130">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="47685-131">Windows Server 2016 nebo Windows 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="47685-131">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="47685-132">[Vyjednávání protokolu v aplikační vrstvě (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) připojení</span><span class="sxs-lookup"><span data-stu-id="47685-132">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="47685-133">Protokol TLS 1.2 nebo vyšší připojení</span><span class="sxs-lookup"><span data-stu-id="47685-133">TLS 1.2 or later connection</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="47685-134">Pokud se připojení HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="47685-134">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="47685-135">Pokud se připojení HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/1.1`.</span><span class="sxs-lookup"><span data-stu-id="47685-135">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

::: moniker-end

<span data-ttu-id="47685-136">HTTP/2 je standardně povolená.</span><span class="sxs-lookup"><span data-stu-id="47685-136">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="47685-137">Pokud nedojde k připojení k protokolu HTTP/2, přejde připojení HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="47685-137">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="47685-138">V příští verzi Windows protokolu HTTP/2 konfigurace příznaky bude k dispozici, včetně zakázat HTTP/2 se souborem HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="47685-138">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="47685-139">Ověřování pomocí protokolu Kerberos v režimu jádra</span><span class="sxs-lookup"><span data-stu-id="47685-139">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="47685-140">Ovladač HTTP.sys delegáty pro ověřování v režimu jádra ověřování protokolem Kerberos.</span><span class="sxs-lookup"><span data-stu-id="47685-140">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="47685-141">Režim ověřování uživatele nepodporuje protokolů Kerberos a HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="47685-141">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="47685-142">Účet počítače musí být použité k dešifrování token/lístek služby Kerberos, která se získá z Active Directory a předá klienta na serveru k ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="47685-142">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="47685-143">Zaregistrujte hlavní název služby (SPN) příslušného hostitele není uživatel aplikace.</span><span class="sxs-lookup"><span data-stu-id="47685-143">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="47685-144">Jak používat ovladač HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="47685-144">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="47685-145">Nakonfigurovat aplikaci ASP.NET Core, aby používala HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="47685-145">Configure the ASP.NET Core app to use HTTP.sys</span></span>

1. <span data-ttu-id="47685-146">Odkaz na balíček v souboru projektu není povinné, při použití [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) (ASP.NET Core 2.1 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="47685-146">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) (ASP.NET Core 2.1 or later).</span></span> <span data-ttu-id="47685-147">Pokud nepoužíváte `Microsoft.AspNetCore.App` Microsoft.aspnetcore.all, přidejte odkaz na balíček [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span><span class="sxs-lookup"><span data-stu-id="47685-147">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

2. <span data-ttu-id="47685-148">Volání [UseHttpSys](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderhttpsysextensions.usehttpsys) rozšiřující metoda při vytváření webového hostitele, zadáte požadované [HTTP.sys možnosti](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions):</span><span class="sxs-lookup"><span data-stu-id="47685-148">Call the [UseHttpSys](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderhttpsysextensions.usehttpsys) extension method when building the web host, specifying any required [HTTP.sys options](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions):</span></span>

   [!code-csharp[](httpsys/sample/Program.cs?name=snippet1&highlight=4-12)]

   <span data-ttu-id="47685-149">Další konfigurace HTTP.sys probíhá prostřednictvím funkce [nastavení registru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span><span class="sxs-lookup"><span data-stu-id="47685-149">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

   <span data-ttu-id="47685-150">**Možnosti HTTP.sys**</span><span class="sxs-lookup"><span data-stu-id="47685-150">**HTTP.sys options**</span></span>

   | <span data-ttu-id="47685-151">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="47685-151">Property</span></span> | <span data-ttu-id="47685-152">Popis</span><span class="sxs-lookup"><span data-stu-id="47685-152">Description</span></span> | <span data-ttu-id="47685-153">Výchozí</span><span class="sxs-lookup"><span data-stu-id="47685-153">Default</span></span> |
   | -------- | ----------- | :-----: |
   | [<span data-ttu-id="47685-154">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="47685-154">AllowSynchronousIO</span></span>](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.allowsynchronousio) | <span data-ttu-id="47685-155">Řídí, zda je povolen synchronní vstupně výstupní `HttpContext.Request.Body` a `HttpContext.Response.Body`.</span><span class="sxs-lookup"><span data-stu-id="47685-155">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
   | [<span data-ttu-id="47685-156">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="47685-156">Authentication.AllowAnonymous</span></span>](/dotnet/api/microsoft.aspnetcore.server.httpsys.authenticationmanager.allowanonymous) | <span data-ttu-id="47685-157">Povolit anonymní žádosti.</span><span class="sxs-lookup"><span data-stu-id="47685-157">Allow anonymous requests.</span></span> | `true` |
   | [<span data-ttu-id="47685-158">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="47685-158">Authentication.Schemes</span></span>](/dotnet/api/microsoft.aspnetcore.server.httpsys.authenticationmanager.schemes) | <span data-ttu-id="47685-159">Určení povolených ověřovací schémata.</span><span class="sxs-lookup"><span data-stu-id="47685-159">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="47685-160">Může změnit kdykoli před disposing naslouchací proces.</span><span class="sxs-lookup"><span data-stu-id="47685-160">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="47685-161">Hodnoty jsou poskytovány [schémata AuthenticationSchemes výčtu](/dotnet/api/microsoft.aspnetcore.server.httpsys.authenticationschemes): `Basic`, `Kerberos`, `Negotiate`, `None`, a `NTLM`.</span><span class="sxs-lookup"><span data-stu-id="47685-161">Values are provided by the [AuthenticationSchemes enum](/dotnet/api/microsoft.aspnetcore.server.httpsys.authenticationschemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
   | [<span data-ttu-id="47685-162">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="47685-162">EnableResponseCaching</span></span>](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.enableresponsecaching) | <span data-ttu-id="47685-163">Pokus o [režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) ukládání do mezipaměti pro odpovědi s oprávněné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="47685-163">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="47685-164">Odpověď nesmí obsahovat `Set-Cookie`, `Vary`, nebo `Pragma` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="47685-164">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="47685-165">Musí obsahovat `Cache-Control` záhlaví to `public` a buď `shared-max-age` nebo `max-age` hodnotu, nebo `Expires` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="47685-165">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
   | [<span data-ttu-id="47685-166">MaxAccepts</span><span class="sxs-lookup"><span data-stu-id="47685-166">MaxAccepts</span></span>](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.maxaccepts) | <span data-ttu-id="47685-167">Maximální počet souběžných přijímá.</span><span class="sxs-lookup"><span data-stu-id="47685-167">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="47685-168">5 &times; [prostředí.<br> ProcessorCount](/dotnet/api/system.environment.processorcount)</span><span class="sxs-lookup"><span data-stu-id="47685-168">5 &times; [Environment.<br>ProcessorCount](/dotnet/api/system.environment.processorcount)</span></span> |
   | [<span data-ttu-id="47685-169">MaxConnections</span><span class="sxs-lookup"><span data-stu-id="47685-169">MaxConnections</span></span>](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.maxconnections) | <span data-ttu-id="47685-170">Maximální počet souběžných připojení tak, aby přijímal.</span><span class="sxs-lookup"><span data-stu-id="47685-170">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="47685-171">Použití `-1` pro nekonečno.</span><span class="sxs-lookup"><span data-stu-id="47685-171">Use `-1` for infinite.</span></span> <span data-ttu-id="47685-172">Použít `null` použít nastavení v registru počítače.</span><span class="sxs-lookup"><span data-stu-id="47685-172">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="47685-173">(bez omezení)</span><span class="sxs-lookup"><span data-stu-id="47685-173">(unlimited)</span></span> |
   | [<span data-ttu-id="47685-174">MaxRequestBodySize</span><span class="sxs-lookup"><span data-stu-id="47685-174">MaxRequestBodySize</span></span>](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.maxrequestbodysize) | <span data-ttu-id="47685-175">Zobrazit <a href="#maxrequestbodysize">MaxRequestBodySize</a> oddílu.</span><span class="sxs-lookup"><span data-stu-id="47685-175">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="47685-176">30000000 bajtů</span><span class="sxs-lookup"><span data-stu-id="47685-176">30000000 bytes</span></span><br><span data-ttu-id="47685-177">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="47685-177">(~28.6 MB)</span></span> |
   | [<span data-ttu-id="47685-178">RequestQueueLimit</span><span class="sxs-lookup"><span data-stu-id="47685-178">RequestQueueLimit</span></span>](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.requestqueuelimit) | <span data-ttu-id="47685-179">Maximální počet požadavků, které lze zařadit do fronty.</span><span class="sxs-lookup"><span data-stu-id="47685-179">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="47685-180">1000</span><span class="sxs-lookup"><span data-stu-id="47685-180">1000</span></span> |
   | [<span data-ttu-id="47685-181">ThrowWriteExceptions</span><span class="sxs-lookup"><span data-stu-id="47685-181">ThrowWriteExceptions</span></span>](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.throwwriteexceptions) | <span data-ttu-id="47685-182">Označení Pokud zápisů tělo odpovědi odpojí selžou z důvodu klienta by měl vyvolat výjimky nebo dokončení normálně.</span><span class="sxs-lookup"><span data-stu-id="47685-182">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="47685-183">(obvykle dokončení)</span><span class="sxs-lookup"><span data-stu-id="47685-183">(complete normally)</span></span> |
   | [<span data-ttu-id="47685-184">Vypršení časových limitů</span><span class="sxs-lookup"><span data-stu-id="47685-184">Timeouts</span></span>](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.timeouts) | <span data-ttu-id="47685-185">Vystavení HTTP.sys [TimeoutManager](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager) konfigurace, která může být také nakonfigurována v registru.</span><span class="sxs-lookup"><span data-stu-id="47685-185">Expose the HTTP.sys [TimeoutManager](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager) configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="47685-186">Pomocí rozhraní API odkazů na další informace o každém nastavení, včetně výchozích hodnot:</span><span class="sxs-lookup"><span data-stu-id="47685-186">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="47685-187">[TimeoutManager.DrainEntityBody](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.drainentitybody) &ndash; povolený čas pro rozhraní API serveru HTTP, chcete-li vyprázdnit obsah entity pro zachování připojení.</span><span class="sxs-lookup"><span data-stu-id="47685-187">[TimeoutManager.DrainEntityBody](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.drainentitybody) &ndash; Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="47685-188">[TimeoutManager.EntityBody](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.entitybody) &ndash; povolený čas pro obsah entity žádosti k doručení.</span><span class="sxs-lookup"><span data-stu-id="47685-188">[TimeoutManager.EntityBody](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.entitybody) &ndash; Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="47685-189">[TimeoutManager.HeaderWait](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.headerwait) &ndash; povolený čas pro rozhraní API serveru HTTP k analýze záhlaví žádosti.</span><span class="sxs-lookup"><span data-stu-id="47685-189">[TimeoutManager.HeaderWait](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.headerwait) &ndash; Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="47685-190">[TimeoutManager.IdleConnection](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.idleconnection) &ndash; povolený čas pro nečinné připojení.</span><span class="sxs-lookup"><span data-stu-id="47685-190">[TimeoutManager.IdleConnection](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.idleconnection) &ndash; Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="47685-191">[TimeoutManager.MinSendBytesPerSecond](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.minsendbytespersecond) &ndash; minimální míra pro odpověď v pro odesílání.</span><span class="sxs-lookup"><span data-stu-id="47685-191">[TimeoutManager.MinSendBytesPerSecond](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.minsendbytespersecond) &ndash; The minimum send rate for the response.</span></span></li><li><span data-ttu-id="47685-192">[TimeoutManager.RequestQueue](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.requestqueue) &ndash; dobu povolenou pro požadavek na zůstat ve frontě, než ji převezme ho.</span><span class="sxs-lookup"><span data-stu-id="47685-192">[TimeoutManager.RequestQueue](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.requestqueue) &ndash; Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
   | [<span data-ttu-id="47685-193">UrlPrefixes</span><span class="sxs-lookup"><span data-stu-id="47685-193">UrlPrefixes</span></span>](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.urlprefixes) | <span data-ttu-id="47685-194">Zadejte [UrlPrefixCollection](/dotnet/api/microsoft.aspnetcore.server.httpsys.urlprefixcollection) registrace se souborem HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="47685-194">Specify the [UrlPrefixCollection](/dotnet/api/microsoft.aspnetcore.server.httpsys.urlprefixcollection) to register with HTTP.sys.</span></span> <span data-ttu-id="47685-195">Nejužitečnější je [UrlPrefixCollection.Add](/dotnet/api/microsoft.aspnetcore.server.httpsys.urlprefixcollection.add), který se používá k přidání předpony do kolekce.</span><span class="sxs-lookup"><span data-stu-id="47685-195">The most useful is [UrlPrefixCollection.Add](/dotnet/api/microsoft.aspnetcore.server.httpsys.urlprefixcollection.add), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="47685-196">Ty mohou změnit kdykoli před disposing naslouchací proces.</span><span class="sxs-lookup"><span data-stu-id="47685-196">These may be modified at any time prior to disposing the listener.</span></span> |  |

   <a name="maxrequestbodysize"></a>
   <span data-ttu-id="47685-197">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="47685-197">**MaxRequestBodySize**</span></span>

   <span data-ttu-id="47685-198">Maximální povolená velikost obsahu jakékoli žádosti v bajtech.</span><span class="sxs-lookup"><span data-stu-id="47685-198">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="47685-199">Pokud je nastavena na `null`, žádost o maximální velikost textu neomezený.</span><span class="sxs-lookup"><span data-stu-id="47685-199">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="47685-200">Toto omezení nemá žádný vliv na upgradované připojení, které jsou vždy neomezený počet.</span><span class="sxs-lookup"><span data-stu-id="47685-200">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

   <span data-ttu-id="47685-201">Doporučená metoda k přepsání omezení v aplikaci ASP.NET Core MVC pro jeden `IActionResult` , je použít [RequestSizeLimitAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requestsizelimitattribute) atributu na metodu akce:</span><span class="sxs-lookup"><span data-stu-id="47685-201">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the [RequestSizeLimitAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requestsizelimitattribute) attribute on an action method:</span></span>

   ```csharp
   [RequestSizeLimit(100000000)]
   public IActionResult MyActionMethod()
   ```

   <span data-ttu-id="47685-202">Pokud se aplikace pokusí po aplikace bylo zahájeno čtení požadavku konfigurovat omezení na vyžádání, je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="47685-202">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="47685-203">`IsReadOnly` Vlastnost lze použít k označení, pokud `MaxRequestBodySize` vlastnost je ve stavu jen pro čtení, to znamená Konfigurace limitu bude příliš pozdě.</span><span class="sxs-lookup"><span data-stu-id="47685-203">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

   <span data-ttu-id="47685-204">Pokud aplikace by měly přepsat [MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.maxrequestbodysize) na žádost, použijte [IHttpMaxRequestBodySizeFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttpmaxrequestbodysizefeature):</span><span class="sxs-lookup"><span data-stu-id="47685-204">If the app should override [MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.maxrequestbodysize) per-request, use the [IHttpMaxRequestBodySizeFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttpmaxrequestbodysizefeature):</span></span>

   [!code-csharp[](httpsys/sample/Startup.cs?name=snippet1&highlight=6-7)]

3. <span data-ttu-id="47685-205">Pokud používáte Visual Studio, ujistěte se, že aplikace není nakonfigurovaná pro spuštění služby IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="47685-205">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

   <span data-ttu-id="47685-206">V sadě Visual Studio je výchozí profil spuštění pro službu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="47685-206">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="47685-207">Chcete-li spustit projekt jako konzolovou aplikaci, ručně změňte vybraný profil, jak je znázorněno na následujícím snímku obrazovky:</span><span class="sxs-lookup"><span data-stu-id="47685-207">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

   ![Vyberte profil aplikace konzoly](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="47685-209">Konfigurace Windows serveru</span><span class="sxs-lookup"><span data-stu-id="47685-209">Configure Windows Server</span></span>

1. <span data-ttu-id="47685-210">Určit, aby porty otevřete pro aplikaci a pomocí brány Windows Firewall nebo [rutin prostředí PowerShell](https://technet.microsoft.com/library/jj554906) otevřít porty brány firewall pro povolení provozu k dosažení HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="47685-210">Determine the ports to open for the app and use Windows Firewall or [PowerShell cmdlets](https://technet.microsoft.com/library/jj554906) to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="47685-211">Při nasazování do virtuálního počítače Azure, otevřít porty ve [skupinu zabezpečení sítě](/azure/virtual-network/security-overview).</span><span class="sxs-lookup"><span data-stu-id="47685-211">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-network/security-overview).</span></span> <span data-ttu-id="47685-212">V následující příkazy a konfigurace aplikací se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="47685-212">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="47685-213">Získat a nainstalovat certifikáty X.509, pokud je to nutné.</span><span class="sxs-lookup"><span data-stu-id="47685-213">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="47685-214">Na Windows, vytvořte pomocí certifikátů podepsaných svým držitelem [rutinu Powershellu New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate).</span><span class="sxs-lookup"><span data-stu-id="47685-214">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="47685-215">Nepodporovaná příklad naleznete v tématu [UpdateIISExpressSSLForChrome.ps1](https://github.com/aspnet/Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span><span class="sxs-lookup"><span data-stu-id="47685-215">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/aspnet/Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="47685-216">Nainstalovat certifikáty podepsané svým držitelem nebo podepsaný certifikační Autoritou na serveru **místního počítače** > **osobní** ukládat.</span><span class="sxs-lookup"><span data-stu-id="47685-216">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="47685-217">Pokud je aplikace [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-deployments-fdd), nainstalujte .NET Core a .NET Framework (Pokud aplikace cílí na rozhraní .NET Framework aplikace .NET Core).</span><span class="sxs-lookup"><span data-stu-id="47685-217">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="47685-218">**.NET core** &ndash; Pokud aplikace vyžaduje .NET Core, získání a spuštění **.NET Core Runtime** instalačního programu z [soubory ke stažení rozhraní .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="47685-218">**.NET Core** &ndash; If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="47685-219">Neinstalujte úplnou sadu SDK na serveru.</span><span class="sxs-lookup"><span data-stu-id="47685-219">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="47685-220">**Rozhraní .NET framework** &ndash; Pokud aplikace vyžaduje rozhraní .NET Framework, přečtěte si článek [Průvodce instalací rozhraní .NET Framework](/dotnet/framework/install/).</span><span class="sxs-lookup"><span data-stu-id="47685-220">**.NET Framework** &ndash; If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="47685-221">Instalace vyžaduje rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="47685-221">Install the required .NET Framework.</span></span> <span data-ttu-id="47685-222">Instalační program pro nejnovější rozhraní .NET Framework je k dispozici z [soubory ke stažení rozhraní .NET Core](https://dotnet.microsoft.com/download) stránky.</span><span class="sxs-lookup"><span data-stu-id="47685-222">The installer for the latest .NET Framework is available from from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="47685-223">Pokud je aplikace [samostatná nasazení](/dotnet/core/deploying/#framework-dependent-deployments-scd), tato aplikace obsahuje modul runtime v jeho nasazení.</span><span class="sxs-lookup"><span data-stu-id="47685-223">If the app is a [self-contained deployment](/dotnet/core/deploying/#framework-dependent-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="47685-224">Na serveru není nutné instalovat rozhraní framework.</span><span class="sxs-lookup"><span data-stu-id="47685-224">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="47685-225">Konfigurace adresy URL a portů v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="47685-225">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="47685-226">Ve výchozím nastavení, ASP.NET Core váže k `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="47685-226">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="47685-227">Pokud chcete nakonfigurovat předpony adres URL a portů, mezi možnosti patří:</span><span class="sxs-lookup"><span data-stu-id="47685-227">To configure URL prefixes and ports, options include:</span></span>

   * [<span data-ttu-id="47685-228">UseUrls</span><span class="sxs-lookup"><span data-stu-id="47685-228">UseUrls</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useurls)
   * <span data-ttu-id="47685-229">`urls` argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="47685-229">`urls` command-line argument</span></span>
   * <span data-ttu-id="47685-230">`ASPNETCORE_URLS` Proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="47685-230">`ASPNETCORE_URLS` environment variable</span></span>
   * [<span data-ttu-id="47685-231">UrlPrefixes</span><span class="sxs-lookup"><span data-stu-id="47685-231">UrlPrefixes</span></span>](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.urlprefixes)

   <span data-ttu-id="47685-232">Následující příklad kódu ukazuje, jak používat [UrlPrefixes](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.urlprefixes) s místní IP adresa serveru `10.0.0.4` na portu 443:</span><span class="sxs-lookup"><span data-stu-id="47685-232">The following code example shows how to use [UrlPrefixes](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.urlprefixes) with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/sample_snapshot/Program.cs?name=snippet1&highlight=11)]

   <span data-ttu-id="47685-233">Výhodou `UrlPrefixes` je okamžitě vygenerována chybová zpráva pro nesprávně formátovaná předpony.</span><span class="sxs-lookup"><span data-stu-id="47685-233">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="47685-234">Nastavení v `UrlPrefixes` přepsat `UseUrls` / `urls` / `ASPNETCORE_URLS` nastavení.</span><span class="sxs-lookup"><span data-stu-id="47685-234">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="47685-235">Proto výhodou `UseUrls`, `urls`a `ASPNETCORE_URLS` proměnná prostředí je, že je snadnější přepínání mezi Kestrel a HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="47685-235">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span> <span data-ttu-id="47685-236">Další informace naleznete v tématu <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="47685-236">For more information, see <xref:fundamentals/host/web-host>.</span></span>

   <span data-ttu-id="47685-237">Používá HTTP.sys [formáty řetězců HTTP Server API UrlPrefix](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span><span class="sxs-lookup"><span data-stu-id="47685-237">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

   > [!WARNING]
   > <span data-ttu-id="47685-238">Vazby nejvyšší úrovně zástupný znak (`http://*:80/` a `http://+:80`) by měl **není** použít.</span><span class="sxs-lookup"><span data-stu-id="47685-238">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="47685-239">Nejvyšší úrovně zástupné vazby vzniknout slabá místa zabezpečení aplikace.</span><span class="sxs-lookup"><span data-stu-id="47685-239">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="47685-240">To platí pro silné a slabé zástupné znaky.</span><span class="sxs-lookup"><span data-stu-id="47685-240">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="47685-241">Používejte explicitní hostitele nebo IP adresy místo zástupné znaky.</span><span class="sxs-lookup"><span data-stu-id="47685-241">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="47685-242">Vazby zástupný znak subdoménu (například `*.mysub.com`) není bezpečnostní riziko, pokud celé nadřazené domény (nikoli `*.com`, což je ohrožené).</span><span class="sxs-lookup"><span data-stu-id="47685-242">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="47685-243">Další informace najdete v tématu [RFC 7230: Oddíl 5.4: Hostitel](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="47685-243">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="47685-244">Preregister předpony adres URL na serveru.</span><span class="sxs-lookup"><span data-stu-id="47685-244">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="47685-245">Integrované nástroje pro konfiguraci HTTP.sys se *netsh.exe*.</span><span class="sxs-lookup"><span data-stu-id="47685-245">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="47685-246">*Netsh.exe* slouží k předpony adres URL rezervovat a přiřadit certifikáty X.509.</span><span class="sxs-lookup"><span data-stu-id="47685-246">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="47685-247">Nástroj vyžaduje oprávnění správce.</span><span class="sxs-lookup"><span data-stu-id="47685-247">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="47685-248">Použití *netsh.exe* nástroj pro registraci adresy URL pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="47685-248">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="47685-249">`<URL>` &ndash; Plně kvalifikovanou URL Uniform Resource Locator ().</span><span class="sxs-lookup"><span data-stu-id="47685-249">`<URL>` &ndash; The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="47685-250">Nepoužívejte zástupné vazby.</span><span class="sxs-lookup"><span data-stu-id="47685-250">Don't use a wildcard binding.</span></span> <span data-ttu-id="47685-251">Použijte platný název hostitele nebo IP adresa.</span><span class="sxs-lookup"><span data-stu-id="47685-251">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="47685-252">*Adresa URL musí obsahovat koncové lomítko.*</span><span class="sxs-lookup"><span data-stu-id="47685-252">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="47685-253">`<USER>` &ndash; Určuje jméno uživatele nebo skupiny uživatelů.</span><span class="sxs-lookup"><span data-stu-id="47685-253">`<USER>` &ndash; Specifies the user or user-group name.</span></span>

   <span data-ttu-id="47685-254">V následujícím příkladu je místní IP adresa serveru `10.0.0.4`:</span><span class="sxs-lookup"><span data-stu-id="47685-254">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="47685-255">Při registraci adresy URL nástroj odpoví `URL reservation successfully added`.</span><span class="sxs-lookup"><span data-stu-id="47685-255">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="47685-256">Chcete-li odstranit registrovaný adresu URL, použijte `delete urlacl` příkaz:</span><span class="sxs-lookup"><span data-stu-id="47685-256">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="47685-257">Zaregistrujte certifikáty X.509 na serveru.</span><span class="sxs-lookup"><span data-stu-id="47685-257">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="47685-258">Použití *netsh.exe* nástroj k registraci certifikátů pro aplikace:</span><span class="sxs-lookup"><span data-stu-id="47685-258">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="47685-259">`<IP>` &ndash; Určuje místní IP adresa pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="47685-259">`<IP>` &ndash; Specifies the local IP address for the binding.</span></span> <span data-ttu-id="47685-260">Nepoužívejte zástupné vazby.</span><span class="sxs-lookup"><span data-stu-id="47685-260">Don't use a wildcard binding.</span></span> <span data-ttu-id="47685-261">Použijte platnou IP adresu.</span><span class="sxs-lookup"><span data-stu-id="47685-261">Use a valid IP address.</span></span>
   * <span data-ttu-id="47685-262">`<PORT>` &ndash; Určuje port pro vazby.</span><span class="sxs-lookup"><span data-stu-id="47685-262">`<PORT>` &ndash; Specifies the port for the binding.</span></span>
   * <span data-ttu-id="47685-263">`<THUMBPRINT>` &ndash; Kryptografický otisk certifikátu X.509.</span><span class="sxs-lookup"><span data-stu-id="47685-263">`<THUMBPRINT>` &ndash; The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="47685-264">`<GUID>` &ndash; Vygeneruje pro vývojáře identifikátor GUID představující aplikace k informačním účelům.</span><span class="sxs-lookup"><span data-stu-id="47685-264">`<GUID>` &ndash; A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="47685-265">Pro referenční účely uložení identifikátoru GUID v aplikaci jako značku balíčku:</span><span class="sxs-lookup"><span data-stu-id="47685-265">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="47685-266">V sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="47685-266">In Visual Studio:</span></span>
     * <span data-ttu-id="47685-267">Otevřete vlastnosti projektu aplikace kliknutím pravým tlačítkem na aplikaci v **Průzkumníka řešení** a vyberete **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="47685-267">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="47685-268">Vyberte **balíčku** kartu.</span><span class="sxs-lookup"><span data-stu-id="47685-268">Select the **Package** tab.</span></span>
     * <span data-ttu-id="47685-269">Zadejte identifikátor GUID, které jste vytvořili **značky** pole.</span><span class="sxs-lookup"><span data-stu-id="47685-269">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="47685-270">Pokud nepoužíváte Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="47685-270">When not using Visual Studio:</span></span>
     * <span data-ttu-id="47685-271">Otevření souboru projektu vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="47685-271">Open the app's project file.</span></span>
     * <span data-ttu-id="47685-272">Přidat `<PackageTags>` vlastnost na nový nebo existující `<PropertyGroup>` identifikátor GUID, který jste vytvořili:</span><span class="sxs-lookup"><span data-stu-id="47685-272">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="47685-273">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="47685-273">In the following example:</span></span>

   * <span data-ttu-id="47685-274">Místní IP adresa serveru je `10.0.0.4`.</span><span class="sxs-lookup"><span data-stu-id="47685-274">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="47685-275">Poskytuje online náhodný GUID generator `appid` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="47685-275">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="47685-276">Při registraci certifikátu nástroj odpoví `SSL Certificate successfully added`.</span><span class="sxs-lookup"><span data-stu-id="47685-276">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="47685-277">Chcete-li odstranit registrace certifikátu, použijte `delete sslcert` příkaz:</span><span class="sxs-lookup"><span data-stu-id="47685-277">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="47685-278">Referenční dokumentace pro *netsh.exe*:</span><span class="sxs-lookup"><span data-stu-id="47685-278">Reference documentation for *netsh.exe*:</span></span>

   * [<span data-ttu-id="47685-279">Příkazy Netsh pro Hypertext Transfer Protocol (HTTP)</span><span class="sxs-lookup"><span data-stu-id="47685-279">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](https://technet.microsoft.com/library/cc725882.aspx)
   * [<span data-ttu-id="47685-280">UrlPrefix řetězce</span><span class="sxs-lookup"><span data-stu-id="47685-280">UrlPrefix Strings</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. <span data-ttu-id="47685-281">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="47685-281">Run the app.</span></span>

   <span data-ttu-id="47685-282">Oprávnění správce se vyžaduje ke spuštění aplikace při vytváření vazby na místního hostitele pomocí protokolu HTTP (nikoli HTTPS) s více než 1 024 číslo portu.</span><span class="sxs-lookup"><span data-stu-id="47685-282">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="47685-283">Pro další konfiguraci (například pomocí místní IP adresa nebo vazbu na port 443) spusťte aplikaci s oprávněním správce.</span><span class="sxs-lookup"><span data-stu-id="47685-283">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="47685-284">Aplikace jsou reaguje na veřejnou IP adresu serveru.</span><span class="sxs-lookup"><span data-stu-id="47685-284">The app responds at the server's public IP address.</span></span> <span data-ttu-id="47685-285">V tomto příkladu je server dostupný z Internetu na veřejné IP adrese `104.214.79.47`.</span><span class="sxs-lookup"><span data-stu-id="47685-285">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="47685-286">V tomto příkladu se používá certifikát pro vývoj.</span><span class="sxs-lookup"><span data-stu-id="47685-286">A development certificate is used in this example.</span></span> <span data-ttu-id="47685-287">Stránka načte bezpečně po vynechání upozornění na nedůvěryhodný certifikát prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="47685-287">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![Načíst okno prohlížeče zobrazující indexová stránka aplikace](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="47685-289">Proxy server a scénáře pro nástroj pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="47685-289">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="47685-290">Pro aplikace hostované souborem HTTP.sys, které pracují s žádostí z Internetu nebo podnikové sítě může být další konfigurace požadovaných při hostování za proxy servery a nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="47685-290">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="47685-291">Další informace najdete v tématu [konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="47685-291">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="47685-292">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="47685-292">Additional resources</span></span>

* [<span data-ttu-id="47685-293">Povolení ověřování Windows pomocí HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="47685-293">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#enable-windows-authentication-with-httpsys)
* [<span data-ttu-id="47685-294">Server HTTP rozhraní API</span><span class="sxs-lookup"><span data-stu-id="47685-294">HTTP Server API</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [<span data-ttu-id="47685-295">úložiště GitHub ASPNET/HttpSysServer (zdrojového kódu)</span><span class="sxs-lookup"><span data-stu-id="47685-295">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* <xref:fundamentals/host/index>
* <xref:test/troubleshoot>
