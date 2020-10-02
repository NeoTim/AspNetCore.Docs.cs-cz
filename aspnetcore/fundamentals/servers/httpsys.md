---
title: Implementace webového serveru HTTP.sys v ASP.NET Core
author: rick-anderson
description: Přečtěte si o HTTP.sys, webovém serveru pro ASP.NET Core ve Windows. HTTP.sys je alternativou k Kestrel, která je založená na HTTP.sys ovladač režimu jádra, který je možné použít k přímému připojení k Internetu bez služby IIS.
monikerRange: '>= aspnetcore-2.1'
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
uid: fundamentals/servers/httpsys
ms.openlocfilehash: 8ed9ec3447205107194ffa5c329c0e5ae0fc5553
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653968"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="d684c-104">Implementace webového serveru HTTP.sys v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d684c-104">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="d684c-105">[Dykstra](https://github.com/tdykstra) a [Chris Rossův](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="d684c-105">By [Tom Dykstra](https://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="d684c-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) je [webový server pro ASP.NET Core](xref:fundamentals/servers/index) , který běží jenom ve Windows.</span><span class="sxs-lookup"><span data-stu-id="d684c-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="d684c-107">HTTP.sys je alternativou k serveru [Kestrel](xref:fundamentals/servers/kestrel) a nabízí některé funkce, které Kestrel neposkytují.</span><span class="sxs-lookup"><span data-stu-id="d684c-107">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d684c-108">HTTP.sys není kompatibilní s [modulem ASP.NET Core](xref:host-and-deploy/aspnet-core-module) a nedá se použít se službou IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="d684c-108">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="d684c-109">HTTP.sys podporuje následující funkce:</span><span class="sxs-lookup"><span data-stu-id="d684c-109">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="d684c-110">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="d684c-110">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="d684c-111">Sdílení portů</span><span class="sxs-lookup"><span data-stu-id="d684c-111">Port sharing</span></span>
* <span data-ttu-id="d684c-112">HTTPS s SNI</span><span class="sxs-lookup"><span data-stu-id="d684c-112">HTTPS with SNI</span></span>
* <span data-ttu-id="d684c-113">HTTP/2 přes TLS (Windows 10 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="d684c-113">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="d684c-114">Přímý přenos souborů</span><span class="sxs-lookup"><span data-stu-id="d684c-114">Direct file transmission</span></span>
* <span data-ttu-id="d684c-115">Ukládání odpovědí do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="d684c-115">Response caching</span></span>
* <span data-ttu-id="d684c-116">WebSockets (Windows 8 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="d684c-116">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="d684c-117">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="d684c-117">Supported Windows versions:</span></span>

* <span data-ttu-id="d684c-118">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d684c-118">Windows 7 or later</span></span>
* <span data-ttu-id="d684c-119">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d684c-119">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="d684c-120">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d684c-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="d684c-121">Kdy použít HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="d684c-121">When to use HTTP.sys</span></span>

<span data-ttu-id="d684c-122">HTTP.sys je užitečná pro nasazení, kde:</span><span class="sxs-lookup"><span data-stu-id="d684c-122">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="d684c-123">Server je potřeba zveřejnit přímo na internetu bez použití IIS.</span><span class="sxs-lookup"><span data-stu-id="d684c-123">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys komunikuje přímo s internetem.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="d684c-125">Interní nasazení vyžaduje funkci, která není v Kestrel k dispozici, jako je například [ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="d684c-125">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys komunikuje přímo s interní sítí.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="d684c-127">HTTP.sys je Vyspělá technologie, která chrání před mnoha typy útoků a poskytuje odolnost, zabezpečení a škálovatelnost plně funkčního webového serveru.</span><span class="sxs-lookup"><span data-stu-id="d684c-127">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="d684c-128">Služba IIS sám spouští jako naslouchací proces HTTP nad HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d684c-128">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="d684c-129">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="d684c-129">HTTP/2 support</span></span>

<span data-ttu-id="d684c-130">[Protokol HTTP/2](https://httpwg.org/specs/rfc7540.html) je povolený pro aplikace ASP.NET Core, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="d684c-130">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="d684c-131">Windows Server 2016/Windows 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d684c-131">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="d684c-132">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="d684c-132">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="d684c-133">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d684c-133">TLS 1.2 or later connection</span></span>

<span data-ttu-id="d684c-134">Pokud je vytvořeno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="d684c-134">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="d684c-135">HTTP/2 je ve výchozím nastavení povolené.</span><span class="sxs-lookup"><span data-stu-id="d684c-135">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="d684c-136">Pokud připojení HTTP/2 není navázáno, připojení se vrátí k HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="d684c-136">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="d684c-137">V budoucí verzi Windows budou k dispozici příznaky konfigurace protokolu HTTP/2, včetně možnosti zakázat protokol HTTP/2 s HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d684c-137">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="d684c-138">Ověřování v režimu jádra pomocí protokolu Kerberos</span><span class="sxs-lookup"><span data-stu-id="d684c-138">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="d684c-139">HTTP.sys deleguje ověřování režimu jádra pomocí ověřovacího protokolu Kerberos.</span><span class="sxs-lookup"><span data-stu-id="d684c-139">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="d684c-140">Ověřování pomocí uživatelského režimu není pro protokol Kerberos a HTTP.sys podporováno.</span><span class="sxs-lookup"><span data-stu-id="d684c-140">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="d684c-141">Účet počítače se musí použít k dešifrování tokenu nebo lístku protokolu Kerberos, který je získaný ze služby Active Directory a který přesměruje klient na server za účelem ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="d684c-141">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="d684c-142">Zaregistrujte hlavní název služby (SPN) pro hostitele, nikoli uživatele aplikace.</span><span class="sxs-lookup"><span data-stu-id="d684c-142">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="d684c-143">Jak používat HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="d684c-143">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="d684c-144">Konfigurace aplikace ASP.NET Core pro použití HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="d684c-144">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="d684c-145"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>Při sestavování hostitele zavolejte metodu rozšíření a určete požadované <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> .</span><span class="sxs-lookup"><span data-stu-id="d684c-145">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="d684c-146">Následující příklad nastaví možnosti na jejich výchozí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="d684c-146">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="d684c-147">Další konfigurace HTTP.sys se zpracovává prostřednictvím [nastavení registru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span><span class="sxs-lookup"><span data-stu-id="d684c-147">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="d684c-148">\*\* MožnostiHTTP.sys\*\*</span><span class="sxs-lookup"><span data-stu-id="d684c-148">**HTTP.sys options**</span></span>

| <span data-ttu-id="d684c-149">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="d684c-149">Property</span></span> | <span data-ttu-id="d684c-150">Popis</span><span class="sxs-lookup"><span data-stu-id="d684c-150">Description</span></span> | <span data-ttu-id="d684c-151">Výchozí</span><span class="sxs-lookup"><span data-stu-id="d684c-151">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="d684c-152">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="d684c-152">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="d684c-153">Určuje, zda je povolen synchronní vstup/výstup pro `HttpContext.Request.Body` a `HttpContext.Response.Body` .</span><span class="sxs-lookup"><span data-stu-id="d684c-153">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="d684c-154">Ověřování. AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="d684c-154">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="d684c-155">Povoluje anonymní požadavky.</span><span class="sxs-lookup"><span data-stu-id="d684c-155">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="d684c-156">Ověřování. schémata</span><span class="sxs-lookup"><span data-stu-id="d684c-156">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="d684c-157">Určete povolená schémata ověřování.</span><span class="sxs-lookup"><span data-stu-id="d684c-157">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="d684c-158">Může být kdykoli změněno před vyřazením naslouchacího procesu.</span><span class="sxs-lookup"><span data-stu-id="d684c-158">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="d684c-159">Hodnoty jsou k dispozici ve [výčtu schémata AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic` , `Kerberos` , `Negotiate` , `None` a `NTLM` .</span><span class="sxs-lookup"><span data-stu-id="d684c-159">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="d684c-160">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="d684c-160">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="d684c-161">Pokusit se o ukládání do mezipaměti v [režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) pro odpovědi s oprávněnými záhlavími.</span><span class="sxs-lookup"><span data-stu-id="d684c-161">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="d684c-162">Odpověď nesmí obsahovat `Set-Cookie` `Vary` hlavičky, ani `Pragma` .</span><span class="sxs-lookup"><span data-stu-id="d684c-162">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="d684c-163">Musí obsahovat `Cache-Control` hlavičku, která je `public` a buď `shared-max-age` `max-age` hodnota nebo, nebo `Expires` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="d684c-163">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="d684c-164">Maximální počet souběžných přijetí.</span><span class="sxs-lookup"><span data-stu-id="d684c-164">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="d684c-165">5 &times; [prostředí. <br> ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="d684c-165">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="d684c-166">Maximální počet souběžných připojení, která se mají přijmout</span><span class="sxs-lookup"><span data-stu-id="d684c-166">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="d684c-167">Použijte `-1` pro nekonečné.</span><span class="sxs-lookup"><span data-stu-id="d684c-167">Use `-1` for infinite.</span></span> <span data-ttu-id="d684c-168">Použijte `null` k použití nastavení pro počítač v rámci registru.</span><span class="sxs-lookup"><span data-stu-id="d684c-168">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="d684c-169">(počítačově v šířce</span><span class="sxs-lookup"><span data-stu-id="d684c-169">(machine-wide</span></span><br><span data-ttu-id="d684c-170">nastavením</span><span class="sxs-lookup"><span data-stu-id="d684c-170">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="d684c-171">Viz část <a href="#maxrequestbodysize">MaxRequestBodySize</a> .</span><span class="sxs-lookup"><span data-stu-id="d684c-171">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="d684c-172">30000000 bajtů</span><span class="sxs-lookup"><span data-stu-id="d684c-172">30000000 bytes</span></span><br><span data-ttu-id="d684c-173">(~ 28,6 MB)</span><span class="sxs-lookup"><span data-stu-id="d684c-173">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="d684c-174">Maximální počet požadavků, které lze zařadit do fronty.</span><span class="sxs-lookup"><span data-stu-id="d684c-174">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="d684c-175">1000</span><span class="sxs-lookup"><span data-stu-id="d684c-175">1000</span></span> |
| `RequestQueueMode` | <span data-ttu-id="d684c-176">Označuje, zda je server zodpovědný za vytvoření a konfiguraci fronty požadavků nebo zda se má připojit k existující frontě.</span><span class="sxs-lookup"><span data-stu-id="d684c-176">This indicates whether the server is responsible for creating and configuring the request queue, or if it should attach to an existing queue.</span></span><br><span data-ttu-id="d684c-177">Většina stávajících možností konfigurace se nedá použít při připojování k existující frontě.</span><span class="sxs-lookup"><span data-stu-id="d684c-177">Most existing configuration options do not apply when attaching to an existing queue.</span></span> | `RequestQueueMode.Create` |
| `RequestQueueName` | <span data-ttu-id="d684c-178">Název fronty požadavků HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d684c-178">The name of the HTTP.sys request queue.</span></span> | <span data-ttu-id="d684c-179">`null` (Anonymní fronta)</span><span class="sxs-lookup"><span data-stu-id="d684c-179">`null` (Anonymous queue)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="d684c-180">Určuje, jestli tělo odpovědi zapisuje, které selhalo kvůli odpojení klienta, by mělo vyvolat výjimky nebo dokončit normálně.</span><span class="sxs-lookup"><span data-stu-id="d684c-180">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="d684c-181">(normálně dokončit)</span><span class="sxs-lookup"><span data-stu-id="d684c-181">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="d684c-182">Vystavte <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> konfiguraci HTTP.sys, která může být v registru nakonfigurovaná taky.</span><span class="sxs-lookup"><span data-stu-id="d684c-182">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="d684c-183">Pomocí odkazů rozhraní API získáte další informace o jednotlivých nastaveních, včetně výchozích hodnot:</span><span class="sxs-lookup"><span data-stu-id="d684c-183">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="d684c-184">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): čas povolený pro rozhraní API serveru http, který má vyprázdnit tělo entity u připojení Keep-Alive.</span><span class="sxs-lookup"><span data-stu-id="d684c-184">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="d684c-185">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): čas povolený pro doručení těla entity požadavku.</span><span class="sxs-lookup"><span data-stu-id="d684c-185">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="d684c-186">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): čas povolený pro rozhraní API serveru HTTP k analýze hlavičky žádosti.</span><span class="sxs-lookup"><span data-stu-id="d684c-186">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="d684c-187">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): čas povolený pro nečinné připojení.</span><span class="sxs-lookup"><span data-stu-id="d684c-187">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="d684c-188">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): minimální rychlost odesílání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="d684c-188">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="d684c-189">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): čas, který může požadavek zůstat ve frontě požadavků předtím, než ji aplikace vybere.</span><span class="sxs-lookup"><span data-stu-id="d684c-189">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="d684c-190">Zadejte, kam se <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> má zaregistrovat HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d684c-190">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="d684c-191">Nejužitečnější je [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), který se používá k přidání předpony do kolekce.</span><span class="sxs-lookup"><span data-stu-id="d684c-191">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="d684c-192">Ty mohou být kdykoliv změněny předtím, než se naslouchací proces vyřazuje.</span><span class="sxs-lookup"><span data-stu-id="d684c-192">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="d684c-193">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="d684c-193">**MaxRequestBodySize**</span></span>

<span data-ttu-id="d684c-194">Maximální povolená velikost libovolného textu žádosti v bajtech</span><span class="sxs-lookup"><span data-stu-id="d684c-194">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="d684c-195">Při nastavení na `null` je maximální velikost textu požadavku neomezená.</span><span class="sxs-lookup"><span data-stu-id="d684c-195">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="d684c-196">Toto omezení nemá žádný vliv na upgradovaná připojení, která jsou vždycky neomezená.</span><span class="sxs-lookup"><span data-stu-id="d684c-196">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="d684c-197">Doporučená metoda pro přepsání limitu v aplikaci ASP.NET Core MVC pro jeden `IActionResult` je použít <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atribut pro metodu akce:</span><span class="sxs-lookup"><span data-stu-id="d684c-197">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="d684c-198">Výjimka je vyvolána, pokud se aplikace pokusí nakonfigurovat limit žádosti poté, co aplikace začne číst požadavek.</span><span class="sxs-lookup"><span data-stu-id="d684c-198">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="d684c-199">`IsReadOnly`Vlastnost může být použita k označení `MaxRequestBodySize` , zda je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="d684c-199">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="d684c-200">Pokud by měla aplikace přepsat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> jednotlivé požadavky, použijte <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :</span><span class="sxs-lookup"><span data-stu-id="d684c-200">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="d684c-201">Pokud používáte Visual Studio, ujistěte se, že aplikace není nakonfigurovaná tak, aby spouštěla službu IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="d684c-201">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="d684c-202">V aplikaci Visual Studio je výchozím spouštěcím profilem IIS Express.</span><span class="sxs-lookup"><span data-stu-id="d684c-202">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="d684c-203">Chcete-li spustit projekt jako konzolovou aplikaci, ručně změňte vybraný profil, jak je znázorněno na následujícím snímku obrazovky:</span><span class="sxs-lookup"><span data-stu-id="d684c-203">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![Vybrat profil aplikace konzoly](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="d684c-205">Konfigurace Windows serveru</span><span class="sxs-lookup"><span data-stu-id="d684c-205">Configure Windows Server</span></span>

1. <span data-ttu-id="d684c-206">Určete porty, které se mají otevřít pro aplikaci, a pomocí [brány Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) nebo rutiny [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) prostředí PowerShell otevřete porty brány firewall, abyste umožnili provozu HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d684c-206">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="d684c-207">V následujících příkazech a konfiguraci aplikace se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="d684c-207">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="d684c-208">Při nasazování na virtuální počítač Azure otevřete porty ve [skupině zabezpečení sítě](/azure/virtual-machines/windows/nsg-quickstart-portal).</span><span class="sxs-lookup"><span data-stu-id="d684c-208">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="d684c-209">V následujících příkazech a konfiguraci aplikace se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="d684c-209">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="d684c-210">V případě potřeby Získejte a nainstalujte certifikáty X. 509.</span><span class="sxs-lookup"><span data-stu-id="d684c-210">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="d684c-211">V systému Windows vytvořte pomocí [rutiny prostředí PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate)certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="d684c-211">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="d684c-212">Nepodporovaný příklad naleznete v tématu [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span><span class="sxs-lookup"><span data-stu-id="d684c-212">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="d684c-213">Nainstalujte certifikáty podepsané svým držitelem nebo certifikátem podepsaným certifikační autoritou do osobního úložiště **místního počítače** serveru > **Personal** .</span><span class="sxs-lookup"><span data-stu-id="d684c-213">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="d684c-214">Pokud se jedná o [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd), nainstalujte rozhraní .NET core, .NET Framework nebo obojí (Pokud je aplikace aplikace .NET Core cílící na .NET Framework).</span><span class="sxs-lookup"><span data-stu-id="d684c-214">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="d684c-215">**.NET Core**: Pokud aplikace vyžaduje .NET Core, Získejte a spusťte instalační program **modulu runtime .NET Core** ze [souborů ke stažení pro .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="d684c-215">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="d684c-216">Neinstalujte na server plnou sadu SDK.</span><span class="sxs-lookup"><span data-stu-id="d684c-216">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="d684c-217">**.NET Framework**: Pokud aplikace vyžaduje .NET Framework, přečtěte si téma [Průvodce instalací .NET Framework](/dotnet/framework/install/).</span><span class="sxs-lookup"><span data-stu-id="d684c-217">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="d684c-218">Nainstalujte požadovanou .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="d684c-218">Install the required .NET Framework.</span></span> <span data-ttu-id="d684c-219">Instalační program pro nejnovější .NET Framework je k dispozici na stránce [soubory ke stažení pro .NET Core](https://dotnet.microsoft.com/download) .</span><span class="sxs-lookup"><span data-stu-id="d684c-219">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="d684c-220">Pokud je aplikace samostatným [nasazením](/dotnet/core/deploying/#self-contained-deployments-scd), aplikace zahrne modul runtime do svého nasazení.</span><span class="sxs-lookup"><span data-stu-id="d684c-220">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="d684c-221">Na serveru není nutná žádná instalace rozhraní.</span><span class="sxs-lookup"><span data-stu-id="d684c-221">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="d684c-222">Konfigurace adres URL a portů v aplikaci</span><span class="sxs-lookup"><span data-stu-id="d684c-222">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="d684c-223">Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="d684c-223">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d684c-224">Pokud chcete nakonfigurovat předpony a porty adresy URL, můžete použít tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="d684c-224">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="d684c-225">`urls` argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="d684c-225">`urls` command-line argument</span></span>
   * <span data-ttu-id="d684c-226">`ASPNETCORE_URLS` Proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="d684c-226">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="d684c-227">Následující příklad kódu ukazuje, jak používat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> s místní IP adresou serveru `10.0.0.4` na portu 443:</span><span class="sxs-lookup"><span data-stu-id="d684c-227">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="d684c-228">Výhodou `UrlPrefixes` je, že se okamžitě generuje chybová zpráva pro nesprávně naformátované předpony.</span><span class="sxs-lookup"><span data-stu-id="d684c-228">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="d684c-229">Nastavení v `UrlPrefixes` `UseUrls` / `urls` / `ASPNETCORE_URLS` Nastavení přepsání</span><span class="sxs-lookup"><span data-stu-id="d684c-229">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="d684c-230">Proto je výhodou `UseUrls` `urls` `ASPNETCORE_URLS` proměnné prostředí, a jednodušší přepínání mezi Kestrel a HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d684c-230">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="d684c-231">HTTP.sys používá [formáty řetězce UrlPrefix HTTP serveru API](/windows/win32/http/urlprefix-strings).</span><span class="sxs-lookup"><span data-stu-id="d684c-231">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="d684c-232">Nelze použít vazby zástupných znaků na nejvyšší úrovni ( `http://*:80/` a `http://+:80` ). **not**</span><span class="sxs-lookup"><span data-stu-id="d684c-232">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="d684c-233">Vazby na zástupné znaky na nejvyšší úrovni vytvoří chyby zabezpečení aplikace.</span><span class="sxs-lookup"><span data-stu-id="d684c-233">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="d684c-234">To platí pro silné i slabé zástupné znaky.</span><span class="sxs-lookup"><span data-stu-id="d684c-234">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="d684c-235">Místo zástupných znaků použijte explicitní názvy hostitelů nebo IP adresy.</span><span class="sxs-lookup"><span data-stu-id="d684c-235">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="d684c-236">Vazba zástupných znaků subdomény (například `*.mysub.com` ) není bezpečnostní riziko, pokud ovládáte celou nadřazenou doménu (na rozdíl od `*.com` , která je zranitelná).</span><span class="sxs-lookup"><span data-stu-id="d684c-236">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="d684c-237">Další informace najdete v [dokumentu RFC 7230: oddíl 5,4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="d684c-237">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="d684c-238">Předregistrujte předpony adresy URL na serveru.</span><span class="sxs-lookup"><span data-stu-id="d684c-238">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="d684c-239">K \* disnetsh.exe\*je integrovaný nástroj pro konfiguraci HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d684c-239">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="d684c-240">*netsh.exe* se používá k rezervaci předpon adres URL a přiřazování certifikátů X. 509.</span><span class="sxs-lookup"><span data-stu-id="d684c-240">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="d684c-241">Nástroj vyžaduje oprávnění správce.</span><span class="sxs-lookup"><span data-stu-id="d684c-241">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="d684c-242">K registraci adres URL aplikace použijte nástroj *netsh.exe* :</span><span class="sxs-lookup"><span data-stu-id="d684c-242">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="d684c-243">`<URL>`: Plně kvalifikované adresy URL (Uniform Resource Locator).</span><span class="sxs-lookup"><span data-stu-id="d684c-243">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="d684c-244">Nepoužívejte vazbu zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="d684c-244">Don't use a wildcard binding.</span></span> <span data-ttu-id="d684c-245">Použijte platný název hostitele nebo místní IP adresu.</span><span class="sxs-lookup"><span data-stu-id="d684c-245">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="d684c-246">*Adresa URL musí obsahovat koncové lomítko.*</span><span class="sxs-lookup"><span data-stu-id="d684c-246">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="d684c-247">`<USER>`: Určuje název uživatele nebo skupiny uživatelů.</span><span class="sxs-lookup"><span data-stu-id="d684c-247">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="d684c-248">V následujícím příkladu je místní IP adresa serveru `10.0.0.4` :</span><span class="sxs-lookup"><span data-stu-id="d684c-248">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="d684c-249">Když je adresa URL registrována, nástroj odpoví `URL reservation successfully added` .</span><span class="sxs-lookup"><span data-stu-id="d684c-249">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="d684c-250">Pokud chcete odstranit registrovanou adresu URL, použijte `delete urlacl` příkaz:</span><span class="sxs-lookup"><span data-stu-id="d684c-250">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="d684c-251">Zaregistrujte certifikáty X. 509 na serveru.</span><span class="sxs-lookup"><span data-stu-id="d684c-251">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="d684c-252">K registraci certifikátů pro aplikaci použijte nástroj *netsh.exe* :</span><span class="sxs-lookup"><span data-stu-id="d684c-252">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="d684c-253">`<IP>`: Určuje místní IP adresu pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="d684c-253">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="d684c-254">Nepoužívejte vazbu zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="d684c-254">Don't use a wildcard binding.</span></span> <span data-ttu-id="d684c-255">Použijte platnou IP adresu.</span><span class="sxs-lookup"><span data-stu-id="d684c-255">Use a valid IP address.</span></span>
   * <span data-ttu-id="d684c-256">`<PORT>`: Určuje port pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="d684c-256">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="d684c-257">`<THUMBPRINT>`: Kryptografický otisk certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="d684c-257">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="d684c-258">`<GUID>`: Identifikátor GUID generovaný vývojářem, který představuje aplikaci pro informativní účely.</span><span class="sxs-lookup"><span data-stu-id="d684c-258">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="d684c-259">Pro referenční účely uložte identifikátor GUID do aplikace jako značku balíčku:</span><span class="sxs-lookup"><span data-stu-id="d684c-259">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="d684c-260">V aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d684c-260">In Visual Studio:</span></span>
     * <span data-ttu-id="d684c-261">Otevřete vlastnosti projektu aplikace tak, že kliknete pravým tlačítkem na aplikaci v **Průzkumník řešení** a vyberete **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="d684c-261">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="d684c-262">Vyberte kartu **balíček** .</span><span class="sxs-lookup"><span data-stu-id="d684c-262">Select the **Package** tab.</span></span>
     * <span data-ttu-id="d684c-263">Zadejte identifikátor GUID, který jste vytvořili v poli **značky** .</span><span class="sxs-lookup"><span data-stu-id="d684c-263">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="d684c-264">Když nepoužíváte Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d684c-264">When not using Visual Studio:</span></span>
     * <span data-ttu-id="d684c-265">Otevřete soubor projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="d684c-265">Open the app's project file.</span></span>
     * <span data-ttu-id="d684c-266">Přidejte `<PackageTags>` vlastnost do nového nebo existujícího `<PropertyGroup>` s identifikátorem GUID, který jste vytvořili:</span><span class="sxs-lookup"><span data-stu-id="d684c-266">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="d684c-267">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="d684c-267">In the following example:</span></span>

   * <span data-ttu-id="d684c-268">Místní IP adresa serveru je `10.0.0.4` .</span><span class="sxs-lookup"><span data-stu-id="d684c-268">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="d684c-269">Generátor náhodných identifikátorů GUID online poskytuje `appid` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="d684c-269">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="d684c-270">Když je certifikát zaregistrován, nástroj odpoví `SSL Certificate successfully added` .</span><span class="sxs-lookup"><span data-stu-id="d684c-270">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="d684c-271">K odstranění registrace certifikátu použijte `delete sslcert` příkaz:</span><span class="sxs-lookup"><span data-stu-id="d684c-271">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="d684c-272">Referenční dokumentace pro *netsh.exe*:</span><span class="sxs-lookup"><span data-stu-id="d684c-272">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="d684c-273">[Příkazy Netsh pro protokol HTTP (Hypertext Transfer Protocol)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="d684c-273">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="d684c-274">UrlPrefix řetězce</span><span class="sxs-lookup"><span data-stu-id="d684c-274">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="d684c-275">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d684c-275">Run the app.</span></span>

   <span data-ttu-id="d684c-276">Oprávnění správce nejsou nutná ke spuštění aplikace při vytváření vazby na localhost pomocí protokolu HTTP (ne HTTPS) s číslem portu větším než 1024.</span><span class="sxs-lookup"><span data-stu-id="d684c-276">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="d684c-277">U ostatních konfigurací (například pomocí místní IP adresy nebo vazby na port 443) spusťte aplikaci s oprávněními správce.</span><span class="sxs-lookup"><span data-stu-id="d684c-277">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="d684c-278">Aplikace odpoví na veřejnou IP adresu serveru.</span><span class="sxs-lookup"><span data-stu-id="d684c-278">The app responds at the server's public IP address.</span></span> <span data-ttu-id="d684c-279">V tomto příkladu je server dosažitelný z Internetu na jeho veřejné IP adrese `104.214.79.47` .</span><span class="sxs-lookup"><span data-stu-id="d684c-279">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="d684c-280">V tomto příkladu se používá vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="d684c-280">A development certificate is used in this example.</span></span> <span data-ttu-id="d684c-281">Stránka se načte bezpečně po obejít upozornění na nedůvěryhodný certifikát v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="d684c-281">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![Okno prohlížeče zobrazující načtenou stránku indexu aplikace](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d684c-283">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="d684c-283">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d684c-284">Pro aplikace hostované HTTP.sys, které komunikují s požadavky z Internetu nebo podnikové sítě, se může při hostování za proxy servery a nástroji pro vyrovnávání zatížení vyžadovat další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="d684c-284">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="d684c-285">Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="d684c-285">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="advanced-http2-features-to-support-grpc"></a><span data-ttu-id="d684c-286">Rozšířené funkce protokolu HTTP/2 pro podporu gRPC</span><span class="sxs-lookup"><span data-stu-id="d684c-286">Advanced HTTP/2 features to support gRPC</span></span>

<span data-ttu-id="d684c-287">Další funkce HTTP/2 v HTTP.sys podporují gRPC, včetně podpory pro přívěsy odpovědí a odesílání snímků pro resetování.</span><span class="sxs-lookup"><span data-stu-id="d684c-287">Additional HTTP/2 features in HTTP.sys support gRPC, including support for response trailers and sending reset frames.</span></span>

<span data-ttu-id="d684c-288">Požadavky na spuštění gRPC s HTTP.SYS:</span><span class="sxs-lookup"><span data-stu-id="d684c-288">Requirements to run gRPC with HTTP.SYS:</span></span>

* <span data-ttu-id="d684c-289">Windows 10, Build operačního systému 19041,508 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d684c-289">Windows 10, OS Build 19041.508 or later</span></span>
* <span data-ttu-id="d684c-290">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d684c-290">TLS 1.2 or later connection</span></span>

### <a name="trailers"></a><span data-ttu-id="d684c-291">Karoseri</span><span class="sxs-lookup"><span data-stu-id="d684c-291">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="d684c-292">Resetovat</span><span class="sxs-lookup"><span data-stu-id="d684c-292">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

## <a name="additional-resources"></a><span data-ttu-id="d684c-293">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="d684c-293">Additional resources</span></span>

* [<span data-ttu-id="d684c-294">Povolit ověřování systému Windows pomocí HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="d684c-294">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="d684c-295">Rozhraní API serveru HTTP</span><span class="sxs-lookup"><span data-stu-id="d684c-295">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="d684c-296">úložiště GitHubu ASPNET/HttpSysServer (zdrojový kód)</span><span class="sxs-lookup"><span data-stu-id="d684c-296">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="d684c-297">Hostitel</span><span class="sxs-lookup"><span data-stu-id="d684c-297">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="d684c-298">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) je [webový server pro ASP.NET Core](xref:fundamentals/servers/index) , který běží jenom ve Windows.</span><span class="sxs-lookup"><span data-stu-id="d684c-298">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="d684c-299">HTTP.sys je alternativou k serveru [Kestrel](xref:fundamentals/servers/kestrel) a nabízí některé funkce, které Kestrel neposkytují.</span><span class="sxs-lookup"><span data-stu-id="d684c-299">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d684c-300">HTTP.sys není kompatibilní s [modulem ASP.NET Core](xref:host-and-deploy/aspnet-core-module) a nedá se použít se službou IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="d684c-300">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="d684c-301">HTTP.sys podporuje následující funkce:</span><span class="sxs-lookup"><span data-stu-id="d684c-301">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="d684c-302">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="d684c-302">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="d684c-303">Sdílení portů</span><span class="sxs-lookup"><span data-stu-id="d684c-303">Port sharing</span></span>
* <span data-ttu-id="d684c-304">HTTPS s SNI</span><span class="sxs-lookup"><span data-stu-id="d684c-304">HTTPS with SNI</span></span>
* <span data-ttu-id="d684c-305">HTTP/2 přes TLS (Windows 10 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="d684c-305">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="d684c-306">Přímý přenos souborů</span><span class="sxs-lookup"><span data-stu-id="d684c-306">Direct file transmission</span></span>
* <span data-ttu-id="d684c-307">Ukládání odpovědí do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="d684c-307">Response caching</span></span>
* <span data-ttu-id="d684c-308">WebSockets (Windows 8 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="d684c-308">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="d684c-309">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="d684c-309">Supported Windows versions:</span></span>

* <span data-ttu-id="d684c-310">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d684c-310">Windows 7 or later</span></span>
* <span data-ttu-id="d684c-311">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d684c-311">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="d684c-312">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d684c-312">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="d684c-313">Kdy použít HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="d684c-313">When to use HTTP.sys</span></span>

<span data-ttu-id="d684c-314">HTTP.sys je užitečná pro nasazení, kde:</span><span class="sxs-lookup"><span data-stu-id="d684c-314">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="d684c-315">Server je potřeba zveřejnit přímo na internetu bez použití IIS.</span><span class="sxs-lookup"><span data-stu-id="d684c-315">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys komunikuje přímo s internetem.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="d684c-317">Interní nasazení vyžaduje funkci, která není v Kestrel k dispozici, jako je například [ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="d684c-317">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys komunikuje přímo s interní sítí.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="d684c-319">HTTP.sys je Vyspělá technologie, která chrání před mnoha typy útoků a poskytuje odolnost, zabezpečení a škálovatelnost plně funkčního webového serveru.</span><span class="sxs-lookup"><span data-stu-id="d684c-319">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="d684c-320">Služba IIS sám spouští jako naslouchací proces HTTP nad HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d684c-320">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="d684c-321">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="d684c-321">HTTP/2 support</span></span>

<span data-ttu-id="d684c-322">[Protokol HTTP/2](https://httpwg.org/specs/rfc7540.html) je povolený pro aplikace ASP.NET Core, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="d684c-322">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="d684c-323">Windows Server 2016/Windows 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d684c-323">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="d684c-324">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="d684c-324">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="d684c-325">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d684c-325">TLS 1.2 or later connection</span></span>

<span data-ttu-id="d684c-326">Pokud je vytvořeno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="d684c-326">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="d684c-327">HTTP/2 je ve výchozím nastavení povolené.</span><span class="sxs-lookup"><span data-stu-id="d684c-327">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="d684c-328">Pokud připojení HTTP/2 není navázáno, připojení se vrátí k HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="d684c-328">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="d684c-329">V budoucí verzi Windows budou k dispozici příznaky konfigurace protokolu HTTP/2, včetně možnosti zakázat protokol HTTP/2 s HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d684c-329">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="d684c-330">Ověřování v režimu jádra pomocí protokolu Kerberos</span><span class="sxs-lookup"><span data-stu-id="d684c-330">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="d684c-331">HTTP.sys deleguje ověřování režimu jádra pomocí ověřovacího protokolu Kerberos.</span><span class="sxs-lookup"><span data-stu-id="d684c-331">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="d684c-332">Ověřování pomocí uživatelského režimu není pro protokol Kerberos a HTTP.sys podporováno.</span><span class="sxs-lookup"><span data-stu-id="d684c-332">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="d684c-333">Účet počítače se musí použít k dešifrování tokenu nebo lístku protokolu Kerberos, který je získaný ze služby Active Directory a který přesměruje klient na server za účelem ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="d684c-333">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="d684c-334">Zaregistrujte hlavní název služby (SPN) pro hostitele, nikoli uživatele aplikace.</span><span class="sxs-lookup"><span data-stu-id="d684c-334">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="d684c-335">Jak používat HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="d684c-335">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="d684c-336">Konfigurace aplikace ASP.NET Core pro použití HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="d684c-336">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="d684c-337"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>Při sestavování hostitele zavolejte metodu rozšíření a určete požadované <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> .</span><span class="sxs-lookup"><span data-stu-id="d684c-337">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="d684c-338">Následující příklad nastaví možnosti na jejich výchozí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="d684c-338">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="d684c-339">Další konfigurace HTTP.sys se zpracovává prostřednictvím [nastavení registru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span><span class="sxs-lookup"><span data-stu-id="d684c-339">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="d684c-340">\*\* MožnostiHTTP.sys\*\*</span><span class="sxs-lookup"><span data-stu-id="d684c-340">**HTTP.sys options**</span></span>

| <span data-ttu-id="d684c-341">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="d684c-341">Property</span></span> | <span data-ttu-id="d684c-342">Popis</span><span class="sxs-lookup"><span data-stu-id="d684c-342">Description</span></span> | <span data-ttu-id="d684c-343">Výchozí</span><span class="sxs-lookup"><span data-stu-id="d684c-343">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="d684c-344">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="d684c-344">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="d684c-345">Určuje, zda je povolen synchronní vstup/výstup pro `HttpContext.Request.Body` a `HttpContext.Response.Body` .</span><span class="sxs-lookup"><span data-stu-id="d684c-345">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="d684c-346">Ověřování. AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="d684c-346">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="d684c-347">Povoluje anonymní požadavky.</span><span class="sxs-lookup"><span data-stu-id="d684c-347">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="d684c-348">Ověřování. schémata</span><span class="sxs-lookup"><span data-stu-id="d684c-348">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="d684c-349">Určete povolená schémata ověřování.</span><span class="sxs-lookup"><span data-stu-id="d684c-349">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="d684c-350">Může být kdykoli změněno před vyřazením naslouchacího procesu.</span><span class="sxs-lookup"><span data-stu-id="d684c-350">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="d684c-351">Hodnoty jsou k dispozici ve [výčtu schémata AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic` , `Kerberos` , `Negotiate` , `None` a `NTLM` .</span><span class="sxs-lookup"><span data-stu-id="d684c-351">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="d684c-352">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="d684c-352">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="d684c-353">Pokusit se o ukládání do mezipaměti v [režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) pro odpovědi s oprávněnými záhlavími.</span><span class="sxs-lookup"><span data-stu-id="d684c-353">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="d684c-354">Odpověď nesmí obsahovat `Set-Cookie` `Vary` hlavičky, ani `Pragma` .</span><span class="sxs-lookup"><span data-stu-id="d684c-354">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="d684c-355">Musí obsahovat `Cache-Control` hlavičku, která je `public` a buď `shared-max-age` `max-age` hodnota nebo, nebo `Expires` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="d684c-355">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="d684c-356">Maximální počet souběžných přijetí.</span><span class="sxs-lookup"><span data-stu-id="d684c-356">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="d684c-357">5 &times; [prostředí. <br> ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="d684c-357">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="d684c-358">Maximální počet souběžných připojení, která se mají přijmout</span><span class="sxs-lookup"><span data-stu-id="d684c-358">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="d684c-359">Použijte `-1` pro nekonečné.</span><span class="sxs-lookup"><span data-stu-id="d684c-359">Use `-1` for infinite.</span></span> <span data-ttu-id="d684c-360">Použijte `null` k použití nastavení pro počítač v rámci registru.</span><span class="sxs-lookup"><span data-stu-id="d684c-360">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="d684c-361">(počítačově v šířce</span><span class="sxs-lookup"><span data-stu-id="d684c-361">(machine-wide</span></span><br><span data-ttu-id="d684c-362">nastavením</span><span class="sxs-lookup"><span data-stu-id="d684c-362">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="d684c-363">Viz část <a href="#maxrequestbodysize">MaxRequestBodySize</a> .</span><span class="sxs-lookup"><span data-stu-id="d684c-363">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="d684c-364">30000000 bajtů</span><span class="sxs-lookup"><span data-stu-id="d684c-364">30000000 bytes</span></span><br><span data-ttu-id="d684c-365">(~ 28,6 MB)</span><span class="sxs-lookup"><span data-stu-id="d684c-365">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="d684c-366">Maximální počet požadavků, které lze zařadit do fronty.</span><span class="sxs-lookup"><span data-stu-id="d684c-366">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="d684c-367">1000</span><span class="sxs-lookup"><span data-stu-id="d684c-367">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="d684c-368">Určuje, jestli tělo odpovědi zapisuje, které selhalo kvůli odpojení klienta, by mělo vyvolat výjimky nebo dokončit normálně.</span><span class="sxs-lookup"><span data-stu-id="d684c-368">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="d684c-369">(normálně dokončit)</span><span class="sxs-lookup"><span data-stu-id="d684c-369">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="d684c-370">Vystavte <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> konfiguraci HTTP.sys, která může být v registru nakonfigurovaná taky.</span><span class="sxs-lookup"><span data-stu-id="d684c-370">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="d684c-371">Pomocí odkazů rozhraní API získáte další informace o jednotlivých nastaveních, včetně výchozích hodnot:</span><span class="sxs-lookup"><span data-stu-id="d684c-371">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="d684c-372">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): čas povolený pro rozhraní API serveru http, který má vyprázdnit tělo entity u připojení Keep-Alive.</span><span class="sxs-lookup"><span data-stu-id="d684c-372">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="d684c-373">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): čas povolený pro doručení těla entity požadavku.</span><span class="sxs-lookup"><span data-stu-id="d684c-373">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="d684c-374">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): čas povolený pro rozhraní API serveru HTTP k analýze hlavičky žádosti.</span><span class="sxs-lookup"><span data-stu-id="d684c-374">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="d684c-375">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): čas povolený pro nečinné připojení.</span><span class="sxs-lookup"><span data-stu-id="d684c-375">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="d684c-376">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): minimální rychlost odesílání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="d684c-376">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="d684c-377">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): čas, který může požadavek zůstat ve frontě požadavků předtím, než ji aplikace vybere.</span><span class="sxs-lookup"><span data-stu-id="d684c-377">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="d684c-378">Zadejte, kam se <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> má zaregistrovat HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d684c-378">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="d684c-379">Nejužitečnější je [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), který se používá k přidání předpony do kolekce.</span><span class="sxs-lookup"><span data-stu-id="d684c-379">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="d684c-380">Ty mohou být kdykoliv změněny předtím, než se naslouchací proces vyřazuje.</span><span class="sxs-lookup"><span data-stu-id="d684c-380">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="d684c-381">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="d684c-381">**MaxRequestBodySize**</span></span>

<span data-ttu-id="d684c-382">Maximální povolená velikost libovolného textu žádosti v bajtech</span><span class="sxs-lookup"><span data-stu-id="d684c-382">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="d684c-383">Při nastavení na `null` je maximální velikost textu požadavku neomezená.</span><span class="sxs-lookup"><span data-stu-id="d684c-383">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="d684c-384">Toto omezení nemá žádný vliv na upgradovaná připojení, která jsou vždycky neomezená.</span><span class="sxs-lookup"><span data-stu-id="d684c-384">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="d684c-385">Doporučená metoda pro přepsání limitu v aplikaci ASP.NET Core MVC pro jeden `IActionResult` je použít <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atribut pro metodu akce:</span><span class="sxs-lookup"><span data-stu-id="d684c-385">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="d684c-386">Výjimka je vyvolána, pokud se aplikace pokusí nakonfigurovat limit žádosti poté, co aplikace začne číst požadavek.</span><span class="sxs-lookup"><span data-stu-id="d684c-386">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="d684c-387">`IsReadOnly`Vlastnost může být použita k označení `MaxRequestBodySize` , zda je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="d684c-387">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="d684c-388">Pokud by měla aplikace přepsat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> jednotlivé požadavky, použijte <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :</span><span class="sxs-lookup"><span data-stu-id="d684c-388">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="d684c-389">Pokud používáte Visual Studio, ujistěte se, že aplikace není nakonfigurovaná tak, aby spouštěla službu IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="d684c-389">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="d684c-390">V aplikaci Visual Studio je výchozím spouštěcím profilem IIS Express.</span><span class="sxs-lookup"><span data-stu-id="d684c-390">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="d684c-391">Chcete-li spustit projekt jako konzolovou aplikaci, ručně změňte vybraný profil, jak je znázorněno na následujícím snímku obrazovky:</span><span class="sxs-lookup"><span data-stu-id="d684c-391">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![Vybrat profil aplikace konzoly](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="d684c-393">Konfigurace Windows serveru</span><span class="sxs-lookup"><span data-stu-id="d684c-393">Configure Windows Server</span></span>

1. <span data-ttu-id="d684c-394">Určete porty, které se mají otevřít pro aplikaci, a pomocí [brány Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) nebo rutiny [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) prostředí PowerShell otevřete porty brány firewall, abyste umožnili provozu HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d684c-394">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="d684c-395">V následujících příkazech a konfiguraci aplikace se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="d684c-395">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="d684c-396">Při nasazování na virtuální počítač Azure otevřete porty ve [skupině zabezpečení sítě](/azure/virtual-machines/windows/nsg-quickstart-portal).</span><span class="sxs-lookup"><span data-stu-id="d684c-396">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="d684c-397">V následujících příkazech a konfiguraci aplikace se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="d684c-397">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="d684c-398">V případě potřeby Získejte a nainstalujte certifikáty X. 509.</span><span class="sxs-lookup"><span data-stu-id="d684c-398">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="d684c-399">V systému Windows vytvořte pomocí [rutiny prostředí PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate)certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="d684c-399">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="d684c-400">Nepodporovaný příklad naleznete v tématu [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span><span class="sxs-lookup"><span data-stu-id="d684c-400">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="d684c-401">Nainstalujte certifikáty podepsané svým držitelem nebo certifikátem podepsaným certifikační autoritou do osobního úložiště **místního počítače** serveru > **Personal** .</span><span class="sxs-lookup"><span data-stu-id="d684c-401">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="d684c-402">Pokud se jedná o [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd), nainstalujte rozhraní .NET core, .NET Framework nebo obojí (Pokud je aplikace aplikace .NET Core cílící na .NET Framework).</span><span class="sxs-lookup"><span data-stu-id="d684c-402">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="d684c-403">**.NET Core**: Pokud aplikace vyžaduje .NET Core, Získejte a spusťte instalační program **modulu runtime .NET Core** ze [souborů ke stažení pro .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="d684c-403">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="d684c-404">Neinstalujte na server plnou sadu SDK.</span><span class="sxs-lookup"><span data-stu-id="d684c-404">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="d684c-405">**.NET Framework**: Pokud aplikace vyžaduje .NET Framework, přečtěte si téma [Průvodce instalací .NET Framework](/dotnet/framework/install/).</span><span class="sxs-lookup"><span data-stu-id="d684c-405">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="d684c-406">Nainstalujte požadovanou .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="d684c-406">Install the required .NET Framework.</span></span> <span data-ttu-id="d684c-407">Instalační program pro nejnovější .NET Framework je k dispozici na stránce [soubory ke stažení pro .NET Core](https://dotnet.microsoft.com/download) .</span><span class="sxs-lookup"><span data-stu-id="d684c-407">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="d684c-408">Pokud je aplikace samostatným [nasazením](/dotnet/core/deploying/#self-contained-deployments-scd), aplikace zahrne modul runtime do svého nasazení.</span><span class="sxs-lookup"><span data-stu-id="d684c-408">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="d684c-409">Na serveru není nutná žádná instalace rozhraní.</span><span class="sxs-lookup"><span data-stu-id="d684c-409">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="d684c-410">Konfigurace adres URL a portů v aplikaci</span><span class="sxs-lookup"><span data-stu-id="d684c-410">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="d684c-411">Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="d684c-411">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d684c-412">Pokud chcete nakonfigurovat předpony a porty adresy URL, můžete použít tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="d684c-412">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="d684c-413">`urls` argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="d684c-413">`urls` command-line argument</span></span>
   * <span data-ttu-id="d684c-414">`ASPNETCORE_URLS` Proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="d684c-414">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="d684c-415">Následující příklad kódu ukazuje, jak používat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> s místní IP adresou serveru `10.0.0.4` na portu 443:</span><span class="sxs-lookup"><span data-stu-id="d684c-415">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="d684c-416">Výhodou `UrlPrefixes` je, že se okamžitě generuje chybová zpráva pro nesprávně naformátované předpony.</span><span class="sxs-lookup"><span data-stu-id="d684c-416">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="d684c-417">Nastavení v `UrlPrefixes` `UseUrls` / `urls` / `ASPNETCORE_URLS` Nastavení přepsání</span><span class="sxs-lookup"><span data-stu-id="d684c-417">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="d684c-418">Proto je výhodou `UseUrls` `urls` `ASPNETCORE_URLS` proměnné prostředí, a jednodušší přepínání mezi Kestrel a HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d684c-418">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="d684c-419">HTTP.sys používá [formáty řetězce UrlPrefix HTTP serveru API](/windows/win32/http/urlprefix-strings).</span><span class="sxs-lookup"><span data-stu-id="d684c-419">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="d684c-420">Nelze použít vazby zástupných znaků na nejvyšší úrovni ( `http://*:80/` a `http://+:80` ). **not**</span><span class="sxs-lookup"><span data-stu-id="d684c-420">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="d684c-421">Vazby na zástupné znaky na nejvyšší úrovni vytvoří chyby zabezpečení aplikace.</span><span class="sxs-lookup"><span data-stu-id="d684c-421">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="d684c-422">To platí pro silné i slabé zástupné znaky.</span><span class="sxs-lookup"><span data-stu-id="d684c-422">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="d684c-423">Místo zástupných znaků použijte explicitní názvy hostitelů nebo IP adresy.</span><span class="sxs-lookup"><span data-stu-id="d684c-423">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="d684c-424">Vazba zástupných znaků subdomény (například `*.mysub.com` ) není bezpečnostní riziko, pokud ovládáte celou nadřazenou doménu (na rozdíl od `*.com` , která je zranitelná).</span><span class="sxs-lookup"><span data-stu-id="d684c-424">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="d684c-425">Další informace najdete v [dokumentu RFC 7230: oddíl 5,4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="d684c-425">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="d684c-426">Předregistrujte předpony adresy URL na serveru.</span><span class="sxs-lookup"><span data-stu-id="d684c-426">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="d684c-427">K \* disnetsh.exe\*je integrovaný nástroj pro konfiguraci HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d684c-427">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="d684c-428">*netsh.exe* se používá k rezervaci předpon adres URL a přiřazování certifikátů X. 509.</span><span class="sxs-lookup"><span data-stu-id="d684c-428">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="d684c-429">Nástroj vyžaduje oprávnění správce.</span><span class="sxs-lookup"><span data-stu-id="d684c-429">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="d684c-430">K registraci adres URL aplikace použijte nástroj *netsh.exe* :</span><span class="sxs-lookup"><span data-stu-id="d684c-430">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="d684c-431">`<URL>`: Plně kvalifikované adresy URL (Uniform Resource Locator).</span><span class="sxs-lookup"><span data-stu-id="d684c-431">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="d684c-432">Nepoužívejte vazbu zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="d684c-432">Don't use a wildcard binding.</span></span> <span data-ttu-id="d684c-433">Použijte platný název hostitele nebo místní IP adresu.</span><span class="sxs-lookup"><span data-stu-id="d684c-433">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="d684c-434">*Adresa URL musí obsahovat koncové lomítko.*</span><span class="sxs-lookup"><span data-stu-id="d684c-434">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="d684c-435">`<USER>`: Určuje název uživatele nebo skupiny uživatelů.</span><span class="sxs-lookup"><span data-stu-id="d684c-435">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="d684c-436">V následujícím příkladu je místní IP adresa serveru `10.0.0.4` :</span><span class="sxs-lookup"><span data-stu-id="d684c-436">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="d684c-437">Když je adresa URL registrována, nástroj odpoví `URL reservation successfully added` .</span><span class="sxs-lookup"><span data-stu-id="d684c-437">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="d684c-438">Pokud chcete odstranit registrovanou adresu URL, použijte `delete urlacl` příkaz:</span><span class="sxs-lookup"><span data-stu-id="d684c-438">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="d684c-439">Zaregistrujte certifikáty X. 509 na serveru.</span><span class="sxs-lookup"><span data-stu-id="d684c-439">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="d684c-440">K registraci certifikátů pro aplikaci použijte nástroj *netsh.exe* :</span><span class="sxs-lookup"><span data-stu-id="d684c-440">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="d684c-441">`<IP>`: Určuje místní IP adresu pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="d684c-441">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="d684c-442">Nepoužívejte vazbu zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="d684c-442">Don't use a wildcard binding.</span></span> <span data-ttu-id="d684c-443">Použijte platnou IP adresu.</span><span class="sxs-lookup"><span data-stu-id="d684c-443">Use a valid IP address.</span></span>
   * <span data-ttu-id="d684c-444">`<PORT>`: Určuje port pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="d684c-444">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="d684c-445">`<THUMBPRINT>`: Kryptografický otisk certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="d684c-445">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="d684c-446">`<GUID>`: Identifikátor GUID generovaný vývojářem, který představuje aplikaci pro informativní účely.</span><span class="sxs-lookup"><span data-stu-id="d684c-446">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="d684c-447">Pro referenční účely uložte identifikátor GUID do aplikace jako značku balíčku:</span><span class="sxs-lookup"><span data-stu-id="d684c-447">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="d684c-448">V aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d684c-448">In Visual Studio:</span></span>
     * <span data-ttu-id="d684c-449">Otevřete vlastnosti projektu aplikace tak, že kliknete pravým tlačítkem na aplikaci v **Průzkumník řešení** a vyberete **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="d684c-449">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="d684c-450">Vyberte kartu **balíček** .</span><span class="sxs-lookup"><span data-stu-id="d684c-450">Select the **Package** tab.</span></span>
     * <span data-ttu-id="d684c-451">Zadejte identifikátor GUID, který jste vytvořili v poli **značky** .</span><span class="sxs-lookup"><span data-stu-id="d684c-451">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="d684c-452">Když nepoužíváte Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d684c-452">When not using Visual Studio:</span></span>
     * <span data-ttu-id="d684c-453">Otevřete soubor projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="d684c-453">Open the app's project file.</span></span>
     * <span data-ttu-id="d684c-454">Přidejte `<PackageTags>` vlastnost do nového nebo existujícího `<PropertyGroup>` s identifikátorem GUID, který jste vytvořili:</span><span class="sxs-lookup"><span data-stu-id="d684c-454">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="d684c-455">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="d684c-455">In the following example:</span></span>

   * <span data-ttu-id="d684c-456">Místní IP adresa serveru je `10.0.0.4` .</span><span class="sxs-lookup"><span data-stu-id="d684c-456">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="d684c-457">Generátor náhodných identifikátorů GUID online poskytuje `appid` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="d684c-457">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="d684c-458">Když je certifikát zaregistrován, nástroj odpoví `SSL Certificate successfully added` .</span><span class="sxs-lookup"><span data-stu-id="d684c-458">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="d684c-459">K odstranění registrace certifikátu použijte `delete sslcert` příkaz:</span><span class="sxs-lookup"><span data-stu-id="d684c-459">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="d684c-460">Referenční dokumentace pro *netsh.exe*:</span><span class="sxs-lookup"><span data-stu-id="d684c-460">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="d684c-461">[Příkazy Netsh pro protokol HTTP (Hypertext Transfer Protocol)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="d684c-461">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="d684c-462">UrlPrefix řetězce</span><span class="sxs-lookup"><span data-stu-id="d684c-462">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="d684c-463">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d684c-463">Run the app.</span></span>

   <span data-ttu-id="d684c-464">Oprávnění správce nejsou nutná ke spuštění aplikace při vytváření vazby na localhost pomocí protokolu HTTP (ne HTTPS) s číslem portu větším než 1024.</span><span class="sxs-lookup"><span data-stu-id="d684c-464">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="d684c-465">U ostatních konfigurací (například pomocí místní IP adresy nebo vazby na port 443) spusťte aplikaci s oprávněními správce.</span><span class="sxs-lookup"><span data-stu-id="d684c-465">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="d684c-466">Aplikace odpoví na veřejnou IP adresu serveru.</span><span class="sxs-lookup"><span data-stu-id="d684c-466">The app responds at the server's public IP address.</span></span> <span data-ttu-id="d684c-467">V tomto příkladu je server dosažitelný z Internetu na jeho veřejné IP adrese `104.214.79.47` .</span><span class="sxs-lookup"><span data-stu-id="d684c-467">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="d684c-468">V tomto příkladu se používá vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="d684c-468">A development certificate is used in this example.</span></span> <span data-ttu-id="d684c-469">Stránka se načte bezpečně po obejít upozornění na nedůvěryhodný certifikát v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="d684c-469">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![Okno prohlížeče zobrazující načtenou stránku indexu aplikace](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d684c-471">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="d684c-471">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d684c-472">Pro aplikace hostované HTTP.sys, které komunikují s požadavky z Internetu nebo podnikové sítě, se může při hostování za proxy servery a nástroji pro vyrovnávání zatížení vyžadovat další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="d684c-472">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="d684c-473">Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="d684c-473">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d684c-474">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="d684c-474">Additional resources</span></span>

* [<span data-ttu-id="d684c-475">Povolit ověřování systému Windows pomocí HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="d684c-475">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="d684c-476">Rozhraní API serveru HTTP</span><span class="sxs-lookup"><span data-stu-id="d684c-476">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="d684c-477">úložiště GitHubu ASPNET/HttpSysServer (zdrojový kód)</span><span class="sxs-lookup"><span data-stu-id="d684c-477">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="d684c-478">Hostitel</span><span class="sxs-lookup"><span data-stu-id="d684c-478">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="d684c-479">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) je [webový server pro ASP.NET Core](xref:fundamentals/servers/index) , který běží jenom ve Windows.</span><span class="sxs-lookup"><span data-stu-id="d684c-479">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="d684c-480">HTTP.sys je alternativou k serveru [Kestrel](xref:fundamentals/servers/kestrel) a nabízí některé funkce, které Kestrel neposkytují.</span><span class="sxs-lookup"><span data-stu-id="d684c-480">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d684c-481">HTTP.sys není kompatibilní s [modulem ASP.NET Core](xref:host-and-deploy/aspnet-core-module) a nedá se použít se službou IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="d684c-481">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="d684c-482">HTTP.sys podporuje následující funkce:</span><span class="sxs-lookup"><span data-stu-id="d684c-482">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="d684c-483">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="d684c-483">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="d684c-484">Sdílení portů</span><span class="sxs-lookup"><span data-stu-id="d684c-484">Port sharing</span></span>
* <span data-ttu-id="d684c-485">HTTPS s SNI</span><span class="sxs-lookup"><span data-stu-id="d684c-485">HTTPS with SNI</span></span>
* <span data-ttu-id="d684c-486">HTTP/2 přes TLS (Windows 10 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="d684c-486">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="d684c-487">Přímý přenos souborů</span><span class="sxs-lookup"><span data-stu-id="d684c-487">Direct file transmission</span></span>
* <span data-ttu-id="d684c-488">Ukládání odpovědí do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="d684c-488">Response caching</span></span>
* <span data-ttu-id="d684c-489">WebSockets (Windows 8 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="d684c-489">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="d684c-490">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="d684c-490">Supported Windows versions:</span></span>

* <span data-ttu-id="d684c-491">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d684c-491">Windows 7 or later</span></span>
* <span data-ttu-id="d684c-492">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d684c-492">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="d684c-493">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d684c-493">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="d684c-494">Kdy použít HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="d684c-494">When to use HTTP.sys</span></span>

<span data-ttu-id="d684c-495">HTTP.sys je užitečná pro nasazení, kde:</span><span class="sxs-lookup"><span data-stu-id="d684c-495">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="d684c-496">Server je potřeba zveřejnit přímo na internetu bez použití IIS.</span><span class="sxs-lookup"><span data-stu-id="d684c-496">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys komunikuje přímo s internetem.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="d684c-498">Interní nasazení vyžaduje funkci, která není v Kestrel k dispozici, jako je například [ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="d684c-498">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys komunikuje přímo s interní sítí.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="d684c-500">HTTP.sys je Vyspělá technologie, která chrání před mnoha typy útoků a poskytuje odolnost, zabezpečení a škálovatelnost plně funkčního webového serveru.</span><span class="sxs-lookup"><span data-stu-id="d684c-500">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="d684c-501">Služba IIS sám spouští jako naslouchací proces HTTP nad HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d684c-501">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="d684c-502">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="d684c-502">HTTP/2 support</span></span>

<span data-ttu-id="d684c-503">[Protokol HTTP/2](https://httpwg.org/specs/rfc7540.html) je povolený pro aplikace ASP.NET Core, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="d684c-503">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="d684c-504">Windows Server 2016/Windows 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d684c-504">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="d684c-505">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="d684c-505">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="d684c-506">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d684c-506">TLS 1.2 or later connection</span></span>

<span data-ttu-id="d684c-507">Pokud je vytvořeno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="d684c-507">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="d684c-508">HTTP/2 je ve výchozím nastavení povolené.</span><span class="sxs-lookup"><span data-stu-id="d684c-508">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="d684c-509">Pokud připojení HTTP/2 není navázáno, připojení se vrátí k HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="d684c-509">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="d684c-510">V budoucí verzi Windows budou k dispozici příznaky konfigurace protokolu HTTP/2, včetně možnosti zakázat protokol HTTP/2 s HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d684c-510">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="d684c-511">Ověřování v režimu jádra pomocí protokolu Kerberos</span><span class="sxs-lookup"><span data-stu-id="d684c-511">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="d684c-512">HTTP.sys deleguje ověřování režimu jádra pomocí ověřovacího protokolu Kerberos.</span><span class="sxs-lookup"><span data-stu-id="d684c-512">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="d684c-513">Ověřování pomocí uživatelského režimu není pro protokol Kerberos a HTTP.sys podporováno.</span><span class="sxs-lookup"><span data-stu-id="d684c-513">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="d684c-514">Účet počítače se musí použít k dešifrování tokenu nebo lístku protokolu Kerberos, který je získaný ze služby Active Directory a který přesměruje klient na server za účelem ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="d684c-514">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="d684c-515">Zaregistrujte hlavní název služby (SPN) pro hostitele, nikoli uživatele aplikace.</span><span class="sxs-lookup"><span data-stu-id="d684c-515">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="d684c-516">Jak používat HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="d684c-516">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="d684c-517">Konfigurace aplikace ASP.NET Core pro použití HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="d684c-517">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="d684c-518">Odkaz na balíček v souboru projektu není vyžadován při použití [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) ([NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span><span class="sxs-lookup"><span data-stu-id="d684c-518">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="d684c-519">Pokud nepoužíváte `Microsoft.AspNetCore.App` Metapackage, přidejte odkaz na balíček do [Microsoft. AspNetCore. Server. HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span><span class="sxs-lookup"><span data-stu-id="d684c-519">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="d684c-520"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>Při sestavování hostitele zavolejte metodu rozšíření a určete požadované <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> .</span><span class="sxs-lookup"><span data-stu-id="d684c-520">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="d684c-521">Následující příklad nastaví možnosti na jejich výchozí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="d684c-521">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="d684c-522">Další konfigurace HTTP.sys se zpracovává prostřednictvím [nastavení registru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span><span class="sxs-lookup"><span data-stu-id="d684c-522">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="d684c-523">\*\* MožnostiHTTP.sys\*\*</span><span class="sxs-lookup"><span data-stu-id="d684c-523">**HTTP.sys options**</span></span>

| <span data-ttu-id="d684c-524">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="d684c-524">Property</span></span> | <span data-ttu-id="d684c-525">Popis</span><span class="sxs-lookup"><span data-stu-id="d684c-525">Description</span></span> | <span data-ttu-id="d684c-526">Výchozí</span><span class="sxs-lookup"><span data-stu-id="d684c-526">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="d684c-527">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="d684c-527">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="d684c-528">Určuje, zda je povolen synchronní vstup/výstup pro `HttpContext.Request.Body` a `HttpContext.Response.Body` .</span><span class="sxs-lookup"><span data-stu-id="d684c-528">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="d684c-529">Ověřování. AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="d684c-529">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="d684c-530">Povoluje anonymní požadavky.</span><span class="sxs-lookup"><span data-stu-id="d684c-530">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="d684c-531">Ověřování. schémata</span><span class="sxs-lookup"><span data-stu-id="d684c-531">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="d684c-532">Určete povolená schémata ověřování.</span><span class="sxs-lookup"><span data-stu-id="d684c-532">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="d684c-533">Může být kdykoli změněno před vyřazením naslouchacího procesu.</span><span class="sxs-lookup"><span data-stu-id="d684c-533">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="d684c-534">Hodnoty jsou k dispozici ve [výčtu schémata AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic` , `Kerberos` , `Negotiate` , `None` a `NTLM` .</span><span class="sxs-lookup"><span data-stu-id="d684c-534">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="d684c-535">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="d684c-535">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="d684c-536">Pokusit se o ukládání do mezipaměti v [režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) pro odpovědi s oprávněnými záhlavími.</span><span class="sxs-lookup"><span data-stu-id="d684c-536">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="d684c-537">Odpověď nesmí obsahovat `Set-Cookie` `Vary` hlavičky, ani `Pragma` .</span><span class="sxs-lookup"><span data-stu-id="d684c-537">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="d684c-538">Musí obsahovat `Cache-Control` hlavičku, která je `public` a buď `shared-max-age` `max-age` hodnota nebo, nebo `Expires` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="d684c-538">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="d684c-539">Maximální počet souběžných přijetí.</span><span class="sxs-lookup"><span data-stu-id="d684c-539">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="d684c-540">5 &times; [prostředí. <br> ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="d684c-540">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="d684c-541">Maximální počet souběžných připojení, která se mají přijmout</span><span class="sxs-lookup"><span data-stu-id="d684c-541">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="d684c-542">Použijte `-1` pro nekonečné.</span><span class="sxs-lookup"><span data-stu-id="d684c-542">Use `-1` for infinite.</span></span> <span data-ttu-id="d684c-543">Použijte `null` k použití nastavení pro počítač v rámci registru.</span><span class="sxs-lookup"><span data-stu-id="d684c-543">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="d684c-544">(počítačově v šířce</span><span class="sxs-lookup"><span data-stu-id="d684c-544">(machine-wide</span></span><br><span data-ttu-id="d684c-545">nastavením</span><span class="sxs-lookup"><span data-stu-id="d684c-545">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="d684c-546">Viz část <a href="#maxrequestbodysize">MaxRequestBodySize</a> .</span><span class="sxs-lookup"><span data-stu-id="d684c-546">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="d684c-547">30000000 bajtů</span><span class="sxs-lookup"><span data-stu-id="d684c-547">30000000 bytes</span></span><br><span data-ttu-id="d684c-548">(~ 28,6 MB)</span><span class="sxs-lookup"><span data-stu-id="d684c-548">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="d684c-549">Maximální počet požadavků, které lze zařadit do fronty.</span><span class="sxs-lookup"><span data-stu-id="d684c-549">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="d684c-550">1000</span><span class="sxs-lookup"><span data-stu-id="d684c-550">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="d684c-551">Určuje, jestli tělo odpovědi zapisuje, které selhalo kvůli odpojení klienta, by mělo vyvolat výjimky nebo dokončit normálně.</span><span class="sxs-lookup"><span data-stu-id="d684c-551">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="d684c-552">(normálně dokončit)</span><span class="sxs-lookup"><span data-stu-id="d684c-552">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="d684c-553">Vystavte <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> konfiguraci HTTP.sys, která může být v registru nakonfigurovaná taky.</span><span class="sxs-lookup"><span data-stu-id="d684c-553">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="d684c-554">Pomocí odkazů rozhraní API získáte další informace o jednotlivých nastaveních, včetně výchozích hodnot:</span><span class="sxs-lookup"><span data-stu-id="d684c-554">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="d684c-555">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): čas povolený pro rozhraní API serveru http, který má vyprázdnit tělo entity u připojení Keep-Alive.</span><span class="sxs-lookup"><span data-stu-id="d684c-555">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="d684c-556">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): čas povolený pro doručení těla entity požadavku.</span><span class="sxs-lookup"><span data-stu-id="d684c-556">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="d684c-557">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): čas povolený pro rozhraní API serveru HTTP k analýze hlavičky žádosti.</span><span class="sxs-lookup"><span data-stu-id="d684c-557">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="d684c-558">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): čas povolený pro nečinné připojení.</span><span class="sxs-lookup"><span data-stu-id="d684c-558">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="d684c-559">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): minimální rychlost odesílání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="d684c-559">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="d684c-560">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): čas, který může požadavek zůstat ve frontě požadavků předtím, než ji aplikace vybere.</span><span class="sxs-lookup"><span data-stu-id="d684c-560">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="d684c-561">Zadejte, kam se <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> má zaregistrovat HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d684c-561">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="d684c-562">Nejužitečnější je [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), který se používá k přidání předpony do kolekce.</span><span class="sxs-lookup"><span data-stu-id="d684c-562">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="d684c-563">Ty mohou být kdykoliv změněny předtím, než se naslouchací proces vyřazuje.</span><span class="sxs-lookup"><span data-stu-id="d684c-563">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="d684c-564">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="d684c-564">**MaxRequestBodySize**</span></span>

<span data-ttu-id="d684c-565">Maximální povolená velikost libovolného textu žádosti v bajtech</span><span class="sxs-lookup"><span data-stu-id="d684c-565">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="d684c-566">Při nastavení na `null` je maximální velikost textu požadavku neomezená.</span><span class="sxs-lookup"><span data-stu-id="d684c-566">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="d684c-567">Toto omezení nemá žádný vliv na upgradovaná připojení, která jsou vždycky neomezená.</span><span class="sxs-lookup"><span data-stu-id="d684c-567">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="d684c-568">Doporučená metoda pro přepsání limitu v aplikaci ASP.NET Core MVC pro jeden `IActionResult` je použít <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atribut pro metodu akce:</span><span class="sxs-lookup"><span data-stu-id="d684c-568">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="d684c-569">Výjimka je vyvolána, pokud se aplikace pokusí nakonfigurovat limit žádosti poté, co aplikace začne číst požadavek.</span><span class="sxs-lookup"><span data-stu-id="d684c-569">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="d684c-570">`IsReadOnly`Vlastnost může být použita k označení `MaxRequestBodySize` , zda je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="d684c-570">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="d684c-571">Pokud by měla aplikace přepsat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> jednotlivé požadavky, použijte <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :</span><span class="sxs-lookup"><span data-stu-id="d684c-571">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="d684c-572">Pokud používáte Visual Studio, ujistěte se, že aplikace není nakonfigurovaná tak, aby spouštěla službu IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="d684c-572">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="d684c-573">V aplikaci Visual Studio je výchozím spouštěcím profilem IIS Express.</span><span class="sxs-lookup"><span data-stu-id="d684c-573">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="d684c-574">Chcete-li spustit projekt jako konzolovou aplikaci, ručně změňte vybraný profil, jak je znázorněno na následujícím snímku obrazovky:</span><span class="sxs-lookup"><span data-stu-id="d684c-574">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![Vybrat profil aplikace konzoly](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="d684c-576">Konfigurace Windows serveru</span><span class="sxs-lookup"><span data-stu-id="d684c-576">Configure Windows Server</span></span>

1. <span data-ttu-id="d684c-577">Určete porty, které se mají otevřít pro aplikaci, a pomocí [brány Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) nebo rutiny [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) prostředí PowerShell otevřete porty brány firewall, abyste umožnili provozu HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d684c-577">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="d684c-578">V následujících příkazech a konfiguraci aplikace se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="d684c-578">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="d684c-579">Při nasazování na virtuální počítač Azure otevřete porty ve [skupině zabezpečení sítě](/azure/virtual-machines/windows/nsg-quickstart-portal).</span><span class="sxs-lookup"><span data-stu-id="d684c-579">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="d684c-580">V následujících příkazech a konfiguraci aplikace se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="d684c-580">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="d684c-581">V případě potřeby Získejte a nainstalujte certifikáty X. 509.</span><span class="sxs-lookup"><span data-stu-id="d684c-581">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="d684c-582">V systému Windows vytvořte pomocí [rutiny prostředí PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate)certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="d684c-582">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="d684c-583">Nepodporovaný příklad naleznete v tématu [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span><span class="sxs-lookup"><span data-stu-id="d684c-583">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="d684c-584">Nainstalujte certifikáty podepsané svým držitelem nebo certifikátem podepsaným certifikační autoritou do osobního úložiště **místního počítače** serveru > **Personal** .</span><span class="sxs-lookup"><span data-stu-id="d684c-584">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="d684c-585">Pokud se jedná o [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd), nainstalujte rozhraní .NET core, .NET Framework nebo obojí (Pokud je aplikace aplikace .NET Core cílící na .NET Framework).</span><span class="sxs-lookup"><span data-stu-id="d684c-585">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="d684c-586">**.NET Core**: Pokud aplikace vyžaduje .NET Core, Získejte a spusťte instalační program **modulu runtime .NET Core** ze [souborů ke stažení pro .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="d684c-586">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="d684c-587">Neinstalujte na server plnou sadu SDK.</span><span class="sxs-lookup"><span data-stu-id="d684c-587">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="d684c-588">**.NET Framework**: Pokud aplikace vyžaduje .NET Framework, přečtěte si téma [Průvodce instalací .NET Framework](/dotnet/framework/install/).</span><span class="sxs-lookup"><span data-stu-id="d684c-588">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="d684c-589">Nainstalujte požadovanou .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="d684c-589">Install the required .NET Framework.</span></span> <span data-ttu-id="d684c-590">Instalační program pro nejnovější .NET Framework je k dispozici na stránce [soubory ke stažení pro .NET Core](https://dotnet.microsoft.com/download) .</span><span class="sxs-lookup"><span data-stu-id="d684c-590">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="d684c-591">Pokud je aplikace samostatným [nasazením](/dotnet/core/deploying/#self-contained-deployments-scd), aplikace zahrne modul runtime do svého nasazení.</span><span class="sxs-lookup"><span data-stu-id="d684c-591">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="d684c-592">Na serveru není nutná žádná instalace rozhraní.</span><span class="sxs-lookup"><span data-stu-id="d684c-592">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="d684c-593">Konfigurace adres URL a portů v aplikaci</span><span class="sxs-lookup"><span data-stu-id="d684c-593">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="d684c-594">Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="d684c-594">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d684c-595">Pokud chcete nakonfigurovat předpony a porty adresy URL, můžete použít tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="d684c-595">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="d684c-596">`urls` argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="d684c-596">`urls` command-line argument</span></span>
   * <span data-ttu-id="d684c-597">`ASPNETCORE_URLS` Proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="d684c-597">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="d684c-598">Následující příklad kódu ukazuje, jak používat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> s místní IP adresou serveru `10.0.0.4` na portu 443:</span><span class="sxs-lookup"><span data-stu-id="d684c-598">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="d684c-599">Výhodou `UrlPrefixes` je, že se okamžitě generuje chybová zpráva pro nesprávně naformátované předpony.</span><span class="sxs-lookup"><span data-stu-id="d684c-599">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="d684c-600">Nastavení v `UrlPrefixes` `UseUrls` / `urls` / `ASPNETCORE_URLS` Nastavení přepsání</span><span class="sxs-lookup"><span data-stu-id="d684c-600">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="d684c-601">Proto je výhodou `UseUrls` `urls` `ASPNETCORE_URLS` proměnné prostředí, a jednodušší přepínání mezi Kestrel a HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d684c-601">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="d684c-602">HTTP.sys používá [formáty řetězce UrlPrefix HTTP serveru API](/windows/win32/http/urlprefix-strings).</span><span class="sxs-lookup"><span data-stu-id="d684c-602">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="d684c-603">Nelze použít vazby zástupných znaků na nejvyšší úrovni ( `http://*:80/` a `http://+:80` ). **not**</span><span class="sxs-lookup"><span data-stu-id="d684c-603">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="d684c-604">Vazby na zástupné znaky na nejvyšší úrovni vytvoří chyby zabezpečení aplikace.</span><span class="sxs-lookup"><span data-stu-id="d684c-604">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="d684c-605">To platí pro silné i slabé zástupné znaky.</span><span class="sxs-lookup"><span data-stu-id="d684c-605">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="d684c-606">Místo zástupných znaků použijte explicitní názvy hostitelů nebo IP adresy.</span><span class="sxs-lookup"><span data-stu-id="d684c-606">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="d684c-607">Vazba zástupných znaků subdomény (například `*.mysub.com` ) není bezpečnostní riziko, pokud ovládáte celou nadřazenou doménu (na rozdíl od `*.com` , která je zranitelná).</span><span class="sxs-lookup"><span data-stu-id="d684c-607">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="d684c-608">Další informace najdete v [dokumentu RFC 7230: oddíl 5,4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="d684c-608">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="d684c-609">Předregistrujte předpony adresy URL na serveru.</span><span class="sxs-lookup"><span data-stu-id="d684c-609">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="d684c-610">K \* disnetsh.exe\*je integrovaný nástroj pro konfiguraci HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d684c-610">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="d684c-611">*netsh.exe* se používá k rezervaci předpon adres URL a přiřazování certifikátů X. 509.</span><span class="sxs-lookup"><span data-stu-id="d684c-611">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="d684c-612">Nástroj vyžaduje oprávnění správce.</span><span class="sxs-lookup"><span data-stu-id="d684c-612">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="d684c-613">K registraci adres URL aplikace použijte nástroj *netsh.exe* :</span><span class="sxs-lookup"><span data-stu-id="d684c-613">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="d684c-614">`<URL>`: Plně kvalifikované adresy URL (Uniform Resource Locator).</span><span class="sxs-lookup"><span data-stu-id="d684c-614">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="d684c-615">Nepoužívejte vazbu zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="d684c-615">Don't use a wildcard binding.</span></span> <span data-ttu-id="d684c-616">Použijte platný název hostitele nebo místní IP adresu.</span><span class="sxs-lookup"><span data-stu-id="d684c-616">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="d684c-617">*Adresa URL musí obsahovat koncové lomítko.*</span><span class="sxs-lookup"><span data-stu-id="d684c-617">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="d684c-618">`<USER>`: Určuje název uživatele nebo skupiny uživatelů.</span><span class="sxs-lookup"><span data-stu-id="d684c-618">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="d684c-619">V následujícím příkladu je místní IP adresa serveru `10.0.0.4` :</span><span class="sxs-lookup"><span data-stu-id="d684c-619">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="d684c-620">Když je adresa URL registrována, nástroj odpoví `URL reservation successfully added` .</span><span class="sxs-lookup"><span data-stu-id="d684c-620">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="d684c-621">Pokud chcete odstranit registrovanou adresu URL, použijte `delete urlacl` příkaz:</span><span class="sxs-lookup"><span data-stu-id="d684c-621">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="d684c-622">Zaregistrujte certifikáty X. 509 na serveru.</span><span class="sxs-lookup"><span data-stu-id="d684c-622">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="d684c-623">K registraci certifikátů pro aplikaci použijte nástroj *netsh.exe* :</span><span class="sxs-lookup"><span data-stu-id="d684c-623">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="d684c-624">`<IP>`: Určuje místní IP adresu pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="d684c-624">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="d684c-625">Nepoužívejte vazbu zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="d684c-625">Don't use a wildcard binding.</span></span> <span data-ttu-id="d684c-626">Použijte platnou IP adresu.</span><span class="sxs-lookup"><span data-stu-id="d684c-626">Use a valid IP address.</span></span>
   * <span data-ttu-id="d684c-627">`<PORT>`: Určuje port pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="d684c-627">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="d684c-628">`<THUMBPRINT>`: Kryptografický otisk certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="d684c-628">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="d684c-629">`<GUID>`: Identifikátor GUID generovaný vývojářem, který představuje aplikaci pro informativní účely.</span><span class="sxs-lookup"><span data-stu-id="d684c-629">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="d684c-630">Pro referenční účely uložte identifikátor GUID do aplikace jako značku balíčku:</span><span class="sxs-lookup"><span data-stu-id="d684c-630">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="d684c-631">V aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d684c-631">In Visual Studio:</span></span>
     * <span data-ttu-id="d684c-632">Otevřete vlastnosti projektu aplikace tak, že kliknete pravým tlačítkem na aplikaci v **Průzkumník řešení** a vyberete **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="d684c-632">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="d684c-633">Vyberte kartu **balíček** .</span><span class="sxs-lookup"><span data-stu-id="d684c-633">Select the **Package** tab.</span></span>
     * <span data-ttu-id="d684c-634">Zadejte identifikátor GUID, který jste vytvořili v poli **značky** .</span><span class="sxs-lookup"><span data-stu-id="d684c-634">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="d684c-635">Když nepoužíváte Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d684c-635">When not using Visual Studio:</span></span>
     * <span data-ttu-id="d684c-636">Otevřete soubor projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="d684c-636">Open the app's project file.</span></span>
     * <span data-ttu-id="d684c-637">Přidejte `<PackageTags>` vlastnost do nového nebo existujícího `<PropertyGroup>` s identifikátorem GUID, který jste vytvořili:</span><span class="sxs-lookup"><span data-stu-id="d684c-637">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="d684c-638">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="d684c-638">In the following example:</span></span>

   * <span data-ttu-id="d684c-639">Místní IP adresa serveru je `10.0.0.4` .</span><span class="sxs-lookup"><span data-stu-id="d684c-639">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="d684c-640">Generátor náhodných identifikátorů GUID online poskytuje `appid` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="d684c-640">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="d684c-641">Když je certifikát zaregistrován, nástroj odpoví `SSL Certificate successfully added` .</span><span class="sxs-lookup"><span data-stu-id="d684c-641">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="d684c-642">K odstranění registrace certifikátu použijte `delete sslcert` příkaz:</span><span class="sxs-lookup"><span data-stu-id="d684c-642">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="d684c-643">Referenční dokumentace pro *netsh.exe*:</span><span class="sxs-lookup"><span data-stu-id="d684c-643">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="d684c-644">[Příkazy Netsh pro protokol HTTP (Hypertext Transfer Protocol)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="d684c-644">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="d684c-645">UrlPrefix řetězce</span><span class="sxs-lookup"><span data-stu-id="d684c-645">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="d684c-646">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d684c-646">Run the app.</span></span>

   <span data-ttu-id="d684c-647">Oprávnění správce nejsou nutná ke spuštění aplikace při vytváření vazby na localhost pomocí protokolu HTTP (ne HTTPS) s číslem portu větším než 1024.</span><span class="sxs-lookup"><span data-stu-id="d684c-647">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="d684c-648">U ostatních konfigurací (například pomocí místní IP adresy nebo vazby na port 443) spusťte aplikaci s oprávněními správce.</span><span class="sxs-lookup"><span data-stu-id="d684c-648">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="d684c-649">Aplikace odpoví na veřejnou IP adresu serveru.</span><span class="sxs-lookup"><span data-stu-id="d684c-649">The app responds at the server's public IP address.</span></span> <span data-ttu-id="d684c-650">V tomto příkladu je server dosažitelný z Internetu na jeho veřejné IP adrese `104.214.79.47` .</span><span class="sxs-lookup"><span data-stu-id="d684c-650">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="d684c-651">V tomto příkladu se používá vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="d684c-651">A development certificate is used in this example.</span></span> <span data-ttu-id="d684c-652">Stránka se načte bezpečně po obejít upozornění na nedůvěryhodný certifikát v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="d684c-652">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![Okno prohlížeče zobrazující načtenou stránku indexu aplikace](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d684c-654">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="d684c-654">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d684c-655">Pro aplikace hostované HTTP.sys, které komunikují s požadavky z Internetu nebo podnikové sítě, se může při hostování za proxy servery a nástroji pro vyrovnávání zatížení vyžadovat další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="d684c-655">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="d684c-656">Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="d684c-656">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d684c-657">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="d684c-657">Additional resources</span></span>

* [<span data-ttu-id="d684c-658">Povolit ověřování systému Windows pomocí HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="d684c-658">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="d684c-659">Rozhraní API serveru HTTP</span><span class="sxs-lookup"><span data-stu-id="d684c-659">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="d684c-660">úložiště GitHubu ASPNET/HttpSysServer (zdrojový kód)</span><span class="sxs-lookup"><span data-stu-id="d684c-660">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="d684c-661">Hostitel</span><span class="sxs-lookup"><span data-stu-id="d684c-661">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="d684c-662">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) je [webový server pro ASP.NET Core](xref:fundamentals/servers/index) , který běží jenom ve Windows.</span><span class="sxs-lookup"><span data-stu-id="d684c-662">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="d684c-663">HTTP.sys je alternativou k serveru [Kestrel](xref:fundamentals/servers/kestrel) a nabízí některé funkce, které Kestrel neposkytují.</span><span class="sxs-lookup"><span data-stu-id="d684c-663">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d684c-664">HTTP.sys není kompatibilní s [modulem ASP.NET Core](xref:host-and-deploy/aspnet-core-module) a nedá se použít se službou IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="d684c-664">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="d684c-665">HTTP.sys podporuje následující funkce:</span><span class="sxs-lookup"><span data-stu-id="d684c-665">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="d684c-666">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="d684c-666">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="d684c-667">Sdílení portů</span><span class="sxs-lookup"><span data-stu-id="d684c-667">Port sharing</span></span>
* <span data-ttu-id="d684c-668">HTTPS s SNI</span><span class="sxs-lookup"><span data-stu-id="d684c-668">HTTPS with SNI</span></span>
* <span data-ttu-id="d684c-669">HTTP/2 přes TLS (Windows 10 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="d684c-669">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="d684c-670">Přímý přenos souborů</span><span class="sxs-lookup"><span data-stu-id="d684c-670">Direct file transmission</span></span>
* <span data-ttu-id="d684c-671">Ukládání odpovědí do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="d684c-671">Response caching</span></span>
* <span data-ttu-id="d684c-672">WebSockets (Windows 8 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="d684c-672">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="d684c-673">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="d684c-673">Supported Windows versions:</span></span>

* <span data-ttu-id="d684c-674">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d684c-674">Windows 7 or later</span></span>
* <span data-ttu-id="d684c-675">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d684c-675">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="d684c-676">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d684c-676">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="d684c-677">Kdy použít HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="d684c-677">When to use HTTP.sys</span></span>

<span data-ttu-id="d684c-678">HTTP.sys je užitečná pro nasazení, kde:</span><span class="sxs-lookup"><span data-stu-id="d684c-678">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="d684c-679">Server je potřeba zveřejnit přímo na internetu bez použití IIS.</span><span class="sxs-lookup"><span data-stu-id="d684c-679">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys komunikuje přímo s internetem.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="d684c-681">Interní nasazení vyžaduje funkci, která není v Kestrel k dispozici, jako je například [ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="d684c-681">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys komunikuje přímo s interní sítí.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="d684c-683">HTTP.sys je Vyspělá technologie, která chrání před mnoha typy útoků a poskytuje odolnost, zabezpečení a škálovatelnost plně funkčního webového serveru.</span><span class="sxs-lookup"><span data-stu-id="d684c-683">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="d684c-684">Služba IIS sám spouští jako naslouchací proces HTTP nad HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d684c-684">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="d684c-685">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="d684c-685">HTTP/2 support</span></span>

<span data-ttu-id="d684c-686">[Protokol HTTP/2](https://httpwg.org/specs/rfc7540.html) je povolený pro aplikace ASP.NET Core, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="d684c-686">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="d684c-687">Windows Server 2016/Windows 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d684c-687">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="d684c-688">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="d684c-688">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="d684c-689">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d684c-689">TLS 1.2 or later connection</span></span>

<span data-ttu-id="d684c-690">Pokud je vytvořeno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/1.1` .</span><span class="sxs-lookup"><span data-stu-id="d684c-690">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="d684c-691">HTTP/2 je ve výchozím nastavení povolené.</span><span class="sxs-lookup"><span data-stu-id="d684c-691">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="d684c-692">Pokud připojení HTTP/2 není navázáno, připojení se vrátí k HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="d684c-692">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="d684c-693">V budoucí verzi Windows budou k dispozici příznaky konfigurace protokolu HTTP/2, včetně možnosti zakázat protokol HTTP/2 s HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d684c-693">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="d684c-694">Ověřování v režimu jádra pomocí protokolu Kerberos</span><span class="sxs-lookup"><span data-stu-id="d684c-694">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="d684c-695">HTTP.sys deleguje ověřování režimu jádra pomocí ověřovacího protokolu Kerberos.</span><span class="sxs-lookup"><span data-stu-id="d684c-695">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="d684c-696">Ověřování pomocí uživatelského režimu není pro protokol Kerberos a HTTP.sys podporováno.</span><span class="sxs-lookup"><span data-stu-id="d684c-696">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="d684c-697">Účet počítače se musí použít k dešifrování tokenu nebo lístku protokolu Kerberos, který je získaný ze služby Active Directory a který přesměruje klient na server za účelem ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="d684c-697">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="d684c-698">Zaregistrujte hlavní název služby (SPN) pro hostitele, nikoli uživatele aplikace.</span><span class="sxs-lookup"><span data-stu-id="d684c-698">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="d684c-699">Jak používat HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="d684c-699">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="d684c-700">Konfigurace aplikace ASP.NET Core pro použití HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="d684c-700">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="d684c-701">Odkaz na balíček v souboru projektu není vyžadován při použití [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) ([NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span><span class="sxs-lookup"><span data-stu-id="d684c-701">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="d684c-702">Pokud nepoužíváte `Microsoft.AspNetCore.App` Metapackage, přidejte odkaz na balíček do [Microsoft. AspNetCore. Server. HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span><span class="sxs-lookup"><span data-stu-id="d684c-702">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="d684c-703"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>Při sestavování hostitele zavolejte metodu rozšíření a určete požadované <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> .</span><span class="sxs-lookup"><span data-stu-id="d684c-703">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="d684c-704">Následující příklad nastaví možnosti na jejich výchozí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="d684c-704">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="d684c-705">Další konfigurace HTTP.sys se zpracovává prostřednictvím [nastavení registru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span><span class="sxs-lookup"><span data-stu-id="d684c-705">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="d684c-706">\*\* MožnostiHTTP.sys\*\*</span><span class="sxs-lookup"><span data-stu-id="d684c-706">**HTTP.sys options**</span></span>

| <span data-ttu-id="d684c-707">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="d684c-707">Property</span></span> | <span data-ttu-id="d684c-708">Popis</span><span class="sxs-lookup"><span data-stu-id="d684c-708">Description</span></span> | <span data-ttu-id="d684c-709">Výchozí</span><span class="sxs-lookup"><span data-stu-id="d684c-709">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="d684c-710">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="d684c-710">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="d684c-711">Určuje, zda je povolen synchronní vstup/výstup pro `HttpContext.Request.Body` a `HttpContext.Response.Body` .</span><span class="sxs-lookup"><span data-stu-id="d684c-711">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="d684c-712">Ověřování. AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="d684c-712">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="d684c-713">Povoluje anonymní požadavky.</span><span class="sxs-lookup"><span data-stu-id="d684c-713">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="d684c-714">Ověřování. schémata</span><span class="sxs-lookup"><span data-stu-id="d684c-714">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="d684c-715">Určete povolená schémata ověřování.</span><span class="sxs-lookup"><span data-stu-id="d684c-715">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="d684c-716">Může být kdykoli změněno před vyřazením naslouchacího procesu.</span><span class="sxs-lookup"><span data-stu-id="d684c-716">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="d684c-717">Hodnoty jsou k dispozici ve [výčtu schémata AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic` , `Kerberos` , `Negotiate` , `None` a `NTLM` .</span><span class="sxs-lookup"><span data-stu-id="d684c-717">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="d684c-718">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="d684c-718">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="d684c-719">Pokusit se o ukládání do mezipaměti v [režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) pro odpovědi s oprávněnými záhlavími.</span><span class="sxs-lookup"><span data-stu-id="d684c-719">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="d684c-720">Odpověď nesmí obsahovat `Set-Cookie` `Vary` hlavičky, ani `Pragma` .</span><span class="sxs-lookup"><span data-stu-id="d684c-720">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="d684c-721">Musí obsahovat `Cache-Control` hlavičku, která je `public` a buď `shared-max-age` `max-age` hodnota nebo, nebo `Expires` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="d684c-721">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="d684c-722">Maximální počet souběžných přijetí.</span><span class="sxs-lookup"><span data-stu-id="d684c-722">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="d684c-723">5 &times; [prostředí. <br> ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="d684c-723">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="d684c-724">Maximální počet souběžných připojení, která se mají přijmout</span><span class="sxs-lookup"><span data-stu-id="d684c-724">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="d684c-725">Použijte `-1` pro nekonečné.</span><span class="sxs-lookup"><span data-stu-id="d684c-725">Use `-1` for infinite.</span></span> <span data-ttu-id="d684c-726">Použijte `null` k použití nastavení pro počítač v rámci registru.</span><span class="sxs-lookup"><span data-stu-id="d684c-726">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="d684c-727">(počítačově v šířce</span><span class="sxs-lookup"><span data-stu-id="d684c-727">(machine-wide</span></span><br><span data-ttu-id="d684c-728">nastavením</span><span class="sxs-lookup"><span data-stu-id="d684c-728">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="d684c-729">Viz část <a href="#maxrequestbodysize">MaxRequestBodySize</a> .</span><span class="sxs-lookup"><span data-stu-id="d684c-729">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="d684c-730">30000000 bajtů</span><span class="sxs-lookup"><span data-stu-id="d684c-730">30000000 bytes</span></span><br><span data-ttu-id="d684c-731">(~ 28,6 MB)</span><span class="sxs-lookup"><span data-stu-id="d684c-731">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="d684c-732">Maximální počet požadavků, které lze zařadit do fronty.</span><span class="sxs-lookup"><span data-stu-id="d684c-732">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="d684c-733">1000</span><span class="sxs-lookup"><span data-stu-id="d684c-733">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="d684c-734">Určuje, jestli tělo odpovědi zapisuje, které selhalo kvůli odpojení klienta, by mělo vyvolat výjimky nebo dokončit normálně.</span><span class="sxs-lookup"><span data-stu-id="d684c-734">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="d684c-735">(normálně dokončit)</span><span class="sxs-lookup"><span data-stu-id="d684c-735">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="d684c-736">Vystavte <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> konfiguraci HTTP.sys, která může být v registru nakonfigurovaná taky.</span><span class="sxs-lookup"><span data-stu-id="d684c-736">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="d684c-737">Pomocí odkazů rozhraní API získáte další informace o jednotlivých nastaveních, včetně výchozích hodnot:</span><span class="sxs-lookup"><span data-stu-id="d684c-737">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="d684c-738">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): čas povolený pro rozhraní API serveru http, který má vyprázdnit tělo entity u připojení Keep-Alive.</span><span class="sxs-lookup"><span data-stu-id="d684c-738">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="d684c-739">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): čas povolený pro doručení těla entity požadavku.</span><span class="sxs-lookup"><span data-stu-id="d684c-739">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="d684c-740">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): čas povolený pro rozhraní API serveru HTTP k analýze hlavičky žádosti.</span><span class="sxs-lookup"><span data-stu-id="d684c-740">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="d684c-741">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): čas povolený pro nečinné připojení.</span><span class="sxs-lookup"><span data-stu-id="d684c-741">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="d684c-742">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): minimální rychlost odesílání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="d684c-742">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="d684c-743">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): čas, který může požadavek zůstat ve frontě požadavků předtím, než ji aplikace vybere.</span><span class="sxs-lookup"><span data-stu-id="d684c-743">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="d684c-744">Zadejte, kam se <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> má zaregistrovat HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d684c-744">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="d684c-745">Nejužitečnější je [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), který se používá k přidání předpony do kolekce.</span><span class="sxs-lookup"><span data-stu-id="d684c-745">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="d684c-746">Ty mohou být kdykoliv změněny předtím, než se naslouchací proces vyřazuje.</span><span class="sxs-lookup"><span data-stu-id="d684c-746">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="d684c-747">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="d684c-747">**MaxRequestBodySize**</span></span>

<span data-ttu-id="d684c-748">Maximální povolená velikost libovolného textu žádosti v bajtech</span><span class="sxs-lookup"><span data-stu-id="d684c-748">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="d684c-749">Při nastavení na `null` je maximální velikost textu požadavku neomezená.</span><span class="sxs-lookup"><span data-stu-id="d684c-749">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="d684c-750">Toto omezení nemá žádný vliv na upgradovaná připojení, která jsou vždycky neomezená.</span><span class="sxs-lookup"><span data-stu-id="d684c-750">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="d684c-751">Doporučená metoda pro přepsání limitu v aplikaci ASP.NET Core MVC pro jeden `IActionResult` je použít <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atribut pro metodu akce:</span><span class="sxs-lookup"><span data-stu-id="d684c-751">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="d684c-752">Výjimka je vyvolána, pokud se aplikace pokusí nakonfigurovat limit žádosti poté, co aplikace začne číst požadavek.</span><span class="sxs-lookup"><span data-stu-id="d684c-752">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="d684c-753">`IsReadOnly`Vlastnost může být použita k označení `MaxRequestBodySize` , zda je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="d684c-753">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="d684c-754">Pokud by měla aplikace přepsat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> jednotlivé požadavky, použijte <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :</span><span class="sxs-lookup"><span data-stu-id="d684c-754">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="d684c-755">Pokud používáte Visual Studio, ujistěte se, že aplikace není nakonfigurovaná tak, aby spouštěla službu IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="d684c-755">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="d684c-756">V aplikaci Visual Studio je výchozím spouštěcím profilem IIS Express.</span><span class="sxs-lookup"><span data-stu-id="d684c-756">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="d684c-757">Chcete-li spustit projekt jako konzolovou aplikaci, ručně změňte vybraný profil, jak je znázorněno na následujícím snímku obrazovky:</span><span class="sxs-lookup"><span data-stu-id="d684c-757">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![Vybrat profil aplikace konzoly](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="d684c-759">Konfigurace Windows serveru</span><span class="sxs-lookup"><span data-stu-id="d684c-759">Configure Windows Server</span></span>

1. <span data-ttu-id="d684c-760">Určete porty, které se mají otevřít pro aplikaci, a pomocí [brány Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) nebo rutiny [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) prostředí PowerShell otevřete porty brány firewall, abyste umožnili provozu HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d684c-760">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="d684c-761">V následujících příkazech a konfiguraci aplikace se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="d684c-761">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="d684c-762">Při nasazování na virtuální počítač Azure otevřete porty ve [skupině zabezpečení sítě](/azure/virtual-machines/windows/nsg-quickstart-portal).</span><span class="sxs-lookup"><span data-stu-id="d684c-762">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="d684c-763">V následujících příkazech a konfiguraci aplikace se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="d684c-763">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="d684c-764">V případě potřeby Získejte a nainstalujte certifikáty X. 509.</span><span class="sxs-lookup"><span data-stu-id="d684c-764">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="d684c-765">V systému Windows vytvořte pomocí [rutiny prostředí PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate)certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="d684c-765">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="d684c-766">Nepodporovaný příklad naleznete v tématu [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span><span class="sxs-lookup"><span data-stu-id="d684c-766">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="d684c-767">Nainstalujte certifikáty podepsané svým držitelem nebo certifikátem podepsaným certifikační autoritou do osobního úložiště **místního počítače** serveru > **Personal** .</span><span class="sxs-lookup"><span data-stu-id="d684c-767">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="d684c-768">Pokud se jedná o [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd), nainstalujte rozhraní .NET core, .NET Framework nebo obojí (Pokud je aplikace aplikace .NET Core cílící na .NET Framework).</span><span class="sxs-lookup"><span data-stu-id="d684c-768">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="d684c-769">**.NET Core**: Pokud aplikace vyžaduje .NET Core, Získejte a spusťte instalační program **modulu runtime .NET Core** ze [souborů ke stažení pro .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="d684c-769">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="d684c-770">Neinstalujte na server plnou sadu SDK.</span><span class="sxs-lookup"><span data-stu-id="d684c-770">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="d684c-771">**.NET Framework**: Pokud aplikace vyžaduje .NET Framework, přečtěte si téma [Průvodce instalací .NET Framework](/dotnet/framework/install/).</span><span class="sxs-lookup"><span data-stu-id="d684c-771">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="d684c-772">Nainstalujte požadovanou .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="d684c-772">Install the required .NET Framework.</span></span> <span data-ttu-id="d684c-773">Instalační program pro nejnovější .NET Framework je k dispozici na stránce [soubory ke stažení pro .NET Core](https://dotnet.microsoft.com/download) .</span><span class="sxs-lookup"><span data-stu-id="d684c-773">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="d684c-774">Pokud je aplikace samostatným [nasazením](/dotnet/core/deploying/#self-contained-deployments-scd), aplikace zahrne modul runtime do svého nasazení.</span><span class="sxs-lookup"><span data-stu-id="d684c-774">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="d684c-775">Na serveru není nutná žádná instalace rozhraní.</span><span class="sxs-lookup"><span data-stu-id="d684c-775">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="d684c-776">Konfigurace adres URL a portů v aplikaci</span><span class="sxs-lookup"><span data-stu-id="d684c-776">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="d684c-777">Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="d684c-777">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d684c-778">Pokud chcete nakonfigurovat předpony a porty adresy URL, můžete použít tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="d684c-778">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="d684c-779">`urls` argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="d684c-779">`urls` command-line argument</span></span>
   * <span data-ttu-id="d684c-780">`ASPNETCORE_URLS` Proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="d684c-780">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="d684c-781">Následující příklad kódu ukazuje, jak používat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> s místní IP adresou serveru `10.0.0.4` na portu 443:</span><span class="sxs-lookup"><span data-stu-id="d684c-781">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="d684c-782">Výhodou `UrlPrefixes` je, že se okamžitě generuje chybová zpráva pro nesprávně naformátované předpony.</span><span class="sxs-lookup"><span data-stu-id="d684c-782">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="d684c-783">Nastavení v `UrlPrefixes` `UseUrls` / `urls` / `ASPNETCORE_URLS` Nastavení přepsání</span><span class="sxs-lookup"><span data-stu-id="d684c-783">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="d684c-784">Proto je výhodou `UseUrls` `urls` `ASPNETCORE_URLS` proměnné prostředí, a jednodušší přepínání mezi Kestrel a HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d684c-784">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="d684c-785">HTTP.sys používá [formáty řetězce UrlPrefix HTTP serveru API](/windows/win32/http/urlprefix-strings).</span><span class="sxs-lookup"><span data-stu-id="d684c-785">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="d684c-786">Nelze použít vazby zástupných znaků na nejvyšší úrovni ( `http://*:80/` a `http://+:80` ). **not**</span><span class="sxs-lookup"><span data-stu-id="d684c-786">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="d684c-787">Vazby na zástupné znaky na nejvyšší úrovni vytvoří chyby zabezpečení aplikace.</span><span class="sxs-lookup"><span data-stu-id="d684c-787">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="d684c-788">To platí pro silné i slabé zástupné znaky.</span><span class="sxs-lookup"><span data-stu-id="d684c-788">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="d684c-789">Místo zástupných znaků použijte explicitní názvy hostitelů nebo IP adresy.</span><span class="sxs-lookup"><span data-stu-id="d684c-789">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="d684c-790">Vazba zástupných znaků subdomény (například `*.mysub.com` ) není bezpečnostní riziko, pokud ovládáte celou nadřazenou doménu (na rozdíl od `*.com` , která je zranitelná).</span><span class="sxs-lookup"><span data-stu-id="d684c-790">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="d684c-791">Další informace najdete v [dokumentu RFC 7230: oddíl 5,4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="d684c-791">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="d684c-792">Předregistrujte předpony adresy URL na serveru.</span><span class="sxs-lookup"><span data-stu-id="d684c-792">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="d684c-793">K \* disnetsh.exe\*je integrovaný nástroj pro konfiguraci HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d684c-793">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="d684c-794">*netsh.exe* se používá k rezervaci předpon adres URL a přiřazování certifikátů X. 509.</span><span class="sxs-lookup"><span data-stu-id="d684c-794">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="d684c-795">Nástroj vyžaduje oprávnění správce.</span><span class="sxs-lookup"><span data-stu-id="d684c-795">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="d684c-796">K registraci adres URL aplikace použijte nástroj *netsh.exe* :</span><span class="sxs-lookup"><span data-stu-id="d684c-796">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="d684c-797">`<URL>`: Plně kvalifikované adresy URL (Uniform Resource Locator).</span><span class="sxs-lookup"><span data-stu-id="d684c-797">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="d684c-798">Nepoužívejte vazbu zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="d684c-798">Don't use a wildcard binding.</span></span> <span data-ttu-id="d684c-799">Použijte platný název hostitele nebo místní IP adresu.</span><span class="sxs-lookup"><span data-stu-id="d684c-799">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="d684c-800">*Adresa URL musí obsahovat koncové lomítko.*</span><span class="sxs-lookup"><span data-stu-id="d684c-800">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="d684c-801">`<USER>`: Určuje název uživatele nebo skupiny uživatelů.</span><span class="sxs-lookup"><span data-stu-id="d684c-801">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="d684c-802">V následujícím příkladu je místní IP adresa serveru `10.0.0.4` :</span><span class="sxs-lookup"><span data-stu-id="d684c-802">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="d684c-803">Když je adresa URL registrována, nástroj odpoví `URL reservation successfully added` .</span><span class="sxs-lookup"><span data-stu-id="d684c-803">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="d684c-804">Pokud chcete odstranit registrovanou adresu URL, použijte `delete urlacl` příkaz:</span><span class="sxs-lookup"><span data-stu-id="d684c-804">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="d684c-805">Zaregistrujte certifikáty X. 509 na serveru.</span><span class="sxs-lookup"><span data-stu-id="d684c-805">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="d684c-806">K registraci certifikátů pro aplikaci použijte nástroj *netsh.exe* :</span><span class="sxs-lookup"><span data-stu-id="d684c-806">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="d684c-807">`<IP>`: Určuje místní IP adresu pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="d684c-807">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="d684c-808">Nepoužívejte vazbu zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="d684c-808">Don't use a wildcard binding.</span></span> <span data-ttu-id="d684c-809">Použijte platnou IP adresu.</span><span class="sxs-lookup"><span data-stu-id="d684c-809">Use a valid IP address.</span></span>
   * <span data-ttu-id="d684c-810">`<PORT>`: Určuje port pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="d684c-810">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="d684c-811">`<THUMBPRINT>`: Kryptografický otisk certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="d684c-811">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="d684c-812">`<GUID>`: Identifikátor GUID generovaný vývojářem, který představuje aplikaci pro informativní účely.</span><span class="sxs-lookup"><span data-stu-id="d684c-812">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="d684c-813">Pro referenční účely uložte identifikátor GUID do aplikace jako značku balíčku:</span><span class="sxs-lookup"><span data-stu-id="d684c-813">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="d684c-814">V aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d684c-814">In Visual Studio:</span></span>
     * <span data-ttu-id="d684c-815">Otevřete vlastnosti projektu aplikace tak, že kliknete pravým tlačítkem na aplikaci v **Průzkumník řešení** a vyberete **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="d684c-815">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="d684c-816">Vyberte kartu **balíček** .</span><span class="sxs-lookup"><span data-stu-id="d684c-816">Select the **Package** tab.</span></span>
     * <span data-ttu-id="d684c-817">Zadejte identifikátor GUID, který jste vytvořili v poli **značky** .</span><span class="sxs-lookup"><span data-stu-id="d684c-817">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="d684c-818">Když nepoužíváte Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d684c-818">When not using Visual Studio:</span></span>
     * <span data-ttu-id="d684c-819">Otevřete soubor projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="d684c-819">Open the app's project file.</span></span>
     * <span data-ttu-id="d684c-820">Přidejte `<PackageTags>` vlastnost do nového nebo existujícího `<PropertyGroup>` s identifikátorem GUID, který jste vytvořili:</span><span class="sxs-lookup"><span data-stu-id="d684c-820">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="d684c-821">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="d684c-821">In the following example:</span></span>

   * <span data-ttu-id="d684c-822">Místní IP adresa serveru je `10.0.0.4` .</span><span class="sxs-lookup"><span data-stu-id="d684c-822">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="d684c-823">Generátor náhodných identifikátorů GUID online poskytuje `appid` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="d684c-823">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="d684c-824">Když je certifikát zaregistrován, nástroj odpoví `SSL Certificate successfully added` .</span><span class="sxs-lookup"><span data-stu-id="d684c-824">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="d684c-825">K odstranění registrace certifikátu použijte `delete sslcert` příkaz:</span><span class="sxs-lookup"><span data-stu-id="d684c-825">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="d684c-826">Referenční dokumentace pro *netsh.exe*:</span><span class="sxs-lookup"><span data-stu-id="d684c-826">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="d684c-827">[Příkazy Netsh pro protokol HTTP (Hypertext Transfer Protocol)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="d684c-827">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="d684c-828">UrlPrefix řetězce</span><span class="sxs-lookup"><span data-stu-id="d684c-828">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="d684c-829">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d684c-829">Run the app.</span></span>

   <span data-ttu-id="d684c-830">Oprávnění správce nejsou nutná ke spuštění aplikace při vytváření vazby na localhost pomocí protokolu HTTP (ne HTTPS) s číslem portu větším než 1024.</span><span class="sxs-lookup"><span data-stu-id="d684c-830">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="d684c-831">U ostatních konfigurací (například pomocí místní IP adresy nebo vazby na port 443) spusťte aplikaci s oprávněními správce.</span><span class="sxs-lookup"><span data-stu-id="d684c-831">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="d684c-832">Aplikace odpoví na veřejnou IP adresu serveru.</span><span class="sxs-lookup"><span data-stu-id="d684c-832">The app responds at the server's public IP address.</span></span> <span data-ttu-id="d684c-833">V tomto příkladu je server dosažitelný z Internetu na jeho veřejné IP adrese `104.214.79.47` .</span><span class="sxs-lookup"><span data-stu-id="d684c-833">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="d684c-834">V tomto příkladu se používá vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="d684c-834">A development certificate is used in this example.</span></span> <span data-ttu-id="d684c-835">Stránka se načte bezpečně po obejít upozornění na nedůvěryhodný certifikát v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="d684c-835">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![Okno prohlížeče zobrazující načtenou stránku indexu aplikace](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d684c-837">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="d684c-837">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d684c-838">Pro aplikace hostované HTTP.sys, které komunikují s požadavky z Internetu nebo podnikové sítě, se může při hostování za proxy servery a nástroji pro vyrovnávání zatížení vyžadovat další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="d684c-838">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="d684c-839">Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="d684c-839">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d684c-840">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="d684c-840">Additional resources</span></span>

* [<span data-ttu-id="d684c-841">Povolit ověřování systému Windows pomocí HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="d684c-841">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="d684c-842">Rozhraní API serveru HTTP</span><span class="sxs-lookup"><span data-stu-id="d684c-842">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="d684c-843">úložiště GitHubu ASPNET/HttpSysServer (zdrojový kód)</span><span class="sxs-lookup"><span data-stu-id="d684c-843">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="d684c-844">Hostitel</span><span class="sxs-lookup"><span data-stu-id="d684c-844">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
