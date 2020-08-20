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
ms.openlocfilehash: 29af28f512764b9efec682b44c8de1d2ae03ee04
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631131"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="78e6f-104">Implementace webového serveru HTTP.sys v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="78e6f-104">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="78e6f-105">[Dykstra](https://github.com/tdykstra) a [Chris Rossův](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="78e6f-105">By [Tom Dykstra](https://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="78e6f-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) je [webový server pro ASP.NET Core](xref:fundamentals/servers/index) , který běží jenom ve Windows.</span><span class="sxs-lookup"><span data-stu-id="78e6f-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="78e6f-107">HTTP.sys je alternativou k serveru [Kestrel](xref:fundamentals/servers/kestrel) a nabízí některé funkce, které Kestrel neposkytují.</span><span class="sxs-lookup"><span data-stu-id="78e6f-107">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="78e6f-108">HTTP.sys není kompatibilní s [modulem ASP.NET Core](xref:host-and-deploy/aspnet-core-module) a nedá se použít se službou IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="78e6f-108">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="78e6f-109">HTTP.sys podporuje následující funkce:</span><span class="sxs-lookup"><span data-stu-id="78e6f-109">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="78e6f-110">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="78e6f-110">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="78e6f-111">Sdílení portů</span><span class="sxs-lookup"><span data-stu-id="78e6f-111">Port sharing</span></span>
* <span data-ttu-id="78e6f-112">HTTPS s SNI</span><span class="sxs-lookup"><span data-stu-id="78e6f-112">HTTPS with SNI</span></span>
* <span data-ttu-id="78e6f-113">HTTP/2 přes TLS (Windows 10 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="78e6f-113">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="78e6f-114">Přímý přenos souborů</span><span class="sxs-lookup"><span data-stu-id="78e6f-114">Direct file transmission</span></span>
* <span data-ttu-id="78e6f-115">Ukládání odpovědí do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="78e6f-115">Response caching</span></span>
* <span data-ttu-id="78e6f-116">WebSockets (Windows 8 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="78e6f-116">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="78e6f-117">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="78e6f-117">Supported Windows versions:</span></span>

* <span data-ttu-id="78e6f-118">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="78e6f-118">Windows 7 or later</span></span>
* <span data-ttu-id="78e6f-119">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="78e6f-119">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="78e6f-120">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="78e6f-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="78e6f-121">Kdy použít HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="78e6f-121">When to use HTTP.sys</span></span>

<span data-ttu-id="78e6f-122">HTTP.sys je užitečná pro nasazení, kde:</span><span class="sxs-lookup"><span data-stu-id="78e6f-122">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="78e6f-123">Server je potřeba zveřejnit přímo na internetu bez použití IIS.</span><span class="sxs-lookup"><span data-stu-id="78e6f-123">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys komunikuje přímo s internetem.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="78e6f-125">Interní nasazení vyžaduje funkci, která není v Kestrel k dispozici, jako je například [ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="78e6f-125">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys komunikuje přímo s interní sítí.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="78e6f-127">HTTP.sys je Vyspělá technologie, která chrání před mnoha typy útoků a poskytuje odolnost, zabezpečení a škálovatelnost plně funkčního webového serveru.</span><span class="sxs-lookup"><span data-stu-id="78e6f-127">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="78e6f-128">Služba IIS sám spouští jako naslouchací proces HTTP nad HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="78e6f-128">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="78e6f-129">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="78e6f-129">HTTP/2 support</span></span>

<span data-ttu-id="78e6f-130">[Protokol HTTP/2](https://httpwg.org/specs/rfc7540.html) je povolený pro aplikace ASP.NET Core, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="78e6f-130">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="78e6f-131">Windows Server 2016/Windows 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="78e6f-131">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="78e6f-132">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="78e6f-132">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="78e6f-133">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="78e6f-133">TLS 1.2 or later connection</span></span>

<span data-ttu-id="78e6f-134">Pokud je vytvořeno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-134">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="78e6f-135">HTTP/2 je ve výchozím nastavení povolené.</span><span class="sxs-lookup"><span data-stu-id="78e6f-135">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="78e6f-136">Pokud připojení HTTP/2 není navázáno, připojení se vrátí k HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="78e6f-136">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="78e6f-137">V budoucí verzi Windows budou k dispozici příznaky konfigurace protokolu HTTP/2, včetně možnosti zakázat protokol HTTP/2 s HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="78e6f-137">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="78e6f-138">Ověřování v režimu jádra pomocí protokolu Kerberos</span><span class="sxs-lookup"><span data-stu-id="78e6f-138">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="78e6f-139">HTTP.sys deleguje ověřování režimu jádra pomocí ověřovacího protokolu Kerberos.</span><span class="sxs-lookup"><span data-stu-id="78e6f-139">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="78e6f-140">Ověřování pomocí uživatelského režimu není pro protokol Kerberos a HTTP.sys podporováno.</span><span class="sxs-lookup"><span data-stu-id="78e6f-140">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="78e6f-141">Účet počítače se musí použít k dešifrování tokenu nebo lístku protokolu Kerberos, který je získaný ze služby Active Directory a který přesměruje klient na server za účelem ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="78e6f-141">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="78e6f-142">Zaregistrujte hlavní název služby (SPN) pro hostitele, nikoli uživatele aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e6f-142">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="78e6f-143">Jak používat HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="78e6f-143">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="78e6f-144">Konfigurace aplikace ASP.NET Core pro použití HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="78e6f-144">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="78e6f-145"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>Při sestavování hostitele zavolejte metodu rozšíření a určete požadované <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> .</span><span class="sxs-lookup"><span data-stu-id="78e6f-145">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="78e6f-146">Následující příklad nastaví možnosti na jejich výchozí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="78e6f-146">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="78e6f-147">Další konfigurace HTTP.sys se zpracovává prostřednictvím [nastavení registru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span><span class="sxs-lookup"><span data-stu-id="78e6f-147">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="78e6f-148">\*\* MožnostiHTTP.sys\*\*</span><span class="sxs-lookup"><span data-stu-id="78e6f-148">**HTTP.sys options**</span></span>

| <span data-ttu-id="78e6f-149">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="78e6f-149">Property</span></span> | <span data-ttu-id="78e6f-150">Popis</span><span class="sxs-lookup"><span data-stu-id="78e6f-150">Description</span></span> | <span data-ttu-id="78e6f-151">Výchozí</span><span class="sxs-lookup"><span data-stu-id="78e6f-151">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="78e6f-152">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="78e6f-152">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="78e6f-153">Určuje, zda je povolen synchronní vstup/výstup pro `HttpContext.Request.Body` a `HttpContext.Response.Body` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-153">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="78e6f-154">Ověřování. AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="78e6f-154">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="78e6f-155">Povoluje anonymní požadavky.</span><span class="sxs-lookup"><span data-stu-id="78e6f-155">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="78e6f-156">Ověřování. schémata</span><span class="sxs-lookup"><span data-stu-id="78e6f-156">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="78e6f-157">Určete povolená schémata ověřování.</span><span class="sxs-lookup"><span data-stu-id="78e6f-157">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="78e6f-158">Může být kdykoli změněno před vyřazením naslouchacího procesu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-158">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="78e6f-159">Hodnoty jsou k dispozici ve [výčtu schémata AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic` , `Kerberos` , `Negotiate` , `None` a `NTLM` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-159">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="78e6f-160">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="78e6f-160">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="78e6f-161">Pokusit se o ukládání do mezipaměti v [režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) pro odpovědi s oprávněnými záhlavími.</span><span class="sxs-lookup"><span data-stu-id="78e6f-161">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="78e6f-162">Odpověď nesmí obsahovat `Set-Cookie` `Vary` hlavičky, ani `Pragma` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-162">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="78e6f-163">Musí obsahovat `Cache-Control` hlavičku, která je `public` a buď `shared-max-age` `max-age` hodnota nebo, nebo `Expires` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="78e6f-163">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="78e6f-164">Maximální počet souběžných přijetí.</span><span class="sxs-lookup"><span data-stu-id="78e6f-164">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="78e6f-165">5 &times; [prostředí. <br> ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="78e6f-165">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="78e6f-166">Maximální počet souběžných připojení, která se mají přijmout</span><span class="sxs-lookup"><span data-stu-id="78e6f-166">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="78e6f-167">Použijte `-1` pro nekonečné.</span><span class="sxs-lookup"><span data-stu-id="78e6f-167">Use `-1` for infinite.</span></span> <span data-ttu-id="78e6f-168">Použijte `null` k použití nastavení pro počítač v rámci registru.</span><span class="sxs-lookup"><span data-stu-id="78e6f-168">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="78e6f-169">(počítačově v šířce</span><span class="sxs-lookup"><span data-stu-id="78e6f-169">(machine-wide</span></span><br><span data-ttu-id="78e6f-170">nastavením</span><span class="sxs-lookup"><span data-stu-id="78e6f-170">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="78e6f-171">Viz část <a href="#maxrequestbodysize">MaxRequestBodySize</a> .</span><span class="sxs-lookup"><span data-stu-id="78e6f-171">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="78e6f-172">30000000 bajtů</span><span class="sxs-lookup"><span data-stu-id="78e6f-172">30000000 bytes</span></span><br><span data-ttu-id="78e6f-173">(~ 28,6 MB)</span><span class="sxs-lookup"><span data-stu-id="78e6f-173">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="78e6f-174">Maximální počet požadavků, které lze zařadit do fronty.</span><span class="sxs-lookup"><span data-stu-id="78e6f-174">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="78e6f-175">1000</span><span class="sxs-lookup"><span data-stu-id="78e6f-175">1000</span></span> |
| `RequestQueueMode` | <span data-ttu-id="78e6f-176">Označuje, zda je server zodpovědný za vytvoření a konfiguraci fronty požadavků nebo zda se má připojit k existující frontě.</span><span class="sxs-lookup"><span data-stu-id="78e6f-176">This indicates whether the server is responsible for creating and configuring the request queue, or if it should attach to an existing queue.</span></span><br><span data-ttu-id="78e6f-177">Většina stávajících možností konfigurace se nedá použít při připojování k existující frontě.</span><span class="sxs-lookup"><span data-stu-id="78e6f-177">Most existing configuration options do not apply when attaching to an existing queue.</span></span> | `RequestQueueMode.Create` |
| `RequestQueueName` | <span data-ttu-id="78e6f-178">Název fronty požadavků HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="78e6f-178">The name of the HTTP.sys request queue.</span></span> | <span data-ttu-id="78e6f-179">`null` (Anonymní fronta)</span><span class="sxs-lookup"><span data-stu-id="78e6f-179">`null` (Anonymous queue)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="78e6f-180">Určuje, jestli tělo odpovědi zapisuje, které selhalo kvůli odpojení klienta, by mělo vyvolat výjimky nebo dokončit normálně.</span><span class="sxs-lookup"><span data-stu-id="78e6f-180">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="78e6f-181">(normálně dokončit)</span><span class="sxs-lookup"><span data-stu-id="78e6f-181">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="78e6f-182">Vystavte <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> konfiguraci HTTP.sys, která může být v registru nakonfigurovaná taky.</span><span class="sxs-lookup"><span data-stu-id="78e6f-182">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="78e6f-183">Pomocí odkazů rozhraní API získáte další informace o jednotlivých nastaveních, včetně výchozích hodnot:</span><span class="sxs-lookup"><span data-stu-id="78e6f-183">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="78e6f-184">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): čas povolený pro rozhraní API serveru http, který má vyprázdnit tělo entity u připojení Keep-Alive.</span><span class="sxs-lookup"><span data-stu-id="78e6f-184">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="78e6f-185">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): čas povolený pro doručení těla entity požadavku.</span><span class="sxs-lookup"><span data-stu-id="78e6f-185">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="78e6f-186">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): čas povolený pro rozhraní API serveru HTTP k analýze hlavičky žádosti.</span><span class="sxs-lookup"><span data-stu-id="78e6f-186">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="78e6f-187">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): čas povolený pro nečinné připojení.</span><span class="sxs-lookup"><span data-stu-id="78e6f-187">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="78e6f-188">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): minimální rychlost odesílání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="78e6f-188">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="78e6f-189">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): čas, který může požadavek zůstat ve frontě požadavků předtím, než ji aplikace vybere.</span><span class="sxs-lookup"><span data-stu-id="78e6f-189">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="78e6f-190">Zadejte, kam se <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> má zaregistrovat HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="78e6f-190">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="78e6f-191">Nejužitečnější je [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), který se používá k přidání předpony do kolekce.</span><span class="sxs-lookup"><span data-stu-id="78e6f-191">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="78e6f-192">Ty mohou být kdykoliv změněny předtím, než se naslouchací proces vyřazuje.</span><span class="sxs-lookup"><span data-stu-id="78e6f-192">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="78e6f-193">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="78e6f-193">**MaxRequestBodySize**</span></span>

<span data-ttu-id="78e6f-194">Maximální povolená velikost libovolného textu žádosti v bajtech</span><span class="sxs-lookup"><span data-stu-id="78e6f-194">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="78e6f-195">Při nastavení na `null` je maximální velikost textu požadavku neomezená.</span><span class="sxs-lookup"><span data-stu-id="78e6f-195">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="78e6f-196">Toto omezení nemá žádný vliv na upgradovaná připojení, která jsou vždycky neomezená.</span><span class="sxs-lookup"><span data-stu-id="78e6f-196">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="78e6f-197">Doporučená metoda pro přepsání limitu v aplikaci ASP.NET Core MVC pro jeden `IActionResult` je použít <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atribut pro metodu akce:</span><span class="sxs-lookup"><span data-stu-id="78e6f-197">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="78e6f-198">Výjimka je vyvolána, pokud se aplikace pokusí nakonfigurovat limit žádosti poté, co aplikace začne číst požadavek.</span><span class="sxs-lookup"><span data-stu-id="78e6f-198">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="78e6f-199">`IsReadOnly`Vlastnost může být použita k označení `MaxRequestBodySize` , zda je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-199">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="78e6f-200">Pokud by měla aplikace přepsat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> jednotlivé požadavky, použijte <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :</span><span class="sxs-lookup"><span data-stu-id="78e6f-200">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="78e6f-201">Pokud používáte Visual Studio, ujistěte se, že aplikace není nakonfigurovaná tak, aby spouštěla službu IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="78e6f-201">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="78e6f-202">V aplikaci Visual Studio je výchozím spouštěcím profilem IIS Express.</span><span class="sxs-lookup"><span data-stu-id="78e6f-202">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="78e6f-203">Chcete-li spustit projekt jako konzolovou aplikaci, ručně změňte vybraný profil, jak je znázorněno na následujícím snímku obrazovky:</span><span class="sxs-lookup"><span data-stu-id="78e6f-203">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![Vybrat profil aplikace konzoly](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="78e6f-205">Konfigurace Windows serveru</span><span class="sxs-lookup"><span data-stu-id="78e6f-205">Configure Windows Server</span></span>

1. <span data-ttu-id="78e6f-206">Určete porty, které se mají otevřít pro aplikaci, a pomocí [brány Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) nebo rutiny [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) prostředí PowerShell otevřete porty brány firewall, abyste umožnili provozu HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="78e6f-206">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="78e6f-207">V následujících příkazech a konfiguraci aplikace se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="78e6f-207">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="78e6f-208">Při nasazování na virtuální počítač Azure otevřete porty ve [skupině zabezpečení sítě](/azure/virtual-machines/windows/nsg-quickstart-portal).</span><span class="sxs-lookup"><span data-stu-id="78e6f-208">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="78e6f-209">V následujících příkazech a konfiguraci aplikace se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="78e6f-209">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="78e6f-210">V případě potřeby Získejte a nainstalujte certifikáty X. 509.</span><span class="sxs-lookup"><span data-stu-id="78e6f-210">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="78e6f-211">V systému Windows vytvořte pomocí [rutiny prostředí PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate)certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="78e6f-211">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="78e6f-212">Nepodporovaný příklad naleznete v tématu [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span><span class="sxs-lookup"><span data-stu-id="78e6f-212">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="78e6f-213">Nainstalujte certifikáty podepsané svým držitelem nebo certifikátem podepsaným certifikační autoritou do osobního úložiště **místního počítače** serveru > **Personal** .</span><span class="sxs-lookup"><span data-stu-id="78e6f-213">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="78e6f-214">Pokud se jedná o [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd), nainstalujte rozhraní .NET core, .NET Framework nebo obojí (Pokud je aplikace aplikace .NET Core cílící na .NET Framework).</span><span class="sxs-lookup"><span data-stu-id="78e6f-214">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="78e6f-215">**.NET Core**: Pokud aplikace vyžaduje .NET Core, Získejte a spusťte instalační program **modulu runtime .NET Core** ze [souborů ke stažení pro .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="78e6f-215">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="78e6f-216">Neinstalujte na server plnou sadu SDK.</span><span class="sxs-lookup"><span data-stu-id="78e6f-216">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="78e6f-217">**.NET Framework**: Pokud aplikace vyžaduje .NET Framework, přečtěte si téma [Průvodce instalací .NET Framework](/dotnet/framework/install/).</span><span class="sxs-lookup"><span data-stu-id="78e6f-217">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="78e6f-218">Nainstalujte požadovanou .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="78e6f-218">Install the required .NET Framework.</span></span> <span data-ttu-id="78e6f-219">Instalační program pro nejnovější .NET Framework je k dispozici na stránce [soubory ke stažení pro .NET Core](https://dotnet.microsoft.com/download) .</span><span class="sxs-lookup"><span data-stu-id="78e6f-219">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="78e6f-220">Pokud je aplikace samostatným [nasazením](/dotnet/core/deploying/#self-contained-deployments-scd), aplikace zahrne modul runtime do svého nasazení.</span><span class="sxs-lookup"><span data-stu-id="78e6f-220">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="78e6f-221">Na serveru není nutná žádná instalace rozhraní.</span><span class="sxs-lookup"><span data-stu-id="78e6f-221">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="78e6f-222">Konfigurace adres URL a portů v aplikaci</span><span class="sxs-lookup"><span data-stu-id="78e6f-222">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="78e6f-223">Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-223">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="78e6f-224">Pokud chcete nakonfigurovat předpony a porty adresy URL, můžete použít tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="78e6f-224">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="78e6f-225">`urls` argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="78e6f-225">`urls` command-line argument</span></span>
   * <span data-ttu-id="78e6f-226">`ASPNETCORE_URLS` Proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="78e6f-226">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="78e6f-227">Následující příklad kódu ukazuje, jak používat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> s místní IP adresou serveru `10.0.0.4` na portu 443:</span><span class="sxs-lookup"><span data-stu-id="78e6f-227">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="78e6f-228">Výhodou `UrlPrefixes` je, že se okamžitě generuje chybová zpráva pro nesprávně naformátované předpony.</span><span class="sxs-lookup"><span data-stu-id="78e6f-228">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="78e6f-229">Nastavení v `UrlPrefixes` `UseUrls` / `urls` / `ASPNETCORE_URLS` Nastavení přepsání</span><span class="sxs-lookup"><span data-stu-id="78e6f-229">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="78e6f-230">Proto je výhodou `UseUrls` `urls` `ASPNETCORE_URLS` proměnné prostředí, a jednodušší přepínání mezi Kestrel a HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="78e6f-230">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="78e6f-231">HTTP.sys používá [formáty řetězce UrlPrefix HTTP serveru API](/windows/win32/http/urlprefix-strings).</span><span class="sxs-lookup"><span data-stu-id="78e6f-231">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="78e6f-232">Nelze použít vazby zástupných znaků na nejvyšší úrovni ( `http://*:80/` a `http://+:80` ). **not**</span><span class="sxs-lookup"><span data-stu-id="78e6f-232">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="78e6f-233">Vazby na zástupné znaky na nejvyšší úrovni vytvoří chyby zabezpečení aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e6f-233">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="78e6f-234">To platí pro silné i slabé zástupné znaky.</span><span class="sxs-lookup"><span data-stu-id="78e6f-234">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="78e6f-235">Místo zástupných znaků použijte explicitní názvy hostitelů nebo IP adresy.</span><span class="sxs-lookup"><span data-stu-id="78e6f-235">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="78e6f-236">Vazba zástupných znaků subdomény (například `*.mysub.com` ) není bezpečnostní riziko, pokud ovládáte celou nadřazenou doménu (na rozdíl od `*.com` , která je zranitelná).</span><span class="sxs-lookup"><span data-stu-id="78e6f-236">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="78e6f-237">Další informace najdete v [dokumentu RFC 7230: oddíl 5,4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="78e6f-237">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="78e6f-238">Předregistrujte předpony adresy URL na serveru.</span><span class="sxs-lookup"><span data-stu-id="78e6f-238">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="78e6f-239">K \* disnetsh.exe\*je integrovaný nástroj pro konfiguraci HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="78e6f-239">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="78e6f-240">*netsh.exe* se používá k rezervaci předpon adres URL a přiřazování certifikátů X. 509.</span><span class="sxs-lookup"><span data-stu-id="78e6f-240">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="78e6f-241">Nástroj vyžaduje oprávnění správce.</span><span class="sxs-lookup"><span data-stu-id="78e6f-241">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="78e6f-242">K registraci adres URL aplikace použijte nástroj *netsh.exe* :</span><span class="sxs-lookup"><span data-stu-id="78e6f-242">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="78e6f-243">`<URL>`: Plně kvalifikované adresy URL (Uniform Resource Locator).</span><span class="sxs-lookup"><span data-stu-id="78e6f-243">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="78e6f-244">Nepoužívejte vazbu zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="78e6f-244">Don't use a wildcard binding.</span></span> <span data-ttu-id="78e6f-245">Použijte platný název hostitele nebo místní IP adresu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-245">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="78e6f-246">*Adresa URL musí obsahovat koncové lomítko.*</span><span class="sxs-lookup"><span data-stu-id="78e6f-246">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="78e6f-247">`<USER>`: Určuje název uživatele nebo skupiny uživatelů.</span><span class="sxs-lookup"><span data-stu-id="78e6f-247">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="78e6f-248">V následujícím příkladu je místní IP adresa serveru `10.0.0.4` :</span><span class="sxs-lookup"><span data-stu-id="78e6f-248">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="78e6f-249">Když je adresa URL registrována, nástroj odpoví `URL reservation successfully added` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-249">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="78e6f-250">Pokud chcete odstranit registrovanou adresu URL, použijte `delete urlacl` příkaz:</span><span class="sxs-lookup"><span data-stu-id="78e6f-250">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="78e6f-251">Zaregistrujte certifikáty X. 509 na serveru.</span><span class="sxs-lookup"><span data-stu-id="78e6f-251">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="78e6f-252">K registraci certifikátů pro aplikaci použijte nástroj *netsh.exe* :</span><span class="sxs-lookup"><span data-stu-id="78e6f-252">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="78e6f-253">`<IP>`: Určuje místní IP adresu pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-253">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="78e6f-254">Nepoužívejte vazbu zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="78e6f-254">Don't use a wildcard binding.</span></span> <span data-ttu-id="78e6f-255">Použijte platnou IP adresu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-255">Use a valid IP address.</span></span>
   * <span data-ttu-id="78e6f-256">`<PORT>`: Určuje port pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-256">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="78e6f-257">`<THUMBPRINT>`: Kryptografický otisk certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="78e6f-257">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="78e6f-258">`<GUID>`: Identifikátor GUID generovaný vývojářem, který představuje aplikaci pro informativní účely.</span><span class="sxs-lookup"><span data-stu-id="78e6f-258">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="78e6f-259">Pro referenční účely uložte identifikátor GUID do aplikace jako značku balíčku:</span><span class="sxs-lookup"><span data-stu-id="78e6f-259">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="78e6f-260">V aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="78e6f-260">In Visual Studio:</span></span>
     * <span data-ttu-id="78e6f-261">Otevřete vlastnosti projektu aplikace tak, že kliknete pravým tlačítkem na aplikaci v **Průzkumník řešení** a vyberete **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="78e6f-261">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="78e6f-262">Vyberte kartu **balíček** .</span><span class="sxs-lookup"><span data-stu-id="78e6f-262">Select the **Package** tab.</span></span>
     * <span data-ttu-id="78e6f-263">Zadejte identifikátor GUID, který jste vytvořili v poli **značky** .</span><span class="sxs-lookup"><span data-stu-id="78e6f-263">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="78e6f-264">Když nepoužíváte Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="78e6f-264">When not using Visual Studio:</span></span>
     * <span data-ttu-id="78e6f-265">Otevřete soubor projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e6f-265">Open the app's project file.</span></span>
     * <span data-ttu-id="78e6f-266">Přidejte `<PackageTags>` vlastnost do nového nebo existujícího `<PropertyGroup>` s identifikátorem GUID, který jste vytvořili:</span><span class="sxs-lookup"><span data-stu-id="78e6f-266">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="78e6f-267">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="78e6f-267">In the following example:</span></span>

   * <span data-ttu-id="78e6f-268">Místní IP adresa serveru je `10.0.0.4` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-268">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="78e6f-269">Generátor náhodných identifikátorů GUID online poskytuje `appid` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-269">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="78e6f-270">Když je certifikát zaregistrován, nástroj odpoví `SSL Certificate successfully added` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-270">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="78e6f-271">K odstranění registrace certifikátu použijte `delete sslcert` příkaz:</span><span class="sxs-lookup"><span data-stu-id="78e6f-271">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="78e6f-272">Referenční dokumentace pro *netsh.exe*:</span><span class="sxs-lookup"><span data-stu-id="78e6f-272">Reference documentation for *netsh.exe*:</span></span>

   * [<span data-ttu-id="78e6f-273">Příkazy Netsh pro protokol HTTP (Hypertext Transfer Protocol)</span><span class="sxs-lookup"><span data-stu-id="78e6f-273">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](https://technet.microsoft.com/library/cc725882.aspx)
   * [<span data-ttu-id="78e6f-274">UrlPrefix řetězce</span><span class="sxs-lookup"><span data-stu-id="78e6f-274">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="78e6f-275">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="78e6f-275">Run the app.</span></span>

   <span data-ttu-id="78e6f-276">Oprávnění správce nejsou nutná ke spuštění aplikace při vytváření vazby na localhost pomocí protokolu HTTP (ne HTTPS) s číslem portu větším než 1024.</span><span class="sxs-lookup"><span data-stu-id="78e6f-276">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="78e6f-277">U ostatních konfigurací (například pomocí místní IP adresy nebo vazby na port 443) spusťte aplikaci s oprávněními správce.</span><span class="sxs-lookup"><span data-stu-id="78e6f-277">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="78e6f-278">Aplikace odpoví na veřejnou IP adresu serveru.</span><span class="sxs-lookup"><span data-stu-id="78e6f-278">The app responds at the server's public IP address.</span></span> <span data-ttu-id="78e6f-279">V tomto příkladu je server dosažitelný z Internetu na jeho veřejné IP adrese `104.214.79.47` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-279">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="78e6f-280">V tomto příkladu se používá vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="78e6f-280">A development certificate is used in this example.</span></span> <span data-ttu-id="78e6f-281">Stránka se načte bezpečně po obejít upozornění na nedůvěryhodný certifikát v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="78e6f-281">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![Okno prohlížeče zobrazující načtenou stránku indexu aplikace](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="78e6f-283">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="78e6f-283">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="78e6f-284">Pro aplikace hostované HTTP.sys, které komunikují s požadavky z Internetu nebo podnikové sítě, se může při hostování za proxy servery a nástroji pro vyrovnávání zatížení vyžadovat další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="78e6f-284">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="78e6f-285">Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="78e6f-285">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78e6f-286">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="78e6f-286">Additional resources</span></span>

* [<span data-ttu-id="78e6f-287">Povolit ověřování systému Windows pomocí HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="78e6f-287">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="78e6f-288">Rozhraní API serveru HTTP</span><span class="sxs-lookup"><span data-stu-id="78e6f-288">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="78e6f-289">úložiště GitHubu ASPNET/HttpSysServer (zdrojový kód)</span><span class="sxs-lookup"><span data-stu-id="78e6f-289">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="78e6f-290">Hostitel</span><span class="sxs-lookup"><span data-stu-id="78e6f-290">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="78e6f-291">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) je [webový server pro ASP.NET Core](xref:fundamentals/servers/index) , který běží jenom ve Windows.</span><span class="sxs-lookup"><span data-stu-id="78e6f-291">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="78e6f-292">HTTP.sys je alternativou k serveru [Kestrel](xref:fundamentals/servers/kestrel) a nabízí některé funkce, které Kestrel neposkytují.</span><span class="sxs-lookup"><span data-stu-id="78e6f-292">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="78e6f-293">HTTP.sys není kompatibilní s [modulem ASP.NET Core](xref:host-and-deploy/aspnet-core-module) a nedá se použít se službou IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="78e6f-293">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="78e6f-294">HTTP.sys podporuje následující funkce:</span><span class="sxs-lookup"><span data-stu-id="78e6f-294">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="78e6f-295">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="78e6f-295">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="78e6f-296">Sdílení portů</span><span class="sxs-lookup"><span data-stu-id="78e6f-296">Port sharing</span></span>
* <span data-ttu-id="78e6f-297">HTTPS s SNI</span><span class="sxs-lookup"><span data-stu-id="78e6f-297">HTTPS with SNI</span></span>
* <span data-ttu-id="78e6f-298">HTTP/2 přes TLS (Windows 10 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="78e6f-298">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="78e6f-299">Přímý přenos souborů</span><span class="sxs-lookup"><span data-stu-id="78e6f-299">Direct file transmission</span></span>
* <span data-ttu-id="78e6f-300">Ukládání odpovědí do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="78e6f-300">Response caching</span></span>
* <span data-ttu-id="78e6f-301">WebSockets (Windows 8 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="78e6f-301">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="78e6f-302">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="78e6f-302">Supported Windows versions:</span></span>

* <span data-ttu-id="78e6f-303">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="78e6f-303">Windows 7 or later</span></span>
* <span data-ttu-id="78e6f-304">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="78e6f-304">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="78e6f-305">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="78e6f-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="78e6f-306">Kdy použít HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="78e6f-306">When to use HTTP.sys</span></span>

<span data-ttu-id="78e6f-307">HTTP.sys je užitečná pro nasazení, kde:</span><span class="sxs-lookup"><span data-stu-id="78e6f-307">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="78e6f-308">Server je potřeba zveřejnit přímo na internetu bez použití IIS.</span><span class="sxs-lookup"><span data-stu-id="78e6f-308">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys komunikuje přímo s internetem.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="78e6f-310">Interní nasazení vyžaduje funkci, která není v Kestrel k dispozici, jako je například [ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="78e6f-310">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys komunikuje přímo s interní sítí.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="78e6f-312">HTTP.sys je Vyspělá technologie, která chrání před mnoha typy útoků a poskytuje odolnost, zabezpečení a škálovatelnost plně funkčního webového serveru.</span><span class="sxs-lookup"><span data-stu-id="78e6f-312">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="78e6f-313">Služba IIS sám spouští jako naslouchací proces HTTP nad HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="78e6f-313">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="78e6f-314">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="78e6f-314">HTTP/2 support</span></span>

<span data-ttu-id="78e6f-315">[Protokol HTTP/2](https://httpwg.org/specs/rfc7540.html) je povolený pro aplikace ASP.NET Core, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="78e6f-315">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="78e6f-316">Windows Server 2016/Windows 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="78e6f-316">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="78e6f-317">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="78e6f-317">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="78e6f-318">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="78e6f-318">TLS 1.2 or later connection</span></span>

<span data-ttu-id="78e6f-319">Pokud je vytvořeno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-319">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="78e6f-320">HTTP/2 je ve výchozím nastavení povolené.</span><span class="sxs-lookup"><span data-stu-id="78e6f-320">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="78e6f-321">Pokud připojení HTTP/2 není navázáno, připojení se vrátí k HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="78e6f-321">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="78e6f-322">V budoucí verzi Windows budou k dispozici příznaky konfigurace protokolu HTTP/2, včetně možnosti zakázat protokol HTTP/2 s HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="78e6f-322">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="78e6f-323">Ověřování v režimu jádra pomocí protokolu Kerberos</span><span class="sxs-lookup"><span data-stu-id="78e6f-323">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="78e6f-324">HTTP.sys deleguje ověřování režimu jádra pomocí ověřovacího protokolu Kerberos.</span><span class="sxs-lookup"><span data-stu-id="78e6f-324">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="78e6f-325">Ověřování pomocí uživatelského režimu není pro protokol Kerberos a HTTP.sys podporováno.</span><span class="sxs-lookup"><span data-stu-id="78e6f-325">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="78e6f-326">Účet počítače se musí použít k dešifrování tokenu nebo lístku protokolu Kerberos, který je získaný ze služby Active Directory a který přesměruje klient na server za účelem ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="78e6f-326">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="78e6f-327">Zaregistrujte hlavní název služby (SPN) pro hostitele, nikoli uživatele aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e6f-327">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="78e6f-328">Jak používat HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="78e6f-328">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="78e6f-329">Konfigurace aplikace ASP.NET Core pro použití HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="78e6f-329">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="78e6f-330"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>Při sestavování hostitele zavolejte metodu rozšíření a určete požadované <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> .</span><span class="sxs-lookup"><span data-stu-id="78e6f-330">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="78e6f-331">Následující příklad nastaví možnosti na jejich výchozí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="78e6f-331">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="78e6f-332">Další konfigurace HTTP.sys se zpracovává prostřednictvím [nastavení registru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span><span class="sxs-lookup"><span data-stu-id="78e6f-332">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="78e6f-333">\*\* MožnostiHTTP.sys\*\*</span><span class="sxs-lookup"><span data-stu-id="78e6f-333">**HTTP.sys options**</span></span>

| <span data-ttu-id="78e6f-334">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="78e6f-334">Property</span></span> | <span data-ttu-id="78e6f-335">Popis</span><span class="sxs-lookup"><span data-stu-id="78e6f-335">Description</span></span> | <span data-ttu-id="78e6f-336">Výchozí</span><span class="sxs-lookup"><span data-stu-id="78e6f-336">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="78e6f-337">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="78e6f-337">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="78e6f-338">Určuje, zda je povolen synchronní vstup/výstup pro `HttpContext.Request.Body` a `HttpContext.Response.Body` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-338">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="78e6f-339">Ověřování. AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="78e6f-339">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="78e6f-340">Povoluje anonymní požadavky.</span><span class="sxs-lookup"><span data-stu-id="78e6f-340">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="78e6f-341">Ověřování. schémata</span><span class="sxs-lookup"><span data-stu-id="78e6f-341">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="78e6f-342">Určete povolená schémata ověřování.</span><span class="sxs-lookup"><span data-stu-id="78e6f-342">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="78e6f-343">Může být kdykoli změněno před vyřazením naslouchacího procesu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-343">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="78e6f-344">Hodnoty jsou k dispozici ve [výčtu schémata AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic` , `Kerberos` , `Negotiate` , `None` a `NTLM` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-344">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="78e6f-345">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="78e6f-345">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="78e6f-346">Pokusit se o ukládání do mezipaměti v [režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) pro odpovědi s oprávněnými záhlavími.</span><span class="sxs-lookup"><span data-stu-id="78e6f-346">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="78e6f-347">Odpověď nesmí obsahovat `Set-Cookie` `Vary` hlavičky, ani `Pragma` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-347">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="78e6f-348">Musí obsahovat `Cache-Control` hlavičku, která je `public` a buď `shared-max-age` `max-age` hodnota nebo, nebo `Expires` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="78e6f-348">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="78e6f-349">Maximální počet souběžných přijetí.</span><span class="sxs-lookup"><span data-stu-id="78e6f-349">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="78e6f-350">5 &times; [prostředí. <br> ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="78e6f-350">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="78e6f-351">Maximální počet souběžných připojení, která se mají přijmout</span><span class="sxs-lookup"><span data-stu-id="78e6f-351">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="78e6f-352">Použijte `-1` pro nekonečné.</span><span class="sxs-lookup"><span data-stu-id="78e6f-352">Use `-1` for infinite.</span></span> <span data-ttu-id="78e6f-353">Použijte `null` k použití nastavení pro počítač v rámci registru.</span><span class="sxs-lookup"><span data-stu-id="78e6f-353">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="78e6f-354">(počítačově v šířce</span><span class="sxs-lookup"><span data-stu-id="78e6f-354">(machine-wide</span></span><br><span data-ttu-id="78e6f-355">nastavením</span><span class="sxs-lookup"><span data-stu-id="78e6f-355">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="78e6f-356">Viz část <a href="#maxrequestbodysize">MaxRequestBodySize</a> .</span><span class="sxs-lookup"><span data-stu-id="78e6f-356">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="78e6f-357">30000000 bajtů</span><span class="sxs-lookup"><span data-stu-id="78e6f-357">30000000 bytes</span></span><br><span data-ttu-id="78e6f-358">(~ 28,6 MB)</span><span class="sxs-lookup"><span data-stu-id="78e6f-358">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="78e6f-359">Maximální počet požadavků, které lze zařadit do fronty.</span><span class="sxs-lookup"><span data-stu-id="78e6f-359">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="78e6f-360">1000</span><span class="sxs-lookup"><span data-stu-id="78e6f-360">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="78e6f-361">Určuje, jestli tělo odpovědi zapisuje, které selhalo kvůli odpojení klienta, by mělo vyvolat výjimky nebo dokončit normálně.</span><span class="sxs-lookup"><span data-stu-id="78e6f-361">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="78e6f-362">(normálně dokončit)</span><span class="sxs-lookup"><span data-stu-id="78e6f-362">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="78e6f-363">Vystavte <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> konfiguraci HTTP.sys, která může být v registru nakonfigurovaná taky.</span><span class="sxs-lookup"><span data-stu-id="78e6f-363">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="78e6f-364">Pomocí odkazů rozhraní API získáte další informace o jednotlivých nastaveních, včetně výchozích hodnot:</span><span class="sxs-lookup"><span data-stu-id="78e6f-364">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="78e6f-365">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): čas povolený pro rozhraní API serveru http, který má vyprázdnit tělo entity u připojení Keep-Alive.</span><span class="sxs-lookup"><span data-stu-id="78e6f-365">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="78e6f-366">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): čas povolený pro doručení těla entity požadavku.</span><span class="sxs-lookup"><span data-stu-id="78e6f-366">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="78e6f-367">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): čas povolený pro rozhraní API serveru HTTP k analýze hlavičky žádosti.</span><span class="sxs-lookup"><span data-stu-id="78e6f-367">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="78e6f-368">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): čas povolený pro nečinné připojení.</span><span class="sxs-lookup"><span data-stu-id="78e6f-368">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="78e6f-369">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): minimální rychlost odesílání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="78e6f-369">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="78e6f-370">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): čas, který může požadavek zůstat ve frontě požadavků předtím, než ji aplikace vybere.</span><span class="sxs-lookup"><span data-stu-id="78e6f-370">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="78e6f-371">Zadejte, kam se <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> má zaregistrovat HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="78e6f-371">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="78e6f-372">Nejužitečnější je [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), který se používá k přidání předpony do kolekce.</span><span class="sxs-lookup"><span data-stu-id="78e6f-372">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="78e6f-373">Ty mohou být kdykoliv změněny předtím, než se naslouchací proces vyřazuje.</span><span class="sxs-lookup"><span data-stu-id="78e6f-373">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="78e6f-374">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="78e6f-374">**MaxRequestBodySize**</span></span>

<span data-ttu-id="78e6f-375">Maximální povolená velikost libovolného textu žádosti v bajtech</span><span class="sxs-lookup"><span data-stu-id="78e6f-375">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="78e6f-376">Při nastavení na `null` je maximální velikost textu požadavku neomezená.</span><span class="sxs-lookup"><span data-stu-id="78e6f-376">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="78e6f-377">Toto omezení nemá žádný vliv na upgradovaná připojení, která jsou vždycky neomezená.</span><span class="sxs-lookup"><span data-stu-id="78e6f-377">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="78e6f-378">Doporučená metoda pro přepsání limitu v aplikaci ASP.NET Core MVC pro jeden `IActionResult` je použít <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atribut pro metodu akce:</span><span class="sxs-lookup"><span data-stu-id="78e6f-378">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="78e6f-379">Výjimka je vyvolána, pokud se aplikace pokusí nakonfigurovat limit žádosti poté, co aplikace začne číst požadavek.</span><span class="sxs-lookup"><span data-stu-id="78e6f-379">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="78e6f-380">`IsReadOnly`Vlastnost může být použita k označení `MaxRequestBodySize` , zda je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-380">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="78e6f-381">Pokud by měla aplikace přepsat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> jednotlivé požadavky, použijte <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :</span><span class="sxs-lookup"><span data-stu-id="78e6f-381">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="78e6f-382">Pokud používáte Visual Studio, ujistěte se, že aplikace není nakonfigurovaná tak, aby spouštěla službu IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="78e6f-382">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="78e6f-383">V aplikaci Visual Studio je výchozím spouštěcím profilem IIS Express.</span><span class="sxs-lookup"><span data-stu-id="78e6f-383">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="78e6f-384">Chcete-li spustit projekt jako konzolovou aplikaci, ručně změňte vybraný profil, jak je znázorněno na následujícím snímku obrazovky:</span><span class="sxs-lookup"><span data-stu-id="78e6f-384">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![Vybrat profil aplikace konzoly](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="78e6f-386">Konfigurace Windows serveru</span><span class="sxs-lookup"><span data-stu-id="78e6f-386">Configure Windows Server</span></span>

1. <span data-ttu-id="78e6f-387">Určete porty, které se mají otevřít pro aplikaci, a pomocí [brány Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) nebo rutiny [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) prostředí PowerShell otevřete porty brány firewall, abyste umožnili provozu HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="78e6f-387">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="78e6f-388">V následujících příkazech a konfiguraci aplikace se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="78e6f-388">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="78e6f-389">Při nasazování na virtuální počítač Azure otevřete porty ve [skupině zabezpečení sítě](/azure/virtual-machines/windows/nsg-quickstart-portal).</span><span class="sxs-lookup"><span data-stu-id="78e6f-389">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="78e6f-390">V následujících příkazech a konfiguraci aplikace se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="78e6f-390">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="78e6f-391">V případě potřeby Získejte a nainstalujte certifikáty X. 509.</span><span class="sxs-lookup"><span data-stu-id="78e6f-391">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="78e6f-392">V systému Windows vytvořte pomocí [rutiny prostředí PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate)certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="78e6f-392">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="78e6f-393">Nepodporovaný příklad naleznete v tématu [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span><span class="sxs-lookup"><span data-stu-id="78e6f-393">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="78e6f-394">Nainstalujte certifikáty podepsané svým držitelem nebo certifikátem podepsaným certifikační autoritou do osobního úložiště **místního počítače** serveru > **Personal** .</span><span class="sxs-lookup"><span data-stu-id="78e6f-394">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="78e6f-395">Pokud se jedná o [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd), nainstalujte rozhraní .NET core, .NET Framework nebo obojí (Pokud je aplikace aplikace .NET Core cílící na .NET Framework).</span><span class="sxs-lookup"><span data-stu-id="78e6f-395">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="78e6f-396">**.NET Core**: Pokud aplikace vyžaduje .NET Core, Získejte a spusťte instalační program **modulu runtime .NET Core** ze [souborů ke stažení pro .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="78e6f-396">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="78e6f-397">Neinstalujte na server plnou sadu SDK.</span><span class="sxs-lookup"><span data-stu-id="78e6f-397">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="78e6f-398">**.NET Framework**: Pokud aplikace vyžaduje .NET Framework, přečtěte si téma [Průvodce instalací .NET Framework](/dotnet/framework/install/).</span><span class="sxs-lookup"><span data-stu-id="78e6f-398">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="78e6f-399">Nainstalujte požadovanou .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="78e6f-399">Install the required .NET Framework.</span></span> <span data-ttu-id="78e6f-400">Instalační program pro nejnovější .NET Framework je k dispozici na stránce [soubory ke stažení pro .NET Core](https://dotnet.microsoft.com/download) .</span><span class="sxs-lookup"><span data-stu-id="78e6f-400">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="78e6f-401">Pokud je aplikace samostatným [nasazením](/dotnet/core/deploying/#self-contained-deployments-scd), aplikace zahrne modul runtime do svého nasazení.</span><span class="sxs-lookup"><span data-stu-id="78e6f-401">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="78e6f-402">Na serveru není nutná žádná instalace rozhraní.</span><span class="sxs-lookup"><span data-stu-id="78e6f-402">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="78e6f-403">Konfigurace adres URL a portů v aplikaci</span><span class="sxs-lookup"><span data-stu-id="78e6f-403">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="78e6f-404">Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-404">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="78e6f-405">Pokud chcete nakonfigurovat předpony a porty adresy URL, můžete použít tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="78e6f-405">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="78e6f-406">`urls` argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="78e6f-406">`urls` command-line argument</span></span>
   * <span data-ttu-id="78e6f-407">`ASPNETCORE_URLS` Proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="78e6f-407">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="78e6f-408">Následující příklad kódu ukazuje, jak používat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> s místní IP adresou serveru `10.0.0.4` na portu 443:</span><span class="sxs-lookup"><span data-stu-id="78e6f-408">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="78e6f-409">Výhodou `UrlPrefixes` je, že se okamžitě generuje chybová zpráva pro nesprávně naformátované předpony.</span><span class="sxs-lookup"><span data-stu-id="78e6f-409">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="78e6f-410">Nastavení v `UrlPrefixes` `UseUrls` / `urls` / `ASPNETCORE_URLS` Nastavení přepsání</span><span class="sxs-lookup"><span data-stu-id="78e6f-410">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="78e6f-411">Proto je výhodou `UseUrls` `urls` `ASPNETCORE_URLS` proměnné prostředí, a jednodušší přepínání mezi Kestrel a HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="78e6f-411">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="78e6f-412">HTTP.sys používá [formáty řetězce UrlPrefix HTTP serveru API](/windows/win32/http/urlprefix-strings).</span><span class="sxs-lookup"><span data-stu-id="78e6f-412">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="78e6f-413">Nelze použít vazby zástupných znaků na nejvyšší úrovni ( `http://*:80/` a `http://+:80` ). **not**</span><span class="sxs-lookup"><span data-stu-id="78e6f-413">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="78e6f-414">Vazby na zástupné znaky na nejvyšší úrovni vytvoří chyby zabezpečení aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e6f-414">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="78e6f-415">To platí pro silné i slabé zástupné znaky.</span><span class="sxs-lookup"><span data-stu-id="78e6f-415">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="78e6f-416">Místo zástupných znaků použijte explicitní názvy hostitelů nebo IP adresy.</span><span class="sxs-lookup"><span data-stu-id="78e6f-416">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="78e6f-417">Vazba zástupných znaků subdomény (například `*.mysub.com` ) není bezpečnostní riziko, pokud ovládáte celou nadřazenou doménu (na rozdíl od `*.com` , která je zranitelná).</span><span class="sxs-lookup"><span data-stu-id="78e6f-417">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="78e6f-418">Další informace najdete v [dokumentu RFC 7230: oddíl 5,4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="78e6f-418">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="78e6f-419">Předregistrujte předpony adresy URL na serveru.</span><span class="sxs-lookup"><span data-stu-id="78e6f-419">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="78e6f-420">K \* disnetsh.exe\*je integrovaný nástroj pro konfiguraci HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="78e6f-420">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="78e6f-421">*netsh.exe* se používá k rezervaci předpon adres URL a přiřazování certifikátů X. 509.</span><span class="sxs-lookup"><span data-stu-id="78e6f-421">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="78e6f-422">Nástroj vyžaduje oprávnění správce.</span><span class="sxs-lookup"><span data-stu-id="78e6f-422">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="78e6f-423">K registraci adres URL aplikace použijte nástroj *netsh.exe* :</span><span class="sxs-lookup"><span data-stu-id="78e6f-423">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="78e6f-424">`<URL>`: Plně kvalifikované adresy URL (Uniform Resource Locator).</span><span class="sxs-lookup"><span data-stu-id="78e6f-424">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="78e6f-425">Nepoužívejte vazbu zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="78e6f-425">Don't use a wildcard binding.</span></span> <span data-ttu-id="78e6f-426">Použijte platný název hostitele nebo místní IP adresu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-426">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="78e6f-427">*Adresa URL musí obsahovat koncové lomítko.*</span><span class="sxs-lookup"><span data-stu-id="78e6f-427">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="78e6f-428">`<USER>`: Určuje název uživatele nebo skupiny uživatelů.</span><span class="sxs-lookup"><span data-stu-id="78e6f-428">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="78e6f-429">V následujícím příkladu je místní IP adresa serveru `10.0.0.4` :</span><span class="sxs-lookup"><span data-stu-id="78e6f-429">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="78e6f-430">Když je adresa URL registrována, nástroj odpoví `URL reservation successfully added` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-430">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="78e6f-431">Pokud chcete odstranit registrovanou adresu URL, použijte `delete urlacl` příkaz:</span><span class="sxs-lookup"><span data-stu-id="78e6f-431">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="78e6f-432">Zaregistrujte certifikáty X. 509 na serveru.</span><span class="sxs-lookup"><span data-stu-id="78e6f-432">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="78e6f-433">K registraci certifikátů pro aplikaci použijte nástroj *netsh.exe* :</span><span class="sxs-lookup"><span data-stu-id="78e6f-433">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="78e6f-434">`<IP>`: Určuje místní IP adresu pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-434">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="78e6f-435">Nepoužívejte vazbu zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="78e6f-435">Don't use a wildcard binding.</span></span> <span data-ttu-id="78e6f-436">Použijte platnou IP adresu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-436">Use a valid IP address.</span></span>
   * <span data-ttu-id="78e6f-437">`<PORT>`: Určuje port pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-437">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="78e6f-438">`<THUMBPRINT>`: Kryptografický otisk certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="78e6f-438">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="78e6f-439">`<GUID>`: Identifikátor GUID generovaný vývojářem, který představuje aplikaci pro informativní účely.</span><span class="sxs-lookup"><span data-stu-id="78e6f-439">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="78e6f-440">Pro referenční účely uložte identifikátor GUID do aplikace jako značku balíčku:</span><span class="sxs-lookup"><span data-stu-id="78e6f-440">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="78e6f-441">V aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="78e6f-441">In Visual Studio:</span></span>
     * <span data-ttu-id="78e6f-442">Otevřete vlastnosti projektu aplikace tak, že kliknete pravým tlačítkem na aplikaci v **Průzkumník řešení** a vyberete **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="78e6f-442">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="78e6f-443">Vyberte kartu **balíček** .</span><span class="sxs-lookup"><span data-stu-id="78e6f-443">Select the **Package** tab.</span></span>
     * <span data-ttu-id="78e6f-444">Zadejte identifikátor GUID, který jste vytvořili v poli **značky** .</span><span class="sxs-lookup"><span data-stu-id="78e6f-444">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="78e6f-445">Když nepoužíváte Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="78e6f-445">When not using Visual Studio:</span></span>
     * <span data-ttu-id="78e6f-446">Otevřete soubor projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e6f-446">Open the app's project file.</span></span>
     * <span data-ttu-id="78e6f-447">Přidejte `<PackageTags>` vlastnost do nového nebo existujícího `<PropertyGroup>` s identifikátorem GUID, který jste vytvořili:</span><span class="sxs-lookup"><span data-stu-id="78e6f-447">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="78e6f-448">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="78e6f-448">In the following example:</span></span>

   * <span data-ttu-id="78e6f-449">Místní IP adresa serveru je `10.0.0.4` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-449">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="78e6f-450">Generátor náhodných identifikátorů GUID online poskytuje `appid` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-450">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="78e6f-451">Když je certifikát zaregistrován, nástroj odpoví `SSL Certificate successfully added` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-451">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="78e6f-452">K odstranění registrace certifikátu použijte `delete sslcert` příkaz:</span><span class="sxs-lookup"><span data-stu-id="78e6f-452">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="78e6f-453">Referenční dokumentace pro *netsh.exe*:</span><span class="sxs-lookup"><span data-stu-id="78e6f-453">Reference documentation for *netsh.exe*:</span></span>

   * [<span data-ttu-id="78e6f-454">Příkazy Netsh pro protokol HTTP (Hypertext Transfer Protocol)</span><span class="sxs-lookup"><span data-stu-id="78e6f-454">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](https://technet.microsoft.com/library/cc725882.aspx)
   * [<span data-ttu-id="78e6f-455">UrlPrefix řetězce</span><span class="sxs-lookup"><span data-stu-id="78e6f-455">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="78e6f-456">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="78e6f-456">Run the app.</span></span>

   <span data-ttu-id="78e6f-457">Oprávnění správce nejsou nutná ke spuštění aplikace při vytváření vazby na localhost pomocí protokolu HTTP (ne HTTPS) s číslem portu větším než 1024.</span><span class="sxs-lookup"><span data-stu-id="78e6f-457">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="78e6f-458">U ostatních konfigurací (například pomocí místní IP adresy nebo vazby na port 443) spusťte aplikaci s oprávněními správce.</span><span class="sxs-lookup"><span data-stu-id="78e6f-458">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="78e6f-459">Aplikace odpoví na veřejnou IP adresu serveru.</span><span class="sxs-lookup"><span data-stu-id="78e6f-459">The app responds at the server's public IP address.</span></span> <span data-ttu-id="78e6f-460">V tomto příkladu je server dosažitelný z Internetu na jeho veřejné IP adrese `104.214.79.47` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-460">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="78e6f-461">V tomto příkladu se používá vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="78e6f-461">A development certificate is used in this example.</span></span> <span data-ttu-id="78e6f-462">Stránka se načte bezpečně po obejít upozornění na nedůvěryhodný certifikát v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="78e6f-462">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![Okno prohlížeče zobrazující načtenou stránku indexu aplikace](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="78e6f-464">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="78e6f-464">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="78e6f-465">Pro aplikace hostované HTTP.sys, které komunikují s požadavky z Internetu nebo podnikové sítě, se může při hostování za proxy servery a nástroji pro vyrovnávání zatížení vyžadovat další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="78e6f-465">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="78e6f-466">Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="78e6f-466">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78e6f-467">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="78e6f-467">Additional resources</span></span>

* [<span data-ttu-id="78e6f-468">Povolit ověřování systému Windows pomocí HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="78e6f-468">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="78e6f-469">Rozhraní API serveru HTTP</span><span class="sxs-lookup"><span data-stu-id="78e6f-469">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="78e6f-470">úložiště GitHubu ASPNET/HttpSysServer (zdrojový kód)</span><span class="sxs-lookup"><span data-stu-id="78e6f-470">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="78e6f-471">Hostitel</span><span class="sxs-lookup"><span data-stu-id="78e6f-471">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="78e6f-472">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) je [webový server pro ASP.NET Core](xref:fundamentals/servers/index) , který běží jenom ve Windows.</span><span class="sxs-lookup"><span data-stu-id="78e6f-472">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="78e6f-473">HTTP.sys je alternativou k serveru [Kestrel](xref:fundamentals/servers/kestrel) a nabízí některé funkce, které Kestrel neposkytují.</span><span class="sxs-lookup"><span data-stu-id="78e6f-473">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="78e6f-474">HTTP.sys není kompatibilní s [modulem ASP.NET Core](xref:host-and-deploy/aspnet-core-module) a nedá se použít se službou IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="78e6f-474">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="78e6f-475">HTTP.sys podporuje následující funkce:</span><span class="sxs-lookup"><span data-stu-id="78e6f-475">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="78e6f-476">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="78e6f-476">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="78e6f-477">Sdílení portů</span><span class="sxs-lookup"><span data-stu-id="78e6f-477">Port sharing</span></span>
* <span data-ttu-id="78e6f-478">HTTPS s SNI</span><span class="sxs-lookup"><span data-stu-id="78e6f-478">HTTPS with SNI</span></span>
* <span data-ttu-id="78e6f-479">HTTP/2 přes TLS (Windows 10 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="78e6f-479">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="78e6f-480">Přímý přenos souborů</span><span class="sxs-lookup"><span data-stu-id="78e6f-480">Direct file transmission</span></span>
* <span data-ttu-id="78e6f-481">Ukládání odpovědí do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="78e6f-481">Response caching</span></span>
* <span data-ttu-id="78e6f-482">WebSockets (Windows 8 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="78e6f-482">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="78e6f-483">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="78e6f-483">Supported Windows versions:</span></span>

* <span data-ttu-id="78e6f-484">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="78e6f-484">Windows 7 or later</span></span>
* <span data-ttu-id="78e6f-485">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="78e6f-485">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="78e6f-486">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="78e6f-486">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="78e6f-487">Kdy použít HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="78e6f-487">When to use HTTP.sys</span></span>

<span data-ttu-id="78e6f-488">HTTP.sys je užitečná pro nasazení, kde:</span><span class="sxs-lookup"><span data-stu-id="78e6f-488">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="78e6f-489">Server je potřeba zveřejnit přímo na internetu bez použití IIS.</span><span class="sxs-lookup"><span data-stu-id="78e6f-489">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys komunikuje přímo s internetem.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="78e6f-491">Interní nasazení vyžaduje funkci, která není v Kestrel k dispozici, jako je například [ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="78e6f-491">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys komunikuje přímo s interní sítí.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="78e6f-493">HTTP.sys je Vyspělá technologie, která chrání před mnoha typy útoků a poskytuje odolnost, zabezpečení a škálovatelnost plně funkčního webového serveru.</span><span class="sxs-lookup"><span data-stu-id="78e6f-493">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="78e6f-494">Služba IIS sám spouští jako naslouchací proces HTTP nad HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="78e6f-494">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="78e6f-495">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="78e6f-495">HTTP/2 support</span></span>

<span data-ttu-id="78e6f-496">[Protokol HTTP/2](https://httpwg.org/specs/rfc7540.html) je povolený pro aplikace ASP.NET Core, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="78e6f-496">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="78e6f-497">Windows Server 2016/Windows 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="78e6f-497">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="78e6f-498">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="78e6f-498">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="78e6f-499">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="78e6f-499">TLS 1.2 or later connection</span></span>

<span data-ttu-id="78e6f-500">Pokud je vytvořeno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-500">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="78e6f-501">HTTP/2 je ve výchozím nastavení povolené.</span><span class="sxs-lookup"><span data-stu-id="78e6f-501">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="78e6f-502">Pokud připojení HTTP/2 není navázáno, připojení se vrátí k HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="78e6f-502">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="78e6f-503">V budoucí verzi Windows budou k dispozici příznaky konfigurace protokolu HTTP/2, včetně možnosti zakázat protokol HTTP/2 s HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="78e6f-503">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="78e6f-504">Ověřování v režimu jádra pomocí protokolu Kerberos</span><span class="sxs-lookup"><span data-stu-id="78e6f-504">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="78e6f-505">HTTP.sys deleguje ověřování režimu jádra pomocí ověřovacího protokolu Kerberos.</span><span class="sxs-lookup"><span data-stu-id="78e6f-505">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="78e6f-506">Ověřování pomocí uživatelského režimu není pro protokol Kerberos a HTTP.sys podporováno.</span><span class="sxs-lookup"><span data-stu-id="78e6f-506">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="78e6f-507">Účet počítače se musí použít k dešifrování tokenu nebo lístku protokolu Kerberos, který je získaný ze služby Active Directory a který přesměruje klient na server za účelem ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="78e6f-507">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="78e6f-508">Zaregistrujte hlavní název služby (SPN) pro hostitele, nikoli uživatele aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e6f-508">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="78e6f-509">Jak používat HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="78e6f-509">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="78e6f-510">Konfigurace aplikace ASP.NET Core pro použití HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="78e6f-510">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="78e6f-511">Odkaz na balíček v souboru projektu není vyžadován při použití [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) ([NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span><span class="sxs-lookup"><span data-stu-id="78e6f-511">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="78e6f-512">Pokud nepoužíváte `Microsoft.AspNetCore.App` Metapackage, přidejte odkaz na balíček do [Microsoft. AspNetCore. Server. HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span><span class="sxs-lookup"><span data-stu-id="78e6f-512">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="78e6f-513"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>Při sestavování hostitele zavolejte metodu rozšíření a určete požadované <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> .</span><span class="sxs-lookup"><span data-stu-id="78e6f-513">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="78e6f-514">Následující příklad nastaví možnosti na jejich výchozí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="78e6f-514">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="78e6f-515">Další konfigurace HTTP.sys se zpracovává prostřednictvím [nastavení registru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span><span class="sxs-lookup"><span data-stu-id="78e6f-515">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="78e6f-516">\*\* MožnostiHTTP.sys\*\*</span><span class="sxs-lookup"><span data-stu-id="78e6f-516">**HTTP.sys options**</span></span>

| <span data-ttu-id="78e6f-517">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="78e6f-517">Property</span></span> | <span data-ttu-id="78e6f-518">Popis</span><span class="sxs-lookup"><span data-stu-id="78e6f-518">Description</span></span> | <span data-ttu-id="78e6f-519">Výchozí</span><span class="sxs-lookup"><span data-stu-id="78e6f-519">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="78e6f-520">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="78e6f-520">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="78e6f-521">Určuje, zda je povolen synchronní vstup/výstup pro `HttpContext.Request.Body` a `HttpContext.Response.Body` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-521">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="78e6f-522">Ověřování. AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="78e6f-522">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="78e6f-523">Povoluje anonymní požadavky.</span><span class="sxs-lookup"><span data-stu-id="78e6f-523">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="78e6f-524">Ověřování. schémata</span><span class="sxs-lookup"><span data-stu-id="78e6f-524">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="78e6f-525">Určete povolená schémata ověřování.</span><span class="sxs-lookup"><span data-stu-id="78e6f-525">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="78e6f-526">Může být kdykoli změněno před vyřazením naslouchacího procesu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-526">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="78e6f-527">Hodnoty jsou k dispozici ve [výčtu schémata AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic` , `Kerberos` , `Negotiate` , `None` a `NTLM` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-527">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="78e6f-528">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="78e6f-528">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="78e6f-529">Pokusit se o ukládání do mezipaměti v [režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) pro odpovědi s oprávněnými záhlavími.</span><span class="sxs-lookup"><span data-stu-id="78e6f-529">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="78e6f-530">Odpověď nesmí obsahovat `Set-Cookie` `Vary` hlavičky, ani `Pragma` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-530">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="78e6f-531">Musí obsahovat `Cache-Control` hlavičku, která je `public` a buď `shared-max-age` `max-age` hodnota nebo, nebo `Expires` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="78e6f-531">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="78e6f-532">Maximální počet souběžných přijetí.</span><span class="sxs-lookup"><span data-stu-id="78e6f-532">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="78e6f-533">5 &times; [prostředí. <br> ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="78e6f-533">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="78e6f-534">Maximální počet souběžných připojení, která se mají přijmout</span><span class="sxs-lookup"><span data-stu-id="78e6f-534">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="78e6f-535">Použijte `-1` pro nekonečné.</span><span class="sxs-lookup"><span data-stu-id="78e6f-535">Use `-1` for infinite.</span></span> <span data-ttu-id="78e6f-536">Použijte `null` k použití nastavení pro počítač v rámci registru.</span><span class="sxs-lookup"><span data-stu-id="78e6f-536">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="78e6f-537">(počítačově v šířce</span><span class="sxs-lookup"><span data-stu-id="78e6f-537">(machine-wide</span></span><br><span data-ttu-id="78e6f-538">nastavením</span><span class="sxs-lookup"><span data-stu-id="78e6f-538">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="78e6f-539">Viz část <a href="#maxrequestbodysize">MaxRequestBodySize</a> .</span><span class="sxs-lookup"><span data-stu-id="78e6f-539">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="78e6f-540">30000000 bajtů</span><span class="sxs-lookup"><span data-stu-id="78e6f-540">30000000 bytes</span></span><br><span data-ttu-id="78e6f-541">(~ 28,6 MB)</span><span class="sxs-lookup"><span data-stu-id="78e6f-541">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="78e6f-542">Maximální počet požadavků, které lze zařadit do fronty.</span><span class="sxs-lookup"><span data-stu-id="78e6f-542">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="78e6f-543">1000</span><span class="sxs-lookup"><span data-stu-id="78e6f-543">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="78e6f-544">Určuje, jestli tělo odpovědi zapisuje, které selhalo kvůli odpojení klienta, by mělo vyvolat výjimky nebo dokončit normálně.</span><span class="sxs-lookup"><span data-stu-id="78e6f-544">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="78e6f-545">(normálně dokončit)</span><span class="sxs-lookup"><span data-stu-id="78e6f-545">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="78e6f-546">Vystavte <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> konfiguraci HTTP.sys, která může být v registru nakonfigurovaná taky.</span><span class="sxs-lookup"><span data-stu-id="78e6f-546">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="78e6f-547">Pomocí odkazů rozhraní API získáte další informace o jednotlivých nastaveních, včetně výchozích hodnot:</span><span class="sxs-lookup"><span data-stu-id="78e6f-547">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="78e6f-548">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): čas povolený pro rozhraní API serveru http, který má vyprázdnit tělo entity u připojení Keep-Alive.</span><span class="sxs-lookup"><span data-stu-id="78e6f-548">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="78e6f-549">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): čas povolený pro doručení těla entity požadavku.</span><span class="sxs-lookup"><span data-stu-id="78e6f-549">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="78e6f-550">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): čas povolený pro rozhraní API serveru HTTP k analýze hlavičky žádosti.</span><span class="sxs-lookup"><span data-stu-id="78e6f-550">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="78e6f-551">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): čas povolený pro nečinné připojení.</span><span class="sxs-lookup"><span data-stu-id="78e6f-551">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="78e6f-552">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): minimální rychlost odesílání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="78e6f-552">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="78e6f-553">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): čas, který může požadavek zůstat ve frontě požadavků předtím, než ji aplikace vybere.</span><span class="sxs-lookup"><span data-stu-id="78e6f-553">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="78e6f-554">Zadejte, kam se <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> má zaregistrovat HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="78e6f-554">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="78e6f-555">Nejužitečnější je [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), který se používá k přidání předpony do kolekce.</span><span class="sxs-lookup"><span data-stu-id="78e6f-555">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="78e6f-556">Ty mohou být kdykoliv změněny předtím, než se naslouchací proces vyřazuje.</span><span class="sxs-lookup"><span data-stu-id="78e6f-556">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="78e6f-557">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="78e6f-557">**MaxRequestBodySize**</span></span>

<span data-ttu-id="78e6f-558">Maximální povolená velikost libovolného textu žádosti v bajtech</span><span class="sxs-lookup"><span data-stu-id="78e6f-558">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="78e6f-559">Při nastavení na `null` je maximální velikost textu požadavku neomezená.</span><span class="sxs-lookup"><span data-stu-id="78e6f-559">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="78e6f-560">Toto omezení nemá žádný vliv na upgradovaná připojení, která jsou vždycky neomezená.</span><span class="sxs-lookup"><span data-stu-id="78e6f-560">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="78e6f-561">Doporučená metoda pro přepsání limitu v aplikaci ASP.NET Core MVC pro jeden `IActionResult` je použít <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atribut pro metodu akce:</span><span class="sxs-lookup"><span data-stu-id="78e6f-561">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="78e6f-562">Výjimka je vyvolána, pokud se aplikace pokusí nakonfigurovat limit žádosti poté, co aplikace začne číst požadavek.</span><span class="sxs-lookup"><span data-stu-id="78e6f-562">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="78e6f-563">`IsReadOnly`Vlastnost může být použita k označení `MaxRequestBodySize` , zda je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-563">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="78e6f-564">Pokud by měla aplikace přepsat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> jednotlivé požadavky, použijte <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :</span><span class="sxs-lookup"><span data-stu-id="78e6f-564">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="78e6f-565">Pokud používáte Visual Studio, ujistěte se, že aplikace není nakonfigurovaná tak, aby spouštěla službu IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="78e6f-565">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="78e6f-566">V aplikaci Visual Studio je výchozím spouštěcím profilem IIS Express.</span><span class="sxs-lookup"><span data-stu-id="78e6f-566">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="78e6f-567">Chcete-li spustit projekt jako konzolovou aplikaci, ručně změňte vybraný profil, jak je znázorněno na následujícím snímku obrazovky:</span><span class="sxs-lookup"><span data-stu-id="78e6f-567">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![Vybrat profil aplikace konzoly](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="78e6f-569">Konfigurace Windows serveru</span><span class="sxs-lookup"><span data-stu-id="78e6f-569">Configure Windows Server</span></span>

1. <span data-ttu-id="78e6f-570">Určete porty, které se mají otevřít pro aplikaci, a pomocí [brány Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) nebo rutiny [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) prostředí PowerShell otevřete porty brány firewall, abyste umožnili provozu HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="78e6f-570">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="78e6f-571">V následujících příkazech a konfiguraci aplikace se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="78e6f-571">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="78e6f-572">Při nasazování na virtuální počítač Azure otevřete porty ve [skupině zabezpečení sítě](/azure/virtual-machines/windows/nsg-quickstart-portal).</span><span class="sxs-lookup"><span data-stu-id="78e6f-572">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="78e6f-573">V následujících příkazech a konfiguraci aplikace se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="78e6f-573">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="78e6f-574">V případě potřeby Získejte a nainstalujte certifikáty X. 509.</span><span class="sxs-lookup"><span data-stu-id="78e6f-574">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="78e6f-575">V systému Windows vytvořte pomocí [rutiny prostředí PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate)certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="78e6f-575">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="78e6f-576">Nepodporovaný příklad naleznete v tématu [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span><span class="sxs-lookup"><span data-stu-id="78e6f-576">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="78e6f-577">Nainstalujte certifikáty podepsané svým držitelem nebo certifikátem podepsaným certifikační autoritou do osobního úložiště **místního počítače** serveru > **Personal** .</span><span class="sxs-lookup"><span data-stu-id="78e6f-577">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="78e6f-578">Pokud se jedná o [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd), nainstalujte rozhraní .NET core, .NET Framework nebo obojí (Pokud je aplikace aplikace .NET Core cílící na .NET Framework).</span><span class="sxs-lookup"><span data-stu-id="78e6f-578">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="78e6f-579">**.NET Core**: Pokud aplikace vyžaduje .NET Core, Získejte a spusťte instalační program **modulu runtime .NET Core** ze [souborů ke stažení pro .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="78e6f-579">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="78e6f-580">Neinstalujte na server plnou sadu SDK.</span><span class="sxs-lookup"><span data-stu-id="78e6f-580">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="78e6f-581">**.NET Framework**: Pokud aplikace vyžaduje .NET Framework, přečtěte si téma [Průvodce instalací .NET Framework](/dotnet/framework/install/).</span><span class="sxs-lookup"><span data-stu-id="78e6f-581">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="78e6f-582">Nainstalujte požadovanou .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="78e6f-582">Install the required .NET Framework.</span></span> <span data-ttu-id="78e6f-583">Instalační program pro nejnovější .NET Framework je k dispozici na stránce [soubory ke stažení pro .NET Core](https://dotnet.microsoft.com/download) .</span><span class="sxs-lookup"><span data-stu-id="78e6f-583">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="78e6f-584">Pokud je aplikace samostatným [nasazením](/dotnet/core/deploying/#self-contained-deployments-scd), aplikace zahrne modul runtime do svého nasazení.</span><span class="sxs-lookup"><span data-stu-id="78e6f-584">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="78e6f-585">Na serveru není nutná žádná instalace rozhraní.</span><span class="sxs-lookup"><span data-stu-id="78e6f-585">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="78e6f-586">Konfigurace adres URL a portů v aplikaci</span><span class="sxs-lookup"><span data-stu-id="78e6f-586">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="78e6f-587">Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-587">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="78e6f-588">Pokud chcete nakonfigurovat předpony a porty adresy URL, můžete použít tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="78e6f-588">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="78e6f-589">`urls` argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="78e6f-589">`urls` command-line argument</span></span>
   * <span data-ttu-id="78e6f-590">`ASPNETCORE_URLS` Proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="78e6f-590">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="78e6f-591">Následující příklad kódu ukazuje, jak používat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> s místní IP adresou serveru `10.0.0.4` na portu 443:</span><span class="sxs-lookup"><span data-stu-id="78e6f-591">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="78e6f-592">Výhodou `UrlPrefixes` je, že se okamžitě generuje chybová zpráva pro nesprávně naformátované předpony.</span><span class="sxs-lookup"><span data-stu-id="78e6f-592">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="78e6f-593">Nastavení v `UrlPrefixes` `UseUrls` / `urls` / `ASPNETCORE_URLS` Nastavení přepsání</span><span class="sxs-lookup"><span data-stu-id="78e6f-593">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="78e6f-594">Proto je výhodou `UseUrls` `urls` `ASPNETCORE_URLS` proměnné prostředí, a jednodušší přepínání mezi Kestrel a HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="78e6f-594">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="78e6f-595">HTTP.sys používá [formáty řetězce UrlPrefix HTTP serveru API](/windows/win32/http/urlprefix-strings).</span><span class="sxs-lookup"><span data-stu-id="78e6f-595">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="78e6f-596">Nelze použít vazby zástupných znaků na nejvyšší úrovni ( `http://*:80/` a `http://+:80` ). **not**</span><span class="sxs-lookup"><span data-stu-id="78e6f-596">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="78e6f-597">Vazby na zástupné znaky na nejvyšší úrovni vytvoří chyby zabezpečení aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e6f-597">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="78e6f-598">To platí pro silné i slabé zástupné znaky.</span><span class="sxs-lookup"><span data-stu-id="78e6f-598">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="78e6f-599">Místo zástupných znaků použijte explicitní názvy hostitelů nebo IP adresy.</span><span class="sxs-lookup"><span data-stu-id="78e6f-599">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="78e6f-600">Vazba zástupných znaků subdomény (například `*.mysub.com` ) není bezpečnostní riziko, pokud ovládáte celou nadřazenou doménu (na rozdíl od `*.com` , která je zranitelná).</span><span class="sxs-lookup"><span data-stu-id="78e6f-600">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="78e6f-601">Další informace najdete v [dokumentu RFC 7230: oddíl 5,4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="78e6f-601">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="78e6f-602">Předregistrujte předpony adresy URL na serveru.</span><span class="sxs-lookup"><span data-stu-id="78e6f-602">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="78e6f-603">K \* disnetsh.exe\*je integrovaný nástroj pro konfiguraci HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="78e6f-603">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="78e6f-604">*netsh.exe* se používá k rezervaci předpon adres URL a přiřazování certifikátů X. 509.</span><span class="sxs-lookup"><span data-stu-id="78e6f-604">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="78e6f-605">Nástroj vyžaduje oprávnění správce.</span><span class="sxs-lookup"><span data-stu-id="78e6f-605">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="78e6f-606">K registraci adres URL aplikace použijte nástroj *netsh.exe* :</span><span class="sxs-lookup"><span data-stu-id="78e6f-606">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="78e6f-607">`<URL>`: Plně kvalifikované adresy URL (Uniform Resource Locator).</span><span class="sxs-lookup"><span data-stu-id="78e6f-607">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="78e6f-608">Nepoužívejte vazbu zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="78e6f-608">Don't use a wildcard binding.</span></span> <span data-ttu-id="78e6f-609">Použijte platný název hostitele nebo místní IP adresu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-609">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="78e6f-610">*Adresa URL musí obsahovat koncové lomítko.*</span><span class="sxs-lookup"><span data-stu-id="78e6f-610">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="78e6f-611">`<USER>`: Určuje název uživatele nebo skupiny uživatelů.</span><span class="sxs-lookup"><span data-stu-id="78e6f-611">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="78e6f-612">V následujícím příkladu je místní IP adresa serveru `10.0.0.4` :</span><span class="sxs-lookup"><span data-stu-id="78e6f-612">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="78e6f-613">Když je adresa URL registrována, nástroj odpoví `URL reservation successfully added` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-613">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="78e6f-614">Pokud chcete odstranit registrovanou adresu URL, použijte `delete urlacl` příkaz:</span><span class="sxs-lookup"><span data-stu-id="78e6f-614">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="78e6f-615">Zaregistrujte certifikáty X. 509 na serveru.</span><span class="sxs-lookup"><span data-stu-id="78e6f-615">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="78e6f-616">K registraci certifikátů pro aplikaci použijte nástroj *netsh.exe* :</span><span class="sxs-lookup"><span data-stu-id="78e6f-616">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="78e6f-617">`<IP>`: Určuje místní IP adresu pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-617">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="78e6f-618">Nepoužívejte vazbu zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="78e6f-618">Don't use a wildcard binding.</span></span> <span data-ttu-id="78e6f-619">Použijte platnou IP adresu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-619">Use a valid IP address.</span></span>
   * <span data-ttu-id="78e6f-620">`<PORT>`: Určuje port pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-620">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="78e6f-621">`<THUMBPRINT>`: Kryptografický otisk certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="78e6f-621">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="78e6f-622">`<GUID>`: Identifikátor GUID generovaný vývojářem, který představuje aplikaci pro informativní účely.</span><span class="sxs-lookup"><span data-stu-id="78e6f-622">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="78e6f-623">Pro referenční účely uložte identifikátor GUID do aplikace jako značku balíčku:</span><span class="sxs-lookup"><span data-stu-id="78e6f-623">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="78e6f-624">V aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="78e6f-624">In Visual Studio:</span></span>
     * <span data-ttu-id="78e6f-625">Otevřete vlastnosti projektu aplikace tak, že kliknete pravým tlačítkem na aplikaci v **Průzkumník řešení** a vyberete **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="78e6f-625">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="78e6f-626">Vyberte kartu **balíček** .</span><span class="sxs-lookup"><span data-stu-id="78e6f-626">Select the **Package** tab.</span></span>
     * <span data-ttu-id="78e6f-627">Zadejte identifikátor GUID, který jste vytvořili v poli **značky** .</span><span class="sxs-lookup"><span data-stu-id="78e6f-627">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="78e6f-628">Když nepoužíváte Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="78e6f-628">When not using Visual Studio:</span></span>
     * <span data-ttu-id="78e6f-629">Otevřete soubor projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e6f-629">Open the app's project file.</span></span>
     * <span data-ttu-id="78e6f-630">Přidejte `<PackageTags>` vlastnost do nového nebo existujícího `<PropertyGroup>` s identifikátorem GUID, který jste vytvořili:</span><span class="sxs-lookup"><span data-stu-id="78e6f-630">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="78e6f-631">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="78e6f-631">In the following example:</span></span>

   * <span data-ttu-id="78e6f-632">Místní IP adresa serveru je `10.0.0.4` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-632">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="78e6f-633">Generátor náhodných identifikátorů GUID online poskytuje `appid` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-633">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="78e6f-634">Když je certifikát zaregistrován, nástroj odpoví `SSL Certificate successfully added` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-634">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="78e6f-635">K odstranění registrace certifikátu použijte `delete sslcert` příkaz:</span><span class="sxs-lookup"><span data-stu-id="78e6f-635">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="78e6f-636">Referenční dokumentace pro *netsh.exe*:</span><span class="sxs-lookup"><span data-stu-id="78e6f-636">Reference documentation for *netsh.exe*:</span></span>

   * [<span data-ttu-id="78e6f-637">Příkazy Netsh pro protokol HTTP (Hypertext Transfer Protocol)</span><span class="sxs-lookup"><span data-stu-id="78e6f-637">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](https://technet.microsoft.com/library/cc725882.aspx)
   * [<span data-ttu-id="78e6f-638">UrlPrefix řetězce</span><span class="sxs-lookup"><span data-stu-id="78e6f-638">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="78e6f-639">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="78e6f-639">Run the app.</span></span>

   <span data-ttu-id="78e6f-640">Oprávnění správce nejsou nutná ke spuštění aplikace při vytváření vazby na localhost pomocí protokolu HTTP (ne HTTPS) s číslem portu větším než 1024.</span><span class="sxs-lookup"><span data-stu-id="78e6f-640">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="78e6f-641">U ostatních konfigurací (například pomocí místní IP adresy nebo vazby na port 443) spusťte aplikaci s oprávněními správce.</span><span class="sxs-lookup"><span data-stu-id="78e6f-641">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="78e6f-642">Aplikace odpoví na veřejnou IP adresu serveru.</span><span class="sxs-lookup"><span data-stu-id="78e6f-642">The app responds at the server's public IP address.</span></span> <span data-ttu-id="78e6f-643">V tomto příkladu je server dosažitelný z Internetu na jeho veřejné IP adrese `104.214.79.47` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-643">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="78e6f-644">V tomto příkladu se používá vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="78e6f-644">A development certificate is used in this example.</span></span> <span data-ttu-id="78e6f-645">Stránka se načte bezpečně po obejít upozornění na nedůvěryhodný certifikát v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="78e6f-645">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![Okno prohlížeče zobrazující načtenou stránku indexu aplikace](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="78e6f-647">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="78e6f-647">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="78e6f-648">Pro aplikace hostované HTTP.sys, které komunikují s požadavky z Internetu nebo podnikové sítě, se může při hostování za proxy servery a nástroji pro vyrovnávání zatížení vyžadovat další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="78e6f-648">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="78e6f-649">Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="78e6f-649">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78e6f-650">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="78e6f-650">Additional resources</span></span>

* [<span data-ttu-id="78e6f-651">Povolit ověřování systému Windows pomocí HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="78e6f-651">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="78e6f-652">Rozhraní API serveru HTTP</span><span class="sxs-lookup"><span data-stu-id="78e6f-652">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="78e6f-653">úložiště GitHubu ASPNET/HttpSysServer (zdrojový kód)</span><span class="sxs-lookup"><span data-stu-id="78e6f-653">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="78e6f-654">Hostitel</span><span class="sxs-lookup"><span data-stu-id="78e6f-654">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="78e6f-655">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) je [webový server pro ASP.NET Core](xref:fundamentals/servers/index) , který běží jenom ve Windows.</span><span class="sxs-lookup"><span data-stu-id="78e6f-655">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="78e6f-656">HTTP.sys je alternativou k serveru [Kestrel](xref:fundamentals/servers/kestrel) a nabízí některé funkce, které Kestrel neposkytují.</span><span class="sxs-lookup"><span data-stu-id="78e6f-656">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="78e6f-657">HTTP.sys není kompatibilní s [modulem ASP.NET Core](xref:host-and-deploy/aspnet-core-module) a nedá se použít se službou IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="78e6f-657">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="78e6f-658">HTTP.sys podporuje následující funkce:</span><span class="sxs-lookup"><span data-stu-id="78e6f-658">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="78e6f-659">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="78e6f-659">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="78e6f-660">Sdílení portů</span><span class="sxs-lookup"><span data-stu-id="78e6f-660">Port sharing</span></span>
* <span data-ttu-id="78e6f-661">HTTPS s SNI</span><span class="sxs-lookup"><span data-stu-id="78e6f-661">HTTPS with SNI</span></span>
* <span data-ttu-id="78e6f-662">HTTP/2 přes TLS (Windows 10 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="78e6f-662">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="78e6f-663">Přímý přenos souborů</span><span class="sxs-lookup"><span data-stu-id="78e6f-663">Direct file transmission</span></span>
* <span data-ttu-id="78e6f-664">Ukládání odpovědí do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="78e6f-664">Response caching</span></span>
* <span data-ttu-id="78e6f-665">WebSockets (Windows 8 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="78e6f-665">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="78e6f-666">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="78e6f-666">Supported Windows versions:</span></span>

* <span data-ttu-id="78e6f-667">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="78e6f-667">Windows 7 or later</span></span>
* <span data-ttu-id="78e6f-668">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="78e6f-668">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="78e6f-669">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="78e6f-669">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="78e6f-670">Kdy použít HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="78e6f-670">When to use HTTP.sys</span></span>

<span data-ttu-id="78e6f-671">HTTP.sys je užitečná pro nasazení, kde:</span><span class="sxs-lookup"><span data-stu-id="78e6f-671">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="78e6f-672">Server je potřeba zveřejnit přímo na internetu bez použití IIS.</span><span class="sxs-lookup"><span data-stu-id="78e6f-672">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys komunikuje přímo s internetem.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="78e6f-674">Interní nasazení vyžaduje funkci, která není v Kestrel k dispozici, jako je například [ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="78e6f-674">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys komunikuje přímo s interní sítí.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="78e6f-676">HTTP.sys je Vyspělá technologie, která chrání před mnoha typy útoků a poskytuje odolnost, zabezpečení a škálovatelnost plně funkčního webového serveru.</span><span class="sxs-lookup"><span data-stu-id="78e6f-676">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="78e6f-677">Služba IIS sám spouští jako naslouchací proces HTTP nad HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="78e6f-677">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="78e6f-678">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="78e6f-678">HTTP/2 support</span></span>

<span data-ttu-id="78e6f-679">[Protokol HTTP/2](https://httpwg.org/specs/rfc7540.html) je povolený pro aplikace ASP.NET Core, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="78e6f-679">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="78e6f-680">Windows Server 2016/Windows 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="78e6f-680">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="78e6f-681">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="78e6f-681">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="78e6f-682">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="78e6f-682">TLS 1.2 or later connection</span></span>

<span data-ttu-id="78e6f-683">Pokud je vytvořeno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/1.1` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-683">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="78e6f-684">HTTP/2 je ve výchozím nastavení povolené.</span><span class="sxs-lookup"><span data-stu-id="78e6f-684">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="78e6f-685">Pokud připojení HTTP/2 není navázáno, připojení se vrátí k HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="78e6f-685">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="78e6f-686">V budoucí verzi Windows budou k dispozici příznaky konfigurace protokolu HTTP/2, včetně možnosti zakázat protokol HTTP/2 s HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="78e6f-686">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="78e6f-687">Ověřování v režimu jádra pomocí protokolu Kerberos</span><span class="sxs-lookup"><span data-stu-id="78e6f-687">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="78e6f-688">HTTP.sys deleguje ověřování režimu jádra pomocí ověřovacího protokolu Kerberos.</span><span class="sxs-lookup"><span data-stu-id="78e6f-688">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="78e6f-689">Ověřování pomocí uživatelského režimu není pro protokol Kerberos a HTTP.sys podporováno.</span><span class="sxs-lookup"><span data-stu-id="78e6f-689">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="78e6f-690">Účet počítače se musí použít k dešifrování tokenu nebo lístku protokolu Kerberos, který je získaný ze služby Active Directory a který přesměruje klient na server za účelem ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="78e6f-690">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="78e6f-691">Zaregistrujte hlavní název služby (SPN) pro hostitele, nikoli uživatele aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e6f-691">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="78e6f-692">Jak používat HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="78e6f-692">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="78e6f-693">Konfigurace aplikace ASP.NET Core pro použití HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="78e6f-693">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="78e6f-694">Odkaz na balíček v souboru projektu není vyžadován při použití [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) ([NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span><span class="sxs-lookup"><span data-stu-id="78e6f-694">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="78e6f-695">Pokud nepoužíváte `Microsoft.AspNetCore.App` Metapackage, přidejte odkaz na balíček do [Microsoft. AspNetCore. Server. HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span><span class="sxs-lookup"><span data-stu-id="78e6f-695">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="78e6f-696"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>Při sestavování hostitele zavolejte metodu rozšíření a určete požadované <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> .</span><span class="sxs-lookup"><span data-stu-id="78e6f-696">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="78e6f-697">Následující příklad nastaví možnosti na jejich výchozí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="78e6f-697">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="78e6f-698">Další konfigurace HTTP.sys se zpracovává prostřednictvím [nastavení registru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span><span class="sxs-lookup"><span data-stu-id="78e6f-698">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="78e6f-699">\*\* MožnostiHTTP.sys\*\*</span><span class="sxs-lookup"><span data-stu-id="78e6f-699">**HTTP.sys options**</span></span>

| <span data-ttu-id="78e6f-700">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="78e6f-700">Property</span></span> | <span data-ttu-id="78e6f-701">Popis</span><span class="sxs-lookup"><span data-stu-id="78e6f-701">Description</span></span> | <span data-ttu-id="78e6f-702">Výchozí</span><span class="sxs-lookup"><span data-stu-id="78e6f-702">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="78e6f-703">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="78e6f-703">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="78e6f-704">Určuje, zda je povolen synchronní vstup/výstup pro `HttpContext.Request.Body` a `HttpContext.Response.Body` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-704">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="78e6f-705">Ověřování. AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="78e6f-705">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="78e6f-706">Povoluje anonymní požadavky.</span><span class="sxs-lookup"><span data-stu-id="78e6f-706">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="78e6f-707">Ověřování. schémata</span><span class="sxs-lookup"><span data-stu-id="78e6f-707">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="78e6f-708">Určete povolená schémata ověřování.</span><span class="sxs-lookup"><span data-stu-id="78e6f-708">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="78e6f-709">Může být kdykoli změněno před vyřazením naslouchacího procesu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-709">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="78e6f-710">Hodnoty jsou k dispozici ve [výčtu schémata AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic` , `Kerberos` , `Negotiate` , `None` a `NTLM` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-710">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="78e6f-711">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="78e6f-711">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="78e6f-712">Pokusit se o ukládání do mezipaměti v [režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) pro odpovědi s oprávněnými záhlavími.</span><span class="sxs-lookup"><span data-stu-id="78e6f-712">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="78e6f-713">Odpověď nesmí obsahovat `Set-Cookie` `Vary` hlavičky, ani `Pragma` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-713">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="78e6f-714">Musí obsahovat `Cache-Control` hlavičku, která je `public` a buď `shared-max-age` `max-age` hodnota nebo, nebo `Expires` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="78e6f-714">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="78e6f-715">Maximální počet souběžných přijetí.</span><span class="sxs-lookup"><span data-stu-id="78e6f-715">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="78e6f-716">5 &times; [prostředí. <br> ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="78e6f-716">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="78e6f-717">Maximální počet souběžných připojení, která se mají přijmout</span><span class="sxs-lookup"><span data-stu-id="78e6f-717">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="78e6f-718">Použijte `-1` pro nekonečné.</span><span class="sxs-lookup"><span data-stu-id="78e6f-718">Use `-1` for infinite.</span></span> <span data-ttu-id="78e6f-719">Použijte `null` k použití nastavení pro počítač v rámci registru.</span><span class="sxs-lookup"><span data-stu-id="78e6f-719">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="78e6f-720">(počítačově v šířce</span><span class="sxs-lookup"><span data-stu-id="78e6f-720">(machine-wide</span></span><br><span data-ttu-id="78e6f-721">nastavením</span><span class="sxs-lookup"><span data-stu-id="78e6f-721">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="78e6f-722">Viz část <a href="#maxrequestbodysize">MaxRequestBodySize</a> .</span><span class="sxs-lookup"><span data-stu-id="78e6f-722">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="78e6f-723">30000000 bajtů</span><span class="sxs-lookup"><span data-stu-id="78e6f-723">30000000 bytes</span></span><br><span data-ttu-id="78e6f-724">(~ 28,6 MB)</span><span class="sxs-lookup"><span data-stu-id="78e6f-724">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="78e6f-725">Maximální počet požadavků, které lze zařadit do fronty.</span><span class="sxs-lookup"><span data-stu-id="78e6f-725">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="78e6f-726">1000</span><span class="sxs-lookup"><span data-stu-id="78e6f-726">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="78e6f-727">Určuje, jestli tělo odpovědi zapisuje, které selhalo kvůli odpojení klienta, by mělo vyvolat výjimky nebo dokončit normálně.</span><span class="sxs-lookup"><span data-stu-id="78e6f-727">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="78e6f-728">(normálně dokončit)</span><span class="sxs-lookup"><span data-stu-id="78e6f-728">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="78e6f-729">Vystavte <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> konfiguraci HTTP.sys, která může být v registru nakonfigurovaná taky.</span><span class="sxs-lookup"><span data-stu-id="78e6f-729">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="78e6f-730">Pomocí odkazů rozhraní API získáte další informace o jednotlivých nastaveních, včetně výchozích hodnot:</span><span class="sxs-lookup"><span data-stu-id="78e6f-730">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="78e6f-731">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): čas povolený pro rozhraní API serveru http, který má vyprázdnit tělo entity u připojení Keep-Alive.</span><span class="sxs-lookup"><span data-stu-id="78e6f-731">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="78e6f-732">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): čas povolený pro doručení těla entity požadavku.</span><span class="sxs-lookup"><span data-stu-id="78e6f-732">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="78e6f-733">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): čas povolený pro rozhraní API serveru HTTP k analýze hlavičky žádosti.</span><span class="sxs-lookup"><span data-stu-id="78e6f-733">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="78e6f-734">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): čas povolený pro nečinné připojení.</span><span class="sxs-lookup"><span data-stu-id="78e6f-734">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="78e6f-735">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): minimální rychlost odesílání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="78e6f-735">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="78e6f-736">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): čas, který může požadavek zůstat ve frontě požadavků předtím, než ji aplikace vybere.</span><span class="sxs-lookup"><span data-stu-id="78e6f-736">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="78e6f-737">Zadejte, kam se <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> má zaregistrovat HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="78e6f-737">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="78e6f-738">Nejužitečnější je [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), který se používá k přidání předpony do kolekce.</span><span class="sxs-lookup"><span data-stu-id="78e6f-738">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="78e6f-739">Ty mohou být kdykoliv změněny předtím, než se naslouchací proces vyřazuje.</span><span class="sxs-lookup"><span data-stu-id="78e6f-739">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="78e6f-740">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="78e6f-740">**MaxRequestBodySize**</span></span>

<span data-ttu-id="78e6f-741">Maximální povolená velikost libovolného textu žádosti v bajtech</span><span class="sxs-lookup"><span data-stu-id="78e6f-741">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="78e6f-742">Při nastavení na `null` je maximální velikost textu požadavku neomezená.</span><span class="sxs-lookup"><span data-stu-id="78e6f-742">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="78e6f-743">Toto omezení nemá žádný vliv na upgradovaná připojení, která jsou vždycky neomezená.</span><span class="sxs-lookup"><span data-stu-id="78e6f-743">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="78e6f-744">Doporučená metoda pro přepsání limitu v aplikaci ASP.NET Core MVC pro jeden `IActionResult` je použít <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atribut pro metodu akce:</span><span class="sxs-lookup"><span data-stu-id="78e6f-744">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="78e6f-745">Výjimka je vyvolána, pokud se aplikace pokusí nakonfigurovat limit žádosti poté, co aplikace začne číst požadavek.</span><span class="sxs-lookup"><span data-stu-id="78e6f-745">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="78e6f-746">`IsReadOnly`Vlastnost může být použita k označení `MaxRequestBodySize` , zda je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-746">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="78e6f-747">Pokud by měla aplikace přepsat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> jednotlivé požadavky, použijte <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :</span><span class="sxs-lookup"><span data-stu-id="78e6f-747">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="78e6f-748">Pokud používáte Visual Studio, ujistěte se, že aplikace není nakonfigurovaná tak, aby spouštěla službu IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="78e6f-748">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="78e6f-749">V aplikaci Visual Studio je výchozím spouštěcím profilem IIS Express.</span><span class="sxs-lookup"><span data-stu-id="78e6f-749">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="78e6f-750">Chcete-li spustit projekt jako konzolovou aplikaci, ručně změňte vybraný profil, jak je znázorněno na následujícím snímku obrazovky:</span><span class="sxs-lookup"><span data-stu-id="78e6f-750">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![Vybrat profil aplikace konzoly](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="78e6f-752">Konfigurace Windows serveru</span><span class="sxs-lookup"><span data-stu-id="78e6f-752">Configure Windows Server</span></span>

1. <span data-ttu-id="78e6f-753">Určete porty, které se mají otevřít pro aplikaci, a pomocí [brány Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) nebo rutiny [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) prostředí PowerShell otevřete porty brány firewall, abyste umožnili provozu HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="78e6f-753">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="78e6f-754">V následujících příkazech a konfiguraci aplikace se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="78e6f-754">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="78e6f-755">Při nasazování na virtuální počítač Azure otevřete porty ve [skupině zabezpečení sítě](/azure/virtual-machines/windows/nsg-quickstart-portal).</span><span class="sxs-lookup"><span data-stu-id="78e6f-755">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="78e6f-756">V následujících příkazech a konfiguraci aplikace se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="78e6f-756">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="78e6f-757">V případě potřeby Získejte a nainstalujte certifikáty X. 509.</span><span class="sxs-lookup"><span data-stu-id="78e6f-757">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="78e6f-758">V systému Windows vytvořte pomocí [rutiny prostředí PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate)certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="78e6f-758">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="78e6f-759">Nepodporovaný příklad naleznete v tématu [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span><span class="sxs-lookup"><span data-stu-id="78e6f-759">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="78e6f-760">Nainstalujte certifikáty podepsané svým držitelem nebo certifikátem podepsaným certifikační autoritou do osobního úložiště **místního počítače** serveru > **Personal** .</span><span class="sxs-lookup"><span data-stu-id="78e6f-760">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="78e6f-761">Pokud se jedná o [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd), nainstalujte rozhraní .NET core, .NET Framework nebo obojí (Pokud je aplikace aplikace .NET Core cílící na .NET Framework).</span><span class="sxs-lookup"><span data-stu-id="78e6f-761">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="78e6f-762">**.NET Core**: Pokud aplikace vyžaduje .NET Core, Získejte a spusťte instalační program **modulu runtime .NET Core** ze [souborů ke stažení pro .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="78e6f-762">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="78e6f-763">Neinstalujte na server plnou sadu SDK.</span><span class="sxs-lookup"><span data-stu-id="78e6f-763">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="78e6f-764">**.NET Framework**: Pokud aplikace vyžaduje .NET Framework, přečtěte si téma [Průvodce instalací .NET Framework](/dotnet/framework/install/).</span><span class="sxs-lookup"><span data-stu-id="78e6f-764">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="78e6f-765">Nainstalujte požadovanou .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="78e6f-765">Install the required .NET Framework.</span></span> <span data-ttu-id="78e6f-766">Instalační program pro nejnovější .NET Framework je k dispozici na stránce [soubory ke stažení pro .NET Core](https://dotnet.microsoft.com/download) .</span><span class="sxs-lookup"><span data-stu-id="78e6f-766">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="78e6f-767">Pokud je aplikace samostatným [nasazením](/dotnet/core/deploying/#self-contained-deployments-scd), aplikace zahrne modul runtime do svého nasazení.</span><span class="sxs-lookup"><span data-stu-id="78e6f-767">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="78e6f-768">Na serveru není nutná žádná instalace rozhraní.</span><span class="sxs-lookup"><span data-stu-id="78e6f-768">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="78e6f-769">Konfigurace adres URL a portů v aplikaci</span><span class="sxs-lookup"><span data-stu-id="78e6f-769">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="78e6f-770">Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-770">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="78e6f-771">Pokud chcete nakonfigurovat předpony a porty adresy URL, můžete použít tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="78e6f-771">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="78e6f-772">`urls` argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="78e6f-772">`urls` command-line argument</span></span>
   * <span data-ttu-id="78e6f-773">`ASPNETCORE_URLS` Proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="78e6f-773">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="78e6f-774">Následující příklad kódu ukazuje, jak používat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> s místní IP adresou serveru `10.0.0.4` na portu 443:</span><span class="sxs-lookup"><span data-stu-id="78e6f-774">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="78e6f-775">Výhodou `UrlPrefixes` je, že se okamžitě generuje chybová zpráva pro nesprávně naformátované předpony.</span><span class="sxs-lookup"><span data-stu-id="78e6f-775">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="78e6f-776">Nastavení v `UrlPrefixes` `UseUrls` / `urls` / `ASPNETCORE_URLS` Nastavení přepsání</span><span class="sxs-lookup"><span data-stu-id="78e6f-776">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="78e6f-777">Proto je výhodou `UseUrls` `urls` `ASPNETCORE_URLS` proměnné prostředí, a jednodušší přepínání mezi Kestrel a HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="78e6f-777">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="78e6f-778">HTTP.sys používá [formáty řetězce UrlPrefix HTTP serveru API](/windows/win32/http/urlprefix-strings).</span><span class="sxs-lookup"><span data-stu-id="78e6f-778">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="78e6f-779">Nelze použít vazby zástupných znaků na nejvyšší úrovni ( `http://*:80/` a `http://+:80` ). **not**</span><span class="sxs-lookup"><span data-stu-id="78e6f-779">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="78e6f-780">Vazby na zástupné znaky na nejvyšší úrovni vytvoří chyby zabezpečení aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e6f-780">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="78e6f-781">To platí pro silné i slabé zástupné znaky.</span><span class="sxs-lookup"><span data-stu-id="78e6f-781">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="78e6f-782">Místo zástupných znaků použijte explicitní názvy hostitelů nebo IP adresy.</span><span class="sxs-lookup"><span data-stu-id="78e6f-782">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="78e6f-783">Vazba zástupných znaků subdomény (například `*.mysub.com` ) není bezpečnostní riziko, pokud ovládáte celou nadřazenou doménu (na rozdíl od `*.com` , která je zranitelná).</span><span class="sxs-lookup"><span data-stu-id="78e6f-783">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="78e6f-784">Další informace najdete v [dokumentu RFC 7230: oddíl 5,4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="78e6f-784">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="78e6f-785">Předregistrujte předpony adresy URL na serveru.</span><span class="sxs-lookup"><span data-stu-id="78e6f-785">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="78e6f-786">K \* disnetsh.exe\*je integrovaný nástroj pro konfiguraci HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="78e6f-786">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="78e6f-787">*netsh.exe* se používá k rezervaci předpon adres URL a přiřazování certifikátů X. 509.</span><span class="sxs-lookup"><span data-stu-id="78e6f-787">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="78e6f-788">Nástroj vyžaduje oprávnění správce.</span><span class="sxs-lookup"><span data-stu-id="78e6f-788">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="78e6f-789">K registraci adres URL aplikace použijte nástroj *netsh.exe* :</span><span class="sxs-lookup"><span data-stu-id="78e6f-789">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="78e6f-790">`<URL>`: Plně kvalifikované adresy URL (Uniform Resource Locator).</span><span class="sxs-lookup"><span data-stu-id="78e6f-790">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="78e6f-791">Nepoužívejte vazbu zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="78e6f-791">Don't use a wildcard binding.</span></span> <span data-ttu-id="78e6f-792">Použijte platný název hostitele nebo místní IP adresu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-792">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="78e6f-793">*Adresa URL musí obsahovat koncové lomítko.*</span><span class="sxs-lookup"><span data-stu-id="78e6f-793">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="78e6f-794">`<USER>`: Určuje název uživatele nebo skupiny uživatelů.</span><span class="sxs-lookup"><span data-stu-id="78e6f-794">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="78e6f-795">V následujícím příkladu je místní IP adresa serveru `10.0.0.4` :</span><span class="sxs-lookup"><span data-stu-id="78e6f-795">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="78e6f-796">Když je adresa URL registrována, nástroj odpoví `URL reservation successfully added` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-796">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="78e6f-797">Pokud chcete odstranit registrovanou adresu URL, použijte `delete urlacl` příkaz:</span><span class="sxs-lookup"><span data-stu-id="78e6f-797">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="78e6f-798">Zaregistrujte certifikáty X. 509 na serveru.</span><span class="sxs-lookup"><span data-stu-id="78e6f-798">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="78e6f-799">K registraci certifikátů pro aplikaci použijte nástroj *netsh.exe* :</span><span class="sxs-lookup"><span data-stu-id="78e6f-799">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="78e6f-800">`<IP>`: Určuje místní IP adresu pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-800">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="78e6f-801">Nepoužívejte vazbu zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="78e6f-801">Don't use a wildcard binding.</span></span> <span data-ttu-id="78e6f-802">Použijte platnou IP adresu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-802">Use a valid IP address.</span></span>
   * <span data-ttu-id="78e6f-803">`<PORT>`: Určuje port pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-803">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="78e6f-804">`<THUMBPRINT>`: Kryptografický otisk certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="78e6f-804">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="78e6f-805">`<GUID>`: Identifikátor GUID generovaný vývojářem, který představuje aplikaci pro informativní účely.</span><span class="sxs-lookup"><span data-stu-id="78e6f-805">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="78e6f-806">Pro referenční účely uložte identifikátor GUID do aplikace jako značku balíčku:</span><span class="sxs-lookup"><span data-stu-id="78e6f-806">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="78e6f-807">V aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="78e6f-807">In Visual Studio:</span></span>
     * <span data-ttu-id="78e6f-808">Otevřete vlastnosti projektu aplikace tak, že kliknete pravým tlačítkem na aplikaci v **Průzkumník řešení** a vyberete **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="78e6f-808">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="78e6f-809">Vyberte kartu **balíček** .</span><span class="sxs-lookup"><span data-stu-id="78e6f-809">Select the **Package** tab.</span></span>
     * <span data-ttu-id="78e6f-810">Zadejte identifikátor GUID, který jste vytvořili v poli **značky** .</span><span class="sxs-lookup"><span data-stu-id="78e6f-810">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="78e6f-811">Když nepoužíváte Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="78e6f-811">When not using Visual Studio:</span></span>
     * <span data-ttu-id="78e6f-812">Otevřete soubor projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e6f-812">Open the app's project file.</span></span>
     * <span data-ttu-id="78e6f-813">Přidejte `<PackageTags>` vlastnost do nového nebo existujícího `<PropertyGroup>` s identifikátorem GUID, který jste vytvořili:</span><span class="sxs-lookup"><span data-stu-id="78e6f-813">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="78e6f-814">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="78e6f-814">In the following example:</span></span>

   * <span data-ttu-id="78e6f-815">Místní IP adresa serveru je `10.0.0.4` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-815">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="78e6f-816">Generátor náhodných identifikátorů GUID online poskytuje `appid` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="78e6f-816">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="78e6f-817">Když je certifikát zaregistrován, nástroj odpoví `SSL Certificate successfully added` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-817">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="78e6f-818">K odstranění registrace certifikátu použijte `delete sslcert` příkaz:</span><span class="sxs-lookup"><span data-stu-id="78e6f-818">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="78e6f-819">Referenční dokumentace pro *netsh.exe*:</span><span class="sxs-lookup"><span data-stu-id="78e6f-819">Reference documentation for *netsh.exe*:</span></span>

   * [<span data-ttu-id="78e6f-820">Příkazy Netsh pro protokol HTTP (Hypertext Transfer Protocol)</span><span class="sxs-lookup"><span data-stu-id="78e6f-820">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](https://technet.microsoft.com/library/cc725882.aspx)
   * [<span data-ttu-id="78e6f-821">UrlPrefix řetězce</span><span class="sxs-lookup"><span data-stu-id="78e6f-821">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="78e6f-822">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="78e6f-822">Run the app.</span></span>

   <span data-ttu-id="78e6f-823">Oprávnění správce nejsou nutná ke spuštění aplikace při vytváření vazby na localhost pomocí protokolu HTTP (ne HTTPS) s číslem portu větším než 1024.</span><span class="sxs-lookup"><span data-stu-id="78e6f-823">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="78e6f-824">U ostatních konfigurací (například pomocí místní IP adresy nebo vazby na port 443) spusťte aplikaci s oprávněními správce.</span><span class="sxs-lookup"><span data-stu-id="78e6f-824">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="78e6f-825">Aplikace odpoví na veřejnou IP adresu serveru.</span><span class="sxs-lookup"><span data-stu-id="78e6f-825">The app responds at the server's public IP address.</span></span> <span data-ttu-id="78e6f-826">V tomto příkladu je server dosažitelný z Internetu na jeho veřejné IP adrese `104.214.79.47` .</span><span class="sxs-lookup"><span data-stu-id="78e6f-826">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="78e6f-827">V tomto příkladu se používá vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="78e6f-827">A development certificate is used in this example.</span></span> <span data-ttu-id="78e6f-828">Stránka se načte bezpečně po obejít upozornění na nedůvěryhodný certifikát v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="78e6f-828">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![Okno prohlížeče zobrazující načtenou stránku indexu aplikace](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="78e6f-830">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="78e6f-830">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="78e6f-831">Pro aplikace hostované HTTP.sys, které komunikují s požadavky z Internetu nebo podnikové sítě, se může při hostování za proxy servery a nástroji pro vyrovnávání zatížení vyžadovat další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="78e6f-831">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="78e6f-832">Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="78e6f-832">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78e6f-833">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="78e6f-833">Additional resources</span></span>

* [<span data-ttu-id="78e6f-834">Povolit ověřování systému Windows pomocí HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="78e6f-834">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="78e6f-835">Rozhraní API serveru HTTP</span><span class="sxs-lookup"><span data-stu-id="78e6f-835">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="78e6f-836">úložiště GitHubu ASPNET/HttpSysServer (zdrojový kód)</span><span class="sxs-lookup"><span data-stu-id="78e6f-836">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="78e6f-837">Hostitel</span><span class="sxs-lookup"><span data-stu-id="78e6f-837">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
