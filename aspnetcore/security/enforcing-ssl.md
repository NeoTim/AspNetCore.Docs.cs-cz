---
title: Vynucení protokolu HTTPS v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak chcete vyžadovat protokol HTTPS/TLS ve webové aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/01/2018
uid: security/enforcing-ssl
ms.openlocfilehash: 08ce50775d1b5348cb0528a1724cec2e5c72dae2
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152899"
---
# <a name="enforce-https-in-aspnet-core"></a><span data-ttu-id="8b40c-103">Vynucení protokolu HTTPS v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8b40c-103">Enforce HTTPS in ASP.NET Core</span></span>

<span data-ttu-id="8b40c-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8b40c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8b40c-105">Tento dokument ukazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="8b40c-105">This document shows how to:</span></span>

* <span data-ttu-id="8b40c-106">Vyžadovat protokol HTTPS pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="8b40c-106">Require HTTPS for all requests.</span></span>
* <span data-ttu-id="8b40c-107">Přesměrujte všechny požadavky HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8b40c-107">Redirect all HTTP requests to HTTPS.</span></span>

<span data-ttu-id="8b40c-108">Žádné rozhraní API můžete zabránit posláním citlivých dat na první požadavek klienta.</span><span class="sxs-lookup"><span data-stu-id="8b40c-108">No API can prevent a client from sending sensitive data on the first request.</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="8b40c-109">Projekty rozhraní API</span><span class="sxs-lookup"><span data-stu-id="8b40c-109">API projects</span></span>
>
> <span data-ttu-id="8b40c-110">Proveďte **není** použít [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) na webová rozhraní API, která zobrazit citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="8b40c-110">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="8b40c-111">`RequireHttpsAttribute` stavové kódy HTTP se používá pro přesměrování prohlížeče z HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8b40c-111">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="8b40c-112">Klienti rozhraní API nemusí pochopit a dodržují přesměrování z HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8b40c-112">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="8b40c-113">Tito klienti mohou odesílat informace přes protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="8b40c-113">Such clients may send information over HTTP.</span></span> <span data-ttu-id="8b40c-114">Webová rozhraní API by buď:</span><span class="sxs-lookup"><span data-stu-id="8b40c-114">Web APIs should either:</span></span>
>
> * <span data-ttu-id="8b40c-115">Nelze naslouchat na HTTP.</span><span class="sxs-lookup"><span data-stu-id="8b40c-115">Not listen on HTTP.</span></span>
> * <span data-ttu-id="8b40c-116">Ukončete připojení s stavový kód 400 (Chybný požadavek) a není obsluhovat žádosti.</span><span class="sxs-lookup"><span data-stu-id="8b40c-116">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="8b40c-117">Projekty rozhraní API</span><span class="sxs-lookup"><span data-stu-id="8b40c-117">API projects</span></span>
>
> <span data-ttu-id="8b40c-118">Proveďte **není** použít [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) na webová rozhraní API, která zobrazit citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="8b40c-118">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="8b40c-119">`RequireHttpsAttribute` stavové kódy HTTP se používá pro přesměrování prohlížeče z HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8b40c-119">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="8b40c-120">Klienti rozhraní API nemusí pochopit a dodržují přesměrování z HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8b40c-120">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="8b40c-121">Tito klienti mohou odesílat informace přes protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="8b40c-121">Such clients may send information over HTTP.</span></span> <span data-ttu-id="8b40c-122">Webová rozhraní API by buď:</span><span class="sxs-lookup"><span data-stu-id="8b40c-122">Web APIs should either:</span></span>
>
> * <span data-ttu-id="8b40c-123">Nelze naslouchat na HTTP.</span><span class="sxs-lookup"><span data-stu-id="8b40c-123">Not listen on HTTP.</span></span>
> * <span data-ttu-id="8b40c-124">Ukončete připojení s stavový kód 400 (Chybný požadavek) a není obsluhovat žádosti.</span><span class="sxs-lookup"><span data-stu-id="8b40c-124">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>
>
> ## <a name="hsts-and-api-projects"></a><span data-ttu-id="8b40c-125">Projekty HSTS a rozhraní API</span><span class="sxs-lookup"><span data-stu-id="8b40c-125">HSTS and API projects</span></span>
>
> <span data-ttu-id="8b40c-126">Výchozí rozhraní API projekty neobsahují [HSTS](#hsts) protože HSTS je obecná pouze instrukce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="8b40c-126">The default API projects don't include [HSTS](#hsts) because HSTS is generally a browser only instruction.</span></span> <span data-ttu-id="8b40c-127">Proveďte další volající, například telefon nebo aplikací klasické pracovní plochy, **není** dodržují podle pokynů.</span><span class="sxs-lookup"><span data-stu-id="8b40c-127">Other callers, such as phone or desktop apps, do **not** obey the instruction.</span></span> <span data-ttu-id="8b40c-128">Dokonce i v rámci prohlížeče má jeden ověřené volání rozhraní API přes protokol HTTP rizika v nezabezpečené síti.</span><span class="sxs-lookup"><span data-stu-id="8b40c-128">Even within browsers, a single authenticated call to an API over HTTP has risks on insecure networks.</span></span> <span data-ttu-id="8b40c-129">Zabezpečený přístup, je konfigurace projekty rozhraní API a naslouchat pouze reagovat přes protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8b40c-129">The secure approach is to configure API projects to only listen to and respond over HTTPS.</span></span>

::: moniker-end

## <a name="require-https"></a><span data-ttu-id="8b40c-130">Vyžadovat protokol HTTPS</span><span class="sxs-lookup"><span data-stu-id="8b40c-130">Require HTTPS</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="8b40c-131">Doporučujeme tuto produkční ASP.NET Core volání webové aplikace:</span><span class="sxs-lookup"><span data-stu-id="8b40c-131">We recommend that production ASP.NET Core web apps call:</span></span>

* <span data-ttu-id="8b40c-132">Middleware přesměrování protokolu HTTPS (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) pro přesměrování požadavků protokolu HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8b40c-132">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) to redirect HTTP requests to HTTPS.</span></span>
* <span data-ttu-id="8b40c-133">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) k odeslání hlavičky protokolu HTTP striktní přenosu zabezpečení protokolu (HSTS) pro klienty.</span><span class="sxs-lookup"><span data-stu-id="8b40c-133">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) to send HTTP Strict Transport Security Protocol (HSTS) headers to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="8b40c-134">Aplikace nasazené v konfigurace reverzního proxy serveru povolit proxy pro zpracování zabezpečení připojení (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="8b40c-134">Apps deployed in a reverse proxy configuration allow the proxy to handle connection security (HTTPS).</span></span> <span data-ttu-id="8b40c-135">Pokud proxy server zpracovává také přesměrování protokolu HTTPS, není nutné používat Middleware přesměrování protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8b40c-135">If the proxy also handles HTTPS redirection, there's no need to use HTTPS Redirection Middleware.</span></span> <span data-ttu-id="8b40c-136">Pokud proxy server také zpracovává zápis HSTS hlavičky (například [HSTS nativní podporu v IIS 10.0 (1709) nebo novější](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware není potřebné aplikace.</span><span class="sxs-lookup"><span data-stu-id="8b40c-136">If the proxy server also handles writing HSTS headers (for example, [native HSTS support in IIS 10.0 (1709) or later](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware isn't required by the app.</span></span> <span data-ttu-id="8b40c-137">Další informace najdete v tématu [výslovného nesouhlasu z HTTPS/HSTS při vytvoření projektu](#opt-out-of-httpshsts-on-project-creation).</span><span class="sxs-lookup"><span data-stu-id="8b40c-137">For more information, see [Opt-out of HTTPS/HSTS on project creation](#opt-out-of-httpshsts-on-project-creation).</span></span>

### <a name="usehttpsredirection"></a><span data-ttu-id="8b40c-138">UseHttpsRedirection</span><span class="sxs-lookup"><span data-stu-id="8b40c-138">UseHttpsRedirection</span></span>

<span data-ttu-id="8b40c-139">Následující kód volá `UseHttpsRedirection` v `Startup` třídy:</span><span class="sxs-lookup"><span data-stu-id="8b40c-139">The following code calls `UseHttpsRedirection` in the `Startup` class:</span></span>

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet1&highlight=13)]

