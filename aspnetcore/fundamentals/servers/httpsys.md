---
title: Implementace webového serveru HTTP.sys v ASP.NET Core
author: rick-anderson
description: Přečtěte si o HTTP.sys, webovém serveru pro ASP.NET Core ve Windows. HTTP.sys je alternativou k Kestrel, která je založená na HTTP.sys ovladač režimu jádra, který je možné použít k přímému připojení k Internetu bez služby IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
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
ms.openlocfilehash: 384b6559b4ee6140da5cf785ffda3978aafbb132
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016762"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="a7fb7-104">Implementace webového serveru HTTP.sys v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a7fb7-104">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="a7fb7-105">[Dykstra](https://github.com/tdykstra) a [Chris Rossův](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-105">By [Tom Dykstra](https://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="a7fb7-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) je [webový server pro ASP.NET Core](xref:fundamentals/servers/index) , který běží jenom ve Windows.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="a7fb7-107">HTTP.sys je alternativou k serveru [Kestrel](xref:fundamentals/servers/kestrel) a nabízí některé funkce, které Kestrel neposkytují.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-107">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a7fb7-108">HTTP.sys není kompatibilní s [modulem ASP.NET Core](xref:host-and-deploy/aspnet-core-module) a nedá se použít se službou IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-108">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="a7fb7-109">HTTP.sys podporuje následující funkce:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-109">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="a7fb7-110">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="a7fb7-110">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="a7fb7-111">Sdílení portů</span><span class="sxs-lookup"><span data-stu-id="a7fb7-111">Port sharing</span></span>
* <span data-ttu-id="a7fb7-112">HTTPS s SNI</span><span class="sxs-lookup"><span data-stu-id="a7fb7-112">HTTPS with SNI</span></span>
* <span data-ttu-id="a7fb7-113">HTTP/2 přes TLS (Windows 10 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-113">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="a7fb7-114">Přímý přenos souborů</span><span class="sxs-lookup"><span data-stu-id="a7fb7-114">Direct file transmission</span></span>
* <span data-ttu-id="a7fb7-115">Ukládání odpovědí do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="a7fb7-115">Response caching</span></span>
* <span data-ttu-id="a7fb7-116">WebSockets (Windows 8 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-116">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="a7fb7-117">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-117">Supported Windows versions:</span></span>

* <span data-ttu-id="a7fb7-118">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="a7fb7-118">Windows 7 or later</span></span>
* <span data-ttu-id="a7fb7-119">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="a7fb7-119">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="a7fb7-120">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a7fb7-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="a7fb7-121">Kdy použít HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a7fb7-121">When to use HTTP.sys</span></span>

<span data-ttu-id="a7fb7-122">HTTP.sys je užitečná pro nasazení, kde:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-122">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="a7fb7-123">Server je potřeba zveřejnit přímo na internetu bez použití IIS.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-123">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys komunikuje přímo s internetem.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="a7fb7-125">Interní nasazení vyžaduje funkci, která není v Kestrel k dispozici, jako je například [ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-125">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys komunikuje přímo s interní sítí.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="a7fb7-127">HTTP.sys je Vyspělá technologie, která chrání před mnoha typy útoků a poskytuje odolnost, zabezpečení a škálovatelnost plně funkčního webového serveru.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-127">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="a7fb7-128">Služba IIS sám spouští jako naslouchací proces HTTP nad HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-128">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="a7fb7-129">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="a7fb7-129">HTTP/2 support</span></span>

<span data-ttu-id="a7fb7-130">[Protokol HTTP/2](https://httpwg.org/specs/rfc7540.html) je povolený pro aplikace ASP.NET Core, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-130">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="a7fb7-131">Windows Server 2016/Windows 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="a7fb7-131">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="a7fb7-132">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-132">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="a7fb7-133">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="a7fb7-133">TLS 1.2 or later connection</span></span>

<span data-ttu-id="a7fb7-134">Pokud je vytvořeno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-134">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="a7fb7-135">HTTP/2 je ve výchozím nastavení povolené.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-135">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="a7fb7-136">Pokud připojení HTTP/2 není navázáno, připojení se vrátí k HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-136">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="a7fb7-137">V budoucí verzi Windows budou k dispozici příznaky konfigurace protokolu HTTP/2, včetně možnosti zakázat protokol HTTP/2 s HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-137">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="a7fb7-138">Ověřování v režimu jádra pomocí protokolu Kerberos</span><span class="sxs-lookup"><span data-stu-id="a7fb7-138">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="a7fb7-139">HTTP.sys deleguje ověřování režimu jádra pomocí ověřovacího protokolu Kerberos.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-139">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="a7fb7-140">Ověřování pomocí uživatelského režimu není pro protokol Kerberos a HTTP.sys podporováno.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-140">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="a7fb7-141">Účet počítače se musí použít k dešifrování tokenu nebo lístku protokolu Kerberos, který je získaný ze služby Active Directory a který přesměruje klient na server za účelem ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-141">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="a7fb7-142">Zaregistrujte hlavní název služby (SPN) pro hostitele, nikoli uživatele aplikace.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-142">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="a7fb7-143">Jak používat HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a7fb7-143">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="a7fb7-144">Konfigurace aplikace ASP.NET Core pro použití HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a7fb7-144">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="a7fb7-145"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>Při sestavování hostitele zavolejte metodu rozšíření a určete požadované <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-145">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="a7fb7-146">Následující příklad nastaví možnosti na jejich výchozí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-146">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="a7fb7-147">Další konfigurace HTTP.sys se zpracovává prostřednictvím [nastavení registru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-147">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="a7fb7-148">**MožnostiHTTP.sys**</span><span class="sxs-lookup"><span data-stu-id="a7fb7-148">**HTTP.sys options**</span></span>

| <span data-ttu-id="a7fb7-149">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="a7fb7-149">Property</span></span> | <span data-ttu-id="a7fb7-150">Popis</span><span class="sxs-lookup"><span data-stu-id="a7fb7-150">Description</span></span> | <span data-ttu-id="a7fb7-151">Výchozí</span><span class="sxs-lookup"><span data-stu-id="a7fb7-151">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="a7fb7-152">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="a7fb7-152">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="a7fb7-153">Určuje, zda je povolen synchronní vstup/výstup pro `HttpContext.Request.Body` a `HttpContext.Response.Body` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-153">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="a7fb7-154">Ověřování. AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="a7fb7-154">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="a7fb7-155">Povoluje anonymní požadavky.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-155">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="a7fb7-156">Ověřování. schémata</span><span class="sxs-lookup"><span data-stu-id="a7fb7-156">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="a7fb7-157">Určete povolená schémata ověřování.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-157">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="a7fb7-158">Může být kdykoli změněno před vyřazením naslouchacího procesu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-158">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="a7fb7-159">Hodnoty jsou k dispozici ve [výčtu schémata AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic` , `Kerberos` , `Negotiate` , `None` a `NTLM` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-159">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="a7fb7-160">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="a7fb7-160">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="a7fb7-161">Pokusit se o ukládání do mezipaměti v [režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) pro odpovědi s oprávněnými záhlavími.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-161">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="a7fb7-162">Odpověď nesmí obsahovat `Set-Cookie` `Vary` hlavičky, ani `Pragma` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-162">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="a7fb7-163">Musí obsahovat `Cache-Control` hlavičku, která je `public` a buď `shared-max-age` `max-age` hodnota nebo, nebo `Expires` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-163">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="a7fb7-164">Maximální počet souběžných přijetí.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-164">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="a7fb7-165">5 &times; [prostředí. <br> ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-165">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="a7fb7-166">Maximální počet souběžných připojení, která se mají přijmout</span><span class="sxs-lookup"><span data-stu-id="a7fb7-166">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="a7fb7-167">Použijte `-1` pro nekonečné.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-167">Use `-1` for infinite.</span></span> <span data-ttu-id="a7fb7-168">Použijte `null` k použití nastavení pro počítač v rámci registru.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-168">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="a7fb7-169">(počítačově v šířce</span><span class="sxs-lookup"><span data-stu-id="a7fb7-169">(machine-wide</span></span><br><span data-ttu-id="a7fb7-170">nastavením</span><span class="sxs-lookup"><span data-stu-id="a7fb7-170">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="a7fb7-171">Viz část <a href="#maxrequestbodysize">MaxRequestBodySize</a> .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-171">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="a7fb7-172">30000000 bajtů</span><span class="sxs-lookup"><span data-stu-id="a7fb7-172">30000000 bytes</span></span><br><span data-ttu-id="a7fb7-173">(~ 28,6 MB)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-173">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="a7fb7-174">Maximální počet požadavků, které lze zařadit do fronty.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-174">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="a7fb7-175">1000</span><span class="sxs-lookup"><span data-stu-id="a7fb7-175">1000</span></span> |
| `RequestQueueMode` | <span data-ttu-id="a7fb7-176">Označuje, zda je server zodpovědný za vytvoření a konfiguraci fronty požadavků nebo zda se má připojit k existující frontě.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-176">This indicates whether the server is responsible for creating and configuring the request queue, or if it should attach to an existing queue.</span></span><br><span data-ttu-id="a7fb7-177">Většina stávajících možností konfigurace se nedá použít při připojování k existující frontě.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-177">Most existing configuration options do not apply when attaching to an existing queue.</span></span> | `RequestQueueMode.Create` |
| `RequestQueueName` | <span data-ttu-id="a7fb7-178">Název fronty požadavků HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-178">The name of the HTTP.sys request queue.</span></span> | <span data-ttu-id="a7fb7-179">`null`(Anonymní fronta)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-179">`null` (Anonymous queue)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="a7fb7-180">Určuje, jestli tělo odpovědi zapisuje, které selhalo kvůli odpojení klienta, by mělo vyvolat výjimky nebo dokončit normálně.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-180">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="a7fb7-181">(normálně dokončit)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-181">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="a7fb7-182">Vystavte <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> konfiguraci HTTP.sys, která může být v registru nakonfigurovaná taky.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-182">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="a7fb7-183">Pomocí odkazů rozhraní API získáte další informace o jednotlivých nastaveních, včetně výchozích hodnot:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-183">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="a7fb7-184">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): čas povolený pro rozhraní API serveru http, který má vyprázdnit tělo entity u připojení Keep-Alive.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-184">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="a7fb7-185">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): čas povolený pro doručení těla entity požadavku.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-185">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="a7fb7-186">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): čas povolený pro rozhraní API serveru HTTP k analýze hlavičky žádosti.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-186">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="a7fb7-187">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): čas povolený pro nečinné připojení.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-187">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="a7fb7-188">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): minimální rychlost odesílání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-188">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="a7fb7-189">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): čas, který může požadavek zůstat ve frontě požadavků předtím, než ji aplikace vybere.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-189">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="a7fb7-190">Zadejte, kam se <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> má zaregistrovat HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-190">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="a7fb7-191">Nejužitečnější je [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), který se používá k přidání předpony do kolekce.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-191">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="a7fb7-192">Ty mohou být kdykoliv změněny předtím, než se naslouchací proces vyřazuje.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-192">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="a7fb7-193">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="a7fb7-193">**MaxRequestBodySize**</span></span>

<span data-ttu-id="a7fb7-194">Maximální povolená velikost libovolného textu žádosti v bajtech</span><span class="sxs-lookup"><span data-stu-id="a7fb7-194">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="a7fb7-195">Při nastavení na `null` je maximální velikost textu požadavku neomezená.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-195">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="a7fb7-196">Toto omezení nemá žádný vliv na upgradovaná připojení, která jsou vždycky neomezená.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-196">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="a7fb7-197">Doporučená metoda pro přepsání limitu v aplikaci ASP.NET Core MVC pro jeden `IActionResult` je použít <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atribut pro metodu akce:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-197">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="a7fb7-198">Výjimka je vyvolána, pokud se aplikace pokusí nakonfigurovat limit žádosti poté, co aplikace začne číst požadavek.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-198">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="a7fb7-199">`IsReadOnly`Vlastnost může být použita k označení `MaxRequestBodySize` , zda je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-199">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="a7fb7-200">Pokud by měla aplikace přepsat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> jednotlivé požadavky, použijte <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :</span><span class="sxs-lookup"><span data-stu-id="a7fb7-200">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="a7fb7-201">Pokud používáte Visual Studio, ujistěte se, že aplikace není nakonfigurovaná tak, aby spouštěla službu IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-201">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="a7fb7-202">V aplikaci Visual Studio je výchozím spouštěcím profilem IIS Express.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-202">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="a7fb7-203">Chcete-li spustit projekt jako konzolovou aplikaci, ručně změňte vybraný profil, jak je znázorněno na následujícím snímku obrazovky:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-203">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![Vybrat profil aplikace konzoly](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="a7fb7-205">Konfigurace Windows serveru</span><span class="sxs-lookup"><span data-stu-id="a7fb7-205">Configure Windows Server</span></span>

1. <span data-ttu-id="a7fb7-206">Určete porty, které se mají otevřít pro aplikaci, a pomocí [brány Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) nebo rutiny [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) prostředí PowerShell otevřete porty brány firewall, abyste umožnili provozu HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-206">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="a7fb7-207">V následujících příkazech a konfiguraci aplikace se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-207">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="a7fb7-208">Při nasazování na virtuální počítač Azure otevřete porty ve [skupině zabezpečení sítě](/azure/virtual-machines/windows/nsg-quickstart-portal).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-208">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="a7fb7-209">V následujících příkazech a konfiguraci aplikace se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-209">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="a7fb7-210">V případě potřeby Získejte a nainstalujte certifikáty X. 509.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-210">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="a7fb7-211">V systému Windows vytvořte pomocí [rutiny prostředí PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate)certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-211">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="a7fb7-212">Nepodporovaný příklad naleznete v tématu [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-212">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="a7fb7-213">Nainstalujte certifikáty podepsané svým držitelem nebo certifikátem podepsaným certifikační autoritou do osobního úložiště **místního počítače** serveru > **Personal** .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-213">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="a7fb7-214">Pokud se jedná o [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd), nainstalujte rozhraní .NET core, .NET Framework nebo obojí (Pokud je aplikace aplikace .NET Core cílící na .NET Framework).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-214">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="a7fb7-215">**.NET Core**: Pokud aplikace vyžaduje .NET Core, Získejte a spusťte instalační program **modulu runtime .NET Core** ze [souborů ke stažení pro .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-215">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="a7fb7-216">Neinstalujte na server plnou sadu SDK.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-216">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="a7fb7-217">**.NET Framework**: Pokud aplikace vyžaduje .NET Framework, přečtěte si téma [Průvodce instalací .NET Framework](/dotnet/framework/install/).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-217">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="a7fb7-218">Nainstalujte požadovanou .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-218">Install the required .NET Framework.</span></span> <span data-ttu-id="a7fb7-219">Instalační program pro nejnovější .NET Framework je k dispozici na stránce [soubory ke stažení pro .NET Core](https://dotnet.microsoft.com/download) .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-219">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="a7fb7-220">Pokud je aplikace samostatným [nasazením](/dotnet/core/deploying/#self-contained-deployments-scd), aplikace zahrne modul runtime do svého nasazení.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-220">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="a7fb7-221">Na serveru není nutná žádná instalace rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-221">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="a7fb7-222">Konfigurace adres URL a portů v aplikaci</span><span class="sxs-lookup"><span data-stu-id="a7fb7-222">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="a7fb7-223">Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-223">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="a7fb7-224">Pokud chcete nakonfigurovat předpony a porty adresy URL, můžete použít tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-224">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="a7fb7-225">`urls`argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="a7fb7-225">`urls` command-line argument</span></span>
   * <span data-ttu-id="a7fb7-226">`ASPNETCORE_URLS`Proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="a7fb7-226">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="a7fb7-227">Následující příklad kódu ukazuje, jak používat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> s místní IP adresou serveru `10.0.0.4` na portu 443:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-227">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="a7fb7-228">Výhodou `UrlPrefixes` je, že se okamžitě generuje chybová zpráva pro nesprávně naformátované předpony.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-228">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="a7fb7-229">Nastavení v `UrlPrefixes` `UseUrls` / `urls` / `ASPNETCORE_URLS` Nastavení přepsání</span><span class="sxs-lookup"><span data-stu-id="a7fb7-229">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="a7fb7-230">Proto je výhodou `UseUrls` `urls` `ASPNETCORE_URLS` proměnné prostředí, a jednodušší přepínání mezi Kestrel a HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-230">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="a7fb7-231">HTTP.sys používá [formáty řetězce UrlPrefix HTTP serveru API](/windows/win32/http/urlprefix-strings).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-231">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="a7fb7-232">Nelze použít vazby zástupných znaků na nejvyšší úrovni ( `http://*:80/` a `http://+:80` ). **not**</span><span class="sxs-lookup"><span data-stu-id="a7fb7-232">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="a7fb7-233">Vazby na zástupné znaky na nejvyšší úrovni vytvoří chyby zabezpečení aplikace.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-233">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="a7fb7-234">To platí pro silné i slabé zástupné znaky.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-234">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="a7fb7-235">Místo zástupných znaků použijte explicitní názvy hostitelů nebo IP adresy.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-235">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="a7fb7-236">Vazba zástupných znaků subdomény (například `*.mysub.com` ) není bezpečnostní riziko, pokud ovládáte celou nadřazenou doménu (na rozdíl od `*.com` , která je zranitelná).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-236">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="a7fb7-237">Další informace najdete v [dokumentu RFC 7230: oddíl 5,4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-237">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="a7fb7-238">Předregistrujte předpony adresy URL na serveru.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-238">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="a7fb7-239">K *disnetsh.exe*je integrovaný nástroj pro konfiguraci HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-239">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="a7fb7-240">*netsh.exe* se používá k rezervaci předpon adres URL a přiřazování certifikátů X. 509.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-240">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="a7fb7-241">Nástroj vyžaduje oprávnění správce.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-241">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="a7fb7-242">K registraci adres URL aplikace použijte nástroj *netsh.exe* :</span><span class="sxs-lookup"><span data-stu-id="a7fb7-242">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="a7fb7-243">`<URL>`: Plně kvalifikované adresy URL (Uniform Resource Locator).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-243">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="a7fb7-244">Nepoužívejte vazbu zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-244">Don't use a wildcard binding.</span></span> <span data-ttu-id="a7fb7-245">Použijte platný název hostitele nebo místní IP adresu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-245">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="a7fb7-246">*Adresa URL musí obsahovat koncové lomítko.*</span><span class="sxs-lookup"><span data-stu-id="a7fb7-246">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="a7fb7-247">`<USER>`: Určuje název uživatele nebo skupiny uživatelů.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-247">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="a7fb7-248">V následujícím příkladu je místní IP adresa serveru `10.0.0.4` :</span><span class="sxs-lookup"><span data-stu-id="a7fb7-248">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="a7fb7-249">Když je adresa URL registrována, nástroj odpoví `URL reservation successfully added` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-249">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="a7fb7-250">Pokud chcete odstranit registrovanou adresu URL, použijte `delete urlacl` příkaz:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-250">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="a7fb7-251">Zaregistrujte certifikáty X. 509 na serveru.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-251">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="a7fb7-252">K registraci certifikátů pro aplikaci použijte nástroj *netsh.exe* :</span><span class="sxs-lookup"><span data-stu-id="a7fb7-252">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="a7fb7-253">`<IP>`: Určuje místní IP adresu pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-253">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="a7fb7-254">Nepoužívejte vazbu zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-254">Don't use a wildcard binding.</span></span> <span data-ttu-id="a7fb7-255">Použijte platnou IP adresu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-255">Use a valid IP address.</span></span>
   * <span data-ttu-id="a7fb7-256">`<PORT>`: Určuje port pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-256">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="a7fb7-257">`<THUMBPRINT>`: Kryptografický otisk certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-257">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="a7fb7-258">`<GUID>`: Identifikátor GUID generovaný vývojářem, který představuje aplikaci pro informativní účely.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-258">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="a7fb7-259">Pro referenční účely uložte identifikátor GUID do aplikace jako značku balíčku:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-259">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="a7fb7-260">V aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-260">In Visual Studio:</span></span>
     * <span data-ttu-id="a7fb7-261">Otevřete vlastnosti projektu aplikace tak, že kliknete pravým tlačítkem na aplikaci v **Průzkumník řešení** a vyberete **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-261">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="a7fb7-262">Vyberte kartu **balíček** .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-262">Select the **Package** tab.</span></span>
     * <span data-ttu-id="a7fb7-263">Zadejte identifikátor GUID, který jste vytvořili v poli **značky** .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-263">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="a7fb7-264">Když nepoužíváte Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-264">When not using Visual Studio:</span></span>
     * <span data-ttu-id="a7fb7-265">Otevřete soubor projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-265">Open the app's project file.</span></span>
     * <span data-ttu-id="a7fb7-266">Přidejte `<PackageTags>` vlastnost do nového nebo existujícího `<PropertyGroup>` s identifikátorem GUID, který jste vytvořili:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-266">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="a7fb7-267">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-267">In the following example:</span></span>

   * <span data-ttu-id="a7fb7-268">Místní IP adresa serveru je `10.0.0.4` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-268">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="a7fb7-269">Generátor náhodných identifikátorů GUID online poskytuje `appid` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-269">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="a7fb7-270">Když je certifikát zaregistrován, nástroj odpoví `SSL Certificate successfully added` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-270">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="a7fb7-271">K odstranění registrace certifikátu použijte `delete sslcert` příkaz:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-271">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="a7fb7-272">Referenční dokumentace pro *netsh.exe*:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-272">Reference documentation for *netsh.exe*:</span></span>

   * [<span data-ttu-id="a7fb7-273">Příkazy Netsh pro protokol HTTP (Hypertext Transfer Protocol)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-273">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](https://technet.microsoft.com/library/cc725882.aspx)
   * [<span data-ttu-id="a7fb7-274">UrlPrefix řetězce</span><span class="sxs-lookup"><span data-stu-id="a7fb7-274">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="a7fb7-275">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-275">Run the app.</span></span>

   <span data-ttu-id="a7fb7-276">Oprávnění správce nejsou nutná ke spuštění aplikace při vytváření vazby na localhost pomocí protokolu HTTP (ne HTTPS) s číslem portu větším než 1024.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-276">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="a7fb7-277">U ostatních konfigurací (například pomocí místní IP adresy nebo vazby na port 443) spusťte aplikaci s oprávněními správce.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-277">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="a7fb7-278">Aplikace odpoví na veřejnou IP adresu serveru.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-278">The app responds at the server's public IP address.</span></span> <span data-ttu-id="a7fb7-279">V tomto příkladu je server dosažitelný z Internetu na jeho veřejné IP adrese `104.214.79.47` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-279">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="a7fb7-280">V tomto příkladu se používá vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-280">A development certificate is used in this example.</span></span> <span data-ttu-id="a7fb7-281">Stránka se načte bezpečně po obejít upozornění na nedůvěryhodný certifikát v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-281">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![Okno prohlížeče zobrazující načtenou stránku indexu aplikace](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a7fb7-283">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="a7fb7-283">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a7fb7-284">Pro aplikace hostované HTTP.sys, které komunikují s požadavky z Internetu nebo podnikové sítě, se může při hostování za proxy servery a nástroji pro vyrovnávání zatížení vyžadovat další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-284">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="a7fb7-285">Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-285">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a7fb7-286">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a7fb7-286">Additional resources</span></span>

* [<span data-ttu-id="a7fb7-287">Povolit ověřování systému Windows pomocí HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a7fb7-287">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="a7fb7-288">Rozhraní API serveru HTTP</span><span class="sxs-lookup"><span data-stu-id="a7fb7-288">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="a7fb7-289">úložiště GitHubu ASPNET/HttpSysServer (zdrojový kód)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-289">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="a7fb7-290">Hostitel</span><span class="sxs-lookup"><span data-stu-id="a7fb7-290">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="a7fb7-291">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) je [webový server pro ASP.NET Core](xref:fundamentals/servers/index) , který běží jenom ve Windows.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-291">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="a7fb7-292">HTTP.sys je alternativou k serveru [Kestrel](xref:fundamentals/servers/kestrel) a nabízí některé funkce, které Kestrel neposkytují.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-292">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a7fb7-293">HTTP.sys není kompatibilní s [modulem ASP.NET Core](xref:host-and-deploy/aspnet-core-module) a nedá se použít se službou IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-293">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="a7fb7-294">HTTP.sys podporuje následující funkce:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-294">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="a7fb7-295">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="a7fb7-295">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="a7fb7-296">Sdílení portů</span><span class="sxs-lookup"><span data-stu-id="a7fb7-296">Port sharing</span></span>
* <span data-ttu-id="a7fb7-297">HTTPS s SNI</span><span class="sxs-lookup"><span data-stu-id="a7fb7-297">HTTPS with SNI</span></span>
* <span data-ttu-id="a7fb7-298">HTTP/2 přes TLS (Windows 10 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-298">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="a7fb7-299">Přímý přenos souborů</span><span class="sxs-lookup"><span data-stu-id="a7fb7-299">Direct file transmission</span></span>
* <span data-ttu-id="a7fb7-300">Ukládání odpovědí do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="a7fb7-300">Response caching</span></span>
* <span data-ttu-id="a7fb7-301">WebSockets (Windows 8 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-301">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="a7fb7-302">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-302">Supported Windows versions:</span></span>

* <span data-ttu-id="a7fb7-303">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="a7fb7-303">Windows 7 or later</span></span>
* <span data-ttu-id="a7fb7-304">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="a7fb7-304">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="a7fb7-305">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a7fb7-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="a7fb7-306">Kdy použít HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a7fb7-306">When to use HTTP.sys</span></span>

<span data-ttu-id="a7fb7-307">HTTP.sys je užitečná pro nasazení, kde:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-307">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="a7fb7-308">Server je potřeba zveřejnit přímo na internetu bez použití IIS.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-308">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys komunikuje přímo s internetem.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="a7fb7-310">Interní nasazení vyžaduje funkci, která není v Kestrel k dispozici, jako je například [ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-310">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys komunikuje přímo s interní sítí.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="a7fb7-312">HTTP.sys je Vyspělá technologie, která chrání před mnoha typy útoků a poskytuje odolnost, zabezpečení a škálovatelnost plně funkčního webového serveru.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-312">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="a7fb7-313">Služba IIS sám spouští jako naslouchací proces HTTP nad HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-313">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="a7fb7-314">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="a7fb7-314">HTTP/2 support</span></span>

<span data-ttu-id="a7fb7-315">[Protokol HTTP/2](https://httpwg.org/specs/rfc7540.html) je povolený pro aplikace ASP.NET Core, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-315">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="a7fb7-316">Windows Server 2016/Windows 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="a7fb7-316">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="a7fb7-317">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-317">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="a7fb7-318">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="a7fb7-318">TLS 1.2 or later connection</span></span>

<span data-ttu-id="a7fb7-319">Pokud je vytvořeno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-319">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="a7fb7-320">HTTP/2 je ve výchozím nastavení povolené.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-320">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="a7fb7-321">Pokud připojení HTTP/2 není navázáno, připojení se vrátí k HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-321">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="a7fb7-322">V budoucí verzi Windows budou k dispozici příznaky konfigurace protokolu HTTP/2, včetně možnosti zakázat protokol HTTP/2 s HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-322">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="a7fb7-323">Ověřování v režimu jádra pomocí protokolu Kerberos</span><span class="sxs-lookup"><span data-stu-id="a7fb7-323">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="a7fb7-324">HTTP.sys deleguje ověřování režimu jádra pomocí ověřovacího protokolu Kerberos.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-324">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="a7fb7-325">Ověřování pomocí uživatelského režimu není pro protokol Kerberos a HTTP.sys podporováno.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-325">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="a7fb7-326">Účet počítače se musí použít k dešifrování tokenu nebo lístku protokolu Kerberos, který je získaný ze služby Active Directory a který přesměruje klient na server za účelem ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-326">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="a7fb7-327">Zaregistrujte hlavní název služby (SPN) pro hostitele, nikoli uživatele aplikace.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-327">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="a7fb7-328">Jak používat HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a7fb7-328">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="a7fb7-329">Konfigurace aplikace ASP.NET Core pro použití HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a7fb7-329">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="a7fb7-330"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>Při sestavování hostitele zavolejte metodu rozšíření a určete požadované <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-330">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="a7fb7-331">Následující příklad nastaví možnosti na jejich výchozí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-331">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="a7fb7-332">Další konfigurace HTTP.sys se zpracovává prostřednictvím [nastavení registru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-332">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="a7fb7-333">**MožnostiHTTP.sys**</span><span class="sxs-lookup"><span data-stu-id="a7fb7-333">**HTTP.sys options**</span></span>

| <span data-ttu-id="a7fb7-334">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="a7fb7-334">Property</span></span> | <span data-ttu-id="a7fb7-335">Popis</span><span class="sxs-lookup"><span data-stu-id="a7fb7-335">Description</span></span> | <span data-ttu-id="a7fb7-336">Výchozí</span><span class="sxs-lookup"><span data-stu-id="a7fb7-336">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="a7fb7-337">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="a7fb7-337">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="a7fb7-338">Určuje, zda je povolen synchronní vstup/výstup pro `HttpContext.Request.Body` a `HttpContext.Response.Body` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-338">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="a7fb7-339">Ověřování. AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="a7fb7-339">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="a7fb7-340">Povoluje anonymní požadavky.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-340">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="a7fb7-341">Ověřování. schémata</span><span class="sxs-lookup"><span data-stu-id="a7fb7-341">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="a7fb7-342">Určete povolená schémata ověřování.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-342">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="a7fb7-343">Může být kdykoli změněno před vyřazením naslouchacího procesu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-343">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="a7fb7-344">Hodnoty jsou k dispozici ve [výčtu schémata AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic` , `Kerberos` , `Negotiate` , `None` a `NTLM` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-344">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="a7fb7-345">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="a7fb7-345">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="a7fb7-346">Pokusit se o ukládání do mezipaměti v [režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) pro odpovědi s oprávněnými záhlavími.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-346">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="a7fb7-347">Odpověď nesmí obsahovat `Set-Cookie` `Vary` hlavičky, ani `Pragma` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-347">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="a7fb7-348">Musí obsahovat `Cache-Control` hlavičku, která je `public` a buď `shared-max-age` `max-age` hodnota nebo, nebo `Expires` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-348">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="a7fb7-349">Maximální počet souběžných přijetí.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-349">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="a7fb7-350">5 &times; [prostředí. <br> ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-350">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="a7fb7-351">Maximální počet souběžných připojení, která se mají přijmout</span><span class="sxs-lookup"><span data-stu-id="a7fb7-351">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="a7fb7-352">Použijte `-1` pro nekonečné.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-352">Use `-1` for infinite.</span></span> <span data-ttu-id="a7fb7-353">Použijte `null` k použití nastavení pro počítač v rámci registru.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-353">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="a7fb7-354">(počítačově v šířce</span><span class="sxs-lookup"><span data-stu-id="a7fb7-354">(machine-wide</span></span><br><span data-ttu-id="a7fb7-355">nastavením</span><span class="sxs-lookup"><span data-stu-id="a7fb7-355">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="a7fb7-356">Viz část <a href="#maxrequestbodysize">MaxRequestBodySize</a> .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-356">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="a7fb7-357">30000000 bajtů</span><span class="sxs-lookup"><span data-stu-id="a7fb7-357">30000000 bytes</span></span><br><span data-ttu-id="a7fb7-358">(~ 28,6 MB)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-358">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="a7fb7-359">Maximální počet požadavků, které lze zařadit do fronty.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-359">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="a7fb7-360">1000</span><span class="sxs-lookup"><span data-stu-id="a7fb7-360">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="a7fb7-361">Určuje, jestli tělo odpovědi zapisuje, které selhalo kvůli odpojení klienta, by mělo vyvolat výjimky nebo dokončit normálně.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-361">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="a7fb7-362">(normálně dokončit)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-362">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="a7fb7-363">Vystavte <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> konfiguraci HTTP.sys, která může být v registru nakonfigurovaná taky.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-363">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="a7fb7-364">Pomocí odkazů rozhraní API získáte další informace o jednotlivých nastaveních, včetně výchozích hodnot:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-364">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="a7fb7-365">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): čas povolený pro rozhraní API serveru http, který má vyprázdnit tělo entity u připojení Keep-Alive.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-365">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="a7fb7-366">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): čas povolený pro doručení těla entity požadavku.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-366">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="a7fb7-367">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): čas povolený pro rozhraní API serveru HTTP k analýze hlavičky žádosti.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-367">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="a7fb7-368">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): čas povolený pro nečinné připojení.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-368">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="a7fb7-369">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): minimální rychlost odesílání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-369">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="a7fb7-370">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): čas, který může požadavek zůstat ve frontě požadavků předtím, než ji aplikace vybere.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-370">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="a7fb7-371">Zadejte, kam se <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> má zaregistrovat HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-371">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="a7fb7-372">Nejužitečnější je [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), který se používá k přidání předpony do kolekce.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-372">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="a7fb7-373">Ty mohou být kdykoliv změněny předtím, než se naslouchací proces vyřazuje.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-373">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="a7fb7-374">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="a7fb7-374">**MaxRequestBodySize**</span></span>

<span data-ttu-id="a7fb7-375">Maximální povolená velikost libovolného textu žádosti v bajtech</span><span class="sxs-lookup"><span data-stu-id="a7fb7-375">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="a7fb7-376">Při nastavení na `null` je maximální velikost textu požadavku neomezená.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-376">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="a7fb7-377">Toto omezení nemá žádný vliv na upgradovaná připojení, která jsou vždycky neomezená.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-377">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="a7fb7-378">Doporučená metoda pro přepsání limitu v aplikaci ASP.NET Core MVC pro jeden `IActionResult` je použít <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atribut pro metodu akce:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-378">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="a7fb7-379">Výjimka je vyvolána, pokud se aplikace pokusí nakonfigurovat limit žádosti poté, co aplikace začne číst požadavek.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-379">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="a7fb7-380">`IsReadOnly`Vlastnost může být použita k označení `MaxRequestBodySize` , zda je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-380">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="a7fb7-381">Pokud by měla aplikace přepsat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> jednotlivé požadavky, použijte <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :</span><span class="sxs-lookup"><span data-stu-id="a7fb7-381">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="a7fb7-382">Pokud používáte Visual Studio, ujistěte se, že aplikace není nakonfigurovaná tak, aby spouštěla službu IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-382">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="a7fb7-383">V aplikaci Visual Studio je výchozím spouštěcím profilem IIS Express.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-383">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="a7fb7-384">Chcete-li spustit projekt jako konzolovou aplikaci, ručně změňte vybraný profil, jak je znázorněno na následujícím snímku obrazovky:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-384">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![Vybrat profil aplikace konzoly](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="a7fb7-386">Konfigurace Windows serveru</span><span class="sxs-lookup"><span data-stu-id="a7fb7-386">Configure Windows Server</span></span>

1. <span data-ttu-id="a7fb7-387">Určete porty, které se mají otevřít pro aplikaci, a pomocí [brány Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) nebo rutiny [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) prostředí PowerShell otevřete porty brány firewall, abyste umožnili provozu HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-387">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="a7fb7-388">V následujících příkazech a konfiguraci aplikace se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-388">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="a7fb7-389">Při nasazování na virtuální počítač Azure otevřete porty ve [skupině zabezpečení sítě](/azure/virtual-machines/windows/nsg-quickstart-portal).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-389">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="a7fb7-390">V následujících příkazech a konfiguraci aplikace se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-390">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="a7fb7-391">V případě potřeby Získejte a nainstalujte certifikáty X. 509.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-391">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="a7fb7-392">V systému Windows vytvořte pomocí [rutiny prostředí PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate)certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-392">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="a7fb7-393">Nepodporovaný příklad naleznete v tématu [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-393">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="a7fb7-394">Nainstalujte certifikáty podepsané svým držitelem nebo certifikátem podepsaným certifikační autoritou do osobního úložiště **místního počítače** serveru > **Personal** .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-394">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="a7fb7-395">Pokud se jedná o [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd), nainstalujte rozhraní .NET core, .NET Framework nebo obojí (Pokud je aplikace aplikace .NET Core cílící na .NET Framework).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-395">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="a7fb7-396">**.NET Core**: Pokud aplikace vyžaduje .NET Core, Získejte a spusťte instalační program **modulu runtime .NET Core** ze [souborů ke stažení pro .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-396">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="a7fb7-397">Neinstalujte na server plnou sadu SDK.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-397">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="a7fb7-398">**.NET Framework**: Pokud aplikace vyžaduje .NET Framework, přečtěte si téma [Průvodce instalací .NET Framework](/dotnet/framework/install/).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-398">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="a7fb7-399">Nainstalujte požadovanou .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-399">Install the required .NET Framework.</span></span> <span data-ttu-id="a7fb7-400">Instalační program pro nejnovější .NET Framework je k dispozici na stránce [soubory ke stažení pro .NET Core](https://dotnet.microsoft.com/download) .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-400">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="a7fb7-401">Pokud je aplikace samostatným [nasazením](/dotnet/core/deploying/#self-contained-deployments-scd), aplikace zahrne modul runtime do svého nasazení.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-401">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="a7fb7-402">Na serveru není nutná žádná instalace rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-402">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="a7fb7-403">Konfigurace adres URL a portů v aplikaci</span><span class="sxs-lookup"><span data-stu-id="a7fb7-403">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="a7fb7-404">Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-404">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="a7fb7-405">Pokud chcete nakonfigurovat předpony a porty adresy URL, můžete použít tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-405">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="a7fb7-406">`urls`argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="a7fb7-406">`urls` command-line argument</span></span>
   * <span data-ttu-id="a7fb7-407">`ASPNETCORE_URLS`Proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="a7fb7-407">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="a7fb7-408">Následující příklad kódu ukazuje, jak používat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> s místní IP adresou serveru `10.0.0.4` na portu 443:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-408">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="a7fb7-409">Výhodou `UrlPrefixes` je, že se okamžitě generuje chybová zpráva pro nesprávně naformátované předpony.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-409">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="a7fb7-410">Nastavení v `UrlPrefixes` `UseUrls` / `urls` / `ASPNETCORE_URLS` Nastavení přepsání</span><span class="sxs-lookup"><span data-stu-id="a7fb7-410">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="a7fb7-411">Proto je výhodou `UseUrls` `urls` `ASPNETCORE_URLS` proměnné prostředí, a jednodušší přepínání mezi Kestrel a HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-411">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="a7fb7-412">HTTP.sys používá [formáty řetězce UrlPrefix HTTP serveru API](/windows/win32/http/urlprefix-strings).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-412">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="a7fb7-413">Nelze použít vazby zástupných znaků na nejvyšší úrovni ( `http://*:80/` a `http://+:80` ). **not**</span><span class="sxs-lookup"><span data-stu-id="a7fb7-413">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="a7fb7-414">Vazby na zástupné znaky na nejvyšší úrovni vytvoří chyby zabezpečení aplikace.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-414">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="a7fb7-415">To platí pro silné i slabé zástupné znaky.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-415">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="a7fb7-416">Místo zástupných znaků použijte explicitní názvy hostitelů nebo IP adresy.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-416">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="a7fb7-417">Vazba zástupných znaků subdomény (například `*.mysub.com` ) není bezpečnostní riziko, pokud ovládáte celou nadřazenou doménu (na rozdíl od `*.com` , která je zranitelná).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-417">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="a7fb7-418">Další informace najdete v [dokumentu RFC 7230: oddíl 5,4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-418">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="a7fb7-419">Předregistrujte předpony adresy URL na serveru.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-419">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="a7fb7-420">K *disnetsh.exe*je integrovaný nástroj pro konfiguraci HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-420">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="a7fb7-421">*netsh.exe* se používá k rezervaci předpon adres URL a přiřazování certifikátů X. 509.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-421">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="a7fb7-422">Nástroj vyžaduje oprávnění správce.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-422">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="a7fb7-423">K registraci adres URL aplikace použijte nástroj *netsh.exe* :</span><span class="sxs-lookup"><span data-stu-id="a7fb7-423">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="a7fb7-424">`<URL>`: Plně kvalifikované adresy URL (Uniform Resource Locator).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-424">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="a7fb7-425">Nepoužívejte vazbu zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-425">Don't use a wildcard binding.</span></span> <span data-ttu-id="a7fb7-426">Použijte platný název hostitele nebo místní IP adresu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-426">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="a7fb7-427">*Adresa URL musí obsahovat koncové lomítko.*</span><span class="sxs-lookup"><span data-stu-id="a7fb7-427">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="a7fb7-428">`<USER>`: Určuje název uživatele nebo skupiny uživatelů.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-428">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="a7fb7-429">V následujícím příkladu je místní IP adresa serveru `10.0.0.4` :</span><span class="sxs-lookup"><span data-stu-id="a7fb7-429">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="a7fb7-430">Když je adresa URL registrována, nástroj odpoví `URL reservation successfully added` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-430">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="a7fb7-431">Pokud chcete odstranit registrovanou adresu URL, použijte `delete urlacl` příkaz:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-431">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="a7fb7-432">Zaregistrujte certifikáty X. 509 na serveru.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-432">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="a7fb7-433">K registraci certifikátů pro aplikaci použijte nástroj *netsh.exe* :</span><span class="sxs-lookup"><span data-stu-id="a7fb7-433">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="a7fb7-434">`<IP>`: Určuje místní IP adresu pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-434">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="a7fb7-435">Nepoužívejte vazbu zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-435">Don't use a wildcard binding.</span></span> <span data-ttu-id="a7fb7-436">Použijte platnou IP adresu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-436">Use a valid IP address.</span></span>
   * <span data-ttu-id="a7fb7-437">`<PORT>`: Určuje port pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-437">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="a7fb7-438">`<THUMBPRINT>`: Kryptografický otisk certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-438">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="a7fb7-439">`<GUID>`: Identifikátor GUID generovaný vývojářem, který představuje aplikaci pro informativní účely.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-439">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="a7fb7-440">Pro referenční účely uložte identifikátor GUID do aplikace jako značku balíčku:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-440">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="a7fb7-441">V aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-441">In Visual Studio:</span></span>
     * <span data-ttu-id="a7fb7-442">Otevřete vlastnosti projektu aplikace tak, že kliknete pravým tlačítkem na aplikaci v **Průzkumník řešení** a vyberete **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-442">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="a7fb7-443">Vyberte kartu **balíček** .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-443">Select the **Package** tab.</span></span>
     * <span data-ttu-id="a7fb7-444">Zadejte identifikátor GUID, který jste vytvořili v poli **značky** .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-444">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="a7fb7-445">Když nepoužíváte Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-445">When not using Visual Studio:</span></span>
     * <span data-ttu-id="a7fb7-446">Otevřete soubor projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-446">Open the app's project file.</span></span>
     * <span data-ttu-id="a7fb7-447">Přidejte `<PackageTags>` vlastnost do nového nebo existujícího `<PropertyGroup>` s identifikátorem GUID, který jste vytvořili:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-447">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="a7fb7-448">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-448">In the following example:</span></span>

   * <span data-ttu-id="a7fb7-449">Místní IP adresa serveru je `10.0.0.4` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-449">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="a7fb7-450">Generátor náhodných identifikátorů GUID online poskytuje `appid` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-450">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="a7fb7-451">Když je certifikát zaregistrován, nástroj odpoví `SSL Certificate successfully added` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-451">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="a7fb7-452">K odstranění registrace certifikátu použijte `delete sslcert` příkaz:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-452">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="a7fb7-453">Referenční dokumentace pro *netsh.exe*:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-453">Reference documentation for *netsh.exe*:</span></span>

   * [<span data-ttu-id="a7fb7-454">Příkazy Netsh pro protokol HTTP (Hypertext Transfer Protocol)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-454">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](https://technet.microsoft.com/library/cc725882.aspx)
   * [<span data-ttu-id="a7fb7-455">UrlPrefix řetězce</span><span class="sxs-lookup"><span data-stu-id="a7fb7-455">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="a7fb7-456">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-456">Run the app.</span></span>

   <span data-ttu-id="a7fb7-457">Oprávnění správce nejsou nutná ke spuštění aplikace při vytváření vazby na localhost pomocí protokolu HTTP (ne HTTPS) s číslem portu větším než 1024.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-457">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="a7fb7-458">U ostatních konfigurací (například pomocí místní IP adresy nebo vazby na port 443) spusťte aplikaci s oprávněními správce.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-458">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="a7fb7-459">Aplikace odpoví na veřejnou IP adresu serveru.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-459">The app responds at the server's public IP address.</span></span> <span data-ttu-id="a7fb7-460">V tomto příkladu je server dosažitelný z Internetu na jeho veřejné IP adrese `104.214.79.47` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-460">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="a7fb7-461">V tomto příkladu se používá vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-461">A development certificate is used in this example.</span></span> <span data-ttu-id="a7fb7-462">Stránka se načte bezpečně po obejít upozornění na nedůvěryhodný certifikát v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-462">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![Okno prohlížeče zobrazující načtenou stránku indexu aplikace](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a7fb7-464">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="a7fb7-464">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a7fb7-465">Pro aplikace hostované HTTP.sys, které komunikují s požadavky z Internetu nebo podnikové sítě, se může při hostování za proxy servery a nástroji pro vyrovnávání zatížení vyžadovat další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-465">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="a7fb7-466">Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-466">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a7fb7-467">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a7fb7-467">Additional resources</span></span>

* [<span data-ttu-id="a7fb7-468">Povolit ověřování systému Windows pomocí HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a7fb7-468">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="a7fb7-469">Rozhraní API serveru HTTP</span><span class="sxs-lookup"><span data-stu-id="a7fb7-469">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="a7fb7-470">úložiště GitHubu ASPNET/HttpSysServer (zdrojový kód)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-470">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="a7fb7-471">Hostitel</span><span class="sxs-lookup"><span data-stu-id="a7fb7-471">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="a7fb7-472">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) je [webový server pro ASP.NET Core](xref:fundamentals/servers/index) , který běží jenom ve Windows.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-472">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="a7fb7-473">HTTP.sys je alternativou k serveru [Kestrel](xref:fundamentals/servers/kestrel) a nabízí některé funkce, které Kestrel neposkytují.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-473">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a7fb7-474">HTTP.sys není kompatibilní s [modulem ASP.NET Core](xref:host-and-deploy/aspnet-core-module) a nedá se použít se službou IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-474">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="a7fb7-475">HTTP.sys podporuje následující funkce:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-475">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="a7fb7-476">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="a7fb7-476">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="a7fb7-477">Sdílení portů</span><span class="sxs-lookup"><span data-stu-id="a7fb7-477">Port sharing</span></span>
* <span data-ttu-id="a7fb7-478">HTTPS s SNI</span><span class="sxs-lookup"><span data-stu-id="a7fb7-478">HTTPS with SNI</span></span>
* <span data-ttu-id="a7fb7-479">HTTP/2 přes TLS (Windows 10 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-479">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="a7fb7-480">Přímý přenos souborů</span><span class="sxs-lookup"><span data-stu-id="a7fb7-480">Direct file transmission</span></span>
* <span data-ttu-id="a7fb7-481">Ukládání odpovědí do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="a7fb7-481">Response caching</span></span>
* <span data-ttu-id="a7fb7-482">WebSockets (Windows 8 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-482">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="a7fb7-483">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-483">Supported Windows versions:</span></span>

* <span data-ttu-id="a7fb7-484">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="a7fb7-484">Windows 7 or later</span></span>
* <span data-ttu-id="a7fb7-485">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="a7fb7-485">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="a7fb7-486">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a7fb7-486">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="a7fb7-487">Kdy použít HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a7fb7-487">When to use HTTP.sys</span></span>

<span data-ttu-id="a7fb7-488">HTTP.sys je užitečná pro nasazení, kde:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-488">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="a7fb7-489">Server je potřeba zveřejnit přímo na internetu bez použití IIS.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-489">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys komunikuje přímo s internetem.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="a7fb7-491">Interní nasazení vyžaduje funkci, která není v Kestrel k dispozici, jako je například [ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-491">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys komunikuje přímo s interní sítí.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="a7fb7-493">HTTP.sys je Vyspělá technologie, která chrání před mnoha typy útoků a poskytuje odolnost, zabezpečení a škálovatelnost plně funkčního webového serveru.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-493">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="a7fb7-494">Služba IIS sám spouští jako naslouchací proces HTTP nad HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-494">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="a7fb7-495">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="a7fb7-495">HTTP/2 support</span></span>

<span data-ttu-id="a7fb7-496">[Protokol HTTP/2](https://httpwg.org/specs/rfc7540.html) je povolený pro aplikace ASP.NET Core, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-496">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="a7fb7-497">Windows Server 2016/Windows 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="a7fb7-497">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="a7fb7-498">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-498">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="a7fb7-499">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="a7fb7-499">TLS 1.2 or later connection</span></span>

<span data-ttu-id="a7fb7-500">Pokud je vytvořeno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-500">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="a7fb7-501">HTTP/2 je ve výchozím nastavení povolené.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-501">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="a7fb7-502">Pokud připojení HTTP/2 není navázáno, připojení se vrátí k HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-502">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="a7fb7-503">V budoucí verzi Windows budou k dispozici příznaky konfigurace protokolu HTTP/2, včetně možnosti zakázat protokol HTTP/2 s HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-503">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="a7fb7-504">Ověřování v režimu jádra pomocí protokolu Kerberos</span><span class="sxs-lookup"><span data-stu-id="a7fb7-504">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="a7fb7-505">HTTP.sys deleguje ověřování režimu jádra pomocí ověřovacího protokolu Kerberos.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-505">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="a7fb7-506">Ověřování pomocí uživatelského režimu není pro protokol Kerberos a HTTP.sys podporováno.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-506">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="a7fb7-507">Účet počítače se musí použít k dešifrování tokenu nebo lístku protokolu Kerberos, který je získaný ze služby Active Directory a který přesměruje klient na server za účelem ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-507">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="a7fb7-508">Zaregistrujte hlavní název služby (SPN) pro hostitele, nikoli uživatele aplikace.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-508">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="a7fb7-509">Jak používat HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a7fb7-509">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="a7fb7-510">Konfigurace aplikace ASP.NET Core pro použití HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a7fb7-510">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="a7fb7-511">Odkaz na balíček v souboru projektu není vyžadován při použití [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) ([NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-511">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="a7fb7-512">Pokud nepoužíváte `Microsoft.AspNetCore.App` Metapackage, přidejte odkaz na balíček do [Microsoft. AspNetCore. Server. HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-512">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="a7fb7-513"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>Při sestavování hostitele zavolejte metodu rozšíření a určete požadované <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-513">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="a7fb7-514">Následující příklad nastaví možnosti na jejich výchozí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-514">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="a7fb7-515">Další konfigurace HTTP.sys se zpracovává prostřednictvím [nastavení registru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-515">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="a7fb7-516">**MožnostiHTTP.sys**</span><span class="sxs-lookup"><span data-stu-id="a7fb7-516">**HTTP.sys options**</span></span>

| <span data-ttu-id="a7fb7-517">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="a7fb7-517">Property</span></span> | <span data-ttu-id="a7fb7-518">Popis</span><span class="sxs-lookup"><span data-stu-id="a7fb7-518">Description</span></span> | <span data-ttu-id="a7fb7-519">Výchozí</span><span class="sxs-lookup"><span data-stu-id="a7fb7-519">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="a7fb7-520">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="a7fb7-520">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="a7fb7-521">Určuje, zda je povolen synchronní vstup/výstup pro `HttpContext.Request.Body` a `HttpContext.Response.Body` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-521">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="a7fb7-522">Ověřování. AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="a7fb7-522">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="a7fb7-523">Povoluje anonymní požadavky.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-523">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="a7fb7-524">Ověřování. schémata</span><span class="sxs-lookup"><span data-stu-id="a7fb7-524">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="a7fb7-525">Určete povolená schémata ověřování.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-525">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="a7fb7-526">Může být kdykoli změněno před vyřazením naslouchacího procesu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-526">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="a7fb7-527">Hodnoty jsou k dispozici ve [výčtu schémata AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic` , `Kerberos` , `Negotiate` , `None` a `NTLM` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-527">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="a7fb7-528">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="a7fb7-528">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="a7fb7-529">Pokusit se o ukládání do mezipaměti v [režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) pro odpovědi s oprávněnými záhlavími.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-529">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="a7fb7-530">Odpověď nesmí obsahovat `Set-Cookie` `Vary` hlavičky, ani `Pragma` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-530">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="a7fb7-531">Musí obsahovat `Cache-Control` hlavičku, která je `public` a buď `shared-max-age` `max-age` hodnota nebo, nebo `Expires` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-531">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="a7fb7-532">Maximální počet souběžných přijetí.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-532">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="a7fb7-533">5 &times; [prostředí. <br> ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-533">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="a7fb7-534">Maximální počet souběžných připojení, která se mají přijmout</span><span class="sxs-lookup"><span data-stu-id="a7fb7-534">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="a7fb7-535">Použijte `-1` pro nekonečné.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-535">Use `-1` for infinite.</span></span> <span data-ttu-id="a7fb7-536">Použijte `null` k použití nastavení pro počítač v rámci registru.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-536">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="a7fb7-537">(počítačově v šířce</span><span class="sxs-lookup"><span data-stu-id="a7fb7-537">(machine-wide</span></span><br><span data-ttu-id="a7fb7-538">nastavením</span><span class="sxs-lookup"><span data-stu-id="a7fb7-538">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="a7fb7-539">Viz část <a href="#maxrequestbodysize">MaxRequestBodySize</a> .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-539">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="a7fb7-540">30000000 bajtů</span><span class="sxs-lookup"><span data-stu-id="a7fb7-540">30000000 bytes</span></span><br><span data-ttu-id="a7fb7-541">(~ 28,6 MB)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-541">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="a7fb7-542">Maximální počet požadavků, které lze zařadit do fronty.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-542">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="a7fb7-543">1000</span><span class="sxs-lookup"><span data-stu-id="a7fb7-543">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="a7fb7-544">Určuje, jestli tělo odpovědi zapisuje, které selhalo kvůli odpojení klienta, by mělo vyvolat výjimky nebo dokončit normálně.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-544">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="a7fb7-545">(normálně dokončit)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-545">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="a7fb7-546">Vystavte <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> konfiguraci HTTP.sys, která může být v registru nakonfigurovaná taky.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-546">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="a7fb7-547">Pomocí odkazů rozhraní API získáte další informace o jednotlivých nastaveních, včetně výchozích hodnot:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-547">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="a7fb7-548">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): čas povolený pro rozhraní API serveru http, který má vyprázdnit tělo entity u připojení Keep-Alive.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-548">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="a7fb7-549">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): čas povolený pro doručení těla entity požadavku.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-549">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="a7fb7-550">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): čas povolený pro rozhraní API serveru HTTP k analýze hlavičky žádosti.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-550">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="a7fb7-551">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): čas povolený pro nečinné připojení.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-551">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="a7fb7-552">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): minimální rychlost odesílání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-552">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="a7fb7-553">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): čas, který může požadavek zůstat ve frontě požadavků předtím, než ji aplikace vybere.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-553">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="a7fb7-554">Zadejte, kam se <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> má zaregistrovat HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-554">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="a7fb7-555">Nejužitečnější je [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), který se používá k přidání předpony do kolekce.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-555">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="a7fb7-556">Ty mohou být kdykoliv změněny předtím, než se naslouchací proces vyřazuje.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-556">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="a7fb7-557">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="a7fb7-557">**MaxRequestBodySize**</span></span>

<span data-ttu-id="a7fb7-558">Maximální povolená velikost libovolného textu žádosti v bajtech</span><span class="sxs-lookup"><span data-stu-id="a7fb7-558">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="a7fb7-559">Při nastavení na `null` je maximální velikost textu požadavku neomezená.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-559">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="a7fb7-560">Toto omezení nemá žádný vliv na upgradovaná připojení, která jsou vždycky neomezená.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-560">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="a7fb7-561">Doporučená metoda pro přepsání limitu v aplikaci ASP.NET Core MVC pro jeden `IActionResult` je použít <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atribut pro metodu akce:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-561">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="a7fb7-562">Výjimka je vyvolána, pokud se aplikace pokusí nakonfigurovat limit žádosti poté, co aplikace začne číst požadavek.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-562">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="a7fb7-563">`IsReadOnly`Vlastnost může být použita k označení `MaxRequestBodySize` , zda je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-563">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="a7fb7-564">Pokud by měla aplikace přepsat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> jednotlivé požadavky, použijte <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :</span><span class="sxs-lookup"><span data-stu-id="a7fb7-564">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="a7fb7-565">Pokud používáte Visual Studio, ujistěte se, že aplikace není nakonfigurovaná tak, aby spouštěla službu IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-565">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="a7fb7-566">V aplikaci Visual Studio je výchozím spouštěcím profilem IIS Express.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-566">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="a7fb7-567">Chcete-li spustit projekt jako konzolovou aplikaci, ručně změňte vybraný profil, jak je znázorněno na následujícím snímku obrazovky:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-567">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![Vybrat profil aplikace konzoly](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="a7fb7-569">Konfigurace Windows serveru</span><span class="sxs-lookup"><span data-stu-id="a7fb7-569">Configure Windows Server</span></span>

1. <span data-ttu-id="a7fb7-570">Určete porty, které se mají otevřít pro aplikaci, a pomocí [brány Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) nebo rutiny [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) prostředí PowerShell otevřete porty brány firewall, abyste umožnili provozu HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-570">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="a7fb7-571">V následujících příkazech a konfiguraci aplikace se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-571">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="a7fb7-572">Při nasazování na virtuální počítač Azure otevřete porty ve [skupině zabezpečení sítě](/azure/virtual-machines/windows/nsg-quickstart-portal).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-572">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="a7fb7-573">V následujících příkazech a konfiguraci aplikace se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-573">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="a7fb7-574">V případě potřeby Získejte a nainstalujte certifikáty X. 509.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-574">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="a7fb7-575">V systému Windows vytvořte pomocí [rutiny prostředí PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate)certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-575">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="a7fb7-576">Nepodporovaný příklad naleznete v tématu [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-576">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="a7fb7-577">Nainstalujte certifikáty podepsané svým držitelem nebo certifikátem podepsaným certifikační autoritou do osobního úložiště **místního počítače** serveru > **Personal** .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-577">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="a7fb7-578">Pokud se jedná o [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd), nainstalujte rozhraní .NET core, .NET Framework nebo obojí (Pokud je aplikace aplikace .NET Core cílící na .NET Framework).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-578">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="a7fb7-579">**.NET Core**: Pokud aplikace vyžaduje .NET Core, Získejte a spusťte instalační program **modulu runtime .NET Core** ze [souborů ke stažení pro .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-579">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="a7fb7-580">Neinstalujte na server plnou sadu SDK.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-580">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="a7fb7-581">**.NET Framework**: Pokud aplikace vyžaduje .NET Framework, přečtěte si téma [Průvodce instalací .NET Framework](/dotnet/framework/install/).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-581">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="a7fb7-582">Nainstalujte požadovanou .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-582">Install the required .NET Framework.</span></span> <span data-ttu-id="a7fb7-583">Instalační program pro nejnovější .NET Framework je k dispozici na stránce [soubory ke stažení pro .NET Core](https://dotnet.microsoft.com/download) .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-583">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="a7fb7-584">Pokud je aplikace samostatným [nasazením](/dotnet/core/deploying/#self-contained-deployments-scd), aplikace zahrne modul runtime do svého nasazení.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-584">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="a7fb7-585">Na serveru není nutná žádná instalace rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-585">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="a7fb7-586">Konfigurace adres URL a portů v aplikaci</span><span class="sxs-lookup"><span data-stu-id="a7fb7-586">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="a7fb7-587">Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-587">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="a7fb7-588">Pokud chcete nakonfigurovat předpony a porty adresy URL, můžete použít tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-588">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="a7fb7-589">`urls`argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="a7fb7-589">`urls` command-line argument</span></span>
   * <span data-ttu-id="a7fb7-590">`ASPNETCORE_URLS`Proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="a7fb7-590">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="a7fb7-591">Následující příklad kódu ukazuje, jak používat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> s místní IP adresou serveru `10.0.0.4` na portu 443:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-591">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="a7fb7-592">Výhodou `UrlPrefixes` je, že se okamžitě generuje chybová zpráva pro nesprávně naformátované předpony.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-592">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="a7fb7-593">Nastavení v `UrlPrefixes` `UseUrls` / `urls` / `ASPNETCORE_URLS` Nastavení přepsání</span><span class="sxs-lookup"><span data-stu-id="a7fb7-593">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="a7fb7-594">Proto je výhodou `UseUrls` `urls` `ASPNETCORE_URLS` proměnné prostředí, a jednodušší přepínání mezi Kestrel a HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-594">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="a7fb7-595">HTTP.sys používá [formáty řetězce UrlPrefix HTTP serveru API](/windows/win32/http/urlprefix-strings).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-595">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="a7fb7-596">Nelze použít vazby zástupných znaků na nejvyšší úrovni ( `http://*:80/` a `http://+:80` ). **not**</span><span class="sxs-lookup"><span data-stu-id="a7fb7-596">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="a7fb7-597">Vazby na zástupné znaky na nejvyšší úrovni vytvoří chyby zabezpečení aplikace.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-597">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="a7fb7-598">To platí pro silné i slabé zástupné znaky.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-598">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="a7fb7-599">Místo zástupných znaků použijte explicitní názvy hostitelů nebo IP adresy.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-599">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="a7fb7-600">Vazba zástupných znaků subdomény (například `*.mysub.com` ) není bezpečnostní riziko, pokud ovládáte celou nadřazenou doménu (na rozdíl od `*.com` , která je zranitelná).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-600">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="a7fb7-601">Další informace najdete v [dokumentu RFC 7230: oddíl 5,4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-601">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="a7fb7-602">Předregistrujte předpony adresy URL na serveru.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-602">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="a7fb7-603">K *disnetsh.exe*je integrovaný nástroj pro konfiguraci HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-603">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="a7fb7-604">*netsh.exe* se používá k rezervaci předpon adres URL a přiřazování certifikátů X. 509.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-604">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="a7fb7-605">Nástroj vyžaduje oprávnění správce.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-605">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="a7fb7-606">K registraci adres URL aplikace použijte nástroj *netsh.exe* :</span><span class="sxs-lookup"><span data-stu-id="a7fb7-606">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="a7fb7-607">`<URL>`: Plně kvalifikované adresy URL (Uniform Resource Locator).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-607">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="a7fb7-608">Nepoužívejte vazbu zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-608">Don't use a wildcard binding.</span></span> <span data-ttu-id="a7fb7-609">Použijte platný název hostitele nebo místní IP adresu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-609">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="a7fb7-610">*Adresa URL musí obsahovat koncové lomítko.*</span><span class="sxs-lookup"><span data-stu-id="a7fb7-610">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="a7fb7-611">`<USER>`: Určuje název uživatele nebo skupiny uživatelů.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-611">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="a7fb7-612">V následujícím příkladu je místní IP adresa serveru `10.0.0.4` :</span><span class="sxs-lookup"><span data-stu-id="a7fb7-612">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="a7fb7-613">Když je adresa URL registrována, nástroj odpoví `URL reservation successfully added` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-613">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="a7fb7-614">Pokud chcete odstranit registrovanou adresu URL, použijte `delete urlacl` příkaz:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-614">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="a7fb7-615">Zaregistrujte certifikáty X. 509 na serveru.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-615">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="a7fb7-616">K registraci certifikátů pro aplikaci použijte nástroj *netsh.exe* :</span><span class="sxs-lookup"><span data-stu-id="a7fb7-616">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="a7fb7-617">`<IP>`: Určuje místní IP adresu pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-617">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="a7fb7-618">Nepoužívejte vazbu zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-618">Don't use a wildcard binding.</span></span> <span data-ttu-id="a7fb7-619">Použijte platnou IP adresu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-619">Use a valid IP address.</span></span>
   * <span data-ttu-id="a7fb7-620">`<PORT>`: Určuje port pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-620">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="a7fb7-621">`<THUMBPRINT>`: Kryptografický otisk certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-621">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="a7fb7-622">`<GUID>`: Identifikátor GUID generovaný vývojářem, který představuje aplikaci pro informativní účely.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-622">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="a7fb7-623">Pro referenční účely uložte identifikátor GUID do aplikace jako značku balíčku:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-623">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="a7fb7-624">V aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-624">In Visual Studio:</span></span>
     * <span data-ttu-id="a7fb7-625">Otevřete vlastnosti projektu aplikace tak, že kliknete pravým tlačítkem na aplikaci v **Průzkumník řešení** a vyberete **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-625">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="a7fb7-626">Vyberte kartu **balíček** .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-626">Select the **Package** tab.</span></span>
     * <span data-ttu-id="a7fb7-627">Zadejte identifikátor GUID, který jste vytvořili v poli **značky** .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-627">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="a7fb7-628">Když nepoužíváte Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-628">When not using Visual Studio:</span></span>
     * <span data-ttu-id="a7fb7-629">Otevřete soubor projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-629">Open the app's project file.</span></span>
     * <span data-ttu-id="a7fb7-630">Přidejte `<PackageTags>` vlastnost do nového nebo existujícího `<PropertyGroup>` s identifikátorem GUID, který jste vytvořili:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-630">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="a7fb7-631">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-631">In the following example:</span></span>

   * <span data-ttu-id="a7fb7-632">Místní IP adresa serveru je `10.0.0.4` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-632">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="a7fb7-633">Generátor náhodných identifikátorů GUID online poskytuje `appid` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-633">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="a7fb7-634">Když je certifikát zaregistrován, nástroj odpoví `SSL Certificate successfully added` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-634">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="a7fb7-635">K odstranění registrace certifikátu použijte `delete sslcert` příkaz:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-635">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="a7fb7-636">Referenční dokumentace pro *netsh.exe*:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-636">Reference documentation for *netsh.exe*:</span></span>

   * [<span data-ttu-id="a7fb7-637">Příkazy Netsh pro protokol HTTP (Hypertext Transfer Protocol)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-637">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](https://technet.microsoft.com/library/cc725882.aspx)
   * [<span data-ttu-id="a7fb7-638">UrlPrefix řetězce</span><span class="sxs-lookup"><span data-stu-id="a7fb7-638">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="a7fb7-639">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-639">Run the app.</span></span>

   <span data-ttu-id="a7fb7-640">Oprávnění správce nejsou nutná ke spuštění aplikace při vytváření vazby na localhost pomocí protokolu HTTP (ne HTTPS) s číslem portu větším než 1024.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-640">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="a7fb7-641">U ostatních konfigurací (například pomocí místní IP adresy nebo vazby na port 443) spusťte aplikaci s oprávněními správce.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-641">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="a7fb7-642">Aplikace odpoví na veřejnou IP adresu serveru.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-642">The app responds at the server's public IP address.</span></span> <span data-ttu-id="a7fb7-643">V tomto příkladu je server dosažitelný z Internetu na jeho veřejné IP adrese `104.214.79.47` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-643">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="a7fb7-644">V tomto příkladu se používá vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-644">A development certificate is used in this example.</span></span> <span data-ttu-id="a7fb7-645">Stránka se načte bezpečně po obejít upozornění na nedůvěryhodný certifikát v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-645">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![Okno prohlížeče zobrazující načtenou stránku indexu aplikace](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a7fb7-647">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="a7fb7-647">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a7fb7-648">Pro aplikace hostované HTTP.sys, které komunikují s požadavky z Internetu nebo podnikové sítě, se může při hostování za proxy servery a nástroji pro vyrovnávání zatížení vyžadovat další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-648">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="a7fb7-649">Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-649">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a7fb7-650">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a7fb7-650">Additional resources</span></span>

* [<span data-ttu-id="a7fb7-651">Povolit ověřování systému Windows pomocí HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a7fb7-651">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="a7fb7-652">Rozhraní API serveru HTTP</span><span class="sxs-lookup"><span data-stu-id="a7fb7-652">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="a7fb7-653">úložiště GitHubu ASPNET/HttpSysServer (zdrojový kód)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-653">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="a7fb7-654">Hostitel</span><span class="sxs-lookup"><span data-stu-id="a7fb7-654">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="a7fb7-655">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) je [webový server pro ASP.NET Core](xref:fundamentals/servers/index) , který běží jenom ve Windows.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-655">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="a7fb7-656">HTTP.sys je alternativou k serveru [Kestrel](xref:fundamentals/servers/kestrel) a nabízí některé funkce, které Kestrel neposkytují.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-656">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a7fb7-657">HTTP.sys není kompatibilní s [modulem ASP.NET Core](xref:host-and-deploy/aspnet-core-module) a nedá se použít se službou IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-657">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="a7fb7-658">HTTP.sys podporuje následující funkce:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-658">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="a7fb7-659">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="a7fb7-659">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="a7fb7-660">Sdílení portů</span><span class="sxs-lookup"><span data-stu-id="a7fb7-660">Port sharing</span></span>
* <span data-ttu-id="a7fb7-661">HTTPS s SNI</span><span class="sxs-lookup"><span data-stu-id="a7fb7-661">HTTPS with SNI</span></span>
* <span data-ttu-id="a7fb7-662">HTTP/2 přes TLS (Windows 10 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-662">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="a7fb7-663">Přímý přenos souborů</span><span class="sxs-lookup"><span data-stu-id="a7fb7-663">Direct file transmission</span></span>
* <span data-ttu-id="a7fb7-664">Ukládání odpovědí do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="a7fb7-664">Response caching</span></span>
* <span data-ttu-id="a7fb7-665">WebSockets (Windows 8 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-665">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="a7fb7-666">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-666">Supported Windows versions:</span></span>

* <span data-ttu-id="a7fb7-667">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="a7fb7-667">Windows 7 or later</span></span>
* <span data-ttu-id="a7fb7-668">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="a7fb7-668">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="a7fb7-669">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a7fb7-669">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="a7fb7-670">Kdy použít HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a7fb7-670">When to use HTTP.sys</span></span>

<span data-ttu-id="a7fb7-671">HTTP.sys je užitečná pro nasazení, kde:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-671">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="a7fb7-672">Server je potřeba zveřejnit přímo na internetu bez použití IIS.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-672">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys komunikuje přímo s internetem.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="a7fb7-674">Interní nasazení vyžaduje funkci, která není v Kestrel k dispozici, jako je například [ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-674">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys komunikuje přímo s interní sítí.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="a7fb7-676">HTTP.sys je Vyspělá technologie, která chrání před mnoha typy útoků a poskytuje odolnost, zabezpečení a škálovatelnost plně funkčního webového serveru.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-676">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="a7fb7-677">Služba IIS sám spouští jako naslouchací proces HTTP nad HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-677">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="a7fb7-678">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="a7fb7-678">HTTP/2 support</span></span>

<span data-ttu-id="a7fb7-679">[Protokol HTTP/2](https://httpwg.org/specs/rfc7540.html) je povolený pro aplikace ASP.NET Core, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-679">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="a7fb7-680">Windows Server 2016/Windows 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="a7fb7-680">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="a7fb7-681">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-681">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="a7fb7-682">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="a7fb7-682">TLS 1.2 or later connection</span></span>

<span data-ttu-id="a7fb7-683">Pokud je vytvořeno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/1.1` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-683">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="a7fb7-684">HTTP/2 je ve výchozím nastavení povolené.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-684">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="a7fb7-685">Pokud připojení HTTP/2 není navázáno, připojení se vrátí k HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-685">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="a7fb7-686">V budoucí verzi Windows budou k dispozici příznaky konfigurace protokolu HTTP/2, včetně možnosti zakázat protokol HTTP/2 s HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-686">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="a7fb7-687">Ověřování v režimu jádra pomocí protokolu Kerberos</span><span class="sxs-lookup"><span data-stu-id="a7fb7-687">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="a7fb7-688">HTTP.sys deleguje ověřování režimu jádra pomocí ověřovacího protokolu Kerberos.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-688">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="a7fb7-689">Ověřování pomocí uživatelského režimu není pro protokol Kerberos a HTTP.sys podporováno.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-689">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="a7fb7-690">Účet počítače se musí použít k dešifrování tokenu nebo lístku protokolu Kerberos, který je získaný ze služby Active Directory a který přesměruje klient na server za účelem ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-690">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="a7fb7-691">Zaregistrujte hlavní název služby (SPN) pro hostitele, nikoli uživatele aplikace.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-691">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="a7fb7-692">Jak používat HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a7fb7-692">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="a7fb7-693">Konfigurace aplikace ASP.NET Core pro použití HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a7fb7-693">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="a7fb7-694">Odkaz na balíček v souboru projektu není vyžadován při použití [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) ([NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-694">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="a7fb7-695">Pokud nepoužíváte `Microsoft.AspNetCore.App` Metapackage, přidejte odkaz na balíček do [Microsoft. AspNetCore. Server. HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-695">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="a7fb7-696"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>Při sestavování hostitele zavolejte metodu rozšíření a určete požadované <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-696">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="a7fb7-697">Následující příklad nastaví možnosti na jejich výchozí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-697">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="a7fb7-698">Další konfigurace HTTP.sys se zpracovává prostřednictvím [nastavení registru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-698">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="a7fb7-699">**MožnostiHTTP.sys**</span><span class="sxs-lookup"><span data-stu-id="a7fb7-699">**HTTP.sys options**</span></span>

| <span data-ttu-id="a7fb7-700">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="a7fb7-700">Property</span></span> | <span data-ttu-id="a7fb7-701">Popis</span><span class="sxs-lookup"><span data-stu-id="a7fb7-701">Description</span></span> | <span data-ttu-id="a7fb7-702">Výchozí</span><span class="sxs-lookup"><span data-stu-id="a7fb7-702">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="a7fb7-703">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="a7fb7-703">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="a7fb7-704">Určuje, zda je povolen synchronní vstup/výstup pro `HttpContext.Request.Body` a `HttpContext.Response.Body` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-704">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="a7fb7-705">Ověřování. AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="a7fb7-705">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="a7fb7-706">Povoluje anonymní požadavky.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-706">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="a7fb7-707">Ověřování. schémata</span><span class="sxs-lookup"><span data-stu-id="a7fb7-707">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="a7fb7-708">Určete povolená schémata ověřování.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-708">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="a7fb7-709">Může být kdykoli změněno před vyřazením naslouchacího procesu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-709">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="a7fb7-710">Hodnoty jsou k dispozici ve [výčtu schémata AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic` , `Kerberos` , `Negotiate` , `None` a `NTLM` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-710">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="a7fb7-711">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="a7fb7-711">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="a7fb7-712">Pokusit se o ukládání do mezipaměti v [režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) pro odpovědi s oprávněnými záhlavími.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-712">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="a7fb7-713">Odpověď nesmí obsahovat `Set-Cookie` `Vary` hlavičky, ani `Pragma` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-713">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="a7fb7-714">Musí obsahovat `Cache-Control` hlavičku, která je `public` a buď `shared-max-age` `max-age` hodnota nebo, nebo `Expires` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-714">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="a7fb7-715">Maximální počet souběžných přijetí.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-715">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="a7fb7-716">5 &times; [prostředí. <br> ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-716">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="a7fb7-717">Maximální počet souběžných připojení, která se mají přijmout</span><span class="sxs-lookup"><span data-stu-id="a7fb7-717">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="a7fb7-718">Použijte `-1` pro nekonečné.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-718">Use `-1` for infinite.</span></span> <span data-ttu-id="a7fb7-719">Použijte `null` k použití nastavení pro počítač v rámci registru.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-719">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="a7fb7-720">(počítačově v šířce</span><span class="sxs-lookup"><span data-stu-id="a7fb7-720">(machine-wide</span></span><br><span data-ttu-id="a7fb7-721">nastavením</span><span class="sxs-lookup"><span data-stu-id="a7fb7-721">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="a7fb7-722">Viz část <a href="#maxrequestbodysize">MaxRequestBodySize</a> .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-722">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="a7fb7-723">30000000 bajtů</span><span class="sxs-lookup"><span data-stu-id="a7fb7-723">30000000 bytes</span></span><br><span data-ttu-id="a7fb7-724">(~ 28,6 MB)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-724">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="a7fb7-725">Maximální počet požadavků, které lze zařadit do fronty.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-725">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="a7fb7-726">1000</span><span class="sxs-lookup"><span data-stu-id="a7fb7-726">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="a7fb7-727">Určuje, jestli tělo odpovědi zapisuje, které selhalo kvůli odpojení klienta, by mělo vyvolat výjimky nebo dokončit normálně.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-727">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="a7fb7-728">(normálně dokončit)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-728">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="a7fb7-729">Vystavte <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> konfiguraci HTTP.sys, která může být v registru nakonfigurovaná taky.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-729">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="a7fb7-730">Pomocí odkazů rozhraní API získáte další informace o jednotlivých nastaveních, včetně výchozích hodnot:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-730">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="a7fb7-731">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): čas povolený pro rozhraní API serveru http, který má vyprázdnit tělo entity u připojení Keep-Alive.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-731">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="a7fb7-732">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): čas povolený pro doručení těla entity požadavku.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-732">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="a7fb7-733">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): čas povolený pro rozhraní API serveru HTTP k analýze hlavičky žádosti.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-733">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="a7fb7-734">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): čas povolený pro nečinné připojení.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-734">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="a7fb7-735">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): minimální rychlost odesílání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-735">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="a7fb7-736">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): čas, který může požadavek zůstat ve frontě požadavků předtím, než ji aplikace vybere.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-736">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="a7fb7-737">Zadejte, kam se <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> má zaregistrovat HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-737">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="a7fb7-738">Nejužitečnější je [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), který se používá k přidání předpony do kolekce.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-738">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="a7fb7-739">Ty mohou být kdykoliv změněny předtím, než se naslouchací proces vyřazuje.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-739">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="a7fb7-740">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="a7fb7-740">**MaxRequestBodySize**</span></span>

<span data-ttu-id="a7fb7-741">Maximální povolená velikost libovolného textu žádosti v bajtech</span><span class="sxs-lookup"><span data-stu-id="a7fb7-741">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="a7fb7-742">Při nastavení na `null` je maximální velikost textu požadavku neomezená.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-742">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="a7fb7-743">Toto omezení nemá žádný vliv na upgradovaná připojení, která jsou vždycky neomezená.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-743">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="a7fb7-744">Doporučená metoda pro přepsání limitu v aplikaci ASP.NET Core MVC pro jeden `IActionResult` je použít <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atribut pro metodu akce:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-744">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="a7fb7-745">Výjimka je vyvolána, pokud se aplikace pokusí nakonfigurovat limit žádosti poté, co aplikace začne číst požadavek.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-745">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="a7fb7-746">`IsReadOnly`Vlastnost může být použita k označení `MaxRequestBodySize` , zda je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-746">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="a7fb7-747">Pokud by měla aplikace přepsat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> jednotlivé požadavky, použijte <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :</span><span class="sxs-lookup"><span data-stu-id="a7fb7-747">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="a7fb7-748">Pokud používáte Visual Studio, ujistěte se, že aplikace není nakonfigurovaná tak, aby spouštěla službu IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-748">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="a7fb7-749">V aplikaci Visual Studio je výchozím spouštěcím profilem IIS Express.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-749">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="a7fb7-750">Chcete-li spustit projekt jako konzolovou aplikaci, ručně změňte vybraný profil, jak je znázorněno na následujícím snímku obrazovky:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-750">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![Vybrat profil aplikace konzoly](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="a7fb7-752">Konfigurace Windows serveru</span><span class="sxs-lookup"><span data-stu-id="a7fb7-752">Configure Windows Server</span></span>

1. <span data-ttu-id="a7fb7-753">Určete porty, které se mají otevřít pro aplikaci, a pomocí [brány Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) nebo rutiny [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) prostředí PowerShell otevřete porty brány firewall, abyste umožnili provozu HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-753">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="a7fb7-754">V následujících příkazech a konfiguraci aplikace se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-754">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="a7fb7-755">Při nasazování na virtuální počítač Azure otevřete porty ve [skupině zabezpečení sítě](/azure/virtual-machines/windows/nsg-quickstart-portal).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-755">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="a7fb7-756">V následujících příkazech a konfiguraci aplikace se používá port 443.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-756">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="a7fb7-757">V případě potřeby Získejte a nainstalujte certifikáty X. 509.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-757">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="a7fb7-758">V systému Windows vytvořte pomocí [rutiny prostředí PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate)certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-758">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="a7fb7-759">Nepodporovaný příklad naleznete v tématu [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-759">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="a7fb7-760">Nainstalujte certifikáty podepsané svým držitelem nebo certifikátem podepsaným certifikační autoritou do osobního úložiště **místního počítače** serveru > **Personal** .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-760">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="a7fb7-761">Pokud se jedná o [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd), nainstalujte rozhraní .NET core, .NET Framework nebo obojí (Pokud je aplikace aplikace .NET Core cílící na .NET Framework).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-761">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="a7fb7-762">**.NET Core**: Pokud aplikace vyžaduje .NET Core, Získejte a spusťte instalační program **modulu runtime .NET Core** ze [souborů ke stažení pro .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-762">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="a7fb7-763">Neinstalujte na server plnou sadu SDK.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-763">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="a7fb7-764">**.NET Framework**: Pokud aplikace vyžaduje .NET Framework, přečtěte si téma [Průvodce instalací .NET Framework](/dotnet/framework/install/).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-764">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="a7fb7-765">Nainstalujte požadovanou .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-765">Install the required .NET Framework.</span></span> <span data-ttu-id="a7fb7-766">Instalační program pro nejnovější .NET Framework je k dispozici na stránce [soubory ke stažení pro .NET Core](https://dotnet.microsoft.com/download) .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-766">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="a7fb7-767">Pokud je aplikace samostatným [nasazením](/dotnet/core/deploying/#self-contained-deployments-scd), aplikace zahrne modul runtime do svého nasazení.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-767">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="a7fb7-768">Na serveru není nutná žádná instalace rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-768">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="a7fb7-769">Konfigurace adres URL a portů v aplikaci</span><span class="sxs-lookup"><span data-stu-id="a7fb7-769">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="a7fb7-770">Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-770">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="a7fb7-771">Pokud chcete nakonfigurovat předpony a porty adresy URL, můžete použít tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-771">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="a7fb7-772">`urls`argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="a7fb7-772">`urls` command-line argument</span></span>
   * <span data-ttu-id="a7fb7-773">`ASPNETCORE_URLS`Proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="a7fb7-773">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="a7fb7-774">Následující příklad kódu ukazuje, jak používat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> s místní IP adresou serveru `10.0.0.4` na portu 443:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-774">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="a7fb7-775">Výhodou `UrlPrefixes` je, že se okamžitě generuje chybová zpráva pro nesprávně naformátované předpony.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-775">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="a7fb7-776">Nastavení v `UrlPrefixes` `UseUrls` / `urls` / `ASPNETCORE_URLS` Nastavení přepsání</span><span class="sxs-lookup"><span data-stu-id="a7fb7-776">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="a7fb7-777">Proto je výhodou `UseUrls` `urls` `ASPNETCORE_URLS` proměnné prostředí, a jednodušší přepínání mezi Kestrel a HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-777">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="a7fb7-778">HTTP.sys používá [formáty řetězce UrlPrefix HTTP serveru API](/windows/win32/http/urlprefix-strings).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-778">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="a7fb7-779">Nelze použít vazby zástupných znaků na nejvyšší úrovni ( `http://*:80/` a `http://+:80` ). **not**</span><span class="sxs-lookup"><span data-stu-id="a7fb7-779">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="a7fb7-780">Vazby na zástupné znaky na nejvyšší úrovni vytvoří chyby zabezpečení aplikace.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-780">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="a7fb7-781">To platí pro silné i slabé zástupné znaky.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-781">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="a7fb7-782">Místo zástupných znaků použijte explicitní názvy hostitelů nebo IP adresy.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-782">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="a7fb7-783">Vazba zástupných znaků subdomény (například `*.mysub.com` ) není bezpečnostní riziko, pokud ovládáte celou nadřazenou doménu (na rozdíl od `*.com` , která je zranitelná).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-783">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="a7fb7-784">Další informace najdete v [dokumentu RFC 7230: oddíl 5,4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-784">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="a7fb7-785">Předregistrujte předpony adresy URL na serveru.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-785">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="a7fb7-786">K *disnetsh.exe*je integrovaný nástroj pro konfiguraci HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-786">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="a7fb7-787">*netsh.exe* se používá k rezervaci předpon adres URL a přiřazování certifikátů X. 509.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-787">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="a7fb7-788">Nástroj vyžaduje oprávnění správce.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-788">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="a7fb7-789">K registraci adres URL aplikace použijte nástroj *netsh.exe* :</span><span class="sxs-lookup"><span data-stu-id="a7fb7-789">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="a7fb7-790">`<URL>`: Plně kvalifikované adresy URL (Uniform Resource Locator).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-790">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="a7fb7-791">Nepoužívejte vazbu zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-791">Don't use a wildcard binding.</span></span> <span data-ttu-id="a7fb7-792">Použijte platný název hostitele nebo místní IP adresu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-792">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="a7fb7-793">*Adresa URL musí obsahovat koncové lomítko.*</span><span class="sxs-lookup"><span data-stu-id="a7fb7-793">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="a7fb7-794">`<USER>`: Určuje název uživatele nebo skupiny uživatelů.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-794">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="a7fb7-795">V následujícím příkladu je místní IP adresa serveru `10.0.0.4` :</span><span class="sxs-lookup"><span data-stu-id="a7fb7-795">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="a7fb7-796">Když je adresa URL registrována, nástroj odpoví `URL reservation successfully added` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-796">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="a7fb7-797">Pokud chcete odstranit registrovanou adresu URL, použijte `delete urlacl` příkaz:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-797">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="a7fb7-798">Zaregistrujte certifikáty X. 509 na serveru.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-798">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="a7fb7-799">K registraci certifikátů pro aplikaci použijte nástroj *netsh.exe* :</span><span class="sxs-lookup"><span data-stu-id="a7fb7-799">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="a7fb7-800">`<IP>`: Určuje místní IP adresu pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-800">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="a7fb7-801">Nepoužívejte vazbu zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-801">Don't use a wildcard binding.</span></span> <span data-ttu-id="a7fb7-802">Použijte platnou IP adresu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-802">Use a valid IP address.</span></span>
   * <span data-ttu-id="a7fb7-803">`<PORT>`: Určuje port pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-803">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="a7fb7-804">`<THUMBPRINT>`: Kryptografický otisk certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-804">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="a7fb7-805">`<GUID>`: Identifikátor GUID generovaný vývojářem, který představuje aplikaci pro informativní účely.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-805">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="a7fb7-806">Pro referenční účely uložte identifikátor GUID do aplikace jako značku balíčku:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-806">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="a7fb7-807">V aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-807">In Visual Studio:</span></span>
     * <span data-ttu-id="a7fb7-808">Otevřete vlastnosti projektu aplikace tak, že kliknete pravým tlačítkem na aplikaci v **Průzkumník řešení** a vyberete **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-808">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="a7fb7-809">Vyberte kartu **balíček** .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-809">Select the **Package** tab.</span></span>
     * <span data-ttu-id="a7fb7-810">Zadejte identifikátor GUID, který jste vytvořili v poli **značky** .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-810">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="a7fb7-811">Když nepoužíváte Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-811">When not using Visual Studio:</span></span>
     * <span data-ttu-id="a7fb7-812">Otevřete soubor projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-812">Open the app's project file.</span></span>
     * <span data-ttu-id="a7fb7-813">Přidejte `<PackageTags>` vlastnost do nového nebo existujícího `<PropertyGroup>` s identifikátorem GUID, který jste vytvořili:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-813">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="a7fb7-814">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-814">In the following example:</span></span>

   * <span data-ttu-id="a7fb7-815">Místní IP adresa serveru je `10.0.0.4` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-815">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="a7fb7-816">Generátor náhodných identifikátorů GUID online poskytuje `appid` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-816">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="a7fb7-817">Když je certifikát zaregistrován, nástroj odpoví `SSL Certificate successfully added` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-817">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="a7fb7-818">K odstranění registrace certifikátu použijte `delete sslcert` příkaz:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-818">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="a7fb7-819">Referenční dokumentace pro *netsh.exe*:</span><span class="sxs-lookup"><span data-stu-id="a7fb7-819">Reference documentation for *netsh.exe*:</span></span>

   * [<span data-ttu-id="a7fb7-820">Příkazy Netsh pro protokol HTTP (Hypertext Transfer Protocol)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-820">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](https://technet.microsoft.com/library/cc725882.aspx)
   * [<span data-ttu-id="a7fb7-821">UrlPrefix řetězce</span><span class="sxs-lookup"><span data-stu-id="a7fb7-821">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="a7fb7-822">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-822">Run the app.</span></span>

   <span data-ttu-id="a7fb7-823">Oprávnění správce nejsou nutná ke spuštění aplikace při vytváření vazby na localhost pomocí protokolu HTTP (ne HTTPS) s číslem portu větším než 1024.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-823">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="a7fb7-824">U ostatních konfigurací (například pomocí místní IP adresy nebo vazby na port 443) spusťte aplikaci s oprávněními správce.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-824">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="a7fb7-825">Aplikace odpoví na veřejnou IP adresu serveru.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-825">The app responds at the server's public IP address.</span></span> <span data-ttu-id="a7fb7-826">V tomto příkladu je server dosažitelný z Internetu na jeho veřejné IP adrese `104.214.79.47` .</span><span class="sxs-lookup"><span data-stu-id="a7fb7-826">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="a7fb7-827">V tomto příkladu se používá vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-827">A development certificate is used in this example.</span></span> <span data-ttu-id="a7fb7-828">Stránka se načte bezpečně po obejít upozornění na nedůvěryhodný certifikát v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-828">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![Okno prohlížeče zobrazující načtenou stránku indexu aplikace](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a7fb7-830">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="a7fb7-830">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a7fb7-831">Pro aplikace hostované HTTP.sys, které komunikují s požadavky z Internetu nebo podnikové sítě, se může při hostování za proxy servery a nástroji pro vyrovnávání zatížení vyžadovat další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a7fb7-831">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="a7fb7-832">Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="a7fb7-832">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a7fb7-833">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a7fb7-833">Additional resources</span></span>

* [<span data-ttu-id="a7fb7-834">Povolit ověřování systému Windows pomocí HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a7fb7-834">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="a7fb7-835">Rozhraní API serveru HTTP</span><span class="sxs-lookup"><span data-stu-id="a7fb7-835">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="a7fb7-836">úložiště GitHubu ASPNET/HttpSysServer (zdrojový kód)</span><span class="sxs-lookup"><span data-stu-id="a7fb7-836">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="a7fb7-837">Hostitel</span><span class="sxs-lookup"><span data-stu-id="a7fb7-837">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
