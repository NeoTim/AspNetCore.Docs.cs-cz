---
title: Vynutilit HTTPS v ASP.NET Core
author: rick-anderson
description: Naučte se vyžadovat protokol HTTPS/TLS ve ASP.NET Core webové aplikaci.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
uid: security/enforcing-ssl
ms.openlocfilehash: 2a7b4152004cb65ee12487eb4793d42d0b7165d0
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78662636"
---
# <a name="enforce-https-in-aspnet-core"></a><span data-ttu-id="9cbec-103">Vynutilit HTTPS v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9cbec-103">Enforce HTTPS in ASP.NET Core</span></span>

<span data-ttu-id="9cbec-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9cbec-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9cbec-105">V tomto dokumentu se dozvíte, jak:</span><span class="sxs-lookup"><span data-stu-id="9cbec-105">This document shows how to:</span></span>

* <span data-ttu-id="9cbec-106">Vyžadovat protokol HTTPS pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="9cbec-106">Require HTTPS for all requests.</span></span>
* <span data-ttu-id="9cbec-107">Přesměrovat všechny požadavky HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9cbec-107">Redirect all HTTP requests to HTTPS.</span></span>

<span data-ttu-id="9cbec-108">Žádné rozhraní API nemůže klientovi zabránit v posílání citlivých dat na první požadavek.</span><span class="sxs-lookup"><span data-stu-id="9cbec-108">No API can prevent a client from sending sensitive data on the first request.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="9cbec-109">Projekty API</span><span class="sxs-lookup"><span data-stu-id="9cbec-109">API projects</span></span>
>
> <span data-ttu-id="9cbec-110">Nepoužívejte [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) pro webová rozhraní API, která přijímají citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="9cbec-110">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="9cbec-111">`RequireHttpsAttribute` používá ke přesměrování prohlížečů z HTTP na HTTPS stavové kódy HTTP.</span><span class="sxs-lookup"><span data-stu-id="9cbec-111">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="9cbec-112">Klienti rozhraní API nemusí pochopit nebo dodržovat přesměrování z HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9cbec-112">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="9cbec-113">Tito klienti mohou odesílat informace prostřednictvím protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="9cbec-113">Such clients may send information over HTTP.</span></span> <span data-ttu-id="9cbec-114">Webové rozhraní API by mělo mít jednu z těchto:</span><span class="sxs-lookup"><span data-stu-id="9cbec-114">Web APIs should either:</span></span>
>
> * <span data-ttu-id="9cbec-115">Neslouchat na HTTP.</span><span class="sxs-lookup"><span data-stu-id="9cbec-115">Not listen on HTTP.</span></span>
> * <span data-ttu-id="9cbec-116">Ukončete připojení se stavovým kódem 400 (chybný požadavek) a neobsluhuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="9cbec-116">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>
>
> ## <a name="hsts-and-api-projects"></a><span data-ttu-id="9cbec-117">Projekty HSTS a API</span><span class="sxs-lookup"><span data-stu-id="9cbec-117">HSTS and API projects</span></span>
>
> <span data-ttu-id="9cbec-118">Výchozí projekty API neobsahují [HSTS](#hsts) , protože HSTS je všeobecně pouze instrukcí prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="9cbec-118">The default API projects don't include [HSTS](#hsts) because HSTS is generally a browser only instruction.</span></span> <span data-ttu-id="9cbec-119">Další volající, jako jsou telefony nebo desktopové aplikace, **nedodržují** pokyny.</span><span class="sxs-lookup"><span data-stu-id="9cbec-119">Other callers, such as phone or desktop apps, do **not** obey the instruction.</span></span> <span data-ttu-id="9cbec-120">I v prohlížečích má jedno ověřované volání rozhraní API přes protokol HTTP rizika v nezabezpečených sítích.</span><span class="sxs-lookup"><span data-stu-id="9cbec-120">Even within browsers, a single authenticated call to an API over HTTP has risks on insecure networks.</span></span> <span data-ttu-id="9cbec-121">Bezpečným přístupem je nakonfigurovat projekty API tak, aby naslouchaly jenom a reagovaly přes HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9cbec-121">The secure approach is to configure API projects to only listen to and respond over HTTPS.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="9cbec-122">Projekty API</span><span class="sxs-lookup"><span data-stu-id="9cbec-122">API projects</span></span>
>
> <span data-ttu-id="9cbec-123">Nepoužívejte [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) pro webová rozhraní API, která přijímají citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="9cbec-123">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="9cbec-124">`RequireHttpsAttribute` používá ke přesměrování prohlížečů z HTTP na HTTPS stavové kódy HTTP.</span><span class="sxs-lookup"><span data-stu-id="9cbec-124">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="9cbec-125">Klienti rozhraní API nemusí pochopit nebo dodržovat přesměrování z HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9cbec-125">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="9cbec-126">Tito klienti mohou odesílat informace prostřednictvím protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="9cbec-126">Such clients may send information over HTTP.</span></span> <span data-ttu-id="9cbec-127">Webové rozhraní API by mělo mít jednu z těchto:</span><span class="sxs-lookup"><span data-stu-id="9cbec-127">Web APIs should either:</span></span>
>
> * <span data-ttu-id="9cbec-128">Neslouchat na HTTP.</span><span class="sxs-lookup"><span data-stu-id="9cbec-128">Not listen on HTTP.</span></span>
> * <span data-ttu-id="9cbec-129">Ukončete připojení se stavovým kódem 400 (chybný požadavek) a neobsluhuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="9cbec-129">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>

::: moniker-end

## <a name="require-https"></a><span data-ttu-id="9cbec-130">Vyžadovat protokol HTTPS</span><span class="sxs-lookup"><span data-stu-id="9cbec-130">Require HTTPS</span></span>

<span data-ttu-id="9cbec-131">Doporučujeme, aby provozní ASP.NET Core Web Apps používaly:</span><span class="sxs-lookup"><span data-stu-id="9cbec-131">We recommend that production ASP.NET Core web apps use:</span></span>

* <span data-ttu-id="9cbec-132">Middleware pro přesměrování HTTPS (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) pro přesměrování požadavků HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9cbec-132">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) to redirect HTTP requests to HTTPS.</span></span>
* <span data-ttu-id="9cbec-133">HSTS middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) k odeslání hlaviček HSTS (http Strict Transport Security Protocol) do klientů.</span><span class="sxs-lookup"><span data-stu-id="9cbec-133">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) to send HTTP Strict Transport Security Protocol (HSTS) headers to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="9cbec-134">Aplikace nasazené v konfiguraci reverzního proxy serveru umožňují proxy zpracovávat zabezpečení připojení (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="9cbec-134">Apps deployed in a reverse proxy configuration allow the proxy to handle connection security (HTTPS).</span></span> <span data-ttu-id="9cbec-135">Pokud proxy také zpracovává přesměrování protokolu HTTPS, není nutné používat middleware pro přesměrování protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9cbec-135">If the proxy also handles HTTPS redirection, there's no need to use HTTPS Redirection Middleware.</span></span> <span data-ttu-id="9cbec-136">Pokud proxy server také zpracovává zápis hlaviček HSTS (například [nativní podporu HSTS ve službě IIS 10,0 (1709) nebo novější](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), aplikace HSTS middleware není aplikací požadována.</span><span class="sxs-lookup"><span data-stu-id="9cbec-136">If the proxy server also handles writing HSTS headers (for example, [native HSTS support in IIS 10.0 (1709) or later](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware isn't required by the app.</span></span> <span data-ttu-id="9cbec-137">Další informace najdete v tématu [výslovný nedostatek protokolu HTTPS/HSTS při vytváření projektu](#opt-out-of-httpshsts-on-project-creation).</span><span class="sxs-lookup"><span data-stu-id="9cbec-137">For more information, see [Opt-out of HTTPS/HSTS on project creation](#opt-out-of-httpshsts-on-project-creation).</span></span>

### <a name="usehttpsredirection"></a><span data-ttu-id="9cbec-138">UseHttpsRedirection</span><span class="sxs-lookup"><span data-stu-id="9cbec-138">UseHttpsRedirection</span></span>

<span data-ttu-id="9cbec-139">Následující kód volá `UseHttpsRedirection` ve třídě `Startup`:</span><span class="sxs-lookup"><span data-stu-id="9cbec-139">The following code calls `UseHttpsRedirection` in the `Startup` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

<span data-ttu-id="9cbec-140">Předchozí zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="9cbec-140">The preceding highlighted code:</span></span>

* <span data-ttu-id="9cbec-141">Používá výchozí [HttpsRedirectionOptions. RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span><span class="sxs-lookup"><span data-stu-id="9cbec-141">Uses the default [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span></span>
* <span data-ttu-id="9cbec-142">Použije výchozí [HttpsRedirectionOptions. HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null), pokud není přepsána proměnnou prostředí `ASPNETCORE_HTTPS_PORT` nebo [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span><span class="sxs-lookup"><span data-stu-id="9cbec-142">Uses the default [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null) unless overridden by the `ASPNETCORE_HTTPS_PORT` environment variable or [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span></span>

<span data-ttu-id="9cbec-143">Doporučujeme místo trvalých přesměrování použít dočasné přesměrování.</span><span class="sxs-lookup"><span data-stu-id="9cbec-143">We recommend using temporary redirects rather than permanent redirects.</span></span> <span data-ttu-id="9cbec-144">Ukládání odkazů do mezipaměti může způsobit nestabilní chování ve vývojových prostředích.</span><span class="sxs-lookup"><span data-stu-id="9cbec-144">Link caching can cause unstable behavior in development environments.</span></span> <span data-ttu-id="9cbec-145">Pokud upřednostňujete odeslání trvalého stavového kódu přesměrování, když je aplikace v nevývojovém prostředí, přečtěte si část [Konfigurace trvalých přesměrování v produkčním](#configure-permanent-redirects-in-production) prostředí.</span><span class="sxs-lookup"><span data-stu-id="9cbec-145">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, see the [Configure permanent redirects in production](#configure-permanent-redirects-in-production) section.</span></span> <span data-ttu-id="9cbec-146">Doporučujeme používat [HSTS](#http-strict-transport-security-protocol-hsts) k signalizaci klientům, aby se do aplikace poslaly jenom zabezpečené požadavky na prostředky (jenom v produkčním prostředí).</span><span class="sxs-lookup"><span data-stu-id="9cbec-146">We recommend using [HSTS](#http-strict-transport-security-protocol-hsts) to signal to clients that only secure resource requests should be sent to the app (only in production).</span></span>

### <a name="port-configuration"></a><span data-ttu-id="9cbec-147">Konfigurace portu</span><span class="sxs-lookup"><span data-stu-id="9cbec-147">Port configuration</span></span>

<span data-ttu-id="9cbec-148">Pro middleware musí být k dispozici port pro přesměrování nezabezpečené žádosti na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9cbec-148">A port must be available for the middleware to redirect an insecure request to HTTPS.</span></span> <span data-ttu-id="9cbec-149">Pokud není k dispozici žádný port:</span><span class="sxs-lookup"><span data-stu-id="9cbec-149">If no port is available:</span></span>

* <span data-ttu-id="9cbec-150">Přesměrování na HTTPS neproběhne.</span><span class="sxs-lookup"><span data-stu-id="9cbec-150">Redirection to HTTPS doesn't occur.</span></span>
* <span data-ttu-id="9cbec-151">Middleware zaznamená upozornění. "Nepodařilo se určit port HTTPS pro přesměrování".</span><span class="sxs-lookup"><span data-stu-id="9cbec-151">The middleware logs the warning "Failed to determine the https port for redirect."</span></span>

<span data-ttu-id="9cbec-152">Port HTTPS určete pomocí některého z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="9cbec-152">Specify the HTTPS port using any of the following approaches:</span></span>

* <span data-ttu-id="9cbec-153">Nastavte [HttpsRedirectionOptions. HttpsPort](#options).</span><span class="sxs-lookup"><span data-stu-id="9cbec-153">Set [HttpsRedirectionOptions.HttpsPort](#options).</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="9cbec-154">Nastavte nastavení `https_port` [hostitele](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port):</span><span class="sxs-lookup"><span data-stu-id="9cbec-154">Set the `https_port` [host setting](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port):</span></span>

  * <span data-ttu-id="9cbec-155">V konfiguraci hostitele.</span><span class="sxs-lookup"><span data-stu-id="9cbec-155">In host configuration.</span></span>
  * <span data-ttu-id="9cbec-156">Nastavením proměnné prostředí `ASPNETCORE_HTTPS_PORT`.</span><span class="sxs-lookup"><span data-stu-id="9cbec-156">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="9cbec-157">Přidáním položky nejvyšší úrovně v souboru *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="9cbec-157">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/appsettings.json?highlight=2)]

* <span data-ttu-id="9cbec-158">Označení portu pomocí zabezpečeného schématu pomocí [proměnné prostředí ASPNETCORE_URLS](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls).</span><span class="sxs-lookup"><span data-stu-id="9cbec-158">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls).</span></span> <span data-ttu-id="9cbec-159">Proměnná prostředí nakonfiguruje server.</span><span class="sxs-lookup"><span data-stu-id="9cbec-159">The environment variable configures the server.</span></span> <span data-ttu-id="9cbec-160">Middleware nepřímo vyhledá port HTTPS prostřednictvím <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="9cbec-160">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="9cbec-161">Tento přístup nefunguje v nasazeních reverzních proxy serverů.</span><span class="sxs-lookup"><span data-stu-id="9cbec-161">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* <span data-ttu-id="9cbec-162">Nastavte nastavení `https_port` [hostitele](xref:fundamentals/host/web-host#https-port):</span><span class="sxs-lookup"><span data-stu-id="9cbec-162">Set the `https_port` [host setting](xref:fundamentals/host/web-host#https-port):</span></span>

  * <span data-ttu-id="9cbec-163">V konfiguraci hostitele.</span><span class="sxs-lookup"><span data-stu-id="9cbec-163">In host configuration.</span></span>
  * <span data-ttu-id="9cbec-164">Nastavením proměnné prostředí `ASPNETCORE_HTTPS_PORT`.</span><span class="sxs-lookup"><span data-stu-id="9cbec-164">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="9cbec-165">Přidáním položky nejvyšší úrovně v souboru *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="9cbec-165">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/appsettings.json?highlight=2)]

* <span data-ttu-id="9cbec-166">Označení portu pomocí zabezpečeného schématu pomocí [proměnné prostředí ASPNETCORE_URLS](xref:fundamentals/host/web-host#server-urls).</span><span class="sxs-lookup"><span data-stu-id="9cbec-166">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](xref:fundamentals/host/web-host#server-urls).</span></span> <span data-ttu-id="9cbec-167">Proměnná prostředí nakonfiguruje server.</span><span class="sxs-lookup"><span data-stu-id="9cbec-167">The environment variable configures the server.</span></span> <span data-ttu-id="9cbec-168">Middleware nepřímo vyhledá port HTTPS prostřednictvím <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="9cbec-168">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="9cbec-169">Tento přístup nefunguje v nasazeních reverzních proxy serverů.</span><span class="sxs-lookup"><span data-stu-id="9cbec-169">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

* <span data-ttu-id="9cbec-170">Ve vývojovém prostředí nastavte adresu URL HTTPS v souboru *launchsettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="9cbec-170">In development, set an HTTPS URL in *launchsettings.json*.</span></span> <span data-ttu-id="9cbec-171">Pokud se používá IIS Express, povolte HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9cbec-171">Enable HTTPS when IIS Express is used.</span></span>

* <span data-ttu-id="9cbec-172">Nakonfigurujte koncový bod adresy URL HTTPS pro nasazení Edge serveru [Kestrel](xref:fundamentals/servers/kestrel) Server nebo [http. sys](xref:fundamentals/servers/httpsys) s přístupem k veřejnému.</span><span class="sxs-lookup"><span data-stu-id="9cbec-172">Configure an HTTPS URL endpoint for a public-facing edge deployment of [Kestrel](xref:fundamentals/servers/kestrel) server or [HTTP.sys](xref:fundamentals/servers/httpsys) server.</span></span> <span data-ttu-id="9cbec-173">Aplikace používá jenom **jeden port HTTPS** .</span><span class="sxs-lookup"><span data-stu-id="9cbec-173">Only **one HTTPS port** is used by the app.</span></span> <span data-ttu-id="9cbec-174">Middleware zjistí port prostřednictvím <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="9cbec-174">The middleware discovers the port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span>

> [!NOTE]
> <span data-ttu-id="9cbec-175">Když je aplikace spuštěná v konfiguraci reverzního proxy serveru, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="9cbec-175">When an app is run in a reverse proxy configuration, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> isn't available.</span></span> <span data-ttu-id="9cbec-176">Port nastavte pomocí některého z dalších přístupů popsaných v této části.</span><span class="sxs-lookup"><span data-stu-id="9cbec-176">Set the port using one of the other approaches described in this section.</span></span>

### <a name="edge-deployments"></a><span data-ttu-id="9cbec-177">Nasazení Edge</span><span class="sxs-lookup"><span data-stu-id="9cbec-177">Edge deployments</span></span> 

<span data-ttu-id="9cbec-178">Pokud se Kestrel nebo HTTP. sys používá jako veřejný server Edge, Kestrel nebo HTTP. sys musí být nakonfigurované tak, aby naslouchal na obou těchto počítačích:</span><span class="sxs-lookup"><span data-stu-id="9cbec-178">When Kestrel or HTTP.sys is used as a public-facing edge server, Kestrel or HTTP.sys must be configured to listen on both:</span></span>

* <span data-ttu-id="9cbec-179">Zabezpečený port, ve kterém je klient přesměrován (obvykle 443 v produkčním a 5001 ve vývoji).</span><span class="sxs-lookup"><span data-stu-id="9cbec-179">The secure port where the client is redirected (typically, 443 in production and 5001 in development).</span></span>
* <span data-ttu-id="9cbec-180">Nezabezpečený port (obvykle 80 v produkčním a 5000 ve vývoji).</span><span class="sxs-lookup"><span data-stu-id="9cbec-180">The insecure port (typically, 80 in production and 5000 in development).</span></span>

<span data-ttu-id="9cbec-181">Aby aplikace přijímala nezabezpečený požadavek a přesměrovala klienta na zabezpečený port, musí být nezabezpečený port přístupný klientovi.</span><span class="sxs-lookup"><span data-stu-id="9cbec-181">The insecure port must be accessible by the client in order for the app to receive an insecure request and redirect the client to the secure port.</span></span>

<span data-ttu-id="9cbec-182">Další informace najdete v tématu [Konfigurace a <xref:fundamentals/servers/httpsys>koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) .</span><span class="sxs-lookup"><span data-stu-id="9cbec-182">For more information, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) or <xref:fundamentals/servers/httpsys>.</span></span>

### <a name="deployment-scenarios"></a><span data-ttu-id="9cbec-183">Scénáře nasazení</span><span class="sxs-lookup"><span data-stu-id="9cbec-183">Deployment scenarios</span></span>

<span data-ttu-id="9cbec-184">Všechny brány firewall mezi klientem a serverem musí mít také otevřené komunikační porty pro provoz.</span><span class="sxs-lookup"><span data-stu-id="9cbec-184">Any firewall between the client and server must also have communication ports open for traffic.</span></span>

<span data-ttu-id="9cbec-185">Pokud se požadavky předávají v konfiguraci reverzního proxy serveru, před voláním middleware pro přesměrování protokolu HTTPS použijte [middleware pro předané hlavičky](xref:host-and-deploy/proxy-load-balancer) .</span><span class="sxs-lookup"><span data-stu-id="9cbec-185">If requests are forwarded in a reverse proxy configuration, use [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) before calling HTTPS Redirection Middleware.</span></span> <span data-ttu-id="9cbec-186">Middleware předaných hlaviček aktualizuje `Request.Scheme`pomocí hlavičky `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="9cbec-186">Forwarded Headers Middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header.</span></span> <span data-ttu-id="9cbec-187">Middleware povoluje správné fungování identifikátorů URI přesměrování a dalších zásad zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="9cbec-187">The middleware permits redirect URIs and other security policies to work correctly.</span></span> <span data-ttu-id="9cbec-188">Když se nepoužije middleware předávaných hlaviček, back-end aplikace nemusí získat správné schéma a končit smyčkou přesměrování.</span><span class="sxs-lookup"><span data-stu-id="9cbec-188">When Forwarded Headers Middleware isn't used, the backend app might not receive the correct scheme and end up in a redirect loop.</span></span> <span data-ttu-id="9cbec-189">Společná chybová zpráva koncového uživatele je, že došlo k příliš velkému počtu přesměrování.</span><span class="sxs-lookup"><span data-stu-id="9cbec-189">A common end user error message is that too many redirects have occurred.</span></span>

<span data-ttu-id="9cbec-190">Při nasazování do Azure App Service postupujte podle pokynů v [kurzu: vytvoření vazby existujícího vlastního certifikátu SSL k Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="9cbec-190">When deploying to Azure App Service, follow the guidance in [Tutorial: Bind an existing custom SSL certificate to Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

### <a name="options"></a><span data-ttu-id="9cbec-191">Možnosti</span><span class="sxs-lookup"><span data-stu-id="9cbec-191">Options</span></span>

<span data-ttu-id="9cbec-192">Následující zvýrazněný kód volá [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) ke konfiguraci možností middlewaru:</span><span class="sxs-lookup"><span data-stu-id="9cbec-192">The following highlighted code calls [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) to configure middleware options:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


<span data-ttu-id="9cbec-193">Volání `AddHttpsRedirection` je nezbytné pouze ke změně hodnot `HttpsPort` nebo `RedirectStatusCode`.</span><span class="sxs-lookup"><span data-stu-id="9cbec-193">Calling `AddHttpsRedirection` is only necessary to change the values of `HttpsPort` or `RedirectStatusCode`.</span></span>

<span data-ttu-id="9cbec-194">Předchozí zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="9cbec-194">The preceding highlighted code:</span></span>

* <span data-ttu-id="9cbec-195">Nastaví [HttpsRedirectionOptions. RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) na <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, což je výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="9cbec-195">Sets [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) to <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, which is the default value.</span></span> <span data-ttu-id="9cbec-196">Pro přiřazení `RedirectStatusCode`použijte pole <xref:Microsoft.AspNetCore.Http.StatusCodes> třídy.</span><span class="sxs-lookup"><span data-stu-id="9cbec-196">Use the fields of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for assignments to `RedirectStatusCode`.</span></span>
* <span data-ttu-id="9cbec-197">Nastaví port HTTPS na 5001.</span><span class="sxs-lookup"><span data-stu-id="9cbec-197">Sets the HTTPS port to 5001.</span></span>

#### <a name="configure-permanent-redirects-in-production"></a><span data-ttu-id="9cbec-198">Konfigurace trvalých přesměrování v produkčním prostředí</span><span class="sxs-lookup"><span data-stu-id="9cbec-198">Configure permanent redirects in production</span></span>

<span data-ttu-id="9cbec-199">Middleware ve výchozím nastavení odesílá [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) pomocí všech přesměrování.</span><span class="sxs-lookup"><span data-stu-id="9cbec-199">The middleware defaults to sending a [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) with all redirects.</span></span> <span data-ttu-id="9cbec-200">Pokud upřednostňujete odeslání trvalého stavového kódu přesměrování, pokud je aplikace v nevývojovém prostředí, zabalte konfiguraci možností middlewaru do podmíněné kontroly pro nevývojové prostředí.</span><span class="sxs-lookup"><span data-stu-id="9cbec-200">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, wrap the middleware options configuration in a conditional check for a non-Development environment.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9cbec-201">Při konfiguraci služeb v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9cbec-201">When configuring services in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IWebHostEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="9cbec-202">Při konfiguraci služeb v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9cbec-202">When configuring services in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end


## <a name="https-redirection-middleware-alternative-approach"></a><span data-ttu-id="9cbec-203">Alternativní přístup middlewaru přesměrování HTTPS</span><span class="sxs-lookup"><span data-stu-id="9cbec-203">HTTPS Redirection Middleware alternative approach</span></span>

<span data-ttu-id="9cbec-204">Alternativou k použití middlewaru pro přesměrování protokolu HTTPS (`UseHttpsRedirection`) je použití middleware pro přepis adres URL (`AddRedirectToHttps`).</span><span class="sxs-lookup"><span data-stu-id="9cbec-204">An alternative to using HTTPS Redirection Middleware (`UseHttpsRedirection`) is to use URL Rewriting Middleware (`AddRedirectToHttps`).</span></span> <span data-ttu-id="9cbec-205">`AddRedirectToHttps` může také nastavit stavový kód a port při spuštění přesměrování.</span><span class="sxs-lookup"><span data-stu-id="9cbec-205">`AddRedirectToHttps` can also set the status code and port when the redirect is executed.</span></span> <span data-ttu-id="9cbec-206">Další informace najdete v tématu [middleware pro přepis adres URL](xref:fundamentals/url-rewriting).</span><span class="sxs-lookup"><span data-stu-id="9cbec-206">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span>

<span data-ttu-id="9cbec-207">Při přesměrování na HTTPS bez požadavku na další pravidla přesměrování doporučujeme použít middleware Přesměrující protokol HTTPS (`UseHttpsRedirection`) popsané v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="9cbec-207">When redirecting to HTTPS without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware (`UseHttpsRedirection`) described in this topic.</span></span>

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a><span data-ttu-id="9cbec-208">Protokol HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="9cbec-208">HTTP Strict Transport Security Protocol (HSTS)</span></span>

<span data-ttu-id="9cbec-209">Za [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project)je [zabezpečení protokolu HTTP (HSTS Strict Transport Security)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) na základě souhlasu s hlavičkou odpovědi zadané pomocí webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="9cbec-209">Per [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [HTTP Strict Transport Security (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) is an opt-in security enhancement that's specified by a web app through the use of a response header.</span></span> <span data-ttu-id="9cbec-210">Když [prohlížeč, který podporuje HSTS,](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) obdrží tuto hlavičku:</span><span class="sxs-lookup"><span data-stu-id="9cbec-210">When a [browser that supports HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) receives this header:</span></span>

* <span data-ttu-id="9cbec-211">Prohlížeč ukládá konfiguraci pro doménu, která znemožňuje odeslání jakékoli komunikace prostřednictvím protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="9cbec-211">The browser stores configuration for the domain that prevents sending any communication over HTTP.</span></span> <span data-ttu-id="9cbec-212">Prohlížeč vynutí veškerou komunikaci přes protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9cbec-212">The browser forces all communication over HTTPS.</span></span>
* <span data-ttu-id="9cbec-213">Prohlížeč brání uživateli v používání nedůvěryhodných nebo neplatných certifikátů.</span><span class="sxs-lookup"><span data-stu-id="9cbec-213">The browser prevents the user from using untrusted or invalid certificates.</span></span> <span data-ttu-id="9cbec-214">Prohlížeč zakáže výzvy, které umožní uživateli dočasně důvěřovat takovému certifikátu.</span><span class="sxs-lookup"><span data-stu-id="9cbec-214">The browser disables prompts that allow a user to temporarily trust such a certificate.</span></span>

<span data-ttu-id="9cbec-215">Vzhledem k tomu, že klient vynutil HSTS, má některá omezení:</span><span class="sxs-lookup"><span data-stu-id="9cbec-215">Because HSTS is enforced by the client, it has some limitations:</span></span>

* <span data-ttu-id="9cbec-216">Klient musí podporovat HSTS.</span><span class="sxs-lookup"><span data-stu-id="9cbec-216">The client must support HSTS.</span></span>
* <span data-ttu-id="9cbec-217">HSTS vyžaduje alespoň jednu úspěšnou žádost HTTPS k vytvoření zásady HSTS.</span><span class="sxs-lookup"><span data-stu-id="9cbec-217">HSTS requires at least one successful HTTPS request to establish the HSTS policy.</span></span>
* <span data-ttu-id="9cbec-218">Aplikace musí kontrolovat všechny požadavky HTTP a přesměrovat nebo zamítnout požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="9cbec-218">The application must check every HTTP request and redirect or reject the HTTP request.</span></span>

<span data-ttu-id="9cbec-219">ASP.NET Core 2,1 a novější implementuje HSTS s metodou rozšíření `UseHsts`.</span><span class="sxs-lookup"><span data-stu-id="9cbec-219">ASP.NET Core 2.1 and later implements HSTS with the `UseHsts` extension method.</span></span> <span data-ttu-id="9cbec-220">Následující kód volá `UseHsts`, když aplikace není v [režimu pro vývoj](xref:fundamentals/environments):</span><span class="sxs-lookup"><span data-stu-id="9cbec-220">The following code calls `UseHsts` when the app isn't in [development mode](xref:fundamentals/environments):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

<span data-ttu-id="9cbec-221">`UseHsts` se při vývoji nedoporučuje, protože nastavení HSTS jsou prohlížeči vysoce ukládat do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="9cbec-221">`UseHsts` isn't recommended in development because the HSTS settings are highly cacheable by browsers.</span></span> <span data-ttu-id="9cbec-222">Ve výchozím nastavení `UseHsts` nezahrnuje místní adresu zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="9cbec-222">By default, `UseHsts` excludes the local loopback address.</span></span>

<span data-ttu-id="9cbec-223">V produkčních prostředích, která implementují protokol HTTPS poprvé, nastavte počáteční [HstsOptions. maxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) na malou hodnotu pomocí jedné z metod <xref:System.TimeSpan>.</span><span class="sxs-lookup"><span data-stu-id="9cbec-223">For production environments that are implementing HTTPS for the first time, set the initial [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) to a small value using one of the <xref:System.TimeSpan> methods.</span></span> <span data-ttu-id="9cbec-224">Nastavte hodnotu z hodin na ne více než jeden den pro případ, že budete potřebovat obnovit infrastrukturu HTTPS na HTTP.</span><span class="sxs-lookup"><span data-stu-id="9cbec-224">Set the value from hours to no more than a single day in case you need to revert the HTTPS infrastructure to HTTP.</span></span> <span data-ttu-id="9cbec-225">Až si budete jisti udržitelností konfigurace HTTPS, zvyšte hodnotu HSTS `max-age`; běžně používaná hodnota je jeden rok.</span><span class="sxs-lookup"><span data-stu-id="9cbec-225">After you're confident in the sustainability of the HTTPS configuration, increase the HSTS `max-age` value; a commonly used value is one year.</span></span>

<span data-ttu-id="9cbec-226">Následující kód:</span><span class="sxs-lookup"><span data-stu-id="9cbec-226">The following code:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* <span data-ttu-id="9cbec-227">Nastaví přednačtení parametru `Strict-Transport-Security` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="9cbec-227">Sets the preload parameter of the `Strict-Transport-Security` header.</span></span> <span data-ttu-id="9cbec-228">Předběžné načtení není součástí [specifikace RFC HSTS](https://tools.ietf.org/html/rfc6797), ale podporuje je ve webových prohlížečích k přednačtení webů HSTS při nové instalaci.</span><span class="sxs-lookup"><span data-stu-id="9cbec-228">Preload isn't part of the [RFC HSTS specification](https://tools.ietf.org/html/rfc6797), but is supported by web browsers to preload HSTS sites on fresh install.</span></span> <span data-ttu-id="9cbec-229">Další informace najdete na webu [https://hstspreload.org/](https://hstspreload.org/).</span><span class="sxs-lookup"><span data-stu-id="9cbec-229">For more information, see [https://hstspreload.org/](https://hstspreload.org/).</span></span>
* <span data-ttu-id="9cbec-230">Povolí [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), která aplikuje zásady HSTS na hostování subdomén.</span><span class="sxs-lookup"><span data-stu-id="9cbec-230">Enables [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), which applies the HSTS policy to Host subdomains.</span></span>
* <span data-ttu-id="9cbec-231">Explicitně nastaví parametr `max-age` `Strict-Transport-Security` záhlaví na 60 dní.</span><span class="sxs-lookup"><span data-stu-id="9cbec-231">Explicitly sets the `max-age` parameter of the `Strict-Transport-Security` header to 60 days.</span></span> <span data-ttu-id="9cbec-232">Pokud není nastavené, výchozí hodnota je 30 dní.</span><span class="sxs-lookup"><span data-stu-id="9cbec-232">If not set, defaults to 30 days.</span></span> <span data-ttu-id="9cbec-233">Další informace najdete v [direktivě max-age](https://tools.ietf.org/html/rfc6797#section-6.1.1).</span><span class="sxs-lookup"><span data-stu-id="9cbec-233">For more information, see the [max-age directive](https://tools.ietf.org/html/rfc6797#section-6.1.1).</span></span>
* <span data-ttu-id="9cbec-234">Přidá `example.com` do seznamu hostitelů, které mají být vyloučeny.</span><span class="sxs-lookup"><span data-stu-id="9cbec-234">Adds `example.com` to the list of hosts to exclude.</span></span>

<span data-ttu-id="9cbec-235">`UseHsts` vyloučí následující hostitele zpětné smyčky:</span><span class="sxs-lookup"><span data-stu-id="9cbec-235">`UseHsts` excludes the following loopback hosts:</span></span>

* <span data-ttu-id="9cbec-236">`localhost`: adresa zpětné smyčky IPv4.</span><span class="sxs-lookup"><span data-stu-id="9cbec-236">`localhost` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="9cbec-237">`127.0.0.1`: adresa zpětné smyčky IPv4.</span><span class="sxs-lookup"><span data-stu-id="9cbec-237">`127.0.0.1` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="9cbec-238">`[::1]`: adresa zpětné smyčky protokolu IPv6.</span><span class="sxs-lookup"><span data-stu-id="9cbec-238">`[::1]` : The IPv6 loopback address.</span></span>

## <a name="opt-out-of-httpshsts-on-project-creation"></a><span data-ttu-id="9cbec-239">Výslovný nesouhlas s protokolem HTTPS/HSTS při vytváření projektu</span><span class="sxs-lookup"><span data-stu-id="9cbec-239">Opt-out of HTTPS/HSTS on project creation</span></span>

<span data-ttu-id="9cbec-240">V některých případech služby back-end, kde se zabezpečení připojení zpracovává na veřejném hraničním serveru sítě, se konfigurace zabezpečení připojení na jednotlivých uzlech nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="9cbec-240">In some backend service scenarios where connection security is handled at the public-facing edge of the network, configuring connection security at each node isn't required.</span></span> <span data-ttu-id="9cbec-241">Webové aplikace, které jsou generovány ze šablon v aplikaci Visual Studio nebo pomocí příkazu [dotnet New](/dotnet/core/tools/dotnet-new) , umožňují [přesměrování https](#require-https) a [HSTS](#http-strict-transport-security-protocol-hsts).</span><span class="sxs-lookup"><span data-stu-id="9cbec-241">Web apps that are generated from the templates in Visual Studio or from the [dotnet new](/dotnet/core/tools/dotnet-new) command enable [HTTPS redirection](#require-https) and [HSTS](#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="9cbec-242">Pro nasazení, která tyto scénáře nevyžadují, můžete při vytvoření aplikace ze šablony vyjádřit výslovný souhlas s HTTPS/HSTS.</span><span class="sxs-lookup"><span data-stu-id="9cbec-242">For deployments that don't require these scenarios, you can opt-out of HTTPS/HSTS when the app is created from the template.</span></span>

<span data-ttu-id="9cbec-243">Výslovný souhlas s protokolem HTTPS/HSTS:</span><span class="sxs-lookup"><span data-stu-id="9cbec-243">To opt-out of HTTPS/HSTS:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9cbec-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9cbec-244">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="9cbec-245">Zrušte zaškrtnuté políčko **Konfigurovat pro protokol HTTPS** .</span><span class="sxs-lookup"><span data-stu-id="9cbec-245">Uncheck the **Configure for HTTPS** check box.</span></span>

::: moniker range=">= aspnetcore-3.0"

![Dialogové okno Nová ASP.NET Core webové aplikace zobrazující nezaškrtnuté políčko konfigurovat pro protokol HTTPS.](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

![Dialogové okno Nová ASP.NET Core webové aplikace zobrazující nezaškrtnuté políčko konfigurovat pro protokol HTTPS.](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="9cbec-248">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="9cbec-248">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="9cbec-249">Použijte možnost `--no-https`.</span><span class="sxs-lookup"><span data-stu-id="9cbec-249">Use the `--no-https` option.</span></span> <span data-ttu-id="9cbec-250">Například</span><span class="sxs-lookup"><span data-stu-id="9cbec-250">For example</span></span>

```dotnetcli
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a><span data-ttu-id="9cbec-251">Důvěra ASP.NET Core certifikát pro vývoj HTTPS ve Windows a macOS</span><span class="sxs-lookup"><span data-stu-id="9cbec-251">Trust the ASP.NET Core HTTPS development certificate on Windows and macOS</span></span>

<span data-ttu-id="9cbec-252">.NET Core SDK obsahuje certifikát pro vývoj HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9cbec-252">The .NET Core SDK includes an HTTPS development certificate.</span></span> <span data-ttu-id="9cbec-253">Certifikát je nainstalován jako součást prvního spuštění prostředí.</span><span class="sxs-lookup"><span data-stu-id="9cbec-253">The certificate is installed as part of the first-run experience.</span></span> <span data-ttu-id="9cbec-254">Například `dotnet --info` vytváří variaci následujícího výstupu:</span><span class="sxs-lookup"><span data-stu-id="9cbec-254">For example, `dotnet --info` produces a variation of the following output:</span></span>

```
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

<span data-ttu-id="9cbec-255">Instalace .NET Core SDK nainstaluje certifikát pro vývoj ASP.NET Core HTTPS do úložiště certifikátů místního uživatele.</span><span class="sxs-lookup"><span data-stu-id="9cbec-255">Installing the .NET Core SDK installs the ASP.NET Core HTTPS development certificate to the local user certificate store.</span></span> <span data-ttu-id="9cbec-256">Certifikát je nainstalovaný, ale není důvěryhodný.</span><span class="sxs-lookup"><span data-stu-id="9cbec-256">The certificate has been installed, but it's not trusted.</span></span> <span data-ttu-id="9cbec-257">Pro důvěřování certifikátu proveďte jednorázový krok ke spuštění nástroje dotnet `dev-certs` Tool:</span><span class="sxs-lookup"><span data-stu-id="9cbec-257">To trust the certificate, perform the one-time step to run the dotnet `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="9cbec-258">Následující příkaz poskytuje nápovědě k nástroji `dev-certs`:</span><span class="sxs-lookup"><span data-stu-id="9cbec-258">The following command provides help on the `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a><span data-ttu-id="9cbec-259">Jak nastavit certifikát pro vývojáře pro Docker</span><span class="sxs-lookup"><span data-stu-id="9cbec-259">How to set up a developer certificate for Docker</span></span>

<span data-ttu-id="9cbec-260">Podívejte se na [Tento problém GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/6199).</span><span class="sxs-lookup"><span data-stu-id="9cbec-260">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6199).</span></span>

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a><span data-ttu-id="9cbec-261">Důvěřovat certifikátu HTTPS v subsystému Windows pro Linux</span><span class="sxs-lookup"><span data-stu-id="9cbec-261">Trust HTTPS certificate from Windows Subsystem for Linux</span></span>

<span data-ttu-id="9cbec-262">Subsystém Windows pro Linux (WSL) vygeneruje certifikát podepsaný svým držitelem (HTTPS). Konfigurace úložiště certifikátů Windows pro důvěřování certifikátu WSL:</span><span class="sxs-lookup"><span data-stu-id="9cbec-262">The Windows Subsystem for Linux (WSL) generates an HTTPS self-signed cert. To configure the Windows certificate store to trust the WSL certificate:</span></span>

* <span data-ttu-id="9cbec-263">Spuštěním následujícího příkazu exportujte certifikát generovaný WSL: `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span><span class="sxs-lookup"><span data-stu-id="9cbec-263">Run the following command to export the WSL-generated certificate: `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span></span>
* <span data-ttu-id="9cbec-264">V okně WSL spusťte následující příkaz: `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span><span class="sxs-lookup"><span data-stu-id="9cbec-264">In a WSL window, run the following command: `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span></span>

  <span data-ttu-id="9cbec-265">Předchozí příkaz nastaví proměnné prostředí tak, aby Linux používal důvěryhodný certifikát Windows.</span><span class="sxs-lookup"><span data-stu-id="9cbec-265">The preceding command sets the environment variables so Linux uses the Windows trusted certificate.</span></span>

## <a name="troubleshoot-certificate-problems"></a><span data-ttu-id="9cbec-266">Řešení problémů s certifikáty</span><span class="sxs-lookup"><span data-stu-id="9cbec-266">Troubleshoot certificate problems</span></span>

<span data-ttu-id="9cbec-267">Tato část obsahuje informace o tom, kdy byl certifikát pro vývoj ASP.NET Core HTTPS [nainstalovaný a důvěryhodný](#trust), ale přesto máte upozornění prohlížeče, že certifikát není důvěryhodný.</span><span class="sxs-lookup"><span data-stu-id="9cbec-267">This section provides help when the ASP.NET Core HTTPS development certificate has been [installed and trusted](#trust), but you still have browser warnings that the certificate is not trusted.</span></span> <span data-ttu-id="9cbec-268">ASP.NET Core certifikát pro vývoj HTTPS používá [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="9cbec-268">The ASP.NET Core HTTPS development certificate is used by [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

### <a name="all-platforms---certificate-not-trusted"></a><span data-ttu-id="9cbec-269">Všechny platformy – certifikát není důvěryhodný.</span><span class="sxs-lookup"><span data-stu-id="9cbec-269">All platforms - certificate not trusted</span></span>

<span data-ttu-id="9cbec-270">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="9cbec-270">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="9cbec-271">Zavřete všechny otevřené instance prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="9cbec-271">Close any browser instances open.</span></span> <span data-ttu-id="9cbec-272">Otevřete nové okno prohlížeče pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9cbec-272">Open a new browser window to app.</span></span> <span data-ttu-id="9cbec-273">Důvěryhodnost certifikátu je ukládána v mezipaměti prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="9cbec-273">Certificate trust is cached by browsers.</span></span>

<span data-ttu-id="9cbec-274">Předchozí příkazy vyřeší většinu problémů s důvěryhodností prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="9cbec-274">The preceding commands solve most browser trust issues.</span></span> <span data-ttu-id="9cbec-275">Pokud prohlížeč pořád certifikát nedůvěřuje, postupujte podle následujících doporučení pro konkrétní platformu.</span><span class="sxs-lookup"><span data-stu-id="9cbec-275">If the browser is still not trusting the certificate, follow the platform-specific suggestions that follow.</span></span>

### <a name="docker---certificate-not-trusted"></a><span data-ttu-id="9cbec-276">Docker – certifikát není důvěryhodný.</span><span class="sxs-lookup"><span data-stu-id="9cbec-276">Docker - certificate not trusted</span></span>

* <span data-ttu-id="9cbec-277">Odstraňte složku *C:\Users\{uživateli} \AppData\Roaming\ASP.NET\Https* .</span><span class="sxs-lookup"><span data-stu-id="9cbec-277">Delete the *C:\Users\{USER}\AppData\Roaming\ASP.NET\Https* folder.</span></span>
* <span data-ttu-id="9cbec-278">Vyčistěte řešení.</span><span class="sxs-lookup"><span data-stu-id="9cbec-278">Clean the solution.</span></span> <span data-ttu-id="9cbec-279">Odstraňte složky *bin* a *obj* .</span><span class="sxs-lookup"><span data-stu-id="9cbec-279">Delete the *bin* and *obj* folders.</span></span>
* <span data-ttu-id="9cbec-280">Restartujte nástroj pro vývoj.</span><span class="sxs-lookup"><span data-stu-id="9cbec-280">Restart the development tool.</span></span> <span data-ttu-id="9cbec-281">Například Visual Studio, Visual Studio Code nebo Visual Studio pro Mac.</span><span class="sxs-lookup"><span data-stu-id="9cbec-281">For example, Visual Studio, Visual Studio Code, or Visual Studio for Mac.</span></span>

### <a name="windows---certificate-not-trusted"></a><span data-ttu-id="9cbec-282">Windows – certifikát není důvěryhodný.</span><span class="sxs-lookup"><span data-stu-id="9cbec-282">Windows - certificate not trusted</span></span>

* <span data-ttu-id="9cbec-283">Ověřte certifikáty v úložišti certifikátů.</span><span class="sxs-lookup"><span data-stu-id="9cbec-283">Check the certificates in the certificate store.</span></span> <span data-ttu-id="9cbec-284">V části `Current User > Personal > Certificates` a `Current User > Trusted root certification authorities > Certificates` by měl být `localhost` certifikát s `ASP.NET Core HTTPS development certificate` popisným názvem.</span><span class="sxs-lookup"><span data-stu-id="9cbec-284">There should be a `localhost` certificate with the `ASP.NET Core HTTPS development certificate` friendly name both under `Current User > Personal > Certificates` and `Current User > Trusted root certification authorities > Certificates`</span></span>
* <span data-ttu-id="9cbec-285">Odeberte všechny nalezené certifikáty z osobních i důvěryhodných kořenových certifikačních autorit.</span><span class="sxs-lookup"><span data-stu-id="9cbec-285">Remove all the found certificates from both Personal and Trusted root certification authorities.</span></span> <span data-ttu-id="9cbec-286">**Neodstraňujte** certifikát IIS Express localhost.</span><span class="sxs-lookup"><span data-stu-id="9cbec-286">Do **not** remove the IIS Express localhost certificate.</span></span>
* <span data-ttu-id="9cbec-287">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="9cbec-287">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="9cbec-288">Zavřete všechny otevřené instance prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="9cbec-288">Close any browser instances open.</span></span> <span data-ttu-id="9cbec-289">Otevřete nové okno prohlížeče pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9cbec-289">Open a new browser window to app.</span></span>

### <a name="os-x---certificate-not-trusted"></a><span data-ttu-id="9cbec-290">OS X – certifikát není důvěryhodný.</span><span class="sxs-lookup"><span data-stu-id="9cbec-290">OS X - certificate not trusted</span></span>

* <span data-ttu-id="9cbec-291">Otevřete přístup k řetězci klíčů.</span><span class="sxs-lookup"><span data-stu-id="9cbec-291">Open KeyChain Access.</span></span>
* <span data-ttu-id="9cbec-292">Vyberte systémový řetězec klíčů.</span><span class="sxs-lookup"><span data-stu-id="9cbec-292">Select the System keychain.</span></span>
* <span data-ttu-id="9cbec-293">Ověřte přítomnost certifikátu localhost.</span><span class="sxs-lookup"><span data-stu-id="9cbec-293">Check for the presence of a localhost certificate.</span></span>
* <span data-ttu-id="9cbec-294">Ověřte, že obsahuje symbol `+` na ikoně, abyste označili, že je důvěryhodný pro všechny uživatele.</span><span class="sxs-lookup"><span data-stu-id="9cbec-294">Check that it contains a `+` symbol on the icon to indicate it's trusted for all users.</span></span>
* <span data-ttu-id="9cbec-295">Odeberte certifikát ze systémového řetězce klíčů.</span><span class="sxs-lookup"><span data-stu-id="9cbec-295">Remove the certificate from the system keychain.</span></span>
* <span data-ttu-id="9cbec-296">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="9cbec-296">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="9cbec-297">Zavřete všechny otevřené instance prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="9cbec-297">Close any browser instances open.</span></span> <span data-ttu-id="9cbec-298">Otevřete nové okno prohlížeče pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9cbec-298">Open a new browser window to app.</span></span>

<span data-ttu-id="9cbec-299">Informace o řešení problémů s certifikáty pomocí sady Visual Studio najdete v tématu [Chyba protokolu HTTPS pomocí IIS Express (dotnet/AspNetCore #16892)](https://github.com/dotnet/AspNetCore/issues/16892) .</span><span class="sxs-lookup"><span data-stu-id="9cbec-299">See [HTTPS Error using IIS Express (dotnet/AspNetCore #16892)](https://github.com/dotnet/AspNetCore/issues/16892) for troubleshooting certificate issues with Visual Studio.</span></span>

### <a name="iis-express-ssl-certificate-used-with-visual-studio"></a><span data-ttu-id="9cbec-300">IIS Express certifikát SSL používaný se sadou Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9cbec-300">IIS Express SSL certificate used with Visual Studio</span></span>

<span data-ttu-id="9cbec-301">Pokud chcete opravit problémy s certifikátem IIS Express, vyberte v instalačním programu sady Visual Studio možnost **opravit** .</span><span class="sxs-lookup"><span data-stu-id="9cbec-301">To fix problems with the IIS Express certificate, select **Repair** from the Visual Studio installer.</span></span> <span data-ttu-id="9cbec-302">Další informace najdete v [tomto problému GitHubu](https://github.com/dotnet/aspnetcore/issues/16892).</span><span class="sxs-lookup"><span data-stu-id="9cbec-302">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/16892).</span></span>

## <a name="additional-information"></a><span data-ttu-id="9cbec-303">Další informace</span><span class="sxs-lookup"><span data-stu-id="9cbec-303">Additional information</span></span>

* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="9cbec-304">Hostování ASP.NET Core v systému Linux pomocí Apache: Konfigurace HTTPS</span><span class="sxs-lookup"><span data-stu-id="9cbec-304">Host ASP.NET Core on Linux with Apache: HTTPS configuration</span></span>](xref:host-and-deploy/linux-apache#https-configuration)
* [<span data-ttu-id="9cbec-305">Hostitelská ASP.NET Core v systému Linux s Nginx: Konfigurace HTTPS</span><span class="sxs-lookup"><span data-stu-id="9cbec-305">Host ASP.NET Core on Linux with Nginx: HTTPS configuration</span></span>](xref:host-and-deploy/linux-nginx#https-configuration)
* [<span data-ttu-id="9cbec-306">Jak nastavit SSL v IIS</span><span class="sxs-lookup"><span data-stu-id="9cbec-306">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [<span data-ttu-id="9cbec-307">Podpora prohlížeče OWASP HSTS</span><span class="sxs-lookup"><span data-stu-id="9cbec-307">OWASP HSTS browser support</span></span>](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
