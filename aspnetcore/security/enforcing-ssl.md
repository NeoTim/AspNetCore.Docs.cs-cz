---
title: Vynutilit HTTPS v ASP.NET Core
author: rick-anderson
description: Naučte se vyžadovat protokol HTTPS/TLS ve ASP.NET Core webové aplikaci.
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2019
uid: security/enforcing-ssl
ms.openlocfilehash: eafb06d181ca3f085cccb314749c8d4deba074fa
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082564"
---
# <a name="enforce-https-in-aspnet-core"></a><span data-ttu-id="317d8-103">Vynutilit HTTPS v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="317d8-103">Enforce HTTPS in ASP.NET Core</span></span>

<span data-ttu-id="317d8-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="317d8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="317d8-105">V tomto dokumentu se dozvíte, jak:</span><span class="sxs-lookup"><span data-stu-id="317d8-105">This document shows how to:</span></span>

* <span data-ttu-id="317d8-106">Vyžadovat protokol HTTPS pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="317d8-106">Require HTTPS for all requests.</span></span>
* <span data-ttu-id="317d8-107">Přesměrovat všechny požadavky HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="317d8-107">Redirect all HTTP requests to HTTPS.</span></span>

<span data-ttu-id="317d8-108">Žádné rozhraní API nemůže klientovi zabránit v posílání citlivých dat na první požadavek.</span><span class="sxs-lookup"><span data-stu-id="317d8-108">No API can prevent a client from sending sensitive data on the first request.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="317d8-109">Projekty API</span><span class="sxs-lookup"><span data-stu-id="317d8-109">API projects</span></span>
>
> <span data-ttu-id="317d8-110">Nepoužívejte [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) pro webová rozhraní API, která přijímají citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="317d8-110">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="317d8-111">`RequireHttpsAttribute`pomocí stavových kódů HTTP přesměruje prohlížeče z HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="317d8-111">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="317d8-112">Klienti rozhraní API nemusí pochopit nebo dodržovat přesměrování z HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="317d8-112">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="317d8-113">Tito klienti mohou odesílat informace prostřednictvím protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="317d8-113">Such clients may send information over HTTP.</span></span> <span data-ttu-id="317d8-114">Webové rozhraní API by mělo mít jednu z těchto:</span><span class="sxs-lookup"><span data-stu-id="317d8-114">Web APIs should either:</span></span>
>
> * <span data-ttu-id="317d8-115">Neslouchat na HTTP.</span><span class="sxs-lookup"><span data-stu-id="317d8-115">Not listen on HTTP.</span></span>
> * <span data-ttu-id="317d8-116">Ukončete připojení se stavovým kódem 400 (chybný požadavek) a neobsluhuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="317d8-116">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>
>
> ## <a name="hsts-and-api-projects"></a><span data-ttu-id="317d8-117">Projekty HSTS a API</span><span class="sxs-lookup"><span data-stu-id="317d8-117">HSTS and API projects</span></span>
>
> <span data-ttu-id="317d8-118">Výchozí projekty API neobsahují [HSTS](#hsts) , protože HSTS je všeobecně pouze instrukcí prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="317d8-118">The default API projects don't include [HSTS](#hsts) because HSTS is generally a browser only instruction.</span></span> <span data-ttu-id="317d8-119">Další volající, jako jsou telefony nebo desktopové aplikace, **nedodržují** pokyny.</span><span class="sxs-lookup"><span data-stu-id="317d8-119">Other callers, such as phone or desktop apps, do **not** obey the instruction.</span></span> <span data-ttu-id="317d8-120">I v prohlížečích má jedno ověřované volání rozhraní API přes protokol HTTP rizika v nezabezpečených sítích.</span><span class="sxs-lookup"><span data-stu-id="317d8-120">Even within browsers, a single authenticated call to an API over HTTP has risks on insecure networks.</span></span> <span data-ttu-id="317d8-121">Bezpečným přístupem je nakonfigurovat projekty API tak, aby naslouchaly jenom a reagovaly přes HTTPS.</span><span class="sxs-lookup"><span data-stu-id="317d8-121">The secure approach is to configure API projects to only listen to and respond over HTTPS.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="317d8-122">Projekty API</span><span class="sxs-lookup"><span data-stu-id="317d8-122">API projects</span></span>
>
> <span data-ttu-id="317d8-123">Nepoužívejte [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) pro webová rozhraní API, která přijímají citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="317d8-123">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="317d8-124">`RequireHttpsAttribute`pomocí stavových kódů HTTP přesměruje prohlížeče z HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="317d8-124">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="317d8-125">Klienti rozhraní API nemusí pochopit nebo dodržovat přesměrování z HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="317d8-125">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="317d8-126">Tito klienti mohou odesílat informace prostřednictvím protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="317d8-126">Such clients may send information over HTTP.</span></span> <span data-ttu-id="317d8-127">Webové rozhraní API by mělo mít jednu z těchto:</span><span class="sxs-lookup"><span data-stu-id="317d8-127">Web APIs should either:</span></span>
>
> * <span data-ttu-id="317d8-128">Neslouchat na HTTP.</span><span class="sxs-lookup"><span data-stu-id="317d8-128">Not listen on HTTP.</span></span>
> * <span data-ttu-id="317d8-129">Ukončete připojení se stavovým kódem 400 (chybný požadavek) a neobsluhuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="317d8-129">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>

::: moniker-end

## <a name="require-https"></a><span data-ttu-id="317d8-130">Vyžadovat protokol HTTPS</span><span class="sxs-lookup"><span data-stu-id="317d8-130">Require HTTPS</span></span>

<span data-ttu-id="317d8-131">Doporučujeme, aby provozní ASP.NET Core Web Apps používaly:</span><span class="sxs-lookup"><span data-stu-id="317d8-131">We recommend that production ASP.NET Core web apps use:</span></span>

* <span data-ttu-id="317d8-132">Protokol HTTPS přesměrování middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) pro přesměrování požadavků HTTP na https.</span><span class="sxs-lookup"><span data-stu-id="317d8-132">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) to redirect HTTP requests to HTTPS.</span></span>
* <span data-ttu-id="317d8-133">HSTS middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) k odeslání hlaviček HSTS (http Strict Transport Security Protocol) do klientů.</span><span class="sxs-lookup"><span data-stu-id="317d8-133">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) to send HTTP Strict Transport Security Protocol (HSTS) headers to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="317d8-134">Aplikace nasazené v konfiguraci reverzního proxy serveru umožňují proxy zpracovávat zabezpečení připojení (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="317d8-134">Apps deployed in a reverse proxy configuration allow the proxy to handle connection security (HTTPS).</span></span> <span data-ttu-id="317d8-135">Pokud proxy také zpracovává přesměrování protokolu HTTPS, není nutné používat middleware pro přesměrování protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="317d8-135">If the proxy also handles HTTPS redirection, there's no need to use HTTPS Redirection Middleware.</span></span> <span data-ttu-id="317d8-136">Pokud proxy server také zpracovává zápis hlaviček HSTS (například [nativní podporu HSTS ve službě IIS 10,0 (1709) nebo novější](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), aplikace HSTS middleware není aplikací požadována.</span><span class="sxs-lookup"><span data-stu-id="317d8-136">If the proxy server also handles writing HSTS headers (for example, [native HSTS support in IIS 10.0 (1709) or later](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware isn't required by the app.</span></span> <span data-ttu-id="317d8-137">Další informace najdete v tématu [výslovný nedostatek protokolu HTTPS/HSTS při vytváření projektu](#opt-out-of-httpshsts-on-project-creation).</span><span class="sxs-lookup"><span data-stu-id="317d8-137">For more information, see [Opt-out of HTTPS/HSTS on project creation](#opt-out-of-httpshsts-on-project-creation).</span></span>

### <a name="usehttpsredirection"></a><span data-ttu-id="317d8-138">UseHttpsRedirection</span><span class="sxs-lookup"><span data-stu-id="317d8-138">UseHttpsRedirection</span></span>

<span data-ttu-id="317d8-139">Následující kód volá `UseHttpsRedirection` `Startup` ve třídě:</span><span class="sxs-lookup"><span data-stu-id="317d8-139">The following code calls `UseHttpsRedirection` in the `Startup` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

<span data-ttu-id="317d8-140">Předchozí zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="317d8-140">The preceding highlighted code:</span></span>

* <span data-ttu-id="317d8-141">Používá výchozí [HttpsRedirectionOptions. RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span><span class="sxs-lookup"><span data-stu-id="317d8-141">Uses the default [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span></span>
* <span data-ttu-id="317d8-142">Použije výchozí [HttpsRedirectionOptions. HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null), pokud není přepsán `ASPNETCORE_HTTPS_PORT` proměnnou prostředí nebo [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span><span class="sxs-lookup"><span data-stu-id="317d8-142">Uses the default [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null) unless overridden by the `ASPNETCORE_HTTPS_PORT` environment variable or [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span></span>

<span data-ttu-id="317d8-143">Doporučujeme místo trvalých přesměrování použít dočasné přesměrování.</span><span class="sxs-lookup"><span data-stu-id="317d8-143">We recommend using temporary redirects rather than permanent redirects.</span></span> <span data-ttu-id="317d8-144">Ukládání odkazů do mezipaměti může způsobit nestabilní chování ve vývojových prostředích.</span><span class="sxs-lookup"><span data-stu-id="317d8-144">Link caching can cause unstable behavior in development environments.</span></span> <span data-ttu-id="317d8-145">Pokud upřednostňujete odeslání trvalého stavového kódu přesměrování, když je aplikace v nevývojovém prostředí, přečtěte si část [Konfigurace trvalých přesměrování v produkčním](#configure-permanent-redirects-in-production) prostředí.</span><span class="sxs-lookup"><span data-stu-id="317d8-145">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, see the [Configure permanent redirects in production](#configure-permanent-redirects-in-production) section.</span></span> <span data-ttu-id="317d8-146">Doporučujeme používat [HSTS](#http-strict-transport-security-protocol-hsts) k signalizaci klientům, aby se do aplikace poslaly jenom zabezpečené požadavky na prostředky (jenom v produkčním prostředí).</span><span class="sxs-lookup"><span data-stu-id="317d8-146">We recommend using [HSTS](#http-strict-transport-security-protocol-hsts) to signal to clients that only secure resource requests should be sent to the app (only in production).</span></span>

### <a name="port-configuration"></a><span data-ttu-id="317d8-147">Konfigurace portu</span><span class="sxs-lookup"><span data-stu-id="317d8-147">Port configuration</span></span>

<span data-ttu-id="317d8-148">Pro middleware musí být k dispozici port pro přesměrování nezabezpečené žádosti na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="317d8-148">A port must be available for the middleware to redirect an insecure request to HTTPS.</span></span> <span data-ttu-id="317d8-149">Pokud není k dispozici žádný port:</span><span class="sxs-lookup"><span data-stu-id="317d8-149">If no port is available:</span></span>

* <span data-ttu-id="317d8-150">Přesměrování na HTTPS neproběhne.</span><span class="sxs-lookup"><span data-stu-id="317d8-150">Redirection to HTTPS doesn't occur.</span></span>
* <span data-ttu-id="317d8-151">Middleware zaznamená upozornění. "Nepodařilo se určit port HTTPS pro přesměrování".</span><span class="sxs-lookup"><span data-stu-id="317d8-151">The middleware logs the warning "Failed to determine the https port for redirect."</span></span>

<span data-ttu-id="317d8-152">Port HTTPS určete pomocí některého z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="317d8-152">Specify the HTTPS port using any of the following approaches:</span></span>

* <span data-ttu-id="317d8-153">Nastavte [HttpsRedirectionOptions. HttpsPort](#options).</span><span class="sxs-lookup"><span data-stu-id="317d8-153">Set [HttpsRedirectionOptions.HttpsPort](#options).</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="317d8-154">Nastavte nastavení [hostitele:](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port) `https_port`</span><span class="sxs-lookup"><span data-stu-id="317d8-154">Set the `https_port` [host setting](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port):</span></span>

  * <span data-ttu-id="317d8-155">V konfiguraci hostitele.</span><span class="sxs-lookup"><span data-stu-id="317d8-155">In host configuration.</span></span>
  * <span data-ttu-id="317d8-156">Nastavením `ASPNETCORE_HTTPS_PORT` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="317d8-156">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="317d8-157">Přidáním položky nejvyšší úrovně v souboru *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="317d8-157">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/appsettings.json?highlight=2)]

* <span data-ttu-id="317d8-158">Označení portu pomocí zabezpečeného schématu pomocí [proměnné prostředí ASPNETCORE_URLS](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls)</span><span class="sxs-lookup"><span data-stu-id="317d8-158">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls).</span></span> <span data-ttu-id="317d8-159">Proměnná prostředí nakonfiguruje server.</span><span class="sxs-lookup"><span data-stu-id="317d8-159">The environment variable configures the server.</span></span> <span data-ttu-id="317d8-160">Middleware nepřímo vyhledá port HTTPS prostřednictvím <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="317d8-160">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="317d8-161">Tento přístup nefunguje v nasazeních reverzních proxy serverů.</span><span class="sxs-lookup"><span data-stu-id="317d8-161">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* <span data-ttu-id="317d8-162">Nastavte nastavení [hostitele:](xref:fundamentals/host/web-host#https-port) `https_port`</span><span class="sxs-lookup"><span data-stu-id="317d8-162">Set the `https_port` [host setting](xref:fundamentals/host/web-host#https-port):</span></span>

  * <span data-ttu-id="317d8-163">V konfiguraci hostitele.</span><span class="sxs-lookup"><span data-stu-id="317d8-163">In host configuration.</span></span>
  * <span data-ttu-id="317d8-164">Nastavením `ASPNETCORE_HTTPS_PORT` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="317d8-164">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="317d8-165">Přidáním položky nejvyšší úrovně v souboru *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="317d8-165">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/appsettings.json?highlight=2)]

* <span data-ttu-id="317d8-166">Označení portu pomocí zabezpečeného schématu pomocí [proměnné prostředí ASPNETCORE_URLS](xref:fundamentals/host/web-host#server-urls)</span><span class="sxs-lookup"><span data-stu-id="317d8-166">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](xref:fundamentals/host/web-host#server-urls).</span></span> <span data-ttu-id="317d8-167">Proměnná prostředí nakonfiguruje server.</span><span class="sxs-lookup"><span data-stu-id="317d8-167">The environment variable configures the server.</span></span> <span data-ttu-id="317d8-168">Middleware nepřímo vyhledá port HTTPS prostřednictvím <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="317d8-168">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="317d8-169">Tento přístup nefunguje v nasazeních reverzních proxy serverů.</span><span class="sxs-lookup"><span data-stu-id="317d8-169">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

* <span data-ttu-id="317d8-170">Ve vývojovém prostředí nastavte adresu URL HTTPS v souboru *launchsettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="317d8-170">In development, set an HTTPS URL in *launchsettings.json*.</span></span> <span data-ttu-id="317d8-171">Pokud se používá IIS Express, povolte HTTPS.</span><span class="sxs-lookup"><span data-stu-id="317d8-171">Enable HTTPS when IIS Express is used.</span></span>

* <span data-ttu-id="317d8-172">Nakonfigurujte koncový bod adresy URL HTTPS pro nasazení Edge serveru [Kestrel](xref:fundamentals/servers/kestrel) Server nebo [http. sys](xref:fundamentals/servers/httpsys) s přístupem k veřejnému.</span><span class="sxs-lookup"><span data-stu-id="317d8-172">Configure an HTTPS URL endpoint for a public-facing edge deployment of [Kestrel](xref:fundamentals/servers/kestrel) server or [HTTP.sys](xref:fundamentals/servers/httpsys) server.</span></span> <span data-ttu-id="317d8-173">Aplikace používá jenom **jeden port HTTPS** .</span><span class="sxs-lookup"><span data-stu-id="317d8-173">Only **one HTTPS port** is used by the app.</span></span> <span data-ttu-id="317d8-174">Middleware zjistí port prostřednictvím <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="317d8-174">The middleware discovers the port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span>

> [!NOTE]
> <span data-ttu-id="317d8-175">Když je aplikace spuštěná v konfiguraci reverzního proxy serveru <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> , není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="317d8-175">When an app is run in a reverse proxy configuration, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> isn't available.</span></span> <span data-ttu-id="317d8-176">Port nastavte pomocí některého z dalších přístupů popsaných v této části.</span><span class="sxs-lookup"><span data-stu-id="317d8-176">Set the port using one of the other approaches described in this section.</span></span>

### <a name="edge-deployments"></a><span data-ttu-id="317d8-177">Nasazení Edge</span><span class="sxs-lookup"><span data-stu-id="317d8-177">Edge deployments</span></span> 

<span data-ttu-id="317d8-178">Pokud se Kestrel nebo HTTP. sys používá jako veřejný server Edge, Kestrel nebo HTTP. sys musí být nakonfigurované tak, aby naslouchal na obou těchto počítačích:</span><span class="sxs-lookup"><span data-stu-id="317d8-178">When Kestrel or HTTP.sys is used as a public-facing edge server, Kestrel or HTTP.sys must be configured to listen on both:</span></span>

* <span data-ttu-id="317d8-179">Zabezpečený port, ve kterém je klient přesměrován (obvykle 443 v produkčním a 5001 ve vývoji).</span><span class="sxs-lookup"><span data-stu-id="317d8-179">The secure port where the client is redirected (typically, 443 in production and 5001 in development).</span></span>
* <span data-ttu-id="317d8-180">Nezabezpečený port (obvykle 80 v produkčním a 5000 ve vývoji).</span><span class="sxs-lookup"><span data-stu-id="317d8-180">The insecure port (typically, 80 in production and 5000 in development).</span></span>

<span data-ttu-id="317d8-181">Aby aplikace přijímala nezabezpečený požadavek a přesměrovala klienta na zabezpečený port, musí být nezabezpečený port přístupný klientovi.</span><span class="sxs-lookup"><span data-stu-id="317d8-181">The insecure port must be accessible by the client in order for the app to receive an insecure request and redirect the client to the secure port.</span></span>

<span data-ttu-id="317d8-182">Další informace najdete v tématu [Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) nebo <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="317d8-182">For more information, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) or <xref:fundamentals/servers/httpsys>.</span></span>

### <a name="deployment-scenarios"></a><span data-ttu-id="317d8-183">Scénáře nasazení</span><span class="sxs-lookup"><span data-stu-id="317d8-183">Deployment scenarios</span></span>

<span data-ttu-id="317d8-184">Všechny brány firewall mezi klientem a serverem musí mít také otevřené komunikační porty pro provoz.</span><span class="sxs-lookup"><span data-stu-id="317d8-184">Any firewall between the client and server must also have communication ports open for traffic.</span></span>

<span data-ttu-id="317d8-185">Pokud se požadavky předávají v konfiguraci reverzního proxy serveru, před voláním middleware pro přesměrování protokolu HTTPS použijte [middleware pro předané hlavičky](xref:host-and-deploy/proxy-load-balancer) .</span><span class="sxs-lookup"><span data-stu-id="317d8-185">If requests are forwarded in a reverse proxy configuration, use [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) before calling HTTPS Redirection Middleware.</span></span> <span data-ttu-id="317d8-186">Middleware s předanými `Request.Scheme`hlavičkami aktualizuje `X-Forwarded-Proto` , pomocí hlavičky.</span><span class="sxs-lookup"><span data-stu-id="317d8-186">Forwarded Headers Middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header.</span></span> <span data-ttu-id="317d8-187">Middleware povoluje správné fungování identifikátorů URI přesměrování a dalších zásad zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="317d8-187">The middleware permits redirect URIs and other security policies to work correctly.</span></span> <span data-ttu-id="317d8-188">Když se nepoužije middleware předávaných hlaviček, back-end aplikace nemusí získat správné schéma a končit smyčkou přesměrování.</span><span class="sxs-lookup"><span data-stu-id="317d8-188">When Forwarded Headers Middleware isn't used, the backend app might not receive the correct scheme and end up in a redirect loop.</span></span> <span data-ttu-id="317d8-189">Společná chybová zpráva koncového uživatele je, že došlo k příliš velkému počtu přesměrování.</span><span class="sxs-lookup"><span data-stu-id="317d8-189">A common end user error message is that too many redirects have occurred.</span></span>

<span data-ttu-id="317d8-190">Při nasazování do Azure App Service postupujte podle pokynů v [kurzu: Navažte stávající vlastní certifikát SSL na Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="317d8-190">When deploying to Azure App Service, follow the guidance in [Tutorial: Bind an existing custom SSL certificate to Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

### <a name="options"></a><span data-ttu-id="317d8-191">Možnosti</span><span class="sxs-lookup"><span data-stu-id="317d8-191">Options</span></span>

<span data-ttu-id="317d8-192">Následující zvýrazněný kód volá [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) ke konfiguraci možností middlewaru:</span><span class="sxs-lookup"><span data-stu-id="317d8-192">The following highlighted code calls [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) to configure middleware options:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


<span data-ttu-id="317d8-193">Volání `AddHttpsRedirection` je nezbytné pouze ke změně `HttpsPort` hodnot nebo `RedirectStatusCode`.</span><span class="sxs-lookup"><span data-stu-id="317d8-193">Calling `AddHttpsRedirection` is only necessary to change the values of `HttpsPort` or `RedirectStatusCode`.</span></span>

<span data-ttu-id="317d8-194">Předchozí zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="317d8-194">The preceding highlighted code:</span></span>

* <span data-ttu-id="317d8-195">Nastaví [HttpsRedirectionOptions. RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) na <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, což je výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="317d8-195">Sets [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) to <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, which is the default value.</span></span> <span data-ttu-id="317d8-196">Použijte pole <xref:Microsoft.AspNetCore.Http.StatusCodes> třídy pro přiřazení k `RedirectStatusCode`.</span><span class="sxs-lookup"><span data-stu-id="317d8-196">Use the fields of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for assignments to `RedirectStatusCode`.</span></span>
* <span data-ttu-id="317d8-197">Nastaví port HTTPS na 5001.</span><span class="sxs-lookup"><span data-stu-id="317d8-197">Sets the HTTPS port to 5001.</span></span> <span data-ttu-id="317d8-198">Výchozí hodnota je 443.</span><span class="sxs-lookup"><span data-stu-id="317d8-198">The default value is 443.</span></span>

#### <a name="configure-permanent-redirects-in-production"></a><span data-ttu-id="317d8-199">Konfigurace trvalých přesměrování v produkčním prostředí</span><span class="sxs-lookup"><span data-stu-id="317d8-199">Configure permanent redirects in production</span></span>

<span data-ttu-id="317d8-200">Middleware ve výchozím nastavení odesílá [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) pomocí všech přesměrování.</span><span class="sxs-lookup"><span data-stu-id="317d8-200">The middleware defaults to sending a [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) with all redirects.</span></span> <span data-ttu-id="317d8-201">Pokud upřednostňujete odeslání trvalého stavového kódu přesměrování, pokud je aplikace v nevývojovém prostředí, zabalte konfiguraci možností middlewaru do podmíněné kontroly pro nevývojové prostředí.</span><span class="sxs-lookup"><span data-stu-id="317d8-201">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, wrap the middleware options configuration in a conditional check for a non-Development environment.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="317d8-202">Při konfiguraci služeb v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="317d8-202">When configuring services in *Startup.cs*:</span></span>

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

<span data-ttu-id="317d8-203">Při konfiguraci služeb v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="317d8-203">When configuring services in *Startup.cs*:</span></span>

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


## <a name="https-redirection-middleware-alternative-approach"></a><span data-ttu-id="317d8-204">Alternativní přístup middlewaru přesměrování HTTPS</span><span class="sxs-lookup"><span data-stu-id="317d8-204">HTTPS Redirection Middleware alternative approach</span></span>

<span data-ttu-id="317d8-205">Alternativou k použití middlewaru pro přesměrování`UseHttpsRedirection`protokolu HTTPS () je použití přepisu adresy`AddRedirectToHttps`URL ().</span><span class="sxs-lookup"><span data-stu-id="317d8-205">An alternative to using HTTPS Redirection Middleware (`UseHttpsRedirection`) is to use URL Rewriting Middleware (`AddRedirectToHttps`).</span></span> <span data-ttu-id="317d8-206">`AddRedirectToHttps`může také nastavit stavový kód a port při spuštění přesměrování.</span><span class="sxs-lookup"><span data-stu-id="317d8-206">`AddRedirectToHttps` can also set the status code and port when the redirect is executed.</span></span> <span data-ttu-id="317d8-207">Další informace najdete v tématu [middleware pro přepis adres URL](xref:fundamentals/url-rewriting).</span><span class="sxs-lookup"><span data-stu-id="317d8-207">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span>

<span data-ttu-id="317d8-208">Při přesměrování na https bez požadavku na další pravidla přesměrování doporučujeme použít middleware pro přesměrování protokolu HTTPS (`UseHttpsRedirection`) popsaný v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="317d8-208">When redirecting to HTTPS without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware (`UseHttpsRedirection`) described in this topic.</span></span>

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a><span data-ttu-id="317d8-209">Protokol HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="317d8-209">HTTP Strict Transport Security Protocol (HSTS)</span></span>

<span data-ttu-id="317d8-210">Za [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project)je [zabezpečení protokolu HTTP (HSTS Strict Transport Security)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) na základě souhlasu s hlavičkou odpovědi zadané pomocí webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="317d8-210">Per [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [HTTP Strict Transport Security (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) is an opt-in security enhancement that's specified by a web app through the use of a response header.</span></span> <span data-ttu-id="317d8-211">Když [prohlížeč, který podporuje HSTS,](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) obdrží tuto hlavičku:</span><span class="sxs-lookup"><span data-stu-id="317d8-211">When a [browser that supports HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) receives this header:</span></span>

* <span data-ttu-id="317d8-212">Prohlížeč ukládá konfiguraci pro doménu, která znemožňuje odeslání jakékoli komunikace prostřednictvím protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="317d8-212">The browser stores configuration for the domain that prevents sending any communication over HTTP.</span></span> <span data-ttu-id="317d8-213">Prohlížeč vynutí veškerou komunikaci přes protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="317d8-213">The browser forces all communication over HTTPS.</span></span>
* <span data-ttu-id="317d8-214">Prohlížeč brání uživateli v používání nedůvěryhodných nebo neplatných certifikátů.</span><span class="sxs-lookup"><span data-stu-id="317d8-214">The browser prevents the user from using untrusted or invalid certificates.</span></span> <span data-ttu-id="317d8-215">Prohlížeč zakáže výzvy, které umožní uživateli dočasně důvěřovat takovému certifikátu.</span><span class="sxs-lookup"><span data-stu-id="317d8-215">The browser disables prompts that allow a user to temporarily trust such a certificate.</span></span>

<span data-ttu-id="317d8-216">Vzhledem k tomu, že klient vynutil HSTS, má některá omezení:</span><span class="sxs-lookup"><span data-stu-id="317d8-216">Because HSTS is enforced by the client it has some limitations:</span></span>

* <span data-ttu-id="317d8-217">Klient musí podporovat HSTS.</span><span class="sxs-lookup"><span data-stu-id="317d8-217">The client must support HSTS.</span></span>
* <span data-ttu-id="317d8-218">HSTS vyžaduje alespoň jednu úspěšnou žádost HTTPS k vytvoření zásady HSTS.</span><span class="sxs-lookup"><span data-stu-id="317d8-218">HSTS requires at least one successful HTTPS request to establish the HSTS policy.</span></span>
* <span data-ttu-id="317d8-219">Aplikace musí kontrolovat všechny požadavky HTTP a přesměrovat nebo zamítnout požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="317d8-219">The application must check every HTTP request and redirect or reject the HTTP request.</span></span>

<span data-ttu-id="317d8-220">ASP.NET Core 2,1 a novější implementuje HSTS s `UseHsts` metodou rozšíření.</span><span class="sxs-lookup"><span data-stu-id="317d8-220">ASP.NET Core 2.1 and later implements HSTS with the `UseHsts` extension method.</span></span> <span data-ttu-id="317d8-221">Následující kód volá `UseHsts` , když aplikace není v [režimu pro vývoj](xref:fundamentals/environments):</span><span class="sxs-lookup"><span data-stu-id="317d8-221">The following code calls `UseHsts` when the app isn't in [development mode](xref:fundamentals/environments):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

<span data-ttu-id="317d8-222">`UseHsts`nedoporučuje se při vývoji, protože nastavení HSTS jsou prohlížeči vysoce ukládat do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="317d8-222">`UseHsts` isn't recommended in development because the HSTS settings are highly cacheable by browsers.</span></span> <span data-ttu-id="317d8-223">Ve výchozím nastavení `UseHsts` vyloučí místní adresu zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="317d8-223">By default, `UseHsts` excludes the local loopback address.</span></span>

<span data-ttu-id="317d8-224">V produkčních prostředích, která implementují protokol HTTPS poprvé, nastavte počáteční [HstsOptions. maxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) na malou hodnotu pomocí jedné z <xref:System.TimeSpan> metod.</span><span class="sxs-lookup"><span data-stu-id="317d8-224">For production environments that are implementing HTTPS for the first time, set the initial [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) to a small value using one of the <xref:System.TimeSpan> methods.</span></span> <span data-ttu-id="317d8-225">Nastavte hodnotu z hodin na ne více než jeden den pro případ, že budete potřebovat obnovit infrastrukturu HTTPS na HTTP.</span><span class="sxs-lookup"><span data-stu-id="317d8-225">Set the value from hours to no more than a single day in case you need to revert the HTTPS infrastructure to HTTP.</span></span> <span data-ttu-id="317d8-226">Až si budete jisti udržitelností konfigurace HTTPS, zvyšte hodnotu maximálního stáří HSTS; běžně používaná hodnota je jeden rok.</span><span class="sxs-lookup"><span data-stu-id="317d8-226">After you're confident in the sustainability of the HTTPS configuration, increase the HSTS max-age value; a commonly used value is one year.</span></span>

<span data-ttu-id="317d8-227">Následující kód:</span><span class="sxs-lookup"><span data-stu-id="317d8-227">The following code:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* <span data-ttu-id="317d8-228">Nastaví parametr přednačtení záhlaví Strict-Transport-Security.</span><span class="sxs-lookup"><span data-stu-id="317d8-228">Sets the preload parameter of the Strict-Transport-Security header.</span></span> <span data-ttu-id="317d8-229">Předběžné načtení není součástí [specifikace RFC HSTS](https://tools.ietf.org/html/rfc6797), ale podporuje je ve webových prohlížečích k přednačtení webů HSTS při nové instalaci.</span><span class="sxs-lookup"><span data-stu-id="317d8-229">Preload isn't part of the [RFC HSTS specification](https://tools.ietf.org/html/rfc6797), but is supported by web browsers to preload HSTS sites on fresh install.</span></span> <span data-ttu-id="317d8-230">Další [https://hstspreload.org/](https://hstspreload.org/) informace najdete v tématu.</span><span class="sxs-lookup"><span data-stu-id="317d8-230">See [https://hstspreload.org/](https://hstspreload.org/) for more information.</span></span>
* <span data-ttu-id="317d8-231">Povolí [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), která aplikuje zásady HSTS na hostování subdomén.</span><span class="sxs-lookup"><span data-stu-id="317d8-231">Enables [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), which applies the HSTS policy to Host subdomains.</span></span>
* <span data-ttu-id="317d8-232">Explicitně nastaví parametr max-age záhlaví Strict-Transport-Security na 60 dní.</span><span class="sxs-lookup"><span data-stu-id="317d8-232">Explicitly sets the max-age parameter of the Strict-Transport-Security header to 60 days.</span></span> <span data-ttu-id="317d8-233">Pokud není nastavené, výchozí hodnota je 30 dní.</span><span class="sxs-lookup"><span data-stu-id="317d8-233">If not set, defaults to 30 days.</span></span> <span data-ttu-id="317d8-234">Další informace najdete v [direktivě pro maximální stáří](https://tools.ietf.org/html/rfc6797#section-6.1.1) .</span><span class="sxs-lookup"><span data-stu-id="317d8-234">See the [max-age directive](https://tools.ietf.org/html/rfc6797#section-6.1.1) for more information.</span></span>
* <span data-ttu-id="317d8-235">Přidá `example.com` do seznamu hostitelů, které mají být vyloučeny.</span><span class="sxs-lookup"><span data-stu-id="317d8-235">Adds `example.com` to the list of hosts to exclude.</span></span>

<span data-ttu-id="317d8-236">`UseHsts`vyloučí následující hostitele zpětné smyčky:</span><span class="sxs-lookup"><span data-stu-id="317d8-236">`UseHsts` excludes the following loopback hosts:</span></span>

* <span data-ttu-id="317d8-237">`localhost` : Adresa zpětné smyčky IPv4.</span><span class="sxs-lookup"><span data-stu-id="317d8-237">`localhost` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="317d8-238">`127.0.0.1` : Adresa zpětné smyčky IPv4.</span><span class="sxs-lookup"><span data-stu-id="317d8-238">`127.0.0.1` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="317d8-239">`[::1]` : Adresa zpětné smyčky IPv6.</span><span class="sxs-lookup"><span data-stu-id="317d8-239">`[::1]` : The IPv6 loopback address.</span></span>

## <a name="opt-out-of-httpshsts-on-project-creation"></a><span data-ttu-id="317d8-240">Výslovný nesouhlas s protokolem HTTPS/HSTS při vytváření projektu</span><span class="sxs-lookup"><span data-stu-id="317d8-240">Opt-out of HTTPS/HSTS on project creation</span></span>

<span data-ttu-id="317d8-241">V některých případech služby back-end, kde se zabezpečení připojení zpracovává na veřejném hraničním serveru sítě, se konfigurace zabezpečení připojení na jednotlivých uzlech nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="317d8-241">In some backend service scenarios where connection security is handled at the public-facing edge of the network, configuring connection security at each node isn't required.</span></span> <span data-ttu-id="317d8-242">Webové aplikace, které jsou generovány ze šablon v aplikaci Visual Studio nebo pomocí příkazu [dotnet New](/dotnet/core/tools/dotnet-new) , umožňují [přesměrování https](#require-https) a [HSTS](#http-strict-transport-security-protocol-hsts).</span><span class="sxs-lookup"><span data-stu-id="317d8-242">Web apps that are generated from the templates in Visual Studio or from the [dotnet new](/dotnet/core/tools/dotnet-new) command enable [HTTPS redirection](#require-https) and [HSTS](#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="317d8-243">Pro nasazení, která tyto scénáře nevyžadují, můžete při vytvoření aplikace ze šablony vyjádřit výslovný souhlas s HTTPS/HSTS.</span><span class="sxs-lookup"><span data-stu-id="317d8-243">For deployments that don't require these scenarios, you can opt-out of HTTPS/HSTS when the app is created from the template.</span></span>

<span data-ttu-id="317d8-244">Výslovný souhlas s protokolem HTTPS/HSTS:</span><span class="sxs-lookup"><span data-stu-id="317d8-244">To opt-out of HTTPS/HSTS:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="317d8-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="317d8-245">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="317d8-246">Zrušte zaškrtnuté políčko **Konfigurovat pro protokol HTTPS** .</span><span class="sxs-lookup"><span data-stu-id="317d8-246">Uncheck the **Configure for HTTPS** check box.</span></span>

::: moniker range=">= aspnetcore-3.0"

![Dialogové okno Nová ASP.NET Core webové aplikace zobrazující nezaškrtnuté políčko konfigurovat pro protokol HTTPS.](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

![Dialogové okno Nová ASP.NET Core webové aplikace zobrazující nezaškrtnuté políčko konfigurovat pro protokol HTTPS.](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="317d8-249">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="317d8-249">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="317d8-250">`--no-https` Použijte možnost.</span><span class="sxs-lookup"><span data-stu-id="317d8-250">Use the `--no-https` option.</span></span> <span data-ttu-id="317d8-251">Příklad</span><span class="sxs-lookup"><span data-stu-id="317d8-251">For example</span></span>

```dotnetcli
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a><span data-ttu-id="317d8-252">Důvěra ASP.NET Core certifikát pro vývoj HTTPS ve Windows a macOS</span><span class="sxs-lookup"><span data-stu-id="317d8-252">Trust the ASP.NET Core HTTPS development certificate on Windows and macOS</span></span>

<span data-ttu-id="317d8-253">.NET Core SDK obsahuje certifikát pro vývoj HTTPS.</span><span class="sxs-lookup"><span data-stu-id="317d8-253">The .NET Core SDK includes an HTTPS development certificate.</span></span> <span data-ttu-id="317d8-254">Certifikát je nainstalován jako součást prvního spuštění prostředí.</span><span class="sxs-lookup"><span data-stu-id="317d8-254">The certificate is installed as part of the first-run experience.</span></span> <span data-ttu-id="317d8-255">Například `dotnet --info` vytváří výstup podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="317d8-255">For example, `dotnet --info` produces output similar to the following:</span></span>

```text
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

<span data-ttu-id="317d8-256">Instalace .NET Core SDK nainstaluje certifikát pro vývoj ASP.NET Core HTTPS do úložiště certifikátů místního uživatele.</span><span class="sxs-lookup"><span data-stu-id="317d8-256">Installing the .NET Core SDK installs the ASP.NET Core HTTPS development certificate to the local user certificate store.</span></span> <span data-ttu-id="317d8-257">Certifikát je nainstalovaný, ale není důvěryhodný.</span><span class="sxs-lookup"><span data-stu-id="317d8-257">The certificate has been installed, but it's not trusted.</span></span> <span data-ttu-id="317d8-258">Pro důvěřování certifikátu proveďte jednorázový krok ke spuštění nástroje dotnet `dev-certs` :</span><span class="sxs-lookup"><span data-stu-id="317d8-258">To trust the certificate perform the one-time step to run the dotnet `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="317d8-259">Následující příkaz nabízí informace o `dev-certs` nástroji:</span><span class="sxs-lookup"><span data-stu-id="317d8-259">The following command provides help on the `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a><span data-ttu-id="317d8-260">Jak nastavit certifikát pro vývojáře pro Docker</span><span class="sxs-lookup"><span data-stu-id="317d8-260">How to set up a developer certificate for Docker</span></span>

<span data-ttu-id="317d8-261">Podívejte se na [Tento problém GitHubu](https://github.com/aspnet/AspNetCore.Docs/issues/6199).</span><span class="sxs-lookup"><span data-stu-id="317d8-261">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6199).</span></span>

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a><span data-ttu-id="317d8-262">Důvěřovat certifikátu HTTPS v subsystému Windows pro Linux</span><span class="sxs-lookup"><span data-stu-id="317d8-262">Trust HTTPS certificate from Windows Subsystem for Linux</span></span>

<span data-ttu-id="317d8-263">Subsystém Windows pro Linux (WSL) vygeneruje certifikát podepsaný svým držitelem (HTTPS). Konfigurace úložiště certifikátů Windows pro důvěřování certifikátu WSL:</span><span class="sxs-lookup"><span data-stu-id="317d8-263">The Windows Subsystem for Linux (WSL) generates a HTTPS self-signed cert. To configure the Windows certificate store to trust the WSL certificate:</span></span>

* <span data-ttu-id="317d8-264">Spuštěním následujícího příkazu exportujte certifikát vygenerovaný WSL:`dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span><span class="sxs-lookup"><span data-stu-id="317d8-264">Run the following command to export the WSL generated certificate: `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span></span>
* <span data-ttu-id="317d8-265">V okně WSL spusťte následující příkaz:`ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span><span class="sxs-lookup"><span data-stu-id="317d8-265">In a WSL window, run the following command: `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span></span>

  <span data-ttu-id="317d8-266">Předchozí příkaz nastaví proměnné prostředí tak, aby Linux používal důvěryhodný certifikát Windows.</span><span class="sxs-lookup"><span data-stu-id="317d8-266">The preceding command sets the environment variables so Linux uses the Windows trusted certificate.</span></span>

## <a name="additional-information"></a><span data-ttu-id="317d8-267">Další informace</span><span class="sxs-lookup"><span data-stu-id="317d8-267">Additional information</span></span>

* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="317d8-268">Hostování ASP.NET Core v systému Linux pomocí Apache: Konfigurace HTTPS</span><span class="sxs-lookup"><span data-stu-id="317d8-268">Host ASP.NET Core on Linux with Apache: HTTPS configuration</span></span>](xref:host-and-deploy/linux-apache#https-configuration)
* [<span data-ttu-id="317d8-269">ASP.NET Core hostitele na platformě Linux s Nginx: Konfigurace HTTPS</span><span class="sxs-lookup"><span data-stu-id="317d8-269">Host ASP.NET Core on Linux with Nginx: HTTPS configuration</span></span>](xref:host-and-deploy/linux-nginx#https-configuration)
* [<span data-ttu-id="317d8-270">Jak nastavit SSL v IIS</span><span class="sxs-lookup"><span data-stu-id="317d8-270">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [<span data-ttu-id="317d8-271">Podpora prohlížeče OWASP HSTS</span><span class="sxs-lookup"><span data-stu-id="317d8-271">OWASP HSTS browser support</span></span>](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