<span data-ttu-id="8b40c-140">Předchozí zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="8b40c-140">The preceding highlighted code:</span></span>

* <span data-ttu-id="8b40c-141">Používá výchozí [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span><span class="sxs-lookup"><span data-stu-id="8b40c-141">Uses the default [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span></span>
* <span data-ttu-id="8b40c-142">Používá výchozí [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null), pokud není přepsán `ASPNETCORE_HTTPS_PORT` proměnné prostředí nebo [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span><span class="sxs-lookup"><span data-stu-id="8b40c-142">Uses the default [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null) unless overridden by the `ASPNETCORE_HTTPS_PORT` environment variable or [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span></span>

<span data-ttu-id="8b40c-143">Doporučujeme používat dočasné přesměrování spíše než trvalé přesměrování.</span><span class="sxs-lookup"><span data-stu-id="8b40c-143">We recommend using temporary redirects rather than permanent redirects.</span></span> <span data-ttu-id="8b40c-144">Ukládání do mezipaměti odkazu může způsobit nestabilní chování ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="8b40c-144">Link caching can cause unstable behavior in development environments.</span></span> <span data-ttu-id="8b40c-145">Pokud chcete poslat kód stavu trvalé přesměrování, když se aplikace nachází mimo vývojové prostředí, najdete v článku [Konfigurace trvalé přesměrování v produkčním prostředí](#configure-permanent-redirects-in-production) oddílu.</span><span class="sxs-lookup"><span data-stu-id="8b40c-145">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, see the [Configure permanent redirects in production](#configure-permanent-redirects-in-production) section.</span></span> <span data-ttu-id="8b40c-146">Doporučujeme používat [HSTS](#http-strict-transport-security-protocol-hsts) na signál pro klienty, kteří pouze zabezpečit prostředek požadavků odesílaných do aplikace (jenom v produkčním prostředí).</span><span class="sxs-lookup"><span data-stu-id="8b40c-146">We recommend using [HSTS](#http-strict-transport-security-protocol-hsts) to signal to clients that only secure resource requests should be sent to the app (only in production).</span></span>

### <a name="port-configuration"></a><span data-ttu-id="8b40c-147">Konfigurace portu</span><span class="sxs-lookup"><span data-stu-id="8b40c-147">Port configuration</span></span>

<span data-ttu-id="8b40c-148">Port musí být k dispozici pro daný middleware pro přesměrování nezabezpečený požadavek na protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8b40c-148">A port must be available for the middleware to redirect an insecure request to HTTPS.</span></span> <span data-ttu-id="8b40c-149">Pokud je k dispozici žádný port:</span><span class="sxs-lookup"><span data-stu-id="8b40c-149">If no port is available:</span></span>

* <span data-ttu-id="8b40c-150">Nedojde k přesměrování na protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8b40c-150">Redirection to HTTPS doesn't occur.</span></span>
* <span data-ttu-id="8b40c-151">Middleware protokoluje upozornění "Nepovedlo se určit port https pro přesměrování."</span><span class="sxs-lookup"><span data-stu-id="8b40c-151">The middleware logs the warning "Failed to determine the https port for redirect."</span></span>

<span data-ttu-id="8b40c-152">Zadejte port HTTPS pomocí kteréhokoli z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="8b40c-152">Specify the HTTPS port using any of the following approaches:</span></span>

* <span data-ttu-id="8b40c-153">Nastavte [HttpsRedirectionOptions.HttpsPort](#options).</span><span class="sxs-lookup"><span data-stu-id="8b40c-153">Set [HttpsRedirectionOptions.HttpsPort](#options).</span></span>
* <span data-ttu-id="8b40c-154">Nastavte `ASPNETCORE_HTTPS_PORT` proměnné prostředí nebo [nastavení konfigurace webového hostitele https_port](xref:fundamentals/host/web-host#https-port):</span><span class="sxs-lookup"><span data-stu-id="8b40c-154">Set the `ASPNETCORE_HTTPS_PORT` environment variable or [https_port Web Host configuration setting](xref:fundamentals/host/web-host#https-port):</span></span>

  <span data-ttu-id="8b40c-155">**Klíč**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="8b40c-155">**Key**: `https_port`</span></span>  
  <span data-ttu-id="8b40c-156">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="8b40c-156">**Type**: *string*</span></span>  
  <span data-ttu-id="8b40c-157">**Výchozí**: Výchozí hodnota není nastavená.</span><span class="sxs-lookup"><span data-stu-id="8b40c-157">**Default**: A default value isn't set.</span></span>  
  <span data-ttu-id="8b40c-158">**Sada s použitím**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="8b40c-158">**Set using**: `UseSetting`</span></span>  
  <span data-ttu-id="8b40c-159">**Proměnná prostředí**: `<PREFIX_>HTTPS_PORT` (Předpona je `ASPNETCORE_` při použití [webového hostitele](xref:fundamentals/host/web-host).)</span><span class="sxs-lookup"><span data-stu-id="8b40c-159">**Environment variable**: `<PREFIX_>HTTPS_PORT` (The prefix is `ASPNETCORE_` when using the [Web Host](xref:fundamentals/host/web-host).)</span></span>

  <span data-ttu-id="8b40c-160">Při konfiguraci <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> v `Program`:</span><span class="sxs-lookup"><span data-stu-id="8b40c-160">When configuring an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> in `Program`:</span></span>

  [!code-csharp[](enforcing-ssl/sample-snapshot/Program.cs?name=snippet_Program&highlight=10)]
* <span data-ttu-id="8b40c-161">Označení portu pomocí zabezpečené schéma `ASPNETCORE_URLS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="8b40c-161">Indicate a port with the secure scheme using the `ASPNETCORE_URLS` environment variable.</span></span> <span data-ttu-id="8b40c-162">Proměnná prostředí nakonfiguruje server.</span><span class="sxs-lookup"><span data-stu-id="8b40c-162">The environment variable configures the server.</span></span> <span data-ttu-id="8b40c-163">Middleware nepřímo zjistí port HTTPS přes <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="8b40c-163">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="8b40c-164">Tento postup nefunguje v nasazeních reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="8b40c-164">This approach doesn't work in reverse proxy deployments.</span></span>
* <span data-ttu-id="8b40c-165">Při vývoji, nastavte adresu URL HTTPS *launchsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="8b40c-165">In development, set an HTTPS URL in *launchsettings.json*.</span></span> <span data-ttu-id="8b40c-166">Povolení HTTPS, když se používá služba IIS Express.</span><span class="sxs-lookup"><span data-stu-id="8b40c-166">Enable HTTPS when IIS Express is used.</span></span>
* <span data-ttu-id="8b40c-167">Konfigurace koncového bodu adresy URL HTTPS nasazení veřejnou hraniční [Kestrel](xref:fundamentals/servers/kestrel) serveru nebo [HTTP.sys](xref:fundamentals/servers/httpsys) serveru.</span><span class="sxs-lookup"><span data-stu-id="8b40c-167">Configure an HTTPS URL endpoint for a public-facing edge deployment of [Kestrel](xref:fundamentals/servers/kestrel) server or [HTTP.sys](xref:fundamentals/servers/httpsys) server.</span></span> <span data-ttu-id="8b40c-168">Pouze **jeden port HTTPS** používají aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8b40c-168">Only **one HTTPS port** is used by the app.</span></span> <span data-ttu-id="8b40c-169">Middleware zjistí port přes <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="8b40c-169">The middleware discovers the port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span>

> [!NOTE]
> <span data-ttu-id="8b40c-170">Při spuštění aplikace v konfiguraci reverzní proxy server, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="8b40c-170">When an app is run in a reverse proxy configuration, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> isn't available.</span></span> <span data-ttu-id="8b40c-171">Nastavte port pomocí jednoho z jiných postupů popsaných v této části.</span><span class="sxs-lookup"><span data-stu-id="8b40c-171">Set the port using one of the other approaches described in this section.</span></span>

<span data-ttu-id="8b40c-172">Při Kestrel nebo ovladač HTTP.sys se používá jako veřejnou hraniční server, musí být Kestrel nebo ovladač HTTP.sys nakonfigurovaná k naslouchání na obojí:</span><span class="sxs-lookup"><span data-stu-id="8b40c-172">When Kestrel or HTTP.sys is used as a public-facing edge server, Kestrel or HTTP.sys must be configured to listen on both:</span></span>

* <span data-ttu-id="8b40c-173">Zabezpečený port, ve kterém se klient přesměruje (obvykle 443 v produkčním prostředí a 5001 ve vývoji).</span><span class="sxs-lookup"><span data-stu-id="8b40c-173">The secure port where the client is redirected (typically, 443 in production and 5001 in development).</span></span>
* <span data-ttu-id="8b40c-174">Nezabezpečené port (standardně 80 v produkčním prostředí) a 5000 ve vývoji.</span><span class="sxs-lookup"><span data-stu-id="8b40c-174">The insecure port (typically, 80 in production and 5000 in development).</span></span>

<span data-ttu-id="8b40c-175">Nezabezpečené port musí být přístupné pro klienta v pořadí pro aplikaci pro příjem nezabezpečený požadavek a přesměrování klienta na zabezpečený port.</span><span class="sxs-lookup"><span data-stu-id="8b40c-175">The insecure port must be accessible by the client in order for the app to receive an insecure request and redirect the client to the secure port.</span></span>

<span data-ttu-id="8b40c-176">Další informace najdete v tématu [konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) nebo <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="8b40c-176">For more information, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) or <xref:fundamentals/servers/httpsys>.</span></span>

### <a name="deployment-scenarios"></a><span data-ttu-id="8b40c-177">Scénáře nasazení</span><span class="sxs-lookup"><span data-stu-id="8b40c-177">Deployment scenarios</span></span>

<span data-ttu-id="8b40c-178">Všechny brány firewall mezi klientem a serverem musí mít také komunikační porty otevřete pro provoz.</span><span class="sxs-lookup"><span data-stu-id="8b40c-178">Any firewall between the client and server must also have communication ports open for traffic.</span></span>

<span data-ttu-id="8b40c-179">Pokud žádosti se předávají v konfiguraci reverzní proxy server, použijte [předané Middleware záhlaví](xref:host-and-deploy/proxy-load-balancer) před voláním Middleware přesměrování protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8b40c-179">If requests are forwarded in a reverse proxy configuration, use [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) before calling HTTPS Redirection Middleware.</span></span> <span data-ttu-id="8b40c-180">Předané Middleware záhlaví aktualizací `Request.Scheme`, použije `X-Forwarded-Proto` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="8b40c-180">Forwarded Headers Middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header.</span></span> <span data-ttu-id="8b40c-181">Povolení middleware přesměrování identifikátory URI a další zásady zabezpečení fungovat správně.</span><span class="sxs-lookup"><span data-stu-id="8b40c-181">The middleware permits redirect URIs and other security policies to work correctly.</span></span> <span data-ttu-id="8b40c-182">Kdy předané Middleware záhlaví nepoužívá, back-endu aplikace nemusí dostávat správné schéma a ukládaly ve smyčce přesměrování.</span><span class="sxs-lookup"><span data-stu-id="8b40c-182">When Forwarded Headers Middleware isn't used, the backend app might not receive the correct scheme and end up in a redirect loop.</span></span> <span data-ttu-id="8b40c-183">Běžné chybová zpráva koncovým uživatelem se, že došlo k příliš mnoha přesměrování.</span><span class="sxs-lookup"><span data-stu-id="8b40c-183">A common end user error message is that too many redirects have occurred.</span></span>

<span data-ttu-id="8b40c-184">Při nasazování do služby Azure App Service, postupujte podle pokynů v [kurzu: Vytvoření vazby existujícího vlastního certifikátu SSL k Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="8b40c-184">When deploying to Azure App Service, follow the guidance in [Tutorial: Bind an existing custom SSL certificate to Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

### <a name="options"></a><span data-ttu-id="8b40c-185">Možnosti</span><span class="sxs-lookup"><span data-stu-id="8b40c-185">Options</span></span>

<span data-ttu-id="8b40c-186">Následující zvýrazněný kód volá [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) konfigurace middlewaru možností:</span><span class="sxs-lookup"><span data-stu-id="8b40c-186">The following highlighted code calls [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) to configure middleware options:</span></span>

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet2&highlight=14-99)]

<span data-ttu-id="8b40c-187">Volání `AddHttpsRedirection` jenom je potřeba změnit hodnoty `HttpsPort` nebo `RedirectStatusCode`.</span><span class="sxs-lookup"><span data-stu-id="8b40c-187">Calling `AddHttpsRedirection` is only necessary to change the values of `HttpsPort` or `RedirectStatusCode`.</span></span>

<span data-ttu-id="8b40c-188">Předchozí zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="8b40c-188">The preceding highlighted code:</span></span>

* <span data-ttu-id="8b40c-189">Nastaví [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) k <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, což je výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="8b40c-189">Sets [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) to <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, which is the default value.</span></span> <span data-ttu-id="8b40c-190">Použijte pole <xref:Microsoft.AspNetCore.Http.StatusCodes> třídu pro přiřazení k `RedirectStatusCode`.</span><span class="sxs-lookup"><span data-stu-id="8b40c-190">Use the fields of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for assignments to `RedirectStatusCode`.</span></span>
* <span data-ttu-id="8b40c-191">Nastaví HTTPS port 5001.</span><span class="sxs-lookup"><span data-stu-id="8b40c-191">Sets the HTTPS port to 5001.</span></span> <span data-ttu-id="8b40c-192">Výchozí hodnota je 443.</span><span class="sxs-lookup"><span data-stu-id="8b40c-192">The default value is 443.</span></span>

#### <a name="configure-permanent-redirects-in-production"></a><span data-ttu-id="8b40c-193">Konfigurace trvalé přesměrování v produkčním prostředí</span><span class="sxs-lookup"><span data-stu-id="8b40c-193">Configure permanent redirects in production</span></span>

<span data-ttu-id="8b40c-194">Middleware odeslání výchozí hodnota je [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) s všechny přesměrování.</span><span class="sxs-lookup"><span data-stu-id="8b40c-194">The middleware defaults to sending a [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) with all redirects.</span></span> <span data-ttu-id="8b40c-195">Pokud chcete poslat kód stavu trvalé přesměrování, když se aplikace nachází mimo vývojové prostředí, zabalte možnosti konfigurace middlewaru v podmíněnou kontrolu mimo vývojové prostředí.</span><span class="sxs-lookup"><span data-stu-id="8b40c-195">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, wrap the middleware options configuration in a conditional check for a non-Development environment.</span></span>

<span data-ttu-id="8b40c-196">Při konfiguraci `IWebHostBuilder` v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="8b40c-196">When configuring an `IWebHostBuilder` in *Startup.cs*:</span></span>

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

## <a name="https-redirection-middleware-alternative-approach"></a><span data-ttu-id="8b40c-197">Alternativním přístupem Middleware přesměrování protokolu HTTPS</span><span class="sxs-lookup"><span data-stu-id="8b40c-197">HTTPS Redirection Middleware alternative approach</span></span>

<span data-ttu-id="8b40c-198">Alternativou k používání HTTPS přesměrování Middleware (`UseHttpsRedirection`), je použít Middleware pro přepis adres URL (`AddRedirectToHttps`).</span><span class="sxs-lookup"><span data-stu-id="8b40c-198">An alternative to using HTTPS Redirection Middleware (`UseHttpsRedirection`) is to use URL Rewriting Middleware (`AddRedirectToHttps`).</span></span> <span data-ttu-id="8b40c-199">`AddRedirectToHttps` Můžete také nastavit stavový kód a portu při spuštění přesměrování.</span><span class="sxs-lookup"><span data-stu-id="8b40c-199">`AddRedirectToHttps` can also set the status code and port when the redirect is executed.</span></span> <span data-ttu-id="8b40c-200">Další informace najdete v tématu [Middleware pro přepis adres URL](xref:fundamentals/url-rewriting).</span><span class="sxs-lookup"><span data-stu-id="8b40c-200">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span>

<span data-ttu-id="8b40c-201">Při přesměrování na HTTPS bez nutnosti další přesměrování pravidla, doporučujeme používat Middleware přesměrování protokolu HTTPS (`UseHttpsRedirection`) popsané v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="8b40c-201">When redirecting to HTTPS without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware (`UseHttpsRedirection`) described in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

<span data-ttu-id="8b40c-202">[RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) se používá tak, aby vyžadovala protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8b40c-202">The [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) is used to require HTTPS.</span></span> <span data-ttu-id="8b40c-203">`[RequireHttpsAttribute]` můžete uspořádání, řadiče nebo metod, nebo je možné uplatňovat globálně.</span><span class="sxs-lookup"><span data-stu-id="8b40c-203">`[RequireHttpsAttribute]` can decorate controllers or methods, or can be applied globally.</span></span> <span data-ttu-id="8b40c-204">Použít atribut globálně, přidejte následující kód, který `ConfigureServices` v `Startup`:</span><span class="sxs-lookup"><span data-stu-id="8b40c-204">To apply the attribute globally, add the following code to `ConfigureServices` in `Startup`:</span></span>

[!code-csharp[](~/security/authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet2&highlight=4-999)]

<span data-ttu-id="8b40c-205">Předchozí zvýrazněný kód technologie používat všechny požadavky `HTTPS`, proto požadavky HTTP jsou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="8b40c-205">The preceding highlighted code requires all requests use `HTTPS`; therefore, HTTP requests are ignored.</span></span> <span data-ttu-id="8b40c-206">Následující zvýrazněný kód přesměruje všechny požadavky HTTP na HTTPS:</span><span class="sxs-lookup"><span data-stu-id="8b40c-206">The following highlighted code redirects all HTTP requests to HTTPS:</span></span>

[!code-csharp[](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet_AddRedirectToHttps&highlight=7-999)]

<span data-ttu-id="8b40c-207">Další informace najdete v tématu [Middleware pro přepis adres URL](xref:fundamentals/url-rewriting).</span><span class="sxs-lookup"><span data-stu-id="8b40c-207">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span> <span data-ttu-id="8b40c-208">Middleware také umožňuje, že aplikace nastaví stavový kód nebo kód stavu a port, který při spuštění přesměrování.</span><span class="sxs-lookup"><span data-stu-id="8b40c-208">The middleware also permits the app to set the status code or the status code and the port when the redirect is executed.</span></span>

<span data-ttu-id="8b40c-209">Globálně vyžadování protokolu HTTPS (`options.Filters.Add(new RequireHttpsAttribute());`) je z bezpečnostních důvodů.</span><span class="sxs-lookup"><span data-stu-id="8b40c-209">Requiring HTTPS globally (`options.Filters.Add(new RequireHttpsAttribute());`) is a security best practice.</span></span> <span data-ttu-id="8b40c-210">Použití `[RequireHttps]` atribut na všechny řadiče/Razor Pages není považován za stejně bezpečné jako globálně vyžadování protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8b40c-210">Applying the `[RequireHttps]` attribute to all controllers/Razor Pages isn't considered as secure as requiring HTTPS globally.</span></span> <span data-ttu-id="8b40c-211">Nebudete moct zaručit, `[RequireHttps]` atributu se použije při přidání nových řadičů a stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="8b40c-211">You can't guarantee the `[RequireHttps]` attribute is applied when new controllers and Razor Pages are added.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a><span data-ttu-id="8b40c-212">Protokol zabezpečení striktní přenos HTTP (HSTS)</span><span class="sxs-lookup"><span data-stu-id="8b40c-212">HTTP Strict Transport Security Protocol (HSTS)</span></span>

<span data-ttu-id="8b40c-213">Za [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [striktní přenos HTTP zabezpečení (HSTS)](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) je vylepšení zabezpečení přihlášení, která je zadána pomocí hlavičky odpovědi webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="8b40c-213">Per [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [HTTP Strict Transport Security (HSTS)](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) is an opt-in security enhancement that's specified by a web app through the use of a response header.</span></span> <span data-ttu-id="8b40c-214">Když [prohlížeč, který podporuje HSTS](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support) obdrží toto záhlaví:</span><span class="sxs-lookup"><span data-stu-id="8b40c-214">When a [browser that supports HSTS](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support) receives this header:</span></span>

* <span data-ttu-id="8b40c-215">Prohlížeč ukládá konfiguraci pro doménu, která zabrání odeslání libovolné komunikaci přes protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="8b40c-215">The browser stores configuration for the domain that prevents sending any communication over HTTP.</span></span> <span data-ttu-id="8b40c-216">Prohlížeč vynutí veškerou komunikaci přes protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8b40c-216">The browser forces all communication over HTTPS.</span></span>
* <span data-ttu-id="8b40c-217">Prohlížeč znemožní uživateli pomocí certifikátů nedůvěryhodný nebo neplatný.</span><span class="sxs-lookup"><span data-stu-id="8b40c-217">The browser prevents the user from using untrusted or invalid certificates.</span></span> <span data-ttu-id="8b40c-218">Prohlížeč zakáže výzev, které umožní uživateli tohoto certifikátu dočasně důvěřovat.</span><span class="sxs-lookup"><span data-stu-id="8b40c-218">The browser disables prompts that allow a user to temporarily trust such a certificate.</span></span>

<span data-ttu-id="8b40c-219">Protože klient se vynucuje HSTS má určitá omezení:</span><span class="sxs-lookup"><span data-stu-id="8b40c-219">Because HSTS is enforced by the client it has some limitations:</span></span>

* <span data-ttu-id="8b40c-220">Klient musí podporovat HSTS.</span><span class="sxs-lookup"><span data-stu-id="8b40c-220">The client must support HSTS.</span></span>
* <span data-ttu-id="8b40c-221">HSTS vyžaduje alespoň jeden úspěšného požadavku HTTPS k navázání HSTS zásad.</span><span class="sxs-lookup"><span data-stu-id="8b40c-221">HSTS requires at least one successful HTTPS request to establish the HSTS policy.</span></span>
* <span data-ttu-id="8b40c-222">Aplikace musí zkontrolovat každého požadavku HTTP a přesměrování nebo zamítnutí žádosti protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="8b40c-222">The application must check every HTTP request and redirect or reject the HTTP request.</span></span>

<span data-ttu-id="8b40c-223">Implementuje HSTS s ASP.NET Core 2.1 nebo vyšší `UseHsts` – metoda rozšíření.</span><span class="sxs-lookup"><span data-stu-id="8b40c-223">ASP.NET Core 2.1 or later implements HSTS with the `UseHsts` extension method.</span></span> <span data-ttu-id="8b40c-224">Následující kód volá `UseHsts` když aplikace není v [vývojový režim](xref:fundamentals/environments):</span><span class="sxs-lookup"><span data-stu-id="8b40c-224">The following code calls `UseHsts` when the app isn't in [development mode](xref:fundamentals/environments):</span></span>

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet1&highlight=10)]

<span data-ttu-id="8b40c-225">`UseHsts` není doporučeno při vývoji vzhledem k tomu, že jsou nastavení HSTS dokonalé prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="8b40c-225">`UseHsts` isn't recommended in development because the HSTS settings are highly cacheable by browsers.</span></span> <span data-ttu-id="8b40c-226">Ve výchozím nastavení `UseHsts` nezahrnuje adresu místní zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="8b40c-226">By default, `UseHsts` excludes the local loopback address.</span></span>

<span data-ttu-id="8b40c-227">Pro produkční prostředí implementace HTTPS poprvé, nastavte počáteční [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) malou hodnotu pomocí jedné z <xref:System.TimeSpan> metody.</span><span class="sxs-lookup"><span data-stu-id="8b40c-227">For production environments implementing HTTPS for the first time, set the initial [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) to a small value using one of the <xref:System.TimeSpan> methods.</span></span> <span data-ttu-id="8b40c-228">Nastavte hodnotu hodiny na no více než jeden den v případě, že budete potřebovat obnovit infrastruktury HTTPS do HTTP.</span><span class="sxs-lookup"><span data-stu-id="8b40c-228">Set the value from hours to no more than a single day in case you need to revert the HTTPS infrastructure to HTTP.</span></span> <span data-ttu-id="8b40c-229">Poté, co jste si jisti udržitelnost konfigurace protokolu HTTPS, zvýšit hodnotu max-age HSTS. běžně používané hodnota je 1 rok.</span><span class="sxs-lookup"><span data-stu-id="8b40c-229">After you're confident in the sustainability of the HTTPS configuration, increase the HSTS max-age value; a commonly used value is one year.</span></span>

<span data-ttu-id="8b40c-230">Následující kód:</span><span class="sxs-lookup"><span data-stu-id="8b40c-230">The following code:</span></span>

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet2&highlight=5-12)]

* <span data-ttu-id="8b40c-231">Nastaví parametr přednačtení záhlaví zabezpečení přenosu Strict.</span><span class="sxs-lookup"><span data-stu-id="8b40c-231">Sets the preload parameter of the Strict-Transport-Security header.</span></span> <span data-ttu-id="8b40c-232">Předinstalační není součástí [specifikaci RFC HSTS](https://tools.ietf.org/html/rfc6797), ale podporuje webových prohlížečů přednačtení HSTS weby na čerstvou instalaci.</span><span class="sxs-lookup"><span data-stu-id="8b40c-232">Preload isn't part of the [RFC HSTS specification](https://tools.ietf.org/html/rfc6797), but is supported by web browsers to preload HSTS sites on fresh install.</span></span> <span data-ttu-id="8b40c-233">Zobrazit [ https://hstspreload.org/ ](https://hstspreload.org/) Další informace.</span><span class="sxs-lookup"><span data-stu-id="8b40c-233">See [https://hstspreload.org/](https://hstspreload.org/) for more information.</span></span>
* <span data-ttu-id="8b40c-234">Umožňuje [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), které se vztahují zásady HSTS na hostiteli subdomény.</span><span class="sxs-lookup"><span data-stu-id="8b40c-234">Enables [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), which applies the HSTS policy to Host subdomains.</span></span>
* <span data-ttu-id="8b40c-235">Explicitně nastaví parametr max-age záhlaví zabezpečení přenosu Strict na 60 dnů.</span><span class="sxs-lookup"><span data-stu-id="8b40c-235">Explicitly sets the max-age parameter of the Strict-Transport-Security header to 60 days.</span></span> <span data-ttu-id="8b40c-236">Pokud není nastavena výchozí hodnota je 30 dní.</span><span class="sxs-lookup"><span data-stu-id="8b40c-236">If not set, defaults to 30 days.</span></span> <span data-ttu-id="8b40c-237">Najdete v článku [max-age směrnice](https://tools.ietf.org/html/rfc6797#section-6.1.1) Další informace.</span><span class="sxs-lookup"><span data-stu-id="8b40c-237">See the [max-age directive](https://tools.ietf.org/html/rfc6797#section-6.1.1) for more information.</span></span>
* <span data-ttu-id="8b40c-238">Přidá `example.com` do seznamu hostitelů mají vyloučit.</span><span class="sxs-lookup"><span data-stu-id="8b40c-238">Adds `example.com` to the list of hosts to exclude.</span></span>

<span data-ttu-id="8b40c-239">`UseHsts` vyloučí následující zpětné smyčky hostitele:</span><span class="sxs-lookup"><span data-stu-id="8b40c-239">`UseHsts` excludes the following loopback hosts:</span></span>

* <span data-ttu-id="8b40c-240">`localhost` : IPv4 adresu zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="8b40c-240">`localhost` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="8b40c-241">`127.0.0.1` : IPv4 adresu zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="8b40c-241">`127.0.0.1` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="8b40c-242">`[::1]` : Adresu zpětné smyčky protokolu IPv6.</span><span class="sxs-lookup"><span data-stu-id="8b40c-242">`[::1]` : The IPv6 loopback address.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="opt-out-of-httpshsts-on-project-creation"></a><span data-ttu-id="8b40c-243">Odhlásit HTTPS/HSTS při vytvoření projektu</span><span class="sxs-lookup"><span data-stu-id="8b40c-243">Opt-out of HTTPS/HSTS on project creation</span></span>

<span data-ttu-id="8b40c-244">V některých scénářích služby back-endu kde zabezpečení připojení probíhá v veřejnou hraniční části sítě není vyžadována konfigurace zabezpečení připojení v každém uzlu.</span><span class="sxs-lookup"><span data-stu-id="8b40c-244">In some backend service scenarios where connection security is handled at the public-facing edge of the network, configuring connection security at each node isn't required.</span></span> <span data-ttu-id="8b40c-245">Webová aplikace vygenerované ze šablony v sadě Visual Studio nebo z [dotnet nové](/dotnet/core/tools/dotnet-new) povolit příkaz [HTTPS přesměrování](#require-https) a [HSTS](#http-strict-transport-security-protocol-hsts).</span><span class="sxs-lookup"><span data-stu-id="8b40c-245">Web apps generated from the templates in Visual Studio or from the [dotnet new](/dotnet/core/tools/dotnet-new) command enable [HTTPS redirection](#require-https) and [HSTS](#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="8b40c-246">Pro nasazení, které nevyžadují tyto scénáře které můžete výslovný nesouhlas s HTTPS/HSTS při vytvoření aplikace ze šablony.</span><span class="sxs-lookup"><span data-stu-id="8b40c-246">For deployments that don't require these scenarios, you can opt-out of HTTPS/HSTS when the app is created from the template.</span></span>

<span data-ttu-id="8b40c-247">Chcete odhlásit HTTPS/HSTS:</span><span class="sxs-lookup"><span data-stu-id="8b40c-247">To opt-out of HTTPS/HSTS:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8b40c-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b40c-248">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="8b40c-249">Zrušte zaškrtnutí políčka **konfigurace pro protokol HTTPS** zaškrtávací políčko.</span><span class="sxs-lookup"><span data-stu-id="8b40c-249">Uncheck the **Configure for HTTPS** check box.</span></span>

![Nové webové aplikace ASP.NET Core dialogové okno zobrazující konfigurací pro HTTPS zaškrtávací políčko nevybranou.](enforcing-ssl/_static/out.png)

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="8b40c-251">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="8b40c-251">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="8b40c-252">Použití `--no-https` možnost.</span><span class="sxs-lookup"><span data-stu-id="8b40c-252">Use the `--no-https` option.</span></span> <span data-ttu-id="8b40c-253">Příklad</span><span class="sxs-lookup"><span data-stu-id="8b40c-253">For example</span></span>

```console
dotnet new webapp --no-https
```

---

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a><span data-ttu-id="8b40c-254">Důvěřovat certifikátu vývoj pro ASP.NET Core HTTPS ve Windows a macOS</span><span class="sxs-lookup"><span data-stu-id="8b40c-254">Trust the ASP.NET Core HTTPS development certificate on Windows and macOS</span></span>

<span data-ttu-id="8b40c-255">Sada .NET core SDK zahrnuje vývoj nesprávný certifikát HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8b40c-255">.NET Core SDK includes a HTTPS development certificate.</span></span> <span data-ttu-id="8b40c-256">Certifikát je nainstalován jako součást prvního spuštění.</span><span class="sxs-lookup"><span data-stu-id="8b40c-256">The certificate is installed as part of the first-run experience.</span></span> <span data-ttu-id="8b40c-257">Například `dotnet --info` vytváří výstup podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="8b40c-257">For example, `dotnet --info` produces output similar to the following:</span></span>

```text
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

<span data-ttu-id="8b40c-258">Instalace sady .NET Core SDK nainstaluje certifikát pro vývoj ASP.NET Core HTTPS do úložiště certifikátů místního uživatele.</span><span class="sxs-lookup"><span data-stu-id="8b40c-258">Installing the .NET Core SDK installs the ASP.NET Core HTTPS development certificate to the local user certificate store.</span></span> <span data-ttu-id="8b40c-259">Certifikát je nainstalovaný, ale není důvěryhodný.</span><span class="sxs-lookup"><span data-stu-id="8b40c-259">The certificate has been installed, but it's not trusted.</span></span> <span data-ttu-id="8b40c-260">Důvěřovat certifikátu provést jednorázové krok a spusťte dotnet `dev-certs` nástroje:</span><span class="sxs-lookup"><span data-stu-id="8b40c-260">To trust the certificate perform the one-time step to run the dotnet `dev-certs` tool:</span></span>

```console
dotnet dev-certs https --trust
```

<span data-ttu-id="8b40c-261">Následující příkaz nápovědy týkající `dev-certs` nástroje:</span><span class="sxs-lookup"><span data-stu-id="8b40c-261">The following command provides help on the `dev-certs` tool:</span></span>

```console
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a><span data-ttu-id="8b40c-262">Jak nastavit certifikát pro vývojáře pro Docker</span><span class="sxs-lookup"><span data-stu-id="8b40c-262">How to set up a developer certificate for Docker</span></span>

<span data-ttu-id="8b40c-263">Zobrazit [tento problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/6199).</span><span class="sxs-lookup"><span data-stu-id="8b40c-263">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6199).</span></span>

::: moniker-end

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a><span data-ttu-id="8b40c-264">Důvěřovat certifikátu protokolu HTTPS ze subsystému Windows pro Linux</span><span class="sxs-lookup"><span data-stu-id="8b40c-264">Trust HTTPS certificate from Windows Subsystem for Linux</span></span>

<span data-ttu-id="8b40c-265">Subsystém Windows pro Linux (WSL) generuje HTTPS certifikátu podepsaného svým držitelem. Postup konfigurace úložiště certifikátů Windows důvěřovat certifikátům WSL:</span><span class="sxs-lookup"><span data-stu-id="8b40c-265">The Windows Subsystem for Linux (WSL) generates a HTTPS self-signed cert. To configure the Windows certificate store to trust the WSL certificate:</span></span>

* <span data-ttu-id="8b40c-266">Spuštěním následujícího příkazu exportujte certifikát WSL vygeneruje: `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span><span class="sxs-lookup"><span data-stu-id="8b40c-266">Run the following command to export the WSL generated certificate: `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span></span>
* <span data-ttu-id="8b40c-267">V okně WSL spusťte následující příkaz: `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span><span class="sxs-lookup"><span data-stu-id="8b40c-267">In a WSL window, run the following command: `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span></span>

  <span data-ttu-id="8b40c-268">Ve výstupu předchozího příkazu nastaví proměnné prostředí, takže tento důvěryhodný certifikát Windows používá Linux.</span><span class="sxs-lookup"><span data-stu-id="8b40c-268">The preceding command sets the environment variables so Linux uses the Windows trusted certificate.</span></span>

## <a name="additional-information"></a><span data-ttu-id="8b40c-269">Další informace</span><span class="sxs-lookup"><span data-stu-id="8b40c-269">Additional information</span></span>

* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="8b40c-270">Hostitele ASP.NET Core v Linuxu pomocí Apache: Konfigurace protokolu HTTPS</span><span class="sxs-lookup"><span data-stu-id="8b40c-270">Host ASP.NET Core on Linux with Apache: HTTPS configuration</span></span>](xref:host-and-deploy/linux-apache#https-configuration)
* [<span data-ttu-id="8b40c-271">Hostitele ASP.NET Core v Linuxu se serverem Nginx: Konfigurace protokolu HTTPS</span><span class="sxs-lookup"><span data-stu-id="8b40c-271">Host ASP.NET Core on Linux with Nginx: HTTPS configuration</span></span>](xref:host-and-deploy/linux-nginx#https-configuration)
* [<span data-ttu-id="8b40c-272">Nastavení protokolu SSL ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="8b40c-272">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [<span data-ttu-id="8b40c-273">Podpora prohlížeče OWASP HSTS</span><span class="sxs-lookup"><span data-stu-id="8b40c-273">OWASP HSTS browser support</span></span>](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
